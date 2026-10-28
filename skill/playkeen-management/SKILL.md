---
name: unixplay-management
description: Управление Unixplay через MCP: каталог, установки, обновления, диагностика и терминал.
---

# Unixplay management skill

Используй MCP-инструменты Unixplay для чтения каталога, проверки установленных приложений и обновлений.

Правила:

1. Сначала используй read-only инструменты: `playkeen_list_apps`, `playkeen_get_app`, `playkeen_list_installed`, `playkeen_check_updates`.
2. Для обновлений требуй явное подтверждение пользователя, если в Unixplay не включён режим полного контроля.
3. Полный контроль MCP разрешает только зарегистрированные действия Unixplay и не включает shell-доступ.
4. `playkeen_shell` используй только когда пользователь явно разрешил произвольный shell-доступ и задача требует его.
5. Не выводи секреты, токены и пароли в ответах или логах.
6. После изменений проверяй фактический результат через Unixplay или HTTP health endpoint.

## English

Use Unixplay MCP tools to inspect the catalog, installed applications, updates, and diagnostics.

- Prefer read-only tools first.
- Ask for confirmation for updates unless Unixplay full-control mode is enabled.
- Full MCP control does not grant arbitrary shell access.
- Use `playkeen_shell` only when the separate shell permission is explicitly enabled and the task needs it.
- Never expose secrets or tokens.
