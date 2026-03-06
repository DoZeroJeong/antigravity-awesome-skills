# SQL 쿼리 최적화 패턴 플레이북 (Django ORM 가이드)

이 파일은 `sql-optimization-patterns` 스킬에서 참조하는 상세 패턴, 체크리스트, 그리고 Django ORM 기반의 해결책과 최적화 패턴을 포함합니다.

## 핵심 개념 (Core Concepts)

### 1. 쿼리 실행 계획 확인 (EXPLAIN)

Django에서는 쿼리셋의 성능을 평가할 때 백엔드 DB의 Query Plan을 분석하는 것이 중요합니다.
Django `QuerySet.explain()` 파이프라인이나 `django-debug-toolbar`를 활용하세요.

```python
# 일반적인 Explain
print(User.objects.filter(email='user@example.com').explain())

# PostgreSQL 특화 (아날라이즈 포함)
print(User.objects.filter(email='user@example.com').explain(analyze=True, verbose=True))
```

### 2. 인덱스 전략 (Index Strategies)

데이터베이스 성능 튜닝에서 인덱스는 가장 강력한 도구이며, Django 모델에 손쉽게 정의할 수 있습니다.

```python
from django.db import models
from django.db.models import Index, Func, F

class User(models.Model):
    email = models.EmailField(unique=True)
    status = models.CharField(max_length=20)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        indexes = [
            # 단일 B-Tree 인덱스 (기본)
            Index(fields=['email'], name='idx_user_email'),
            
            # 복합 인덱스 (순서가 매우 중요함)
            Index(fields=['status', 'created_at'], name='idx_status_created'),
            
            # 부분 인덱스 (Partial Index; 특정 조건에서만 인덱싱하여 크기 절약)
            Index(
                fields=['email'], 
                name='idx_active_user_email',
                condition=models.Q(status='active')
            ),
            
            # 표현식 인덱스 (Expression Index; 소문자 변환 등 함수 적용 상태로 인덱싱)
            # (PostgreSQL/최신 Django 지원)
            Index(
                Func(F('email'), function='LOWER'), 
                name='idx_users_lower_email'
            ),
            
            # 커버링 인덱스 (포함된 필드를 읽을 때 테이블 접근 과정을 생략)
            # PostgreSQL의 INCLUDE 구문 활용
            Index(fields=['email'], include=['status', 'created_at'], name='idx_email_cover')
        ]
```

---

## 최적화 패턴 (Optimization Patterns)

### 패턴 1: N+1 쿼리 완벽 제거

가장 빈번하게 발생하며 성능을 크게 떨어뜨리는 원인입니다. `select_related()`와 `prefetch_related()`를 올바르게 혼용해 써야 합니다.

**Anti-Pattern (N+1 발생):**

```python
# Bad: 유저 10명을 찾고 (1 쿼리), 각각에 대해 관련된 profile을 찾음 (10 쿼리)
users = User.objects.all()[:10]
for user in users:
    print(user.profile.bio)  # 여기서 계속 DB 히트 발생
```

**Good 패턴 (JOIN 또는 배치 로딩 방식의 Django ORM 치환):**

```python
# 정방향 (Foreign Key, OneToOne) 관계 -> JOIN (select_related)
users = User.objects.select_related('profile').all()[:10]
for user in users:
    print(user.profile.bio)  # DB 히트 없음 (이미 JOIN으로 다 가져옴)

# 역방향 (ManyToMany, 역방향 Foreign Key) 관계 -> IN 절을 활용한 분리 쿼리 (prefetch_related)
users = User.objects.prefetch_related('orders').all()[:10]
for user in users:
    print([order.total for order in user.orders.all()])  # DB 히트 없음
```

### 패턴 2: 대용량 데이터 페이지네이션 최적화

**Bad:** 뒷 페이지로 넘어갈수록 OFFSET 스캔 비용이 급격히 증가합니다.

```python
# 매우 느림: OFFSET 사용
from django.core.paginator import Paginator
users = User.objects.order_by('-created_at')
paginator = Paginator(users, 20)
page_1000 = paginator.get_page(1000) # LIMIT 20 OFFSET 20000 쿼리 생성
```

**Good: 커서 기반 페이지네이션 (Cursor Pagination) 또는 Keyset Pagination**

```python
# ID나 날짜를 기억했다가 그 이후부터 데이터를 가져옵니다.
latest_id = 20000 # 이전 페이지 마지막 아이템의 ID 기록
page_users = User.objects.filter(id__lt=latest_id).order_by('-id')[:20]
```

*(DRF에서는 `CursorPagination` 클래스를 사용하면 훨씬 간단합니다.)*

### 패턴 3: 효율적인 집계 함수 (Aggregate)

모든 객체를 `all()`로 부른 뒤 Python `len()`이나 반복문으로 집계하는 것은 금물입니다.

**Count와 그룹핑:**

