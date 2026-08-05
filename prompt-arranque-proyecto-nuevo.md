# Prompt de arranque — Proyecto nuevo (documentación primero)

Vamos a arrancar un proyecto nuevo. Antes de escribir una sola línea de código,
quiero que construyamos primero la documentación base que va a gobernar todo
el desarrollo. Esta es la metodología: **documentar el qué y el cómo antes del
con qué**. El stack técnico (lenguaje, framework, base de datos, servicios
externos) se decide después, como una tarea más dentro de `TASKS.md` — no es
parte de esta fase.

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
4. No proponer ni fijar stack técnico, base de datos ni servicios externos en
   esta fase — esos van después, como tareas en `TASKS.md`.

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
- **Roles**: tabla de roles y qué puede ver/hacer cada uno.
- **Flujos principales**: paso a paso de cada flujo core (alta de usuario,
  flujo transaccional principal, aprobaciones, etc.).
- **Entidades del dominio**: cada entidad con sus campos relevantes y reglas
  de negocio asociadas (no es un modelo de datos técnico, es conceptual).
- **Estados y transiciones**: para cada entidad que tenga ciclo de vida
  (pedido, solicitud, ticket, etc.), estados posibles y qué dispara cada
  transición.
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

### 3. `TASKS.md` — Tablero de tareas

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
    recién ahí se decide y registra el stack técnico, la base de datos y los
    servicios externos necesarios — es la base de la que todo lo demás
    depende.
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

### 4. `CLAUDE.md` (o el archivo de instrucciones de tu agente de IA)

Instrucciones operativas para cualquier sesión de trabajo con IA en este
repo. Debe incluir:

- Que toda sesión debe leer `BUSINESS_LOGIC.md` y `DESIGN.md` **antes** de
  tocar código o diseño.
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
- Puntero a documentos adicionales si se agregan más adelante (ej. un mapa
  de código, un documento de decisiones de arquitectura, etc.).

### 5. `README.md`

Mínimo y para humanos: una o dos líneas de qué es el proyecto y un índice con
links a `BUSINESS_LOGIC.md`, `DESIGN.md` y `TASKS.md`. No dupliques contenido
acá — es un punto de entrada, no un resumen.

## Herramientas/metodología de desarrollo a aplicar durante todo el proyecto

Estas son prácticas de proceso, independientes del stack técnico que se elija
más adelante:

- **Documentación viva**: `BUSINESS_LOGIC.md` y `DESIGN.md` se actualizan
  cada vez que una decisión de negocio o de diseño cambia — no quedan
  congelados en la v1.
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
- **Prototipado visual antes de implementar UI**: cuando haya que decidir
  entre varias direcciones visuales o layouts, generar un explorador
  interactivo (artifact/prototipo navegable) para decidir antes de construir
  la UI real, en vez de iterar directamente sobre el código de producción.
- **Sistema de tokens de diseño**: toda decisión de color/tipografía/espacio
  se traduce a variables reutilizables, nunca valores sueltos en componentes.
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
   ayudame a elegir, y redactá el documento completo.
4. Con ambos aprobados, generá `TASKS.md`:
   a. Primero identificá los módulos/entidades del dominio a partir de
      `BUSINESS_LOGIC.md` y mapeá sus dependencias entre sí (qué módulo
      necesita que otro exista primero).
   b. Ordená las secciones del backlog siguiendo ese mapa de dependencias,
      de lo más primordial (setup, auth, entidades base sin dependencias) a
      lo más derivado (features que consumen varias entidades a la vez).
   c. Desglosá cada módulo en cards chicas, probables de punta a punta cada
      una — nada de cards que equivalgan a "construir todo el módulo".
   d. "Setup del proyecto" va primero siempre — ahí es donde recién se
      conversa stack técnico, base de datos y servicios externos.
5. Generá `CLAUDE.md` y `README.md`.
6. Recién ahí, esperá mi confirmación para empezar a tocar código. A partir
   de ahí, el ciclo de trabajo por cada card es siempre:
   a. Implementar únicamente esa card (nunca varias a la vez, nunca el
      módulo completo).
   b. Si aplica, recorrer el flujo con una sesión de Claude en el navegador
      como primer filtro automático.
   c. Pedirme la prueba manual y **detenerte ahí** — no seguir con la
      próxima card ni marcar esta como hecha hasta que yo confirme que la
      probé y funciona.
   d. Recién con mi confirmación, marcar la card `[x]` en `TASKS.md` con su
      nota, y pasar a la siguiente card respetando el orden de dependencias.
