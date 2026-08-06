# Prompt de arranque — Proyecto nuevo (documentación primero)

Vamos a arrancar un proyecto nuevo. Antes de escribir una sola línea de código,
quiero que construyamos primero la documentación base que va a gobernar todo
el desarrollo. Esta es la metodología: **documentar el qué y el cómo antes del
con qué**. El stack técnico de este proyecto puntual (nombres de proyecto,
servicios contratados, variables de entorno) se registra después, como parte
del módulo "Setup del proyecto" en `TASKS.md` — pero la elección de *qué*
tecnologías usar ya tiene un default fijo (ver "Stack técnico por defecto"
más abajo) que no hace falta re-evaluar desde cero en cada proyecto nuevo.

## Rol que quiero que asumas

Actuá como product lead + arquitecto técnico. Tu trabajo en esta fase NO es
programar, es:

1. Hacerme las preguntas necesarias para entender el negocio, el usuario y el
   problema, agrupadas por tema (no una por una, no me abrumes).
2. Redactar los documentos de la lista de abajo, uno a la vez, mostrándomelos
   para que yo apruebe o corrija antes de pasar al siguiente.
3. No avanzar de un documento al siguiente hasta que yo confirme que el
   anterior está aprobado. Si quedan puntos sin resolver, dejalos explícitos
   en una sección "Preguntas abiertas / a definir más adelante" y seguí.
4. No re-decidir el stack técnico desde cero — ya hay uno por defecto (ver
   abajo). Lo que sí va después, como tareas en `TASKS.md`, es la
   configuración concreta de ese stack para este proyecto puntual (crear el
   proyecto de Supabase, el bucket de R2, etc.) y cualquier desviación
   justificada del default.

## Stack técnico por defecto

Salvo que este proyecto tenga una necesidad real que justifique desviarse,
usamos siempre lo mismo — evita re-evaluar de cero en cada proyecto nuevo y
mantiene consistencia entre proyectos. Si hace falta desviarse, la desviación
se declara explícitamente (con el motivo) en el módulo "Setup del proyecto"
de `TASKS.md`, no se decide en silencio a mitad de una card.

- **Frontend**: Next.js. Despliegue en **Vercel**.
- **Backend, base de datos y autenticación**: **Supabase** (Postgres + Row
  Level Security + Auth). Es la fuente de verdad de datos y usuarios salvo
  excepción justificada.
- **APIs de propósito específico** (ej. subida de archivos a servicios de
  terceros, integraciones que no encajan bien en un Route Handler de Next):
  **Bun**, desplegado en **Railway**. No se crea un servicio Bun aparte "por
  si acaso" — solo cuando hay una necesidad concreta que Next/Supabase no
  resuelven directamente.
- **Almacenamiento de archivos**: **Cloudflare R2**.
- **Dominio y DNS**: **Cloudflare**.

Esto no reemplaza el módulo "Setup del proyecto" de `TASKS.md`: ahí se sigue
dejando constancia explícita de la configuración concreta de este proyecto
(URL del proyecto Supabase, nombre del bucket, dominios, variables de entorno
necesarias) — simplemente ya no hace falta decidir el stack en sí desde cero.

## Estándares de infraestructura y proceso por defecto

Igual que el stack técnico, estas prácticas ya están decididas de antemano —
no se re-evalúan proyecto por proyecto salvo justificación explícita:

- **Entornos separados por proyecto de Supabase, no por branch**: desarrollo
  y producción son dos proyectos de Supabase distintos (alcanza con el plan
  Free — 2 proyectos activos, sin costo). La separación por *branching* de
  Supabase es una feature del plan Pro con costo adicional por hora y queda
  como mejora opcional futura (ej. previews automáticos por PR), no como
  default. Un tercer entorno de staging, si hace falta, también implica
  upgrade y se evalúa caso por caso, no es parte del setup base.
- **Migraciones de base de datos versionadas**: todo cambio de esquema
  (tablas, columnas, políticas RLS) se escribe como archivo de migración SQL
  commiteado en el repo (`supabase/migrations/`) y se aplica vía Supabase
  CLI — nunca un cambio hecho solo a mano en el Dashboard sin dejar rastro en
  git. Esto es lo que permite aplicar el mismo esquema a los proyectos
  separados de dev/prod sin repetirlo a mano, y es coherente con la regla ya
  existente de que toda tabla nueva lleva RLS desde el mismo commit que la
  crea — esa política también vive en el archivo de migración.
- **CI en cada push/PR**: pipeline de GitHub Actions que corre lint,
  type-check y build antes de dar el código por bueno. No reemplaza la
  prueba manual bloqueante — la complementa como primera red de errores
  mecánicos (typos, tipos, imports rotos) que hoy dependían solo de que se
  notaran a mano.
