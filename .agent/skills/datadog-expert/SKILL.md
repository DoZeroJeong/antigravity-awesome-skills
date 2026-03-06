---
name: datadog-expert
description: Python (Django/FastAPI) 프로젝트에서 Datadog을 효과적으로 활용하기 위한 가이드입니다. 설정, 로깅, 트레이싱, 메트릭 수집 방법을 다룹니다.
---

# Datadog Expert

## 개요 (Overview)

이 스킬은 Datadog을 사용하여 애플리케이션의 가시성을 확보하고 성능 문제를 진단하는 방법을 안내합니다.
`ddtrace` 라이브러리를 활용한 APM 설정, 로그와 트레이스 연결(Log Injection), 커스텀 메트릭 수집 등을 포함합니다.

## 사용 시점 (When to use)

- 새로운 서비스나 프로젝트에 Datadog 모니터링을 연동할 때
- 로그와 트레이스가 연결되지 않아 디버깅이 어려울 때
- 비즈니스 로직의 성능을 측정하거나 특정 이벤트 발생 빈도를 추적하고 싶을 때 (`statsd` 활용)
- 복잡한 분산 시스템에서 요청의 흐름을 추적해야 할 때

## 지침 (Instructions)

### 1. 설치 (Installation)

필요한 패키지를 설치합니다.

```bash
pip install ddtrace datadog
```

### 2. 설정 (Configuration)

환경 변수를 통해 Datadog 에이전트와 연결하고 서비스 정보를 설정합니다.
가능한 코드 변경 없이 환경 변수로 제어하는 것을 권장합니다.

**필수 환경 변수:**

- `DD_API_KEY`: Datadog API Key (에이전트 실행 시 필요)
- `DD_ENV`: 환경 이름 (예: `production`, `staging`, `local`)
- `DD_SERVICE`: 서비스 이름 (예: `my-backend`, `payment-service`)
- `DD_VERSION`: 애플리케이션 버전 (빌드/배포 버전)
- `DD_AGENT_HOST`: Datadog 에이전트 호스트 (기본값 `localhost`. Docker 환경에서는 보통 `datadog-agent` 컨테이너 이름 사용)
- `DD_LOGS_INJECTION`: `true`로 설정하여 로그에 Trace ID 자동 주입

#### Django 통합 (Django Integration)

Django 프로젝트에서는 `INSTALLED_APPS`에 `ddtrace.contrib.django`를 추가하여 통합합니다.

```python
# settings.py
INSTALLED_APPS += [
    'ddtrace.contrib.django',
]
```

### 3. 로깅 설정 (Logging Setup)

Python의 표준 `logging` 모듈을 사용하여 Datadog과 연동합니다.
`python-json-logger`를 사용하는 방법과 표준 포맷을 사용하는 방법이 있습니다.

#### 옵션 A: JSON 로깅 (권장)

JSON 포맷으로 로그를 남기면 Datadog에서 자동으로 파싱하고 트레이스와 연결할 수 있습니다.

```python
# logging configuration example
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'json': {
            '()': 'pythonjsonlogger.jsonlogger.JsonFormatter',
            'format': '%(asctime)s %(levelname)s %(name)s %(message)s'
        },
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'json'
        },
    },
    'root': {
        'handlers': ['console'],
        'level': 'INFO',
    },
}
```

#### 옵션 B: 표준 포맷 + Trace ID 주입 (Standard Format)

JSON 로거를 사용하지 않는 경우, 아래와 같이 포맷터에 `dd.*` 필드를 명시적으로 추가하여 트레이스 정보를 로그에 남길 수 있습니다.

```python
LOGGING = {
    # ...
    "formatters": {
        "datadog": {
            "format": (
                "%(asctime)s %(levelname)s [%(name)s] [%(filename)s:%(lineno)d] "
                "[dd.service=%(dd.service)s dd.env=%(dd.env)s dd.version=%(dd.version)s "
                "dd.trace_id=%(dd.trace_id)s dd.span_id=%(dd.span_id)s] "
                "- %(message)s"
            ),
            "style": "%",
        },
    },
    "handlers": {
        "datadog": {
            "class": "logging.StreamHandler",
            "level": "INFO",
            "formatter": "datadog",
        },
    },
    "root": {
        "handlers": ["datadog"],
        "level": "INFO"
    }
}
```

