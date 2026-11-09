# Contributing

Thanks for your interest in **OpenFlux**. Documentation fixes, reports with reproducible issues and small focused PRs are welcome.

## Getting started

1. Read the [README](README.md).
2. Before starting, check open Issues and Pull Requests to avoid duplicated work.
3. Use the **Bug report** form for bugs, **Feature request** for ideas, or a blank issue for anything else.
4. For code changes, create a branch from the current `main` and a PR with a small, reviewable diff.

## Project rules

- State the build (commit or release), OS/architecture and the transport used.
- Do not publish real server credentials or working transport configurations.
- Respect the GPL-3.0-or-later license and third-party attribution in [NOTICE](NOTICE).
- Do not publish real passwords, tokens, subscriptions, VPN keys, WAN addresses, private IP inventories or configuration dumps — sanitize them.
- Do not claim a physical device, VPS or network was tested if it was not.
- Respect the existing license and third-party attribution.

## PR checklist

- Explain **what** changed, **why**, and how it was verified.
- Run the appropriate checks (CI or local); if you could not verify something, say so.
- Do not touch release tags, protected branches or operational infrastructure in a docs-only PR.

## Security

**Do not disclose unpatched vulnerabilities publicly via Issues.** Use the private channel from [SECURITY.md](SECURITY.md). Sanitize configurations and logs before publishing.

## Стандарт небольших и проверяемых изменений

Для всех проектов `saymer-alt` действует [общий инженерный стандарт ревью](https://github.com/saymer-alt/saymer-infrastructure/blob/main/docs/engineering-change-review-standard.md). Это правило для **людей и ИИ**, а не файл инструкций агенту.

**Одна проблема → минимальное смысловое исправление → необходимые тесты → один обозримый PR → подтверждение результата.** Не смешивайте hotfix с рефакторингом, переименованиями и посторонними функциями. Если найден другой независимый дефект — отдельное Issue/PR. Размер изменений определяется не количеством строк, а возможностью доказать и проверить их; не отрывайте обязательные тесты и миграции от исправления. Большой неделимый diff требует отдельного обоснования и review. В описании PR указывайте границы, проверки и `NOT_TESTED`; CI PASS не означает field/runtime PASS. Проектные правила безопасности, веток и выпуска сохраняются.
