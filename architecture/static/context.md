# Диаграмма контекста (C4 - L1)

- **Бизнес-пользователь** - единственный актор: загружает датасет, ведёт диалог,
  получает конфигурацию и результаты обучения.
- **LLM Provider** - внешняя система, «мозг» агента.
- **Граница доверия:** всё, что уходит в промпт, покидает контролируемую системой инфраструктуру.

```mermaid
flowchart LR

    classDef external stroke:#888888,stroke-dasharray: 4 4

    user(["Бизнес\nпользователь"])
    automl["AutoML System"]
    provider["LLM Provider"]

    user --> automl
    automl --> provider

    class provider external
```
