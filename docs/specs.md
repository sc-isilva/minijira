# PRD: Mini Jira (V1)

| Campo | Valor |
|---|---|
| Versión | 1.1 (borrador): base de datos cambiada a Supabase (D-01) |
| Fecha | 23-09-2026 |
| Autora | Isis Silva |
| Fuentes | Transcripción del kick-off "Mini Jira" (24 de octubre) y respuestas de la Fase 1 |

**Cómo se citan las fuentes**

- `(T-Lnn, Persona)`: línea *nn* de la transcripción y quién habló.
- `(T-Acuerdos)`: sección "Acuerdos de la reunión" (L67-72).
- `(R-Pn)`: respuesta a la pregunta *n* del diagnóstico de la Fase 1.
- `(D-nn)`: decisión posterior de la autora. D-01 (23-09-2026): usar Supabase solo como base de datos, en lugar de SQL Server. Reemplaza a R-P9.
- `[PENDIENTE]`: falta información. No se completa con suposiciones.

---

## 1. Objetivos

| ID | Objetivo | Fuente |
|---|---|---|
| OBJ-01 | Tener una herramienta interna de gestión de tareas para una empresa de unas 10 personas. | (T-L6, Roberto) |
| OBJ-02 | Que cualquiera pueda usarla sin manual, con una estética moderna y limpia. | (T-L9, Laura), (T-L55, Laura) |
| OBJ-03 | Organizar el trabajo por proyecto, para no mezclar varias iniciativas en una sola lista. | (T-L19, Laura) |
| OBJ-04 | Dar visibilidad de la productividad (tickets cerrados por mes y por proyecto) para el reporte mensual. | (T-L44, Laura), (T-L45, Roberto) |
| OBJ-05 | Tener la V1 en producción en 3 semanas. El plazo no es negociable. | (T-L6, Roberto), (R-P11) |

---

## 2. Alcance

### 2.1 In-Scope (V1)

- Autenticación propia con usuario y contraseña. Cada usuario tiene un email. (R-P1)
- Dos roles: Admin y Usuario. (T-L15, Laura), (R-P2)
- Proyectos: los usuarios pueden crearlos y editarlos. (R-P2)
- Tickets: crear, editar, archivar (borrado lógico) y cambiar de estado. (T-L18, Roberto), (R-P2), (R-P4)
- Asignación de un solo responsable por ticket. (T-L18, Roberto), (R-P5)
- Estados configurables, con libre paso entre ellos. (R-P6)
- Tablero por estados. (T-L58, Roberto), (T-Acuerdos)
- Filtros de tickets. (T-L27-28, Marcos y Laura)
- Comentarios en tickets, que se pueden editar y borrar mostrando quién lo hizo. (T-L40, Laura), (R-P12)
- Notificaciones por email a través del servidor de correo corporativo. (T-L40, Laura), (R-P13)
- Dashboard de métricas visible para todos. (T-L44, Laura), (R-P14)
- Modo oscuro, obligatorio en el MVP. (T-L64, Laura), (R-P15)

### 2.2 Out-of-Scope (V1)

Solo se lista lo que las fuentes excluyen, directa o implícitamente:

- Inicio de sesión con cuentas corporativas (Google Workspace, Microsoft 365 u otra). Se eligió login propio. (R-P1)
- Borrado físico de tickets. Solo hay borrado lógico. (T-L49, Laura), (R-P4)
- Varios responsables por ticket. (R-P5)
- Orden obligatorio al cambiar de estado. (R-P6)

> [PENDIENTE] No se definió qué funciones pasan a una fase 2 para cumplir el plazo (R-P11). Por eso todo lo que figura en los acuerdos sigue dentro del alcance. Ver el riesgo RSK-01.

---

## 3. Stack tecnológico

| Capa | Decisión | Estado | Fuente |
|---|---|---|---|
| Frontend | React | Mencionado en la reunión, sin confirmar en las respuestas | (T-L16, Marcos), (T-L54, Marcos) |
| Backend | Node.js | Mencionado en la reunión, sin confirmar en las respuestas | (T-L16, Marcos), (T-L56, Sofía) |
| Base de datos | PostgreSQL gestionado en Supabase, usado solo como base de datos (relacional). Reemplaza a SQL Server (R-P9) | Confirmado | (T-L34, Marcos), (D-01), ADR-001 |
| ORM o acceso a datos | [PENDIENTE] | Sin decisión | (T-L56, Sofía), (R-P9) |
| Librería de interfaz o sistema de diseño | [PENDIENTE] | Sin decisión | (T-L10, Marcos), (R-P15) |
| Hosting | Servicio gestionado. Proveedor [PENDIENTE] | Parcial | (R-P10) |
| Correo | Servidor de correo corporativo. Protocolo e integración [PENDIENTE] | Parcial | (R-P13) |
| Restricciones de TI | "Puede haber restricciones", sin detallar [PENDIENTE] | Sin decisión | (R-P10) |

---

## 4. Supuestos

