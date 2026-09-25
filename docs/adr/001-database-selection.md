# ADR-001: Base de datos para el MVP

| Campo | Valor |
|---|---|
| Estado | Aceptada (revisada el 23-09-2026: se cambia SQL Server por Supabase) |
| Fecha | 23-09-2026 |
| Decisora | Isis Silva (Arquitectura) |
| Fuente de requerimientos | `docs/specs.md` v1.1 |

**Cómo se citan las fuentes:** igual que en specs.md. `RF-xx` es un requerimiento funcional, `R-Pn` una respuesta de la Fase 1, `D-nn` una decisión posterior, `T-Lnn` una línea de la transcripción, y `OBJ`, `SUP` y `RSK` son objetivos, supuestos y riesgos del PRD.

> **Historial:** la primera versión de este ADR eligió Microsoft SQL Server porque era el motor confirmado en R-P9. La autora reabrió esa decisión (**D-01**) y eligió usar **Supabase solo como base de datos**. specs.md (sección 3) ya refleja el cambio.

> **Nota de verificación:** las características de Supabase y Firebase citadas aquí se basan en su documentación pública conocida hasta junio de 2026. No se pudieron comprobar en línea al redactar este documento.

---

## Contexto y problema

El MVP necesita una base de datos. La decisión D-01 reabre el motor que había fijado R-P9. Los requisitos de specs.md que la condicionan son:

| Requisito | Fuente |
|---|---|
| Modelo relacional para usuarios, proyectos, tickets, etiquetas, estados y comentarios. | T-L34, RF-05, RF-11, RF-30, RF-40 |
| Filtros por fecha, prioridad, responsable, proyecto y etiquetas. | RF-37 |
| Dashboard con tickets cerrados agrupados **por mes y por proyecto**. | RF-48, RF-49 |
| Borrado lógico con trazabilidad. | RF-20, RF-23 |
| **Login propio con usuario y contraseña**, y cada cuenta tiene email. | RF-01, RF-02 |
| Permisos por rol y por dueño, aplicados en la API. | RF-17, RF-18, RF-21, RF-22, architecture.md |
| Despliegue en un **servicio gestionado**, con posibles restricciones de TI. | R-P10, RSK-03 |
| El plazo es de **3 semanas** hasta producción y no es negociable. | OBJ-05, R-P11, RSK-01 |
| Concurrencia sin decidir. | RF-39 [PENDIENTE] |
| Backend en Node.js. | SUP-03 |

## Factores de decisión

1. **FD-1 Modelo de datos:** que resuelva de forma natural las relaciones, los filtros combinados y las agregaciones (T-L34, RF-37, RF-48).
2. **FD-2 Operación:** que sea un servicio gestionado y reduzca la administración de la base de datos dentro del plazo (R-P10, OBJ-05).
3. **FD-3 Compatibilidad con el diseño:** que no obligue a cambiar el login propio ni a sacar los permisos de la API (RF-01, SUP-03, architecture.md).

---

## Opciones consideradas

1. **Supabase, usado solo como base de datos** (PostgreSQL gestionado; la API en Node.js se conecta directo).
2. **Firebase** (Cloud Firestore, una base documental NoSQL).
3. **Microsoft SQL Server gestionado** (la opción original de R-P9).

### Opción 1: Supabase solo como base de datos

| | Argumento | Requerimiento |
|---|---|---|
| ✅ Pro | Es relacional (PostgreSQL): claves foráneas y joins para todas las entidades del dominio. | T-L34, RF-05, RF-11, RF-40 (FD-1) |
| ✅ Pro | Las agregaciones por mes y proyecto se resuelven con SQL (`GROUP BY`). | RF-48, RF-49 (FD-1) |
| ✅ Pro | Admite filtros combinados sobre varias columnas con índices estándar. | RF-37 (FD-1) |
| ✅ Pro | Es un servicio gestionado: la base de datos queda aprovisionada sin administrar servidores. | R-P10, OBJ-05 (FD-2) |
| ✅ Pro | Como solo se usa la base de datos, el login propio (RF-01) y los permisos siguen en la API en Node.js, y los diagramas de arquitectura casi no cambian. | RF-01, RF-17, RF-18, SUP-03 (FD-3) |
| ✅ Pro | El borrado lógico y la trazabilidad se modelan con columnas y restricciones estándar. | RF-20, RF-23 |
| ✅ Pro | Si se decide avisar de conflictos de edición, basta una columna de versión en el ticket. | RF-39 [PENDIENTE] |
| ❌ Contra | No aprovecha la autenticación ni la API autogenerada de Supabase: el ahorro de tiempo se limita a la operación de la base de datos. | OBJ-05, RSK-01 |
| ❌ Contra | Agrega un segundo proveedor: la base de datos queda en Supabase y la aplicación web y la API en otro servicio gestionado, todavía sin definir. | R-P10, RSK-03 |
| ⚠️ Neutro | Las restricciones de TI deben permitir la conexión desde la API hasta Supabase. | RSK-03 |