- **Observabilidad desde el día 1**: Sentry (o equivalente) configurado
  tanto en el frontend (Next.js/Vercel) como en cualquier servicio Bun/
  Railway, como parte del módulo "Setup del proyecto" — no como una pasada
  posterior. El costo en el plan free es prácticamente nulo; la alternativa
  es enterarte de que algo se rompió en producción solo cuando un usuario se
  queja.
- **Backups de base de datos — gate obligatorio antes del primer cliente
  real**: el plan Free de Supabase no incluye backups automáticos. Antes de
  que el proyecto de producción tenga su primer usuario/cliente real, es un
  paso obligatorio (no opcional, no "después") upgradear ese proyecto a Pro
  — el upgrade activa backups diarios automáticos sin configuración
  adicional. El proyecto de desarrollo puede quedarse en Free
  indefinidamente, porque no tiene datos reales que perder.
- **Contrato compartido entre Next y Bun**: cuando el proyecto incluye un
  servicio Bun, los schemas de validación (`zod` u equivalente) se comparten
  entre ambos lados — nunca se definen dos veces por separado con el riesgo
  de que diverjan. Se organiza como una carpeta/paquete compartido dentro
  del mismo repo.

## Documentos a crear (en este orden)

### 1. `BUSINESS_LOGIC.md` — Lógica de negocio

Documento de referencia del dominio. Es lo primero que se lee en cualquier
sesión de trabajo, humana o de IA. Debe incluir:

- **Contexto**: qué es el negocio/proyecto, quién lo pidió, qué problema real
  resuelve, cómo se resuelve hoy sin esta herramienta (si aplica).
- **Objetivo del producto**: qué le permite hacer a cada tipo de usuario.
- **Público objetivo y restricciones de diseño**: quién lo usa, en qué
  contexto (dispositivo, tiempo disponible, nivel técnico), qué eso implica
  para el producto (mobile-first, PWA, fricción mínima, etc. — lo que
  corresponda, sin asumir nada por defecto).
- **Roles**: tabla de roles y qué puede ver/hacer cada uno. El registro es
  **cerrado por defecto** (ver "Modelo de registro" en `SECURITY.md`): no
  hay alta pública, la primera cuenta la crea el desarrollador y de ahí en
  más cada cuenta la crea un rol con permiso explícito para hacerlo. Por
  eso, para todo rol que pueda crear cuentas de otro rol, definir acá,
  explícitamente: (a) **qué permisos exactos** tiene la cuenta creada — no
  asumir "los mismos que quien la creó" ni "los mínimos por defecto"; (b) si
  hay un **límite de cuántas cuentas** puede crear ese rol (fijo, o atado a
  un plan/suscripción) y qué pasa al alcanzarlo (bloquear creación, pedir
  upgrade, etc.). Si no hay límite, decirlo explícitamente ("sin límite")
  en vez de dejarlo sin mencionar.
- **Flujos principales**: paso a paso de cada flujo core (alta de usuario,
  flujo transaccional principal, aprobaciones, etc.).
- **Entidades del dominio**: cada entidad con sus campos relevantes y reglas
  de negocio asociadas (no es un modelo de datos técnico, es conceptual).
- **Estados y transiciones**: para cada entidad que tenga ciclo de vida
  (pedido, solicitud, ticket, etc.), estados posibles y qué dispara cada
  transición.
- **Estrategia de baja por entidad (obligatorio, no opcional)**: para toda
  entidad que se pueda "borrar" o "dar de baja", definir explícitamente acá
  — no al escribir el endpoint — al menos: (a) **soft delete** (queda
  marcada inactiva/archivada, se preserva para auditoría/histórico/reversión)
  vs **hard delete** (se elimina físicamente, sin vuelta atrás); (b) qué pasa
  con las entidades que la referencian (bloquear la baja, cascada, o
  desasociar); (c) quién puede hacerlo y si requiere confirmación reforzada.
  Esta es una decisión de arquitectura de datos con consecuencias que después
  son caras de revertir (una tabla con hard delete no puede recuperar
  histórico retroactivamente) — por defecto, preferir soft delete salvo que
  haya una razón explícita (legal, volumen, privacidad) para lo contrario, y
  dejar esa razón anotada. Si en algún momento del desarrollo surge una
  entidad borrable que no quedó cubierta acá, es una señal de que este
  documento quedó desactualizado: se resuelve la duda con el usuario primero
  y se actualiza esta sección antes de programar la baja — no se asume la
  opción que sea más simple de implementar en el momento.
- **Reglas específicas por rol**: ediciones, cancelaciones, límites de tiempo,
  permisos — quién puede hacer qué y bajo qué condición.
- **Comunicaciones/notificaciones**: en qué momentos se notifica a quién y con
  qué contenido (sin especificar el proveedor todavía).
- **Vistas de la aplicación por rol**: qué secciones/pantallas ve cada rol.
- **Fuera de alcance (explícito)**: qué decidimos que este proyecto NO hace,
  para evitar scope creep y ambigüedad futura.
