# Postgres JSON Aggregation Demo

Mini proyecto standalone (no depende de ningún otro repo) para probar cómo
Postgres puede construir un documento JSON directamente desde SQL usando
`json_build_object` + `json_agg`, en lugar de armarlo en código de
aplicación.

Usa tablas temporales (`CREATE TEMP TABLE`), así que no deja rastro en
ninguna base de datos real: todo vive y muere dentro de la sesión de `psql`.

## Requisitos

- PostgreSQL 18 (instalado localmente, servicio `postgresql-x64-18`, puerto 5432)
- Usuario `postgres`, contraseña local: `<REDACTED>`
  (solo para esta base de dev local — no reusar en ningún entorno real)

## Cómo correrlo

### Opción 1 — Desde VS Code (recomendado)

1. Abre esta carpeta como workspace: `File > Open Folder... >` selecciona
   `postgres-json-demo`.
2. `Terminal > Run Task... > Run Query (query.sql)`
   (o el atajo `Ctrl+Shift+B`, ya que está definida como build task por
   defecto).
3. El resultado aparece en un panel de terminal nuevo.

Esto no depende de ninguna extensión de base de datos — la tarea invoca
`psql.exe` directamente, así que corre igual sin importar qué extensión de
SQL tengas activa (evita el conflicto con la extensión de SQL Server, que
no entiende sintaxis de Postgres).

### Opción 2 — Terminal

`psql` ya quedó agregado al PATH del usuario. **Cierra y vuelve a abrir la
terminal** (o VS Code) para que tome el cambio, luego:

```powershell
$env:PGPASSWORD = "<REDACTED>"
psql -U postgres -h 127.0.0.1 -f query.sql
```

O en Git Bash:

```bash
PGPASSWORD='<REDACTED>' psql -U postgres -h 127.0.0.1 -f query.sql
```

Si no reiniciaste la terminal todavía, usa la ruta completa en su lugar:

```bash
PGPASSWORD='<REDACTED>' "/c/Program Files/PostgreSQL/18/bin/psql.exe" -U postgres -h 127.0.0.1 -f query.sql
```

## Qué hace `query.sql`

1. Crea 3 tablas temporales: `customers`, `orders`, `order_items`.
2. Inserta un cliente, una orden y 3 líneas de producto.
3. Corre un `SELECT` que arma el JSON completo del pedido en una sola
   query, combinando `json_build_object` (para el objeto) con `json_agg`
   (para el arreglo de items).
