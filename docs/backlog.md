# Backlog del MVP: Mini Jira

| Campo | Valor |
|---|---|
| Fuente única | `docs/specs.md` v1.0 |
| Fecha | 23-09-2026 |
| Autora | Isis Silva (Product Owner) |

**Reglas de este backlog**

- Cada historia (HU) indica el RF del PRD que la origina.
- Los escenarios describen comportamiento de negocio (el QUÉ), sin nombres de botones ni de pantallas.
- Si el PRD deja algo sin definir, el escenario lo marca como `[PENDIENTE RF-xx]` y no inventa el resultado.
- Los *edge cases* de la sección 2 son deducciones propias. Cada uno se apoya en un RF, y cuando el PRD no define el resultado queda marcado como `[PENDIENTE]`.

---

## 1. Historias de usuario críticas

### HU-01: Iniciar sesión

**Como** usuario de la empresa **quiero** entrar con mi usuario y contraseña **para** acceder a mis proyectos y tickets.

**Origen:** RF-01, RF-02, RF-03

```gherkin
# language: es
Característica: Autenticación propia

  Escenario: Inicio de sesión con credenciales válidas
    Dado que existe una cuenta con usuario, contraseña y email registrados
    Cuando la persona inicia sesión con ese usuario y esa contraseña
    Entonces accede al sistema
    Y el sistema la reconoce con el rol asignado a su cuenta

  Escenario: Cada cuenta tiene un único rol
    Dado que existe una cuenta de usuario
    Entonces su rol es "Admin" o "Usuario"
```

---

### HU-02: Gestionar proyectos

**Como** usuario **quiero** crear y editar proyectos **para** agrupar los tickets de cada iniciativa y no mezclarlos en una sola lista.

**Origen:** RF-05, RF-06

```gherkin
# language: es
Característica: Proyectos

  Escenario: Un usuario crea un proyecto
    Dado que inicié sesión con rol "Usuario"
    Cuando creo un proyecto nuevo
    Entonces el proyecto queda disponible para agrupar tickets

  Escenario: Un usuario edita un proyecto
    Dado que inicié sesión con rol "Usuario"
    Y existe un proyecto
    Cuando modifico los datos del proyecto
    Entonces el proyecto conserva sus tickets con los datos actualizados

  Escenario: Los tickets se muestran agrupados por proyecto
    Dado que existen tickets en distintos proyectos
    Cuando consulto los tickets de un proyecto
    Entonces solo veo los tickets que pertenecen a ese proyecto
```

---

### HU-03: Crear un ticket

**Como** usuario **quiero** registrar una tarea en un formulario sencillo **para** que el trabajo quede documentado y se pueda seguir.

**Origen:** RF-10, RF-11, RF-12, RF-53

```gherkin
# language: es
Característica: Creación de tickets

  Escenario: Crear un ticket con todos sus campos
    Dado que inicié sesión
    Y existe un proyecto
    Cuando creo un ticket con título, descripción, prioridad, fecha,
      responsable, proyecto y etiquetas
    Entonces el ticket queda registrado en ese proyecto
    Y el ticket muestra todos los datos que ingresé

  Escenario: La descripción admite texto largo
    Dado que inicié sesión
    Cuando creo un ticket con una descripción de varios párrafos
    Entonces el ticket conserva la descripción completa
```

---

### HU-04: Editar un ticket

**Como** usuario **quiero** editar mis tickets **para** mantener su información al día. **Como** admin **quiero** editar cualquier ticket **para** corregir o completar el trabajo del equipo.

**Origen:** RF-17, RF-18

```gherkin
# language: es
Característica: Edición de tickets

  Escenario: Un usuario edita su propio ticket
    Dado que inicié sesión con rol "Usuario"
    Y existe un ticket propio
    Cuando modifico sus datos
    Entonces el ticket queda actualizado

  Escenario: Un admin edita el ticket de otra persona
    Dado que inicié sesión con rol "Admin"
    Y existe un ticket de otro usuario
    Cuando modifico sus datos
    Entonces el ticket queda actualizado
```

---

### HU-05: Eliminar (archivar) un ticket

**Como** usuario **quiero** eliminar mis tickets **para** sacar de circulación el trabajo que ya no aplica, sin perder su historial. **Como** admin **quiero** eliminar tickets de cualquier persona **para** mantener ordenado el sistema dejando trazabilidad.