- **Preguntas abiertas**: lo que queda pendiente de definir más adelante.

### 2. `DESIGN.md` — Esquema de diseño e intención

Documento hermano del anterior: si `BUSINESS_LOGIC.md` define **qué hace** la
app, este define **cómo se ve y por qué**. Debe incluir:

- **Intención**: qué rol cumple el diseño dado el usuario y contexto descritos
  en `BUSINESS_LOGIC.md` (¿debe desaparecer? ¿debe transmitir confianza,
  velocidad, seriedad?). Conectá cada decisión visual con una verdad del
  negocio, no con gusto estético suelto.
- **Principios de diseño**: tabla de principio → qué significa en la
  práctica, específicos de este proyecto (no genéricos de UX 101).
- **Público y contexto de uso**: dispositivo primario, condiciones de uso
  (luz, prisa, interrupciones), nivel técnico, y si hay perfiles distintos
  que comparten base visual pero difieren en densidad de información.
- **Dirección visual**: proponé 2–3 direcciones visuales distintas
  (personalidad + paleta + justificación de por qué encaja con el negocio),
  para que yo elija una. Una vez elegida, marcala como **decidida** y dejá
  las descartadas documentadas (no las borres, quedan de referencia).
  - Si es útil para decidir, generá un explorador visual interactivo
    (artifact) con las opciones antes de fijar la dirección.
  - **Importante**: esta exploración inicial define el *sistema* (paleta,
    tipografía, tokens, principios) aplicado a componentes de muestra — no
    intenta anticipar cómo se van a ver todas las pantallas reales de la
    app. Diseñar las pantallas completas de una sola vez al inicio, antes de
    conocer el detalle de cada módulo, es exactamente lo que produce
    documentos de diseño que quedan desactualizados apenas arranca la
    implementación real. Cada pantalla se diseña cuando le toca (ver
    "Pantallas y patrones — bitácora" más abajo y la sección de
    metodología).
- **Pantallas y patrones — bitácora (vive durante todo el proyecto)**: tabla
  o lista que se va llenando a medida que se construye cada pantalla o
  componente/patrón de interacción reusable (ej. un menú de acciones, un
  encabezado estándar, un FAB de navegación) — no se llena de una sola vez
  al principio. Cada entrada anota: nombre de la pantalla/patrón, módulo de
  `TASKS.md` al que pertenece, decisión visual tomada (o link al artifact de
  prototipo aprobado), y fecha. Una pantalla o patrón nuevo no se considera
  "diseño cerrado" hasta tener su entrada acá — ver la regla de
  prototipado-antes-de-código en la sección de metodología.
- **Paleta — modo claro y modo oscuro**: tabla de tokens (`--bg`, `--surface`,
  `--text`, `--muted`, `--border`, `--primary`, `--on-primary`, `--accent`,
  y tokens semánticos de estado como `--warn`/`--ok`/`--bad`) con su hex y su
  uso. El modo oscuro se recalibra a mano (contraste, saturación), no es una
  inversión automática del claro.
- **Sistema de tokens**: regla explícita de que el color nunca se escribe en
  crudo en los componentes — todo pasa por variables semánticas, separando
  tokens de marca de tokens de estado.
- **Escala de forma y espacio**: radios de borde, escala de espaciado, área
  mínima táctil.
- **Tipografía**: familia de display y de texto (con criterio de por qué),
  y cómo se manejan los números si hay datos tabulares/monetarios.
- **Theming claro/oscuro**: estrategia (automático, manual, ambos) y regla de
  persistencia de la elección del usuario.
- **Layout y navegación por tamaño de pantalla**: cómo cambia la navegación y
  la densidad entre celular, tablet y escritorio.
- **Voz y microcopy**: tono, idioma, cómo se nombran acciones y estados,
  cómo se redactan los estados vacíos y los errores.
- **Piso de calidad (no negociable)**: checklist de accesibilidad y
  responsive mínimo (contraste, foco de teclado, `prefers-reduced-motion`,
  ancho mínimo soportado, etc.).
- **Decisiones cerradas** vs **Pendiente de decidir**: separá claramente qué
  ya está aprobado de lo que sigue abierto.

### 3. `SECURITY.md` — Estándares de seguridad

Documento hermano de `BUSINESS_LOGIC.md` y `DESIGN.md`, aplicado sobre el
stack técnico por defecto. Es lectura obligatoria antes de tocar backend,
base de datos, o cualquier endpoint. Debe incluir:

- **Gestión de secretos y variables de entorno**: toda clave, token, URL de
  servicio o credencial sensible vive en variables de entorno, nunca
  hardcodeada ni commiteada. Cada entorno (local, Vercel preview/producción,
  Railway) tiene su propio set — nunca se reusa una clave de producción en
  desarrollo salvo necesidad real y documentada. Se mantiene un
  `.env.example` con los nombres de las variables (no los valores). Ningún
  log, mensaje de error o respuesta de API imprime el valor de una variable
  sensible.
