# CLAUDE.md: Mini Jira

## Decisiones de arquitectura

- Base de datos: PostgreSQL gestionado en Supabase, usado solo como base de datos; la API en Node.js hace el login propio, los permisos, la lógica de negocio y los emails (no se usan Supabase Auth, su API autogenerada ni sus funciones). Reemplaza a SQL Server (R-P9). Ver `docs/adr/001-database-selection.md`.