| ID | Supuesto | Fuente |
|---|---|---|
| SUP-01 | Habrá unos 10 usuarios. No se indicó crecimiento a corto plazo. | (T-L6, Roberto), (R-P16) |
| SUP-02 | Solo existen los roles Admin y Usuario, porque no se mencionaron otros. | (T-L15, Laura), (R-P2) |
| SUP-03 | El stack React + Node se mantiene porque nadie lo objetó, aunque no se confirmó. | (T-L16, Marcos), (R-P9) |
| SUP-04 | Un ticket pertenece a un solo proyecto, porque el campo "proyecto" aparece en singular. Hay que validarlo. | (R-P7), relacionado con [PENDIENTE] P3 |
| SUP-05 | No hay un objetivo de rendimiento medible. Se busca que "no tarde en cargar". | (T-L35, Laura), (R-P16) |
| SUP-06 | La estética de referencia es "tipo Apple": blanco, limpio y con sombras suaves. | (T-L55, Laura) |

---

## 5. Requerimientos funcionales

### 5.1 Autenticación y usuarios

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-01 | El sistema permite iniciar sesión con usuario y contraseña propios del sistema. | (R-P1) |
| RF-02 | Cada cuenta de usuario tiene un email obligatorio. | (R-P1) |
| RF-03 | Cada usuario tiene uno de dos roles: Admin o Usuario. | (T-L15, Laura), (R-P2) |
| RF-04 | Gestión de usuarios (alta, baja, cambio de rol, restablecer contraseña): quién la hace y cómo [PENDIENTE]. | (T-L16, Marcos) |

### 5.2 Proyectos

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-05 | Los tickets se agrupan por proyecto. | (T-L19, Laura) |
| RF-06 | Un Usuario puede crear y editar proyectos. | (R-P2) |
| RF-07 | Si un Admin puede crear, editar o archivar proyectos: [PENDIENTE]. | (R-P2) |
| RF-08 | Qué proyectos ve cada usuario (todos o solo aquellos de los que es miembro) y si existe el concepto de miembro: [PENDIENTE]. | (T-L20-21, Marcos y Laura), (R-P3 sin respuesta) |
| RF-09 | Si un ticket puede pertenecer a más de un proyecto: [PENDIENTE] (ver SUP-04). | (T-L20, Marcos), (R-P3 sin respuesta) |

### 5.3 Tickets

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-10 | Un usuario puede crear tickets. | (T-L18, Roberto), (R-P2) |
| RF-11 | El ticket tiene estos campos: título, descripción, prioridad, fecha, responsable, proyecto y etiquetas. | (R-P7) |
| RF-12 | La descripción admite texto largo. | (T-L26, Laura) |
| RF-13 | Valores permitidos de prioridad: [PENDIENTE]. | (R-P7) |
| RF-14 | Qué significa el campo "fecha" (fecha límite, de creación u otra): [PENDIENTE]. | (R-P7) |
| RF-15 | Qué campos son obligatorios y cuáles opcionales: [PENDIENTE]. | (R-P7) |
| RF-16 | Etiquetas: si son libres o de un catálogo, y quién las gestiona: [PENDIENTE]. | (R-P7) |
| RF-17 | Un Usuario puede editar solo sus propios tickets. | (R-P2) |
| RF-18 | Un Admin puede editar tickets de cualquier usuario. | (R-P2) |
| RF-19 | Si "propios" significa los que el usuario creó o los que tiene asignados: [PENDIENTE]. | (R-P2) |

### 5.4 Archivado (borrado lógico)

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-20 | Los tickets nunca se borran físicamente. "Eliminar" hace un borrado lógico (archivado). | (T-L49, Laura), (R-P4) |
| RF-21 | Un Usuario puede eliminar o archivar solo sus propios tickets. | (T-L49, Laura), (R-P4) |
| RF-22 | Un Admin puede eliminar o archivar tickets de cualquier usuario. | (R-P2), (R-P4) |
| RF-23 | Cada archivado deja trazabilidad. Qué datos se guardan (quién y cuándo) [PENDIENTE]. | (R-P4) |
| RF-24 | El botón se llama "Eliminar", aunque en realidad archiva. | (T-L49, Laura). Sofía lo cuestionó en (T-L50). Validar el texto final [PENDIENTE] |
| RF-25 | Si los tickets archivados se pueden consultar o restaurar, y por quién: [PENDIENTE]. | (R-P4) |
| RF-26 | Si se pueden archivar tickets en estado final: [PENDIENTE]. | (T-L48, Marcos), (R-P4) |

### 5.5 Asignación

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-27 | Cada ticket tiene un solo responsable. | (T-L24, Sofía), (R-P5) |
| RF-28 | Un Usuario puede asignar tickets a sí mismo o a otros usuarios. | (T-L24, Sofía), (R-P5) |