- **Modelo de registro: cerrado por defecto (invite-only)**: no hay alta
  pública de usuarios. En Supabase Auth, `Authentication → Providers →
  Email → "Allow new users to sign up"` queda **desactivado** — un intento
  de `supabase.auth.signUp()` desde el cliente falla. La única forma de
  crear una cuenta es vía la API de administración
  (`supabase.auth.admin.inviteUserByEmail()` o `.createUser()`), que
  requiere `service_role` y por lo tanto solo puede correr server-side. La
  primera cuenta (super-admin del proyecto) se crea a mano por el
  desarrollador — Dashboard de Supabase o script de una sola vez — como
  parte de "Setup del proyecto" en `TASKS.md`, antes de que exista pantalla
  de la app para eso. De ahí en más, cada cuenta nueva la crea el rol que
  `BUSINESS_LOGIC.md` designe con ese permiso, a través de un endpoint
  protegido que llama a la API de administración — nunca desde el cliente.
  Los permisos de la cuenta creada y el límite de cuántas puede crear cada
  rol (si aplica) son los que defina `BUSINESS_LOGIC.md` en su sección de
  Roles, y **el límite se valida server-side en el mismo endpoint que crea
  la cuenta** — nunca alcanza con ocultar el botón en la UI cuando se llega
  al tope, porque eso no impide llamar al endpoint directamente.
- **Separación de claves de Supabase por nivel de confianza**: la
  `service_role key` (bypassa RLS) nunca corre en el cliente ni se expone en
  el bundle de Next.js — solo en código server-side de confianza (Route
  Handlers/Server Actions, o los endpoints de Bun). La `anon key` es la única
  que llega al cliente, y solo es segura en conjunto con RLS bien definido.
  Si una función necesita `service_role`, se aísla en el mínimo código
  posible y se documenta por qué.
- **Row Level Security de Supabase — obligatorio por defecto**: toda tabla
  nueva se crea con RLS habilitado desde el mismo commit que la crea, con
  postura "deniega por defecto" (sin política, nadie accede salvo
  `service_role`). Las políticas se escriben explícitamente por rol/operación
  siguiendo "Reglas específicas por rol" de `BUSINESS_LOGIC.md`. Ninguna card
  que agregue una tabla se cierra sin sus políticas escritas y probadas.
- **Endpoints siempre protegidos, nunca expuestos por defecto**:
  - Todo endpoint (Route Handler/Server Action de Next, o endpoint de Bun en
    Railway) verifica sesión/autenticación server-side en cada request —
    nunca confía en un rol o permiso que venga del cliente sin validarlo
    contra Supabase.
  - Un endpoint es público solo si se decidió explícitamente que debe serlo
    (ej. webhook firmado). La postura por defecto es "requiere
    autenticación", no al revés.
  - CORS con lista explícita de orígenes permitidos — nunca `*` en un
    endpoint que acepte credenciales o modifique datos.
  - Los endpoints de Bun en Railway no dependen de "nadie conoce la URL"
    como protección — si no son de acceso público intencional, requieren su
    propio mecanismo de auth (verificación del JWT de Supabase, o un secreto
    compartido si el llamador es otro servicio y no un usuario).
- **Rate limiting — evaluado por endpoint, no aplicado en bloque**: no se
  agrega "por si acaso" a todo — se evalúa caso por caso al cerrar la card de
  cada endpoint, y la decisión (con o sin límite, y por qué) queda anotada en
  la nota de cierre en `TASKS.md`. Candidatos que casi siempre lo necesitan:
  autenticación (login, registro, recuperación de contraseña), subida de
  archivos u operaciones con costo, formularios públicos sin autenticación, y
  cualquier endpoint de Bun/Railway expuesto públicamente. Preferir límites
  en el borde (reglas de Cloudflare) para tráfico público masivo, y un
  limitador a nivel de aplicación para límites por usuario autenticado.
- **Subida de archivos a R2**: el cliente nunca recibe credenciales de larga
  duración de R2. La subida se hace vía **URL prefirmada de corta duración**,
  generada server-side (endpoint de Bun) después de validar tipo de archivo,
  tamaño máximo, y que el usuario tiene permiso para subir a esa ruta. La URL
  prefirmada se limita a la key/ruta exacta, nunca a todo el bucket.
- **Validación de entradas en cada límite de API**: todo Route Handler,
  Server Action y endpoint de Bun valida forma y tipo de sus inputs (ej. con
  `zod`) antes de usarlos. Esto es independiente de que Supabase ya prevenga
  inyección SQL vía queries parametrizadas — la validación es sobre la forma
  y las reglas de negocio del dato, no solo sobre inyección.