**주의:** `ddtrace-run` 명령어로 실행하거나 `import ddtrace.auto`를 사용하면 `DD_LOGS_INJECTION=true` 설정 시 자동으로 로그 레코드에 `dd.trace_id`, `dd.span_id`가 주입됩니다.

### 4. 트레이싱 (Tracing)

#### 자동 계측 (Auto-instrumentation)

가장 쉬운 방법은 `ddtrace-run`을 사용하는 것입니다.

```bash
ddtrace-run python app.py
# 또는 uvicorn 사용 시
ddtrace-run uvicorn main:app
# Celery Worker 실행 시
ddtrace-run celery -A my_project worker -l info
```

#### Celery 통합 (Celery Integration)

Celery를 사용할 때 작업(Task)의 실행 흐름을 추적하려면 위체럼 `ddtrace-run`을 사용하여 워커를 실행하거나, 코드 내에서 패치해야 합니다.

```python
# celery.py 또는 __init__.py
from ddtrace import patch_all
patch_all(celery=True)

# 또는 특정 모듈만 패치
from ddtrace import patch
patch(celery=True)
```

#### 수동 계측 (Manual Instrumentation)

특정 함수나 로직을 별도 Span으로 분리하고 싶을 때 데코레이터나 컨텍스트 매니저를 사용합니다.

```python
from ddtrace import tracer

@tracer.wrap()
def my_function():
    # 함수 전체가 하나의 Span이 됨
    pass

def another_function():
    with tracer.trace("operation.name", service="my-service", resource="resource_name") as span:
        # Span에 태그 추가
        span.set_tag("custom.tag", "value")
        # 비즈니스 로직
        pass
```

### 5. 커스텀 메트릭 (Custom Metrics)

`datadog` 라이브러리의 `statsd` 클라이언트를 사용하여 커스텀 메트릭을 전송합니다.

```python
from datadog import statsd

# Counter: 횟수 측정
statsd.increment('my.custom.metric.count', tags=['environment:production'])

# Gauge: 특정 시점의 값 (예: 큐 길이, 메모리 사용량)
statsd.gauge('my.custom.queue.size', 15, tags=['queue:orders'])

# Histogram/Timer: 수행 시간 분포 측정
with statsd.timed('my.custom.operation.time'):
    # 측정할 작업 수행
    process_data()
```

## 모범 사례 (Best Practices)

1. **Unified Service Tagging 준수:** `env`, `service`, `version` 태그를 모든 원격 측정 데이터(로그, 트레이스, 메트릭)에 일관되게 적용하여 데이터 간 상호 연관성을 보장하세요.
2. **Cardinality 관리:** 메트릭 태그에 사용자 ID나 요청 ID와 같이 고유한 값이 무한히 늘어나는 데이터(High Cardinality)를 넣지 마세요. 과도한 비용이 발생할 수 있습니다. 대신 로그나 트레이스 태그를 활용하세요.
3. **에러 추적:** 예외 발생 시 `span.set_exc_info(...)`를 사용하거나 로깅 프레임워크와 연동하여 스택 트레이스를 Datadog Error Tracking에 올바르게 전달하세요.
4. **민감 정보 보호:** 로그나 트레이스에 개인정보(PII)나 비밀번호가 평문으로 남지 않도록 마스킹 처리를 구현하세요. `DD_TRACE_OBFUSCATION_QUERY_STRING_REGEXP` 등의 설정을 활용할 수 있습니다.
5. **분산 추적(Distributed Tracing) 기반 디버깅:** 에러 발생 시 Datadog 로그에 기록된 `trace_id`를 확보하고, 이를 기반으로 `systematic-debugging` 스킬과 연계하여 여러 마이크로서비스 또는 비동기 태스크(Celery) 간의 숨은 병목 요소와 실패 원인을 입체적으로 추적하세요.
