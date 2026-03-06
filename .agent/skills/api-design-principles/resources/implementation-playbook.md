# API 설계 원칙 구현 플레이북 (Django & DRF 가이드)

이 파일은 `api-design-principles` 스킬에서 참조하는 상세 패턴, 체크리스트, 그리고 Django/DRF에 맞춘 코드 예제를 포함합니다.

## 핵심 개념 (Core Concepts)

### 1. RESTful 설계 원칙

**리소스 지향 아키텍처 (Resource-Oriented Architecture)**

- 리소스는 동사가 아닌 명사(users, orders, products)를 사용합니다.
- 동작은 HTTP 메서드(GET, POST, PUT, PATCH, DELETE)를 통해 표현합니다.
- URL은 리소스의 계층 구조를 나타냅니다.
- 일관된 네이밍 컨벤션을 유지합니다.

**HTTP 메서드의 의미:**

- `GET`: 리소스 조회 (멱등성 가짐, 안전함)
- `POST`: 새로운 리소스 생성
- `PUT`: 리소스 전체 교체 (멱등성 가짐)
- `PATCH`: 리소스 부분 업데이트
- `DELETE`: 리소스 삭제 (멱등성 가짐)

### 2. API 버저닝 전략

**URL 기반 버저닝 (DRF 권장):**

```python
# urls.py
patterns = [
    path('api/v1/users/', UserViewSet.as_view({'get': 'list'})),
    path('api/v2/users/', UserV2ViewSet.as_view({'get': 'list'})),
]
```

**Header (Accept) 기반 버저닝:**
DRF의 `AcceptHeaderVersioning` 등을 활용하여 `Accept: application/vnd.company.v1+json` 형태로 버전을 주고받습니다.

---

## REST API 설계 패턴 (Django REST Framework)

### 패턴 1: 리소스 컬렉션 설계 (ViewSets & Routers 활용)

DRF의 `ViewSet`과 `Router`를 사용하면 표준 RESTful URL을 자동으로 생성할 수 있습니다.

```python
# Bad: 액션 지향적인 URL (지양)
# POST /api/createUser
# POST /api/getUserById

# Good: ViewSet을 활용한 리소스 중심 설계
from rest_framework import viewsets
from .models import User
from .serializers import UserSerializer

class UserViewSet(viewsets.ModelViewSet):
    """
    자동 생성되는 엔드포인트:
    GET    /api/users/           # 목록 조회
    POST   /api/users/           # 생성
    GET    /api/users/{id}/      # 단일 조회
    PUT    /api/users/{id}/      # 전체 수정
    PATCH  /api/users/{id}/      # 부분 수정
    DELETE /api/users/{id}/      # 삭제
    """
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

### 패턴 2: 페이지네이션 및 필터링 (Pagination and Filtering)

DRF 내장 Pagination 클래스와 `django-filter`를 함께 사용하는 것이 표준입니다.

```python
from rest_framework import pagination, generics
from django_filters import rest_framework as filters
from .models import User
from .serializers import UserSerializer

# 페이지네이션 커스텀
class StandardResultsSetPagination(pagination.PageNumberPagination):
    page_size = 20
    page_size_query_param = 'page_size'
    max_page_size = 100

# 필터셋 정의
class UserFilter(filters.FilterSet):
    status = filters.CharFilter(lookup_expr='iexact')
    created_after = filters.DateTimeFilter(field_name="created_at", lookup_expr='gte')
    search = filters.CharFilter(method='filter_search')
    
    class Meta:
        model = User
        fields = ['status']
        
    def filter_search(self, queryset, name, value):
        return queryset.filter(name__icontains=value) | queryset.filter(email__icontains=value)