**Origen:** RF-20, RF-21, RF-22, RF-23, RF-24

```gherkin
# language: es
Característica: Archivado de tickets (borrado lógico)

  Escenario: Un usuario elimina su propio ticket
    Dado que inicié sesión con rol "Usuario"
    Y existe un ticket propio
    Cuando elimino el ticket
    Entonces el ticket queda archivado
    Y el ticket no se borra físicamente del sistema
    Y queda un registro de trazabilidad del archivado

  Escenario: Un admin elimina el ticket de otra persona
    Dado que inicié sesión con rol "Admin"
    Y existe un ticket de otro usuario
    Cuando elimino el ticket
    Entonces el ticket queda archivado
    Y queda un registro de trazabilidad del archivado

  Escenario: La acción se presenta como "Eliminar"
    Dado que puedo archivar un ticket
    Entonces la acción se presenta con el nombre "Eliminar"
    # Texto final por validar [PENDIENTE RF-24]
```

---

### HU-06: Asignar un responsable

**Como** usuario **quiero** asignar un ticket a una persona, a mí o a otra, **para** que quede claro quién debe hacerlo.

**Origen:** RF-27, RF-28

```gherkin
# language: es
Característica: Asignación de responsable

  Escenario: Un usuario asigna un ticket a otra persona
    Dado que inicié sesión con rol "Usuario"
    Y existe un ticket
    Y existe otro usuario en el sistema
    Cuando asigno el ticket a ese usuario
    Entonces ese usuario queda como responsable del ticket

  Escenario: Un usuario se asigna un ticket a sí mismo
    Dado que inicié sesión con rol "Usuario"
    Y existe un ticket
    Cuando me asigno el ticket
    Entonces quedo como responsable del ticket
```

---

### HU-07: Mover tickets entre estados en el tablero

**Como** usuario **quiero** ver mis tickets en un tablero por estados y moverlos **para** reflejar el avance real del trabajo.

**Origen:** RF-29, RF-31, RF-32, RF-33

```gherkin
# language: es
Característica: Tablero de estados

  Escenario: El tablero muestra una columna por estado
    Dado que existen estados configurados
    Y existen tickets en distintos estados
    Cuando consulto el tablero
    Entonces veo una columna por cada estado
    Y cada ticket aparece en la columna de su estado actual

  Escenario: Un usuario cambia el estado de su propio ticket
    Dado que inicié sesión con rol "Usuario"
    Y existe un ticket propio en un estado
    Cuando cambio el ticket a otro estado
    Entonces el ticket queda en el nuevo estado

  Esquema del escenario: Se puede pasar de cualquier estado a cualquier otro
    Dado que existe un ticket propio en el estado "<origen>"
    Cuando lo cambio al estado "<destino>"
    Entonces el ticket queda en el estado "<destino>"

    Ejemplos:
      | origen            | destino           |
      | un estado inicial | un estado final   |
      | un estado final   | un estado inicial |
      | un estado final   | un estado intermedio |
    # Estados por defecto sin definir [PENDIENTE RF-34]
```

---

### HU-08: Configurar estados

**Como** responsable de la configuración **quiero** definir los estados del flujo **para** adaptar el tablero a nuestra forma de trabajar.

**Origen:** RF-30, RF-35, RF-36

```gherkin
# language: es
Característica: Estados configurables
  # Quién configura y si es global o por proyecto: [PENDIENTE RF-35]

  Escenario: Agregar un estado al flujo
    Dado que tengo permiso para configurar estados
    Cuando agrego un nuevo estado
    Entonces el tablero muestra una columna para ese estado
    Y los tickets se pueden mover a ese estado

  Escenario: Identificar el estado finalizado
    Dado que tengo permiso para configurar estados
    Cuando defino qué estado cuenta como finalizado
    Entonces el dashboard considera cerrados los tickets en ese estado
    # Mecanismo sin definir [PENDIENTE RF-36]
```

---

### HU-09: Filtrar tickets

**Como** usuario **quiero** filtrar los tickets **para** encontrar rápido lo que necesito.

**Origen:** RF-37