### 5.6 Estados y tablero

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-29 | Los tickets pasan por estados. | (T-L28, Laura) |
| RF-30 | Los estados son configurables. | (R-P6) |
| RF-31 | Un ticket puede pasar de cualquier estado a cualquier otro. | (R-P6) |
| RF-32 | Un Usuario puede cambiar de estado sus propios tickets. | (R-P2) |
| RF-33 | Los tickets se ven en un tablero, con una columna por estado. | (T-L30, Laura), (T-L58, Roberto) |
| RF-34 | Estados iniciales por defecto (3: Por hacer, En progreso, Listo; o 4: con Review): [PENDIENTE]. | (T-L28-31, Laura, Marcos y Roberto) |
| RF-35 | Alcance y responsable de la configuración (global o por proyecto; Admin o Usuario): [PENDIENTE]. | (R-P6) |
| RF-36 | Cómo se marca qué estado cuenta como "finalizado", necesario para el dashboard (RF-48, RF-49): [PENDIENTE]. | (R-P6), (R-P14) |

### 5.7 Filtros

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-37 | Los tickets se pueden filtrar por fecha, prioridad, responsable, proyecto y etiquetas. | (T-L27, Marcos), (T-L28, Laura) |
| RF-38 | Si los filtros se pueden combinar y si se guardan: [PENDIENTE]. | (T-L28, Laura: "potente pero sencillo") |

### 5.8 Concurrencia

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-39 | Qué pasa cuando dos personas guardan el mismo ticket a la vez (gana el último que guarda, o se avisa del conflicto): [PENDIENTE]. | (T-L38, Sofía), (T-L39, Marcos), (R-P8 sin respuesta) |

### 5.9 Comentarios

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-40 | Los usuarios pueden comentar dentro de un ticket. | (T-L40, Laura) |
| RF-41 | Un comentario se puede editar. | (R-P12) |
| RF-42 | Un comentario se puede borrar. | (R-P12) |
| RF-43 | Al editar o borrar un comentario, queda visible qué usuario lo hizo. | (R-P12) |
| RF-44 | Quién puede editar o borrar cada comentario (solo el autor, o también el Admin): [PENDIENTE]. | (R-P12) |
| RF-45 | Cómo se escribe una mención (por ejemplo, @usuario): [PENDIENTE]. | (T-L40, Laura), (R-P12 sin respuesta) |

### 5.10 Notificaciones por email

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-46 | El sistema envía un email al usuario cuando se le asigna un ticket o cuando lo mencionan, usando el servidor de correo corporativo. | (T-L40, Laura), (R-P13) |
| RF-47 | Si hay otros eventos que disparan emails, y el contenido de las plantillas: [PENDIENTE]. | (T-L41, Marcos), (R-P13) |

### 5.11 Dashboard

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-48 | El dashboard muestra gráficas con los tickets cerrados por mes y por proyecto. | (T-L44, Laura) |
| RF-49 | "Cerrado" significa que el ticket llegó a un estado finalizado (ver RF-36). | (R-P14) |
| RF-50 | Todos los usuarios pueden ver el dashboard. | (R-P14) |
| RF-51 | Métricas adicionales: [PENDIENTE]. | (R-P14) |

### 5.12 Interfaz

| ID | Requerimiento | Fuente |
|---|---|---|
| RF-52 | La interfaz tiene modo oscuro, obligatorio en el MVP. | (T-L64, Laura), (R-P15) |
| RF-53 | Hay un formulario sencillo para registrar tickets. | (T-L11, Laura) |

---

## 6. Requerimientos no funcionales

| ID | Requerimiento | Fuente |
|---|---|---|
| RNF-01 | La interfaz se puede usar sin manual. Criterio de aceptación medible [PENDIENTE]. | (T-L9, Laura) |
| RNF-02 | Estética moderna y limpia, "tipo Apple". Guía de marca o librería de componentes [PENDIENTE]. | (T-L55, Laura), (R-P15) |
| RNF-03 | La carga debe ser rápida, sin una meta medible definida. | (T-L35, Laura), (R-P16) |
| RNF-04 | Las contraseñas se guardan de forma segura. Política de contraseñas [PENDIENTE]. | Se deriva de (R-P1) |

---

## 7. Riesgos

| ID | Riesgo | Fuente |
|---|---|---|
| RSK-01 | El plazo de 3 semanas no es negociable y el alcance no se recortó. El Tech Lead advirtió que no es viable (solo autenticación y modelo de datos ocupan una semana). | (T-L36, Marcos), (T-L46, Marcos), (R-P11) |
| RSK-02 | Las decisiones sobre visibilidad de proyectos (RF-08) y concurrencia (RF-39) afectan al modelo de datos y siguen abiertas. | (T-Acuerdos), (R-P3), (R-P8) |
| RSK-03 | Las restricciones de TI del servicio gestionado no se conocen y pueden afectar al despliegue y a la conexión con el correo corporativo. | (R-P10) |

---

## 8. Pendientes que bloquean el desarrollo (por prioridad)

1. Visibilidad y pertenencia de proyectos (RF-08, RF-09).
2. Concurrencia (RF-39).
3. Estados por defecto, dónde se configuran y cuál cuenta como final (RF-34, RF-35, RF-36).
4. Gestión de usuarios (RF-04) y qué significa "tickets propios" (RF-19).
5. ORM y proveedor del servicio gestionado (sección 3).
6. Qué se recorta o se reordena para cumplir las 3 semanas (sección 2.2, RSK-01).