- **Manejo de errores sin fuga de información**: las respuestas de error en
  producción no incluyen stack traces, nombres de tablas/columnas ni detalles
  internos — mensaje genérico al cliente, detalle completo solo en logs del
  servidor.
- **Dominio, DNS y borde (Cloudflare)**: HTTPS forzado en todo
  dominio/subdominio. Si un endpoint de Bun expuesto vía Cloudflare necesita
  protección adicional, evaluar reglas de firewall/WAF de Cloudflare como
  capa extra, no como reemplazo de la autenticación de la aplicación.
- **Dependencias**: mantener el lockfile (Next/Bun) commiteado y revisar
  vulnerabilidades conocidas periódicamente — no es parte del ciclo de cada
  card, pero sí una revisión a agendar en `TASKS.md` si el proyecto es de
  vida larga.
- **Piso de seguridad no negociable (checklist)**: análogo al "piso de
  calidad" de `DESIGN.md` pero para seguridad — ej. RLS habilitado y
  probado, secretos en variables de entorno, CORS explícito, validación de
  input, sin `service_role` en el cliente. Se revisa al cerrar cualquier card
  que toque datos sensibles o autenticación.
- **Decisiones cerradas** vs **Pendiente de decidir**: igual que en
  `DESIGN.md` — separar qué ya se resolvió (ej. "sin MFA por ahora") de lo
  que sigue abierto.

### 4. `TASKS.md` — Tablero de tareas

Kanban en Markdown, es la única fuente de verdad del progreso (no uses un
tracker externo salvo que yo lo pida explícitamente). Estructura:

- **Convenciones**: `[ ]` pendiente / `[x]` hecho, prioridad con emoji
  (🔴 alta / 🟡 media / 🟢 baja), sub-checklist opcional al desglosar una card.
  Una card solo se marca `[x]` cuando pasó su prueba manual (ver más abajo)
  — no antes.