```python
from django.db.models import Count, Sum

# Bad
count = len(User.objects.filter(status='active'))

# Good
count = User.objects.filter(status='active').count()

# annotate()를 쓰면 GROUP BY 효율 극대화
# 각 유저별로 그들의 'completed' 상태인 주문 개수를 집계
from django.db.models import Q
users_with_order_counts = User.objects.annotate(
    completed_orders=Count('orders', filter=Q(orders__status='completed'))
)
```

### 패턴 4: 서브쿼리 최적화 (Subquery Optimization)

일부 데이터를 가져오기 위해 쓸데없는 역참조 데이터 뭉치를 메모리에 부르는 것을 막을 수 있습니다.

```python
# 유저별 "가장 최근 주문의 총액"을 가져오고자 할 때
from django.db.models import Subquery, OuterRef

# 최신 오더 서브쿼리 정의: 상위 쿼리(User)의 id를 참조(OuterRef)합니다.
newest_order = Order.objects.filter(
    user=OuterRef('pk')
).order_by('-created_at')

# User 목록에 최근 주문 금액 필드를 annotate
users = User.objects.annotate(
    latest_order_total=Subquery(newest_order.values('total')[:1])
)
```

### 패턴 5: 대량 데이터 처리 (Batch Operations)

개별 요소마다 한 번씩 `save()`하는 것은 매우 비효율적입니다.

**대량 생성 (Batch Insert):**

```python
# Bad
for data in data_list:
    User.objects.create(**data)

# Good (batch_size 조정으로 메모리 터짐 방지)
user_instances = [User(**data) for data in data_list]
User.objects.bulk_create(user_instances, batch_size=1000)
```

**대량 업데이트 (Batch Update):**

```python
# Bad
for user in users_to_update:
    user.status = 'inactive'
    user.save()

# Good (QuerySet 차원 업데이트)
User.objects.filter(id__in=user_id_list).update(status='inactive')

# Good (인스턴스별로 서로 다른 값 업데이트 시)
for user in user_instances:
    # 파이썬 레벨에서 필드 값 세팅
    user.status = calculate_status(user)
    
# DB에 한 번에 변경 반영 (id 매칭)
User.objects.bulk_update(user_instances, ['status'], batch_size=1000)
```

## 고급 기법 (Advanced Techniques)

### Materialized Views 관리

복잡하고 무거운 대시보드 쿼리를 미리 연산해두는 방법입니다.
Django ORM은 Materialized View를 직접 다루는 명령어가 기본으로 없으므로, 직접 Raw SQL + 빈번히 동기화하는 태스크를 작성하거나, 관련 서드파티 패키지(예: `django-postgres-extra`)를 사용할 수 있습니다.

```python
# 마이그레이션 파일 내 Raw SQL 활용
from django.db import migrations

class Migration(migrations.Migration):
    operations = [
        migrations.RunSQL(
            sql="""
            CREATE MATERIALIZED VIEW user_order_summary AS
            SELECT u.id, COUNT(o.id) as total_orders
            FROM user u LEFT JOIN orders o ON u.id = o.user_id
            GROUP BY u.id;
            """,
            reverse_sql="DROP MATERIALIZED VIEW user_order_summary;"
        )
    ]
```

### 파티셔닝 (Partitioning)

매우 커지는 시계열 로그 테이블 등은 파티셔닝 하는 것이 좋습니다. Django에서는 파티셔닝을 전용으로 지원하는 플러그인(`django-postgres-extra`, `architect` 등)을 쓰거나 직접 마이그레이션에 DDL문을 기입해야 합니다.

## 피해야 할 함정 (Common Pitfalls in Django)

- **.only() 와 .defer() 조심하기**: 특정 컬럼만 부르고자 쓸 때, 불러오지 않은 다른 필드에 코드상 접근하게 되면 무수한 단일 SELECT 문이 발생하며 심각한 N+1 장애를 초래합니다. 접근할 필드만 정확히 `values()`나 `values_list()`로 뽑거나, 로직상 참조가 완벽히 없는 시점에만 사용하세요.
- **불필요한 len(), list(), 인덱싱 ([0]) 수행 막기**: Queryset 평가(evaluation)는 쿼리셋을 반복문, 길이 체크, 캐스팅 등으로 소모할 때 즉시 트리거됩니다. 데이터베이스 함수(`exists()`, `count()`, `first()`)를 통해 쿼리 단에서 데이터를 판별하는 것이 훨씬 가볍습니다.
- **오버 인덱싱**: 무분별한 `db_index=True` 설정은 쓰기(Write) 작업인 INSERT/UPDATE 성능을 몹시 저하시킵니다. 조건절(WHERE / filter)과 정렬절(ORDER BY)에서 자주 사용되는 컬럼 위주로 조합하세요.
- **`.all()`의 무심결 사용 막기**: 무거운 테이블에서 `.all()`을 선언하는 즉시 모든 데이터 행 데이터가 메모리로 올라올 수 있습니다. 반드시 적절한 필터링이나 페이지네이션, Slicing(`[:100]`)을 걸어야 합니다.