### Opción 2: Firebase (Cloud Firestore)

| | Argumento | Requerimiento |
|---|---|---|
| ✅ Pro | Es un servicio gestionado. | R-P10 (FD-2) |
| ✅ Pro | Admite transacciones, útiles si se decide avisar de conflictos de edición. | RF-39 [PENDIENTE] |
| ❌ Contra | Es documental y no tiene joins: las relaciones obligan a duplicar datos o a hacer varias lecturas. | T-L34, RF-05, RF-11, RF-40 (FD-1) |
| ❌ Contra | Sus agregaciones no agrupan por campos: "cerrados por mes y por proyecto" requiere contadores precalculados. | RF-48, RF-49 (FD-1) |
| ❌ Contra | Los filtros sobre cinco criterios requieren índices compuestos por cada combinación y tienen restricciones de consulta. | RF-37 (FD-1) |
| ❌ Contra | Está pensado para usarse con sus reglas de seguridad y su autenticación desde el cliente, lo que choca con los permisos en la API y el login propio. | RF-01, SUP-03 (FD-3) |

### Opción 3: Microsoft SQL Server gestionado

| | Argumento | Requerimiento |
|---|---|---|
| ✅ Pro | Es relacional: resuelve relaciones, filtros y agregaciones con SQL. | T-L34, RF-37, RF-48 (FD-1) |
| ✅ Pro | Mantiene el login propio y los permisos en la API. | RF-01, SUP-03 (FD-3) |
| ✅ Pro | Era el motor confirmado originalmente. | R-P9 |
| ❌ Contra | Se descarta por decisión de la autora, que lo reemplaza por Supabase. | D-01 |
| ⚠️ Neutro | Para el modelo del MVP no ofrece una ventaja funcional sobre PostgreSQL: ambos cubren FD-1 y FD-3. | RF-37, RF-48, RF-01 |

---

## Decisión

**Opción elegida: 1, Supabase usado solo como base de datos (PostgreSQL gestionado).** La API en Node.js se conecta directo a PostgreSQL y conserva el login propio, los permisos, la lógica de negocio y el envío de emails.

**Justificación**

1. **D-01** fija Supabase como proveedor y descarta SQL Server.
2. **FD-1:** PostgreSQL cubre el modelo relacional, los filtros combinados (RF-37) y las métricas agrupadas (RF-48) igual que SQL Server. Firebase no los cubre.
3. **FD-3:** usarlo solo como base de datos mantiene RF-01 tal como está especificado y deja intactos los diagramas de contenedores y de secuencia.
4. **FD-2:** se gana operación gestionada de la base de datos sin depender de la autenticación ni de la API de Supabase.

**Qué no se usa de Supabase en el MVP:** Supabase Auth, la API autogenerada, las políticas por fila (Row Level Security) como mecanismo de permisos, Edge Functions ni Realtime. Adoptar cualquiera de ellas requiere un ADR nuevo.

---

## Consecuencias

**Positivas**

- El modelo relacional, los filtros (RF-37) y el dashboard (RF-48) se resuelven con SQL, sin duplicar datos.
- La base de datos es gestionada desde el primer día (R-P10).
- `architecture/architecture.md` solo cambia la tecnología del contenedor de datos.

**Negativas y riesgos**

- El trabajo de autenticación y API sigue siendo propio, así que se mantiene RSK-01 (plazo de 3 semanas).
- Hay dos proveedores: Supabase para los datos y otro, sin definir, para la aplicación web y la API (R-P10).
- La conectividad con Supabase y con el correo corporativo depende de las restricciones de TI (RSK-03).

**Acciones derivadas**

| Acción | Referencia |
|---|---|
| Crear el proyecto de Supabase y definir la región según las restricciones de TI. | R-P10, RSK-03 |
| Elegir el ORM o la capa de acceso a datos para Node.js compatible con PostgreSQL. | Sección 3 [PENDIENTE] |
| Elegir el servicio gestionado donde correrán la aplicación web y la API. | R-P10 |
| Confirmar la conectividad desde la API hacia Supabase y hacia el correo corporativo. | RF-46, RSK-03 |
| Definir la política de contraseñas del login propio. | RNF-04 [PENDIENTE] |