# View 활용
class UserListView(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    pagination_class = StandardResultsSetPagination
    filter_backends = (filters.DjangoFilterBackend,)
    filterset_class = UserFilter
```

### 패턴 3: 일관된 에러 처리 및 상태 코드 (Error Handling)

DRF에서는 의도적인 에러를 발생시킬 때 `APIException`을 상속하여 사용하고, 커스텀 Exception Handler를 추가해 응답 형식을 일관되게 맞출 수 있습니다.

```python
# exceptions.py
from rest_framework.exceptions import APIException
from rest_framework import status

class ResourceNotFound(APIException):
    status_code = status.HTTP_404_NOT_FOUND
    default_detail = '요청한 리소스를 찾을 수 없습니다.'
    default_code = 'not_found'

# 커스텀 에러 핸들러 (settings.py 에 EXCEPTION_HANDLER로 등록 필요)
from rest_framework.views import exception_handler

def custom_exception_handler(exc, context):
    response = exception_handler(exc, context)

    if response is not None:
        custom_response_data = {
            'error': exc.__class__.__name__,
            'message': response.data.get('detail', '오류가 발생했습니다.'),
            'status_code': response.status_code,
            'details': response.data if 'detail' not in response.data else None
        }
        response.data = custom_response_data

    return response
```

### 패턴 4: HATEOAS (HyperlinkedModelSerializer 활용)

관련된 리소스의 URL을 응답에 포함시켜, 클라이언트가 다음 행동을 URL에 의존하게 만듭니다.

```python
from rest_framework import serializers
from .models import User

class UserSerializer(serializers.HyperlinkedModelSerializer):
    # url 필드가 기본적으로 추가되며, HyperlinkedIdentityField를 활용
    orders_url = serializers.HyperlinkedIdentityField(
        view_name='user-orders-list',
        lookup_field='pk'
    )

    class Meta:
        model = User
        fields = ['url', 'id', 'name', 'email', 'orders_url']
```

---

## GraphQL 설계 패턴 (Strawberry + Django)

최신 Django GraphQL 환경에서는 `strawberry-graphql-django`를 적극 추천합니다.

### 패턴 1: 스키마 및 N+1 문제 해결 (Type Definition & DataLoader)

Strawberry Django는 ORM의 연관 객체를 최적화해서 가져오기 위해 기본적으로 최적화 기능(optimizer)을 제공합니다. (직접 DataLoader를 짤 필요가 크게 줄어듭니다.)

```python
import strawberry
import strawberry_django
from strawberry_django import optimizer
from .models import User, Order

# 타입 정의 (Types)
@strawberry_django.type(User)
class UserType:
    id: strawberry.auto
    email: strawberry.auto
    name: strawberry.auto
    # 연관된 모델 지정 (Django ForeignKey/RelatedName)
    orders: list['OrderType']

@strawberry_django.type(Order)
class OrderType:
    id: strawberry.auto
    status: strawberry.auto
    total: strawberry.auto
    user: UserType

# Query 정의
@strawberry.type
class Query:
    # 단일 조회 (id 활용)
    user: UserType = strawberry_django.field()
    
    # 목록 조회 (Pagination + Optimizer 활용하여 N+1 방지)
    users: list[UserType] = strawberry_django.field(
        pagination=True,
    )

    orders: list[OrderType] = strawberry_django.field()

# Mutation 정의
@strawberry.type
class Mutation:
    @strawberry_django.mutation
    def create_user(self, email: str, name: str) -> UserType:
        return User.objects.create(email=email, name=name)

# 스키마 생성 및 Django Optimizer 확장 등록
schema = strawberry.Schema(
    query=Query,
    mutation=Mutation,
    extensions=[
        optimizer.DjangoOptimizerExtension, # ORM N+1 문제 자동 해결
    ]
)
```

## 모범 사례 (Best Practices)

### REST API (DRF)

1. **일관성 있는 라우팅**: `DefaultRouter`를 활용해 일관된 `/api/users/` 명명규칙을 따르세요.
2. **Stateless 유지**: 서버의 세션 상태보다 Token(JWT, TokenAuth) 기반 인증을 주로 사용하세요.
3. **상태 코드의 올바른 사용**: DRF 제공 `status` 모듈 (`status.HTTP_200_OK`, `status.HTTP_400_BAD_REQUEST` 등) 상수 활용.
4. **시리얼라이저 분리**: 직렬화(Read)를 위한 시리얼라이저와 유효성 검증(Write)용을 분리하면 복잡도를 낮출 수 있습니다.
5. **Throttling (속도 제한)**: DRF 내장 Throttling 클래스를 활용하여 API 오남용을 막습니다.

### GraphQL API

1. **Schema First vs Code First**: Python/Django 생태계에서는 Strawberry를 이용한 Code-First 접근이 ORM과의 결합 면에서 더 유지보수가 편합니다.
2. **N+1 문제 주의**: `prefetch_related/select_related` 또는 Optimizer Extensions 모듈을 필수로 사용해야 합니다.
3. **복잡도 제어**: GraphQL은 클라이언트가 너무 깊은 계층의 데이터를 요청하면 서버에 부담이 되므로, Depth나 Complexity 제한을 추가하세요.

## 피해야 할 함정 (Common Pitfalls)

- **비즈니스 로직을 Model에 다 넣기 (Fat Model)** 혹은 **Serializer에 다 넣기**: 역할 분리가 모호해지면 유지보수가 어렵습니다. 가급적 서비스 레이어(Service Layer)를 두거나 메서드를 적절히 분배하세요.
- **REST 명사 규칙 위반**: `/api/upload_image` 같은 URL은 지양하고 `/api/images/` 에 POST를 날리는 방식을 채택합니다.
- **N+1 문제를 무시하고 SerializerMethodField 남발**: DRF에서 `SerializerMethodField` 안에서 또 쿼리를 짜면 N+1이 터지기 가장 쉽습니다. 뷰(View)의 `get_queryset`에서 미리 처리(prefetch_related)해야 합니다.
