# Domain Documentation

This repository uses a **single-context** domain documentation layout.

## Layout

- **Glossary**: `GLOSSARY.md` at the root contains the project's shared vocabulary and domain concepts.
- **ADRs**: Architecture Decision Records are stored in `docs/adr/`.

## Consumer Rules

When reading or updating domain documentation:

1. **Consult the Glossary**: Always check `GLOSSARY.md` first to ensure consistent terminology is used in code and documentation.
2. **Review ADRs**: Before proposing significant architectural changes, review existing records in `docs/adr/` to understand the rationale behind current decisions.
3. **Update Together**: When introducing a new domain concept, update `GLOSSARY.md` and create a corresponding ADR if the change impacts architecture.
