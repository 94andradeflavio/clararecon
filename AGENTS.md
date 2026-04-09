# AGENTS.md - Agentic Coding Guidelines for reNgine

reNgine is an automated web application reconnaissance/vulnerability scanning suite built with Django, Celery, and PostgreSQL.

---

## Build, Test, and Run Commands

### Docker Compose (Primary)

```bash
# Build and start all services
make up

# Apply database migrations
make migrate

# Run all tests
make test

# Run specific test file
docker-compose exec celery python3 -m unittest tests/test_scan.py
docker-compose exec celery python3 -m unittest tests/test_nmap.py

# Run a single test method
docker-compose exec celery python3 -m unittest tests.test_scan.TestOnlineScan.test_http_crawl

# Django management commands
docker-compose exec web python3 manage.py <command>
docker-compose exec web python3 manage.py createsuperuser
```

---

## Code Style Guidelines

### Imports

Standard library → third-party → Django → local apps. Use explicit imports over wildcard.

```python
import os
import json
import subprocess
from celery import chain, group
from django.db.models import Count
from reNgine.common_func import *
from scanEngine.models import EngineType
```

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Classes | PascalCase | `class ScanHistory:` |
| Functions | snake_case | `def initiate_scan()` |
| Variables | snake_case | `scan_history_id` |
| Constants | UPPER_SNAKE_CASE | `MAX_CONCURRENCY = 80` |
| Celery tasks | snake_case | `@app.task(name='initiate_scan')` |

### Type Annotations

Not currently enforced. Add type hints when modifying functions, especially for public APIs.

```python
def initiate_scan(scan_history_id: int, domain_id: int, engine_id: Optional[int] = None) -> dict:
    ...
```

### Error Handling

- Use try/except with specific exception types
- Log errors with `get_task_logger(__name__)`
- Return meaningful error messages

```python
logger = get_task_logger(__name__)
try:
    scan = ScanHistory.objects.get(id=scan_history_id)
except ScanHistory.DoesNotExist:
    logger.error(f'Scan {scan_history_id} not found')
    return {'error': 'Scan not found'}
```

### Django Patterns

- Use `get_object_or_404` for single object retrieval
- Use `.filter()`, `.values()`, `.annotate()` for efficient queries
- Follow: `class Meta: ordering = ['-created']`
- Use migrations: `python manage.py makemigrations`

### Celery Tasks

- Use `@app.task` with `bind=False` for most tasks
- Use `bind=True` when needing `self` for retries/state
- Queue critical tasks to `initiate_scan_queue`
- Use `logger.info/warning/error` from `get_task_logger(__name__)`

```python
@app.task(name='initiate_scan', bind=False, queue='initiate_scan_queue')
def initiate_scan(scan_history_id, domain_id, ...):
    logger.info('Initiating scan on celery')
```

### Formatting

- 4 spaces indentation (no tabs)
- Max 120 characters per line
- Use f-strings for string interpolation
- Use `yaml.dump()` for YAML, `json.dumps(indent=4)` for debugging

---

## File Organization

| Directory | Purpose |
|-----------|---------|
| `web/reNgine/` | Core tasks, celery, settings, utilities |
| `web/reNgine/tasks.py` | All Celery scan tasks (~4700 lines) |
| `web/reNgine/common_func.py` | Shared utilities (~1700 lines) |
| `web/reNgine/definitions.py` | Constants, defaults, tool paths |
| `web/api/` | REST API endpoints |
| `web/scanEngine/` | Engine configs, wordlists, notifications |
| `web/startScan/` | Scan history, results, vulnerabilities |
| `web/targetApp/` | Domain targets, WHOIS, organizations |

---

## Common Patterns

```python
# Get or create
domain, _ = Domain.objects.get_or_create(name=domain_name)

# Bulk create
Subdomain.objects.bulk_create(subdomains_list)

# Run external tools
from reNgine.tasks import run_command
result = run_command(['subfinder', '-d', domain])

# Return task results
return {'subdomains': subdomain_list, 'count': len(subdomain_list)}
```

---

## Testing

- Tests in `web/tests/` directory
- Use `unittest.TestCase`
- Set env vars before imports:

```python
os.environ['RENGINE_SECRET_KEY'] = 'secret'
os.environ['CELERY_ALWAYS_EAGER'] = 'True'
```

---

## Key Files

- `web/reNgine/tasks.py` - Main scan orchestration (Celery tasks)
- `web/reNgine/common_func.py` - Shared utility functions
- `web/reNgine/definitions.py` - Constants and tool paths
- `web/reNgine/settings.py` - Django settings
- `web/api/views.py` - REST API endpoints
- `startScan/models.py` - Scan result models

---

## Gotchas

1. Celery tasks run inside Docker - use `docker-compose exec` for debugging
2. Database operations in tasks must save to DB explicitly (not just return)
3. YAML configs stored in `EngineType.yaml_configuration` field
4. Scan results stored relationally - models in `startScan/models.py`
5. Role permissions defined in `reNgine/roles.py` (Sys Admin, Pentester, Auditor)
