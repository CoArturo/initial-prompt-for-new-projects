# Documentación primero

Un prompt de arranque para empezar proyectos nuevos con un agente de IA, y la guía que
explica por qué está escrito así.

La idea que lo sostiene cabe en una frase: **documentar el qué y el cómo antes del con
qué**. Antes de la primera línea de código se escribe un conjunto pequeño de documentos
que el agente lee al inicio de cada sesión y que funcionan como memoria, contrato y freno.

No es un framework ni una metodología con nombre propio. Es un punto de partida que puedes
copiar, recortar y adaptar a tu forma de trabajar.

---

## Contenido del repositorio

| Archivo | Qué es |
|---|---|
| [`prompt-arranque-proyecto-nuevo.md`](prompt-arranque-proyecto-nuevo.md) | El prompt completo. Es lo que copias y pegas como primer mensaje en tu agente. |
| `README.md` | Este documento: la explicación de cada pieza del prompt, por qué existe y cuándo puedes saltártela. |

Si tienes prisa, ve directo al prompt y salta el resto. Esta guía es para cuando quieras
entender las decisiones detrás de cada regla, o adaptarlas.

## Índice

1. [El problema que resuelve](#1-el-problema-que-resuelve)
2. [Cómo usarlo, en cinco pasos](#2-cómo-usarlo-en-cinco-pasos)
3. [Cómo leer esta guía](#3-cómo-leer-esta-guía)
4. [El rol que se le asigna al agente](#4-el-rol-que-se-le-asigna-al-agente)
5. [El stack por defecto](#5-el-stack-por-defecto)
6. [Estándares de infraestructura y proceso](#6-estándares-de-infraestructura-y-proceso)
7. [Los seis documentos, en orden](#7-los-seis-documentos-en-orden)
8. [El ciclo de trabajo](#8-el-ciclo-de-trabajo)
9. [Ajuste por envergadura](#9-ajuste-por-envergadura)
10. [Límites y advertencias](#10-límites-y-advertencias)
11. [Adaptarlo a otra herramienta o a otro stack](#11-adaptarlo-a-otra-herramienta-o-a-otro-stack)

---

## 1. El problema que resuelve

El modo por defecto de trabajar con un agente es pedirle una aplicación y mirar cómo
aparece. Funciona sorprendentemente bien durante unas horas. Después empieza el segundo
acto: nadie recuerda por qué una entidad se borra en cascada, hay tres tonos de azul
distintos porque cada pantalla eligió el suyo, una tabla quedó sin políticas de acceso
«para agregarlas después», y la única persona que sabía cómo debía funcionar el negocio se
lo explicó al modelo en un chat que ya se perdió.

La causa no es la calidad del código generado. Es que el agente **necesita contexto que no
tiene y, si falta, lo inventa**. Y lo inventa eligiendo, casi siempre, la opción más simple
de programar en ese momento: borrado físico en lugar de lógico, endpoint público en lugar
de autenticado, un color escrito a mano en lugar de un token. Ninguna de esas decisiones se
anuncia. Todas son caras de revertir tres semanas después.

Este prompt ataca exactamente eso. Los documentos no son burocracia: son el contexto que al
agente le falta, escrito una vez y disponible en cada sesión.

## 2. Cómo usarlo, en cinco pasos

1. **Crea un repositorio vacío** y abre tu agente de IA dentro de él. El repositorio va
   primero: los documentos tienen que nacer versionados.
2. **Pega [el prompt](prompt-arranque-proyecto-nuevo.md) como primer mensaje.** Si tu
   proyecto se desvía del stack por defecto, decláralo en ese mismo mensaje y pide que la
   desviación quede registrada.
3. **Contesta las preguntas.** Sin prisa y sin resolver todo: lo que no sepas va a
   «Preguntas abiertas» y sigues adelante.
4. **Aprueba los documentos uno por uno**, leyéndolos de verdad. Es la última oportunidad
   barata de descubrir que entendiste mal tu propio problema.
5. **Empieza por la primera card de _Setup del proyecto_.** Vas a tardar más de lo que
   esperabas en llegar a la primera pantalla, y eso es exactamente lo que se busca.

La primera vez da la sensación de estar frenando algo que quería correr. Esa sensación es
correcta, y es el punto: con un agente, la velocidad nunca fue el cuello de botella. Lo
escaso es el criterio, y el criterio hay que escribirlo antes de que empiece a hacer falta.

## 3. Cómo leer esta guía

Cada pieza del prompt se explica con la misma estructura —qué pide, por qué existe— y luego
una o más de estas cuatro anotaciones:

> **Intención** — El motivo real detrás de la regla. Casi siempre es un error concreto que
> alguien ya cometió.

> **Se puede saltar si…** — Condiciones bajo las cuales esa parte resulta desproporcionada
> para el tamaño de tu proyecto.

> **Sustituciones razonables** — Tecnologías equivalentes. El prompt fija un stack, pero
> casi todo es intercambiable si entiendes qué rol cumple cada pieza.

> **No negociable** — Reglas que conviene no saltarse ni en un proyecto de fin de semana,
> porque el costo de omitirlas es asimétrico.

---

## 4. El rol que se le asigna al agente

Lo primero que hace el prompt es reasignar el rol: _«actúa como product lead + arquitecto
técnico; tu trabajo en esta fase NO es programar»_. Y le da cuatro instrucciones:

1. Hacer las preguntas necesarias para entender el negocio, el usuario y el problema,
   **agrupadas por tema**.
2. Redactar los documentos uno por uno, mostrándolos para aprobación.
3. No avanzar al siguiente documento sin confirmación explícita; lo que quede sin resolver
   va a una sección «Preguntas abiertas».
4. No re-decidir el stack técnico desde cero.

> **Intención**
>
> Un agente entrenado para ser útil interpreta el silencio como permiso. Si no sabe si el
> borrado es lógico o físico, elige. Este bloque invierte esa asimetría: durante toda la
> fase de arranque, la unidad de trabajo del agente es _la pregunta_, no el entregable.
>
> Los dos detalles pequeños hacen la mayor parte del trabajo. **«Agrupadas por tema»** evita
> el ping-pong de una pregunta por turno, que agota a cualquiera y hace que termines
> contestando «lo que te parezca». Y **«Preguntas abiertas»** es una válvula de escape: sin
> ella, una sola indecisión bloquea el documento entero.

La señal de que esto funciona es incómoda al principio: pediste un proyecto y te devolvió un
cuestionario. Si en cambio empieza a escribir archivos, el prompt no se aplicó y conviene
insistir.

> **Se puede saltar si…** — Nunca, honestamente. Es la parte más barata del proceso y la de
> mayor retorno. Incluso para un experimento de una tarde, diez minutos de preguntas cambian
> el resultado. Lo que sí escala hacia abajo es la _cantidad_ de preguntas: para algo
> pequeño, un bloque de cinco alcanza.

## 5. El stack por defecto

El prompt fija el stack de antemano para no volver a discutirlo en cada proyecto:

| Capa | Por defecto | Sustituciones razonables |
|---|---|---|
| Frontend y despliegue | Next.js · Vercel | Remix, SvelteKit, Nuxt · Netlify, Cloudflare Pages |
| Base de datos, auth y backend | Supabase (Postgres + RLS + Auth) | Postgres administrado + tu capa de auth; Firebase si aceptas el modelo NoSQL |
| Servicio aparte, cuando hace falta | Bun · Railway | Node o Deno · Fly.io, Render, Cloud Run — o ninguno |
| Almacenamiento de archivos | Cloudflare R2 | S3, Backblaze B2, Supabase Storage |
| Dominio y DNS | Cloudflare | Cualquier proveedor con HTTPS forzado |

Dos matices que suelen pasarse por alto:

- El servicio aparte **no se crea «por si acaso»**. Solo cuando hay una necesidad concreta
  que Next y Supabase no resuelven directamente.
- Elegir el stack y **configurarlo** son cosas distintas. Los nombres de proyecto, buckets,
  dominios y variables de entorno no se deciden aquí: se registran después, como cards del
  módulo _Setup del proyecto_ en `TASKS.md`.

> **Intención** — Cada decisión de stack que se reabre en un proyecto nuevo es tiempo gastado
> en un problema que ya resolviste. Congelarlo también le da al agente un terreno conocido:
> sabe qué patrones aplicar sin inventarlos.

> **Sustituciones razonables** — Todo el stack es intercambiable si entiendes qué rol cumple
> cada pieza. Lo que **no** conviene sustituir a la ligera es la propiedad de la que dependen
> las reglas de seguridad más adelante: una base de datos con permisos a nivel de fila (como
> RLS) y una separación clara entre claves de cliente y claves de servidor. Si tu alternativa
> no ofrece eso, hay que reescribir `SECURITY.md`, no solo cambiar nombres.

La regla que salva a esto de convertirse en dogma es la de desviación: puedes cambiar
cualquier pieza, pero la desviación **se declara con su motivo** en el documento de tareas.
Lo prohibido no es desviarse; es desviarse en silencio a mitad de una tarea.

## 6. Estándares de infraestructura y proceso

Seis decisiones que también vienen dadas, para no re-evaluarlas proyecto por proyecto.

### Entornos separados por proyecto, no por rama

Desarrollo y producción son dos proyectos de Supabase distintos. Alcanza con el plan Free,
que permite dos proyectos activos. El _branching_ de Supabase es una función de pago y queda
como mejora opcional, no como base.

### Migraciones de base de datos versionadas

Todo cambio de esquema —tablas, columnas, políticas— se escribe como archivo SQL en
`supabase/migrations/` y se aplica con la CLI. Nunca un cambio hecho a mano en el panel sin
rastro en git.

> **Intención** — Es lo que permite aplicar el mismo esquema a dos entornos sin repetirlo a
> mano. Y encaja con la regla de que toda tabla nace con sus políticas de acceso: esa política
> vive en el mismo archivo de migración que crea la tabla.

> **No negociable** — En cuanto exista un entorno de producción con datos reales. Sin
> migraciones versionadas, dev y prod divergen en silencio y te enteras en el peor momento.

### Integración continua en cada push

Un pipeline que corre lint, type-check y build. No reemplaza la prueba manual: la complementa
como primera red para errores mecánicos —typos, tipos, imports rotos— que de otro modo
dependen de que alguien los note.

> **Se puede saltar si…** — El proyecto es de una sola persona y de vida corta. En cuanto hay
> una segunda persona, o el proyecto pasa de unas semanas, el costo de configurarlo se paga
> solo.

### Observabilidad desde el día uno

Sentry o equivalente, configurado en el frontend y en cualquier servicio aparte, como parte
del setup inicial y no como una pasada posterior.

> **Se puede saltar si…** — Todavía no hay usuarios además de ti. En cuanto exista una segunda
> persona usando la app, actívalo: la alternativa es enterarte de que algo se rompió cuando
> alguien se queja, y sin rastro de qué pasó.

### Backups antes del primer cliente real

El plan gratuito de Supabase no incluye backups automáticos. Antes de que el proyecto de
producción tenga su primer usuario real, el upgrade a Pro es obligatorio: activa backups
diarios sin configuración adicional. El proyecto de desarrollo puede quedarse en Free
indefinidamente, porque no tiene datos que perder.

> **No negociable** — Es la única regla del prompt con forma de compuerta: no es «cuando
> puedas», es antes de que exista el primer dato que no sea tuyo.

### Contrato compartido entre servicios

Cuando el proyecto tiene un servicio aparte, los esquemas de validación se comparten entre
ambos lados en una carpeta común, nunca definidos dos veces.

> **Se puede saltar si…** — No tienes un segundo servicio, que es el caso más común. Aplica
> únicamente cuando existen dos runtimes que hablan entre sí.

---

## 7. Los seis documentos, en orden

El orden importa: cada documento se apoya en el anterior. La seguridad no se puede escribir
sin conocer los roles; las tareas no se pueden ordenar sin conocer las entidades.

### 1. `BUSINESS_LOGIC.md` — la lógica de negocio

El documento de referencia del dominio, y lo primero que se lee en cualquier sesión. Incluye
contexto del negocio, objetivo por tipo de usuario, público y restricciones de diseño, tabla
de roles, flujos principales, entidades con sus reglas, estados y transiciones, reglas por
rol, notificaciones, vistas por rol, un apartado explícito de **fuera de alcance** y las
preguntas abiertas.

> **Intención** — Es el sustituto escrito de todo lo que hoy vive únicamente en tu cabeza. Un
> agente sin este documento no distingue entre una regla de negocio y una preferencia tuya,
> así que trata todo como preferencia y lo cambia sin avisar.

Dos secciones justifican, por sí solas, el proceso entero.

**Estrategia de baja por entidad.** Para cada entidad que se pueda dar de baja se define de
antemano: borrado lógico o físico, qué pasa con lo que la referencia (bloquear, cascada o
desasociar) y quién puede hacerlo. Por defecto se prefiere el borrado lógico, y la razón para
elegir lo contrario queda anotada.

> **Intención** — Es la decisión que un agente toma peor cuando se la dejas. Sin una regla,
> elige lo más simple de programar, que es el borrado físico. Una tabla con borrado físico no
> puede recuperar histórico de forma retroactiva: cuando alguien pregunta «¿qué pasó con el
> pedido que se canceló en marzo?», la respuesta ya no existe.

**Fuera de alcance explícito.** La lista de lo que este proyecto decidió no hacer.

> **Intención** — Un agente al que le pides «un sistema de reservas» va a proponer
> notificaciones, panel de métricas y multi-idioma, porque parecen razonables. Escribir lo que
> queda afuera es más barato que discutirlo cada vez, y te obliga a decidirlo una sola vez.

> **Se puede saltar si…** — El proyecto es un experimento personal. Aun así, media página con
> las entidades, los roles y el fuera de alcance cambia el resultado por completo.

### 2. `DESIGN.md` — esquema de diseño e intención

Si el anterior define **qué hace** la app, este define **cómo se ve y por qué**: intención del
diseño conectada con una verdad del negocio, principios propios del proyecto, dirección
visual, paleta en modo claro y oscuro, escala de forma y espacio, tipografía, layout por
tamaño de pantalla, voz y microcopy, y un piso de accesibilidad no negociable.

Tres ideas cargan con el peso:

**Los tokens son obligatorios.** Ningún color escrito en crudo dentro de un componente. Todo
pasa por variables semánticas, separando tokens de marca de tokens de estado.

**El modo oscuro se recalibra a mano.** No es una inversión automática del claro.

**La bitácora de pantallas.** Una tabla que se llena a medida que se construye cada pantalla o
patrón reusable —un menú de acciones, un encabezado estándar, una navegación—, no de una sola
vez al principio. Cada entrada anota la pantalla, el módulo al que pertenece, la decisión
visual tomada y la fecha.

> **Intención** — Esto corrige el error más común de los documentos de diseño: intentar
> anticipar todas las pantallas el día uno. Ese documento queda desactualizado apenas empieza
> la implementación real. El día uno se fija el _sistema_; cada pantalla se diseña cuando le
> toca, y su decisión queda registrada en el momento en que se toma.

De ahí sale la regla que más fricción quita después: toda card que introduzca una pantalla o
un patrón nuevo empieza con un **prototipo navegable** construido sobre los tokens ya
definidos, aprobado antes de escribir código de producción.

> **Se puede saltar si…** — Es una herramienta interna sin cara visible, o un proyecto de una
> sola pantalla. Lo que no se salta en ningún tamaño son los tokens: el costo de agregarlos
> después es reescribir todos los componentes.

### 3. `SECURITY.md` — estándares de seguridad

Lectura obligatoria antes de tocar backend, base de datos o cualquier endpoint. Es el
documento más prescriptivo del prompt, y el que más se apoya en el stack elegido.

**Registro cerrado por defecto.** No hay alta pública de usuarios. El signup público queda
desactivado en el panel de Supabase; la única forma de crear una cuenta es la API de
administración, que requiere la clave de servicio y por lo tanto solo corre del lado del
servidor. La primera cuenta la crea el desarrollador a mano, como parte del setup.

> **Intención** — Es una postura, no una recomendación universal: el prompt está pensado para
> aplicaciones de negocio con usuarios conocidos. Si construyes un producto con registro
> autoservicio, esta sección se reescribe entera, y conviene hacerlo a conciencia en lugar de
> borrarla.

Si un rol puede crear cuentas de otro rol, `BUSINESS_LOGIC.md` define qué permisos exactos
recibe la cuenta creada y cuántas puede crear. El límite **se valida en el servidor**, en el
mismo endpoint que crea la cuenta.

> **Intención** — Ocultar el botón en la interfaz cuando se llega al tope no impide llamar al
> endpoint directamente. Es un patrón que se repite en todo el documento: la interfaz nunca es
> el lugar donde se aplica una regla.

El resto del piso mínimo:

- **Separación de claves por nivel de confianza.** La clave de servicio ignora las políticas
  de acceso y nunca llega al cliente ni al bundle. La clave pública es la única que viaja al
  navegador, y solo es segura junto con RLS bien definido.
- **RLS obligatorio.** Toda tabla nueva nace con RLS activado desde el mismo commit que la
  crea, con postura de denegar por defecto. Ninguna card que agregue una tabla se cierra sin
  sus políticas escritas y probadas.
- **Endpoints autenticados por defecto.** Uno es público solo si se decidió explícitamente que
  debe serlo. CORS con lista explícita de orígenes, nunca `*` en algo que acepte credenciales
  o modifique datos. Un endpoint en un servicio aparte no se protege con «nadie conoce la
  URL».
- **Rate limit evaluado por endpoint**, no aplicado en bloque ni ignorado. La decisión —con
  límite y cuál, o sin límite y por qué— queda anotada al cerrar la card. Candidatos casi
  seguros: login, recuperación de contraseña, subidas de archivos y formularios públicos.
- **Subida de archivos con URL prefirmada de corta duración**, generada del lado del servidor
  después de validar tipo, tamaño y permiso, y limitada a la ruta exacta. El cliente nunca
  recibe credenciales de larga duración.
- **Validación de entradas en cada límite de API**, sobre la forma y las reglas del dato, no
  solo contra inyección.
- **Errores sin fuga de información**: mensaje genérico al cliente, detalle completo en los
  logs del servidor.

> **No negociable** — Secretos en variables de entorno, RLS en toda tabla, endpoints
> autenticados por defecto y la clave de servicio fuera del cliente. Son cuatro reglas que no
> cuestan tiempo si se aplican desde el principio, y que salen caras y en silencio cuando se
> omiten.

> **Se puede saltar si…** — En un MVP interno puedes diferir el rate limiting y el detalle de
> las políticas por rol. No difieras nunca activar RLS: una tabla creada sin RLS queda abierta
> hasta que alguien se acuerde, y nadie se acuerda.

### 4. `TASKS.md` — tablero de tareas

Un kanban en Markdown, única fuente de verdad del progreso, sin herramientas externas salvo
pedido explícito. Casillas de pendiente y hecho, prioridad con emoji, y tres secciones:
backlog, en progreso y hecho.

Tiene una idea central que es, probablemente, la más subestimada del prompt: **el backlog se
ordena por dependencia, no por importancia de negocio**. Las secciones se listan en el orden
real en que deben construirse. Un módulo que consume datos de otro nunca se agenda antes que
su dependencia, aunque sea «más importante». Cada sección arranca con una línea `Depende de:`
explícita, para no tener que inferirlo leyendo todo.

> **Intención** — Un agente construye sin quejarse una funcionalidad que depende de algo que
> todavía no existe: la simula, la mockea, deja un `TODO` y sigue. El resultado son cinco
> funcionalidades al 80% y ninguna probable de punta a punta. El orden por dependencia hace que
> eso sea estructuralmente imposible.

La segunda regla: cada card debe ser **pequeña y probable de punta a punta por sí sola**. Si
no se puede probar manualmente en una sola sesión, se parte. Y la primera sección del backlog
es siempre _Setup del proyecto_, desglosada en cards propias: los dos proyectos de Supabase,
las variables de entorno, las migraciones inicializadas, el pipeline de CI, la observabilidad,
el registro cerrado configurado y la primera cuenta creada a mano.

Al cerrarse, cada card deja una nota corta de qué se implementó y qué se decidió —«con borrado
lógico», «ver DESIGN.md §4»—, para que la decisión sea rastreable sin bucear en el historial
de git.

> **Sustituciones razonables** — GitHub Issues o Projects funcionan si tu agente los lee con
> comodidad. Lo que importa es que el tablero **viva en el repositorio y sea texto plano**: el
> agente lo lee sin credenciales, lo edita en el mismo commit que el código y queda versionado
> junto a él. Un tracker externo rompe las tres cosas.

> **Se puede saltar si…** — Con cinco tareas, el kanban puede ser una lista de cinco líneas. Lo
> que no se salta en ningún tamaño es el orden por dependencia y el tamaño reducido de las
> cards.

### 5. `CLAUDE.md` — instrucciones operativas del agente

El archivo de reglas que la herramienta carga automáticamente en cada sesión. Es donde vive
todo lo que no quieres volver a explicar: leer los tres documentos antes de tocar nada;
registrar el progreso solo en el tablero respetando dependencias; desarrollo modular e
incremental; prohibido desarrollar en bloque; prototipo antes de código en toda pantalla
nueva; las decisiones con peso arquitectónico se preguntan, no se asumen; RLS junto con la
tabla; rate limit declarado; ningún secreto en el código; commits pequeños.

Y la regla que sostiene todas las demás: **la prueba manual es obligatoria y bloqueante**.
Ninguna card se marca como hecha sin que la hayas probado en la app real. No alcanza con que
compile, pase el linter o pasen los tests. Al terminar de implementar, el siguiente paso del
agente es siempre pedirte la prueba y _detenerse ahí_.

> **Intención**
>
> Es la regla de mayor retorno de todo el prompt. Sin ella, el modo natural de trabajo es
> encadenar: el agente implementa, asume que funciona y sigue con lo próximo. A las ocho cards
> tienes ocho «hechos» y una app que no funciona, y ya no sabes cuál de las ocho la rompió. La
> prueba bloqueante convierte cada card en un punto de restauración.
>
> El prompt agrega la automatización del navegador como _primer filtro_ antes de pasártela:
> útil para errores obvios, pero explícitamente insuficiente. El agente no puede firmar su
> propio trabajo.

> **Sustituciones razonables** — El nombre del archivo depende de la herramienta: `CLAUDE.md`,
> `AGENTS.md`, `.cursorrules`, `.github/copilot-instructions.md`. El contenido es portable
> entre todas.

> **Se puede saltar si…** — Nada de esto se salta, pero sí conviene **mantenerlo corto**. Un
> archivo de instrucciones de quinientas líneas compite por atención con el trabajo real y las
> reglas empiezan a diluirse. Si crece demasiado, mueve el detalle a los documentos que
> corresponda y deja aquí punteros.

### 6. `README.md` — punto de entrada

Mínimo y para humanos: una o dos líneas de qué es el proyecto y un índice con enlaces a los
otros documentos. Sin duplicar contenido.

> **Intención** — Un README que resume los otros documentos es un cuarto lugar donde la
> información puede quedar desactualizada. Punto de entrada, no resumen.

---

## 8. El ciclo de trabajo

Con los documentos aprobados empieza el código, y el ciclo por card es siempre el mismo:

1. Si la card introduce una pantalla o un patrón de interacción nuevo, primero un **prototipo**
   construido sobre los tokens ya definidos. Se implementa solo con tu aprobación.
2. Si aparece una decisión con peso arquitectónico que la lógica de negocio no resuelve, el
   agente **se detiene y pregunta**. La respuesta se documenta antes de programar.
3. Si la card crea una tabla o expone un endpoint, sus políticas de acceso y su postura de rate
   limit se resuelven ahí, no después.
4. Se implementa **únicamente esa card**. Nunca varias, nunca el módulo completo.
5. Si aplica, el agente recorre el flujo en el navegador como primer filtro automático.
6. Te pide la prueba manual **y se detiene**.
7. Con tu confirmación: se marca la card con su nota, **se actualizan los documentos que esa
   card haya cambiado**, y solo entonces se pasa a la siguiente.

> **Intención**
>
> El paso 7 es el que sostiene todo el edificio, y el más fácil de dejar pasar. Los documentos
> de intención tienen que seguir describiendo _cómo es la app hoy_, no cómo se planeó al
> principio. Si en tres semanas describen otra cosa, el agente los lee igual, les cree igual y
> trabaja con información falsa, que es peor que no tener documentos. Por eso actualizarlos no
> es un paso aparte ni una retrospectiva: es parte de cerrar la card.

> **No negociable** — Los pasos 4, 6 y 7 son el núcleo irreductible del método: una card a la
> vez, prueba humana antes de cerrar, documentos actualizados al cerrar. Si tuvieras que
> quedarte con tres líneas de todo este documento, son esas.

---

## 9. Ajuste por envergadura

El prompt completo está calibrado para una aplicación de negocio con usuarios reales. Aplicarlo
entero a un experimento de una tarde es desproporcionado y solo logra que abandones el método.
Esta es la escala:

| Pieza | Experimento de fin de semana | App real, pocos usuarios | Producto con clientes |
|---|---|---|---|
| `BUSINESS_LOGIC.md` | Media página | Completo | **Completo** |
| Estrategia de baja por entidad | Una línea | **Sí** | **Sí** |
| Fuera de alcance explícito | **Sí** | **Sí** | **Sí** |
| `DESIGN.md` | Solo tokens | Completo | **Completo** |
| Bitácora de pantallas | Omitir | Sí | **Sí** |
| Prototipo antes de código | Omitir | En pantallas nuevas | **Siempre** |
| `SECURITY.md` | Checklist mínimo | **Completo** | **Completo + revisión** |
| RLS en cada tabla | **Sí** | **Sí** | **Sí** |
| Secretos en variables de entorno | **Sí** | **Sí** | **Sí** |
| Rate limiting | Omitir | Solo en auth | **Evaluado por endpoint** |
| `TASKS.md` | Lista simple | Kanban completo | **Kanban completo** |
| Orden por dependencia | **Sí** | **Sí** | **Sí** |
| `CLAUDE.md` | Versión corta | **Completo** | **Completo** |
| Prueba manual bloqueante | **Sí** | **Sí** | **Sí** |
| Entornos dev/prod separados | Omitir | **Sí** | **Sí** |
| Migraciones versionadas | Omitir | **Sí** | **Sí** |
| CI (lint, tipos, build) | Omitir | Recomendado | **Sí** |
| Observabilidad | Omitir | Recomendado | **Sí** |
| Backups automáticos | Omitir | Si hay datos ajenos | **Obligatorio** |

La columna de la izquierda no está vacía por casualidad. Incluso en el proyecto más pequeño
quedan seis reglas, y todas comparten una propiedad: **omitirlas cuesta poco hoy y muchísimo
después**. Media página de lógica de negocio, un alcance cerrado, RLS, secretos fuera del
código, orden por dependencia y prueba manual. Ese es el piso.

---

## 10. Límites y advertencias

**Es un punto de vista, no un estándar.** Nace de un contexto específico: aplicaciones de
negocio a medida, construidas por una persona sola, con usuarios conocidos y registro cerrado.
Si tu contexto es otro —un producto con registro autoservicio, un equipo donde «yo apruebo»
deja de ser una sola persona, una app móvil nativa, un proyecto de datos o modelos— varias
piezas cambian de forma y algunas se caen enteras. Adóptalo como base y adáptalo.

**Tiene un costo real y por adelantado.** La fase de documentos toma horas antes de la primera
línea de código. Si tu objetivo es validar una idea en una tarde y descartarla, este proceso es
contraproducente: usa la columna izquierda de la tabla y nada más. Rinde cuando el proyecto va
a sobrevivir a la semana en que lo empezaste.

**El riesgo no es el papeleo, es el ritual.** Un documento que nadie actualiza es peor que no
tenerlo, porque el agente lo lee y le cree. Todo se apoya en una sola disciplina —actualizar al
cerrar cada card— y si esa disciplina se cae, el resto se convierte en decoración con
consecuencias.

**La IA no verifica por ti.** Ningún documento sustituye la prueba manual. Un agente puede
decirte con total confianza que algo funciona sin haberlo ejecutado nunca.

**Esto es un piso de seguridad, no una auditoría.** El checklist cubre los errores más comunes
y costosos, no todos los riesgos. Una política de RLS mal escrita da falsa sensación de
protección, y este método no te va a avisar. Si vas a manejar datos de salud, pagos, documentos
de identidad o información de menores, esto no alcanza: busca revisión de alguien con
experiencia en seguridad.

**Los precios y las funciones cambian.** La compuerta de backups asume el modelo de planes
actual de Supabase; los límites del plan gratuito, los nombres de las funciones y las
capacidades de cada servicio se mueven. Verifica los términos vigentes antes de apoyar una
decisión en ellos. Como criterio general: **trata las tecnologías como intercambiables y las
reglas como lo permanente**. Los nombres de producto van a envejecer; «toda tabla nueva nace
con sus políticas» no.

**Asume una persona que decide.** El ciclo depende de que haya alguien disponible para aprobar
documentos, prototipos y pruebas. En un equipo hay que definir quién es esa persona para cada
tipo de decisión, o el flujo se traba en la primera aprobación.

---

## 11. Adaptarlo a otra herramienta o a otro stack

- **Otro agente.** El prompt no depende de ninguna herramienta en particular. Cambia el nombre
  del archivo de instrucciones (`CLAUDE.md` → `AGENTS.md`, `.cursorrules`,
  `.github/copilot-instructions.md`) y listo. El paso de automatización del navegador es
  opcional: si tu agente no lo tiene, se salta y la prueba manual sigue igual.
- **Otro stack.** Sustituye las tecnologías en la sección correspondiente y revisa `SECURITY.md`
  con cuidado: las reglas de RLS y de separación de claves asumen una base de datos con
  permisos por fila y dos niveles de credencial. Si tu alternativa no los tiene, esa protección
  hay que reconstruirla en la capa de aplicación, no darla por hecha.
- **Otro idioma.** El prompt está en español. Traducirlo funciona sin cambios estructurales; lo
  único que conviene mantener literal son los nombres de archivo, porque el agente los usa como
  referencia cruzada entre documentos.
- **Recortarlo.** Empieza por la columna que te corresponda de la [tabla de
  escala](#9-ajuste-por-envergadura) y agrega piezas a medida que el proyecto crezca. Es más
  fácil sumar una sección que sostener un proceso que te queda grande.

---

Si adaptas el prompt y algo te resulta útil —una regla que faltaba, una que sobraba, una
sustitución que funcionó mejor—, los issues y los pull requests son bienvenidos.