```gherkin
# language: es
Característica: Filtros de tickets

  Esquema del escenario: Filtrar por un criterio
    Dado que existen tickets con distintos valores de "<criterio>"
    Cuando filtro los tickets por un valor de "<criterio>"
    Entonces solo veo los tickets que tienen ese valor

    Ejemplos:
      | criterio    |
      | fecha       |
      | prioridad   |
      | responsable |
      | proyecto    |
      | etiquetas   |
```

---

### HU-10: Comentar en un ticket

**Como** usuario **quiero** comentar dentro de un ticket **para** conversar sobre el trabajo sin salir del sistema.

**Origen:** RF-40, RF-41, RF-42, RF-43

```gherkin
# language: es
Característica: Comentarios

  Escenario: Agregar un comentario
    Dado que inicié sesión
    Y existe un ticket
    Cuando agrego un comentario al ticket
    Entonces el comentario queda visible en el ticket

  Escenario: Editar un comentario deja constancia de quién lo editó
    Dado que existe un comentario en un ticket
    Cuando un usuario autorizado edita el comentario
    Entonces el comentario muestra el nuevo contenido
    Y queda visible qué usuario lo editó

  Escenario: Borrar un comentario deja constancia de quién lo borró
    Dado que existe un comentario en un ticket
    Cuando un usuario autorizado borra el comentario
    Entonces queda visible qué usuario lo borró
    # Quién está autorizado: [PENDIENTE RF-44]
```

---

### HU-11: Recibir notificaciones por email

**Como** usuario **quiero** recibir un email cuando me asignan un ticket o me mencionan **para** enterarme a tiempo sin revisar el sistema todo el día.

**Origen:** RF-46

```gherkin
# language: es
Característica: Notificaciones por email

  Escenario: Email al ser asignado
    Dado que tengo una cuenta con email registrado
    Cuando otro usuario me asigna un ticket
    Entonces recibo un email que me avisa de la asignación
    Y el email se envía a través del servidor de correo corporativo

  Escenario: Email al ser mencionado
    Dado que tengo una cuenta con email registrado
    Cuando otro usuario me menciona en un comentario
    Entonces recibo un email que me avisa de la mención
    # Forma de escribir la mención: [PENDIENTE RF-45]
```

---

### HU-12: Consultar el dashboard

**Como** miembro de la empresa **quiero** ver cuántos tickets se cierran al mes por proyecto **para** mostrar el avance en el reporte mensual.

**Origen:** RF-48, RF-49, RF-50

```gherkin
# language: es
Característica: Dashboard de métricas

  Escenario: Ver los tickets cerrados por mes y proyecto
    Dado que existen tickets que llegaron al estado finalizado en distintos meses y proyectos
    Cuando consulto el dashboard
    Entonces veo gráficas con la cantidad de tickets cerrados por mes
    Y puedo distinguir la cantidad por proyecto

  Esquema del escenario: Todos los roles pueden ver el dashboard
    Dado que inicié sesión con rol "<rol>"
    Cuando consulto el dashboard
    Entonces veo las métricas

    Ejemplos:
      | rol     |
      | Admin   |
      | Usuario |
```

---

### HU-13: Usar modo oscuro

**Como** usuario **quiero** activar el modo oscuro **para** trabajar con la apariencia que prefiero.

**Origen:** RF-52

```gherkin
# language: es
Característica: Modo oscuro

  Escenario: Activar el modo oscuro
    Dado que inicié sesión
    Cuando activo el modo oscuro
    Entonces la interfaz se muestra con la apariencia oscura

  Escenario: Volver al modo claro
    Dado que tengo activo el modo oscuro
    Cuando desactivo el modo oscuro
    Entonces la interfaz se muestra con la apariencia clara
```

---

## 2. Edge cases y escenarios de fallo (deducidos)

### EC-01: Autenticación (RF-01, RF-02)

```gherkin
# language: es
Característica: Fallos de autenticación

  Escenario: Contraseña incorrecta
    Dado que existe una cuenta registrada
    Cuando alguien intenta iniciar sesión con una contraseña incorrecta
    Entonces no accede al sistema
    # Mensaje exacto y límite de intentos: [PENDIENTE RNF-04]

  Escenario: Usuario inexistente
    Dado que no existe una cuenta con cierto usuario
    Cuando alguien intenta iniciar sesión con ese usuario
    Entonces no accede al sistema

  Escenario: Cuenta sin email
    Dado que se registra una cuenta nueva
    Cuando la cuenta no tiene email
    Entonces la cuenta no se crea
    # Quién da de alta las cuentas: [PENDIENTE RF-04]
```

### EC-02: Permisos sobre tickets (RF-17, RF-18, RF-19, RF-32)

```gherkin
# language: es
Característica: Límites de permisos del rol Usuario

  Escenario: Un usuario intenta editar el ticket de otra persona
    Dado que inicié sesión con rol "Usuario"
    Y existe un ticket que no es mío
    Cuando intento modificar sus datos
    Entonces el cambio no se aplica
    Y el ticket conserva sus datos originales

  Escenario: Un usuario intenta cambiar el estado del ticket de otra persona
    Dado que inicié sesión con rol "Usuario"
    Y existe un ticket que no es mío
    Cuando intento cambiarlo de estado
    Entonces el ticket permanece en su estado original
    # Qué cuenta como "ticket propio" (creado o asignado): [PENDIENTE RF-19]
```

### EC-03: Archivado (RF-20 a RF-26)

```gherkin
# language: es
Característica: Casos límite del archivado

  Escenario: Un usuario intenta eliminar el ticket de otra persona
    Dado que inicié sesión con rol "Usuario"
    Y existe un ticket que no es mío
    Cuando intento eliminarlo
    Entonces el ticket no se archiva

  Escenario: Un ticket archivado se conserva en el sistema
    Dado que un ticket fue archivado
    Entonces el ticket sigue existiendo en el sistema
    Y conserva la trazabilidad de su archivado

  Escenario: Consultar o restaurar un ticket archivado
    Dado que un ticket fue archivado
    Cuando alguien intenta consultarlo o restaurarlo
    Entonces [PENDIENTE RF-25]

  Escenario: Archivar un ticket en estado finalizado
    Dado que un ticket está en el estado finalizado
    Cuando su dueño o un admin intenta eliminarlo
    Entonces [PENDIENTE RF-26]

  Escenario: Un ticket archivado en el dashboard
    Dado que un ticket cerrado fue archivado
    Cuando consulto el dashboard
    Entonces [PENDIENTE RF-25, RF-49]: sin definir si sigue contando como cerrado
```

### EC-04: Asignación (RF-27, RF-28)

```gherkin
# language: es
Característica: Casos límite de la asignación

  Escenario: Reasignar un ticket mantiene un solo responsable
    Dado que un ticket tiene un responsable
    Cuando se asigna el ticket a otro usuario
    Entonces el ticket queda con un único responsable: el nuevo

  Escenario: No se pueden asignar dos responsables a la vez
    Dado que existe un ticket
    Cuando se intenta asignar a dos usuarios al mismo tiempo
    Entonces el ticket queda con un único responsable

  Escenario: Autoasignación y email
    Dado que me asigno un ticket a mí mismo
    Entonces [PENDIENTE RF-47]: sin definir si recibo el email de asignación
```

### EC-05: Estados y tablero (RF-30, RF-31, RF-35, RF-36)

```gherkin
# language: es
Característica: Casos límite de los estados

  Escenario: Reabrir un ticket cerrado
    Dado que un ticket está en el estado finalizado
    Cuando su dueño lo cambia a un estado anterior
    Entonces el ticket queda en ese estado
    Y [PENDIENTE RF-49]: sin definir si deja de contar como cerrado en el dashboard

  Escenario: Quitar un estado que tiene tickets
    Dado que un estado contiene tickets
    Cuando alguien intenta quitar ese estado de la configuración
    Entonces [PENDIENTE RF-35]: sin definir qué pasa con esos tickets

  Escenario: No hay ningún estado marcado como finalizado
    Dado que ningún estado está marcado como finalizado
    Cuando consulto el dashboard
    Entonces [PENDIENTE RF-36]: sin definir qué muestra
```

### EC-06: Concurrencia (RF-39)

```gherkin
# language: es
Característica: Edición simultánea de un ticket
  # BLOQUEADO: la regla de negocio no está decidida [PENDIENTE RF-39]

  Escenario: Dos personas guardan el mismo ticket a la vez
    Dado que dos usuarios con permiso abren el mismo ticket
    Y ambos modifican el mismo campo
    Cuando los dos guardan sus cambios casi al mismo tiempo
    Entonces [PENDIENTE RF-39]: gana el último que guarda, o se avisa del conflicto
```

### EC-07: Filtros (RF-37, RF-38)

```gherkin
# language: es
Característica: Casos límite de los filtros

  Escenario: Un filtro sin coincidencias
    Dado que ningún ticket tiene cierta etiqueta
    Cuando filtro por esa etiqueta
    Entonces no se muestra ningún ticket

  Escenario: Combinar varios criterios
    Dado que existen tickets con distintos responsables y prioridades
    Cuando filtro por un responsable y una prioridad a la vez
    Entonces [PENDIENTE RF-38]: sin definir si los filtros se combinan
```

### EC-08: Comentarios (RF-42, RF-43, RF-44)

```gherkin
# language: es
Característica: Casos límite de los comentarios

  Escenario: Un comentario borrado deja rastro
    Dado que un comentario fue borrado
    Cuando alguien consulta el ticket
    Entonces sigue visible qué usuario borró el comentario

  Escenario: Editar un comentario de otra persona
    Dado que existe un comentario escrito por otro usuario
    Cuando intento editarlo o borrarlo
    Entonces [PENDIENTE RF-44]: sin definir si está permitido
```

### EC-09: Notificaciones por email (RF-46, RF-47)

```gherkin
# language: es
Característica: Fallos en las notificaciones

  Escenario: El servidor de correo no está disponible
    Dado que el servidor de correo corporativo no responde
    Cuando me asignan un ticket
    Entonces [PENDIENTE RF-47]: sin definir si el envío se reintenta
      y si la asignación se guarda igual

  Escenario: Mencionar a alguien que no existe
    Dado que un comentario menciona a un usuario que no existe
    Cuando se publica el comentario
    Entonces no se envía ningún email
    # Formato de la mención: [PENDIENTE RF-45]
```

### EC-10: Proyectos (RF-08, RF-09)

```gherkin
# language: es
Característica: Visibilidad de proyectos
  # BLOQUEADO: regla sin decidir [PENDIENTE RF-08, RF-09]

  Escenario: Un usuario consulta un proyecto en el que no participa
    Dado que existe un proyecto con tickets en los que no participo
    Cuando intento ver ese proyecto
    Entonces [PENDIENTE RF-08]: sin definir si lo veo
```

### EC-11: Modo oscuro (RF-52)

```gherkin
# language: es
Característica: Cobertura del modo oscuro

  Escenario: El modo oscuro se aplica en todas las vistas del MVP
    Dado que tengo activo el modo oscuro
    Cuando consulto el tablero, un ticket, sus comentarios o el dashboard
    Entonces todas esas vistas se muestran con la apariencia oscura
    Y los textos y las gráficas siguen siendo legibles
```

---

## 3. Trazabilidad RF → HU / EC

| RF | Historias y edge cases |
|---|---|
| RF-01, RF-02, RF-03 | HU-01, EC-01 |
| RF-05, RF-06 | HU-02 |
| RF-08, RF-09 | EC-10 (bloqueado) |
| RF-10, RF-11, RF-12, RF-53 | HU-03 |
| RF-17, RF-18, RF-19 | HU-04, EC-02 |
| RF-20 a RF-26 | HU-05, EC-03 |
| RF-27, RF-28 | HU-06, EC-04 |
| RF-29, RF-31, RF-32, RF-33 | HU-07, EC-02, EC-05 |
| RF-30, RF-35, RF-36 | HU-08, EC-05 |
| RF-37, RF-38 | HU-09, EC-07 |
| RF-39 | EC-06 (bloqueado) |
| RF-40 a RF-44 | HU-10, EC-08 |
| RF-45, RF-46, RF-47 | HU-11, EC-04, EC-09 |
| RF-48, RF-49, RF-50 | HU-12, EC-03, EC-05 |
| RF-52 | HU-13, EC-11 |

**No cubiertos por ninguna historia** (están [PENDIENTE] en el PRD y no hay comportamiento que especificar): RF-04, RF-07, RF-13, RF-14, RF-15, RF-16, RF-34, RF-51.