- **Backlog ordenado por dependencia, no por importancia de negocio**: las
  secciones del backlog se listan en el **orden real en que deben
  construirse**, empezando por los módulos primordiales de los que dependen
  los demás (ej. "Setup del proyecto" y autenticación van antes que cualquier
  entidad de negocio; una entidad base como "Zonas" va antes que "Pedidos" si
  los pedidos referencian zonas). Un módulo que consume datos o funcionalidad
  de otro nunca se agenda antes que su dependencia — así se evita construir
  algo a medias porque le falta una pieza de la que depende.
  - Cada sección de módulo empieza con una línea `Depende de: —` (o la lista
    de módulos previos que necesita) para que la dependencia quede explícita
    y no haya que inferirla leyendo todo el documento.
  - Cada card, además, debe estar desglosada en un tamaño **chico y
    probable de punta a punta por sí sola** (ver "Desarrollo en lotes
    chicos" más abajo) — si una card es demasiado grande para probarse
    manualmente en una sola sesión, se parte en sub-cards.
  - Si dos módulos no dependen entre sí, van en el orden que prefieras — la
    regla solo fuerza la secuencia cuando hay una dependencia real, no
    prioridad de negocio (un módulo "poco importante" pero sin el cual otro
    no puede construirse igual va primero).
  - La primera sección del backlog siempre es **Setup del proyecto**, donde
    se registra la configuración concreta del stack por defecto para este
    proyecto y cualquier desviación justificada. Incluye, como cards propias
    (desglosadas, no una sola card gigante):
    - Proyectos de Supabase separados para dev y prod (bucket de R2, dominio
      en Cloudflare, despliegues en Vercel/Railway).
    - Variables de entorno y `.env.example`; RLS habilitado por defecto en
      Supabase antes de crear la primera tabla de negocio; CORS configurado.
    - Migraciones de base de datos inicializadas (`supabase/migrations/`).
    - CI (GitHub Actions: lint, type-check, build).
    - Observabilidad (Sentry u equivalente) en Next/Vercel y en Bun/Railway
      si aplica.
    - Modelo de registro cerrado configurado (signup público desactivado en
      Supabase Auth) y la primera cuenta (super-admin) creada a mano.
    Es la base de la que todo lo demás depende. El upgrade a Pro de Supabase
    para activar backups automáticos en producción **no** va acá — es un
    gate aparte justo antes de lanzar con el primer cliente real (ver
    "Estándares de infraestructura y proceso por defecto").
- **En progreso**: lo que se está trabajando ahora mismo. En cualquier
  momento debería poder haber como máximo un módulo "primordial" abierto a
  la vez — no se arranca un módulo nuevo si el módulo del que depende sigue
  incompleto. Tampoco se abre una card nueva mientras la anterior esté
  esperando prueba manual.
- **Hecho**: cerradas, con una nota corta de qué se implementó y dónde
  (referencia a archivos/decisiones clave), no solo el check.

Cada card, al cerrarse, debe dejar una nota breve de la decisión tomada si
hubo alguna (ej. "→ con soft delete", "→ ver DESIGN.md §4") para que quede
trazable sin tener que bucear en el historial de git.

### 5. `CLAUDE.md` (o el archivo de instrucciones de tu agente de IA)

Instrucciones operativas para cualquier sesión de trabajo con IA en este
repo. Debe incluir:

- Que toda sesión debe leer `BUSINESS_LOGIC.md`, `DESIGN.md` y `SECURITY.md`
  **antes** de tocar código, diseño, backend o cualquier endpoint.
- Que el progreso se registra únicamente en `TASKS.md`, respetando el orden
  de dependencia del backlog — no se empieza un módulo si sus dependencias
  declaradas siguen sin cerrar en `TASKS.md`.
- Que el desarrollo es **modular e incremental**: cada módulo se construye,
  se prueba y se da por cerrado antes de empezar el siguiente que dependa de
  él. No se dejan varios módulos a medio terminar en paralelo cuando uno
  depende del otro.
- **Prohibido desarrollar en bloque**: bajo ningún concepto se arranca a
  construir "la aplicación completa" ni un módulo entero de una sola pasada.
  El trabajo siempre se segrega en la unidad más chica posible que se pueda
  probar de punta a punta (una card de `TASKS.md`, o menos si hace falta).
  Si una tarea que pido implica varios módulos o pantallas a la vez, tu
  trabajo es primero descomponerla en `TASKS.md` y proponerme el orden, no
  implementarla toda junta.
- **Prototipo antes de código en toda card de UI nueva**: si la card
  introduce una pantalla o un patrón de interacción que no existe todavía,
  el primer paso es un prototipo (artifact) con los tokens de `DESIGN.md`,
  no código de producción. Se implementa recién con mi aprobación del
  prototipo, y al cerrar la card se agrega la entrada correspondiente en la
  bitácora de "Pantallas y patrones" de `DESIGN.md`.
- **Decisiones de peso arquitectónico no se asumen**: si al implementar una
  card aparece una decisión no cubierta explícitamente en `BUSINESS_LOGIC.md`
  (ej. estrategia de borrado de una entidad, qué pasa con sus referencias,
  cascadas, concurrencia), se detiene esa card, se me pregunta directamente,
  y la respuesta se documenta en `BUSINESS_LOGIC.md` antes de programar la
  solución — nunca se elige en silencio la opción más simple de codear.
- **Toda tabla nueva de Supabase lleva RLS desde el mismo commit**: sin
  política escrita y probada para esa tabla, la card no se considera cerrada.
  Nunca se deja una tabla "para agregarle RLS después".
- **Todo endpoint nuevo declara explícitamente su postura de rate limit al
  cerrar la card**: con límite (y qué límite) o sin límite (y por qué no) —
  ver criterios en `SECURITY.md`. No se agrega rate limit reflexivamente a
  todo, pero tampoco se deja sin evaluar.
- **Ningún secreto, clave o URL de servicio se escribe en código o se
  commitea**: siempre en variables de entorno, con `.env.example` actualizado
  cuando se agrega una variable nueva.
- **Prueba manual obligatoria y bloqueante**: ninguna card se marca como
  hecha sin que yo la haya probado manualmente en la app real (no alcanza
  con que el código compile, pase lint o pasen tests automáticos). Al
  terminar de implementar una card, el siguiente paso es siempre pedirme que
  la pruebe y esperar mi confirmación antes de seguir con la próxima —
  nunca se sigue encadenando trabajo nuevo sobre una card sin confirmar.
- **Prueba automática como complemento, no como reemplazo**: cuando
  corresponda, usá una sesión de Claude en el navegador (herramienta de
  automatización de browser) para recorrer el flujo antes de pasármelo a mí,
  como primer filtro. Esto ayuda a detectar errores obvios antes, pero no
  sustituye mi prueba manual — la card sigue sin poder cerrarse hasta que yo
  la confirme.
- Convenciones de commits (mensajes descriptivos, commits chicos y
  frecuentes, sin mezclar features no relacionadas).
- Reglas de trabajo específicas que vayamos acordando sobre la marcha
  (qué evitar, qué repetir) — este archivo se actualiza vivo durante todo el
  proyecto, no solo al inicio.
- Puntero a documentos adicionales, incluido `SECURITY.md`, y a cualquier
  otro que se agregue más adelante (ej. un mapa de código, un documento de
  decisiones de arquitectura, etc.).

### 6. `README.md`

Mínimo y para humanos: una o dos líneas de qué es el proyecto y un índice con
links a `BUSINESS_LOGIC.md`, `DESIGN.md`, `SECURITY.md` y `TASKS.md`. No
dupliques contenido acá — es un punto de entrada, no un resumen.

## Herramientas/metodología de desarrollo a aplicar durante todo el proyecto

Estas son prácticas de proceso, aplicables sobre el stack técnico por
defecto (o el que se haya decidido usar en su lugar, si el proyecto se desvió
con justificación):

- **Documentación viva**: `BUSINESS_LOGIC.md`, `DESIGN.md` y `SECURITY.md` se
  actualizan cada vez que una decisión de negocio, diseño o seguridad cambia
  — no quedan congelados en la v1.
- **Implementación modular por dependencia**: antes de escribir código de un
  módulo, verificá contra `TASKS.md` que todos los módulos de los que
  depende ya están cerrados. Si un módulo nuevo requiere algo de otro que
  todavía no existe, el trabajo real es primero completar (o al menos dejar
  utilizable) esa dependencia — no mockearla ni improvisarla "por ahora"
  salvo que yo lo pida explícitamente. Esto evita construir dos veces lo
  mismo y evita features a medias por depender de piezas inexistentes.
- **Desarrollo en lotes chicos, nunca en bloque**: no se implementa la
  aplicación completa, ni un módulo entero, de una sola vez. Cada ciclo de
  trabajo entrega la unidad más pequeña que tenga sentido probar por sí
  sola. Si una petición mía implica varias piezas, primero se descompone en
  `TASKS.md` en cards chicas y ordenadas por dependencia, y se implementa y
  prueba una por una — nunca todas juntas.
- **Prueba manual siempre, prueba automática como apoyo**: cada lote chico
  se prueba de dos formas — (1) de forma manual por mí, en la app real, algo
  **indispensable y no reemplazable**; y (2) opcionalmente antes, de forma
  automática, con una sesión de Claude operando el navegador para recorrer
  el flujo como primer filtro. El segundo nunca sustituye al primero. Una
  card no avanza a "Hecho" en `TASKS.md` hasta tener mi confirmación manual.
- **Kanban en Markdown** (`TASKS.md`) como única fuente de progreso, sin
  herramientas externas de gestión salvo pedido explícito.
- **Prototipado visual antes de implementar UI — por pantalla, no solo al
  inicio**: esto no se limita a elegir la dirección visual general al
  arrancar `DESIGN.md`. Toda card de `TASKS.md` que introduzca una pantalla
  nueva o un patrón de interacción nuevo (un tipo de menú, un header
  reusable, una navegación) debe empezar con un prototipo (artifact
  navegable) construido sobre los tokens ya definidos en `DESIGN.md`,
  mostrado para aprobación, **antes** de escribir el código de producción de
  esa pantalla. Recién con la aprobación se implementa. Esto reemplaza el
  patrón de "proponer un estilo general al día 1 y de ahí construir todas
  las pantallas directo en código" — que en la práctica termina generando
  pantallas y patrones (menús, headers, navegación) que nunca quedan
  documentados como decisión de diseño, solo como código.
- **Decisiones con peso arquitectónico se preguntan, no se asumen**: si
  durante la implementación de una card aparece una decisión que no está ya
  resuelta explícitamente en `BUSINESS_LOGIC.md` o `DESIGN.md` y que sería
  cara de revertir después (ejemplos: soft delete vs hard delete, qué pasa
  con referencias al borrar algo, cascadas, migraciones de datos,
  concurrencia, particionamiento de datos por fecha/período) — no se elige
  la opción más simple de programar en el momento. Se detiene el trabajo de
  esa card, se pregunta explícitamente, y la respuesta se documenta en el
  documento correspondiente (`BUSINESS_LOGIC.md` para reglas de negocio,
  `DESIGN.md` para patrones de interacción) antes de escribir el código que
  depende de esa decisión.
- **Los documentos de intención son un espejo del estado real, no una
  bitácora de un solo sentido**: `TASKS.md` registra *qué se hizo y cuándo*,
  pero `BUSINESS_LOGIC.md` y `DESIGN.md` tienen que seguir describiendo con
  precisión *cómo es la app hoy*. Si una card cambia o agrega algo que esos
  documentos describen (una regla de negocio, una decisión de layout, un
  patrón de UI), **cerrar esa card incluye actualizar el documento
  correspondiente** — no es un paso aparte, opcional, ni algo que se resuelve
  después con una retrospectiva. Si al cerrar una card no queda claro si algo
  debería actualizarse en `BUSINESS_LOGIC.md`/`DESIGN.md`, esa duda se
  resuelve antes de marcar `[x]`, no se pospone.
- **Sistema de tokens de diseño**: toda decisión de color/tipografía/espacio
  se traduce a variables reutilizables, nunca valores sueltos en componentes.
- **Seguridad por defecto, no como capa posterior**: endpoints protegidos y
  RLS habilitado son parte de la implementación de cada módulo, no una pasada
  de "hardening" al final del proyecto. Ver `SECURITY.md` para el detalle
  completo; en resumen — secretos siempre en variables de entorno, nunca en
  código; toda tabla de Supabase con RLS desde que se crea; todo endpoint
  autenticado por defecto salvo que se decida explícitamente que es público;
  rate limit evaluado (no asumido ni ignorado) por cada endpoint nuevo; y
  archivos subidos a R2 siempre vía URL prefirmada generada server-side,
  nunca con credenciales largas en el cliente.
- **Mapa de código navegable** (si la herramienta de desarrollo lo soporta,
  ej. CodeGraph u otra indexación estructural): usarlo para explorar y
  entender el código existente antes de recurrir a búsqueda de texto plano,
  y mantener un documento puntero (ej. `CODEGRAPH.md`) si se genera una
  visualización, aclarando que es una foto fija que hay que regenerar.
- **Memoria persistente entre sesiones**: si la herramienta de IA soporta
  memoria/notas persistentes, usarla para decisiones de contexto que no
  pertenecen al código ni a los documentos de negocio/diseño (preferencias de
  forma de trabajar, contexto de por qué se tomó una decisión no obvia).
- **Commits pequeños y trazables**, uno por unidad de trabajo coherente —
  alineados 1 a 1 con el lote/card que se acaba de probar y cerrar.

## Cómo quiero que arranques

1. Hacéme las preguntas necesarias para poder escribir `BUSINESS_LOGIC.md`
   (contexto del negocio, usuarios, roles, flujo principal). Agrupalas por
   tema.
2. Con mis respuestas, redactá un borrador de `BUSINESS_LOGIC.md` completo y
   mostrámelo para revisión.
3. Una vez aprobado, pasá a `DESIGN.md`: proponé direcciones visuales,
   ayudame a elegir, y redactá el documento completo (recordá: esto fija el
   sistema — tokens y dirección — no todas las pantallas).
4. Con `BUSINESS_LOGIC.md` aprobado, redactá `SECURITY.md` aplicando el
   stack técnico por defecto (Supabase/Next/Bun/R2/Cloudflare) a las
   entidades, roles y flujos ya definidos — en particular, qué tablas van a
   necesitar qué políticas de RLS y qué endpoints van a necesitar evaluación
   de rate limit. Mostrámelo para revisión igual que los anteriores.
5. Con los tres aprobados, generá `TASKS.md`:
   a. Primero identificá los módulos/entidades del dominio a partir de
      `BUSINESS_LOGIC.md` y mapeá sus dependencias entre sí (qué módulo
      necesita que otro exista primero).
   b. Ordená las secciones del backlog siguiendo ese mapa de dependencias,
      de lo más primordial (setup, auth, entidades base sin dependencias) a
      lo más derivado (features que consumen varias entidades a la vez).
   c. Desglosá cada módulo en cards chicas, probables de punta a punta cada
      una — nada de cards que equivalgan a "construir todo el módulo".
   d. "Setup del proyecto" va primero siempre, desglosado en las cards que
      ya detalla la sección "### 4. `TASKS.md`" más arriba (proyectos de
      Supabase separados por entorno, variables de entorno, migraciones,
      CI, observabilidad, modelo de registro cerrado con la primera cuenta
      creada a mano).
6. Generá `CLAUDE.md` y `README.md`.
7. Recién ahí, esperá mi confirmación para empezar a tocar código. A partir
   de ahí, el ciclo de trabajo por cada card es siempre:
   a. Si la card introduce una pantalla o un patrón de interacción nuevo:
      primero un prototipo (artifact) sobre los tokens de `DESIGN.md`,
      mostrármelo y esperar mi aprobación antes de escribir código de
      producción.
   b. Si la card requiere una decisión con peso arquitectónico que
      `BUSINESS_LOGIC.md` no resuelve explícitamente (estrategia de borrado,
      cascadas, concurrencia, etc.): preguntármela directamente y esperar mi
      respuesta antes de programar — nunca asumir la opción más simple.
   c. Si la card crea una tabla nueva en Supabase o expone un endpoint
      nuevo: escribir y probar las políticas de RLS de esa tabla antes de
      darla por lista, y decidir explícitamente (según `SECURITY.md`) si el
      endpoint necesita rate limit — ninguna de las dos cosas se posterga.
   d. Implementar únicamente esa card (nunca varias a la vez, nunca el
      módulo completo).
   e. Si aplica, recorrer el flujo con una sesión de Claude en el navegador
      como primer filtro automático.
   f. Pedirme la prueba manual y **detenerte ahí** — no seguir con la
      próxima card ni marcar esta como hecha hasta que yo confirme que la
      probé y funciona.
   g. Recién con mi confirmación: (i) marcar la card `[x]` en `TASKS.md` con
      su nota; (ii) si la card cambió o agregó algo que `BUSINESS_LOGIC.md`,
      `DESIGN.md` o `SECURITY.md` describen, actualizar ese documento en el
      mismo momento (incluida la bitácora de "Pantallas y patrones" si
      corresponde) — no queda pendiente para después; (iii) pasar a la
      siguiente card respetando el orden de dependencias.