# GitLab CI Docker Jobs Module

Этот модуль содержит переиспользуемые шаблоны для работы с Docker в GitLab CI/CD.

## Использование в других проектах

### Вариант 1: Подключение из того же репозитория (локальный)

```yaml
include:
  - local: '.gitlab/docker-jobs.yml'

build:my-service:
  extends: .docker_build_template
  variables:
    SERVICE_NAME: my-service
    DOCKERFILE_PATH: Dockerfile
    BUILD_CONTEXT: .
```

### Вариант 2: Подключение из внешнего репозитория

```yaml
include:
  - project: 'your-group/devops-itmo'
    ref: main
    file: '.gitlab/docker-jobs.yml'

build:my-service:
  extends: .docker_build_template
  variables:
    SERVICE_NAME: my-service
    DOCKERFILE_PATH: Dockerfile
    BUILD_CONTEXT: .
```

### Вариант 3: Подключение по HTTP (если репозиторий публичный)

```yaml
include:
  - remote: 'https://gitlab.com/your-group/devops-itmo/-/raw/main/.gitlab/docker-jobs.yml'

build:my-service:
  extends: .docker_build_template
  variables:
    SERVICE_NAME: my-service
    DOCKERFILE_PATH: Dockerfile
    BUILD_CONTEXT: .
```

## Доступные шаблоны

### `.docker_build_template`

Базовый шаблон для сборки Docker образа.

**Переменные:**
- `SERVICE_NAME` (обязательная) - имя сервиса для тегирования образа
- `DOCKERFILE_PATH` (обязательная) - путь к Dockerfile
- `BUILD_CONTEXT` (обязательная) - контекст сборки

**Пример:**
```yaml
build:backend:
  extends: .docker_build_template
  variables:
    SERVICE_NAME: backend
    DOCKERFILE_PATH: backend/Dockerfile
    BUILD_CONTEXT: backend
```

### `.docker_build_with_cache_template`

Расширенный шаблон для мультистейдж сборки с кешированием промежуточных этапов.

**Переменные:**
- `SERVICE_NAME` (обязательная) - имя сервиса
- `DOCKERFILE_PATH` (обязательная) - путь к Dockerfile
- `BUILD_CONTEXT` (обязательная) - контекст сборки
- `BUILD_STAGES` (опциональная) - названия промежуточных stage для кеширования (через пробел)

**Пример:**
```yaml
build:frontend:
  extends: .docker_build_with_cache_template
  variables:
    SERVICE_NAME: frontend
    DOCKERFILE_PATH: frontend/Dockerfile
    BUILD_CONTEXT: frontend
    BUILD_STAGES: "build dependencies"
```

### `.docker_scan_template`

Шаблон для сканирования образа на уязвимости с помощью Trivy.

**Переменные:**
- `SERVICE_NAME` (обязательная) - имя сервиса

**Пример:**
```yaml
scan:backend:
  extends: .docker_scan_template
  variables:
    SERVICE_NAME: backend
  needs:
    - build:backend
```

### `.docker_cleanup_template`

Шаблон для очистки Docker кеша (ручной запуск).

**Пример:**
```yaml
cleanup:
  extends: .docker_cleanup_template
  when: manual
```

## Автоматические переменные GitLab

Модуль использует встроенные переменные GitLab:

- `$CI_REGISTRY` - адрес Container Registry
- `$CI_REGISTRY_IMAGE` - полный путь к образу в registry
- `$CI_REGISTRY_USER` - пользователь для входа в registry
- `$CI_REGISTRY_PASSWORD` - пароль для входа в registry
- `$CI_COMMIT_SHORT_SHA` - короткий SHA коммита (для тегов)
- `$CI_COMMIT_REF_SLUG` - имя ветки в slug формате
- `$CI_PROJECT_PATH` - путь проекта в GitLab

## Требования

- GitLab Runner с Docker executor
- Docker-in-Docker (dind) сервис
- Включенный Container Registry в GitLab проекте

## Примеры использования

См. файл `.gitlab-ci.yml` в корне проекта для полного примера.