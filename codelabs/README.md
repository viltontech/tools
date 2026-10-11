# Cómo creamos codelabs en Vilton

Esta carpeta tiene los codelabs de Vilton escritos en Markdown. `claat` los convierte en tutoriales interactivos paso a paso, y la app [Vilton Codelabs](https://github.com/viltontech/codelabs-app) los muestra a los alumnos.

La creación está automatizada con agentes de IA, pero **una persona aprueba siempre el temario** antes de que se escriba el codelab.

## El flujo

```
 Tema
  │
  ▼
 0. topic-researcher ───────► codelabs/investigaciones/<tema>.md
  │   Investiga en fuentes oficiales: cómo funciona, funcionalidades,
  │   nombres de la interfaz en español, planes, permisos, novedades.
  ▼
 1. curriculum-designer ────► codelabs/temarios/<id>.md  (pendiente de aprobación)
  │   Elige lo que tiene valor práctico y arma los pasos.
  ▼
 2. 👤 Persona ─────────────► aprueba · pide cambios (vuelve a 1) · cancela
  │
  ▼
 3. codelab-author ─────────► codelabs/<id>.md  (status: Draft)
  │   Escribe el codelab siguiendo el temario y valida que exporte.
  ▼
 4. codelab-reviewer ───────► APROBADO o lista de cambios (vuelve a 3, hasta 3 veces)
  │
  ▼
 Borrador listo para revisar en el navegador y publicar
```

## Cómo usarlo

### Con Claude Code (recomendado)

Abrí Claude Code en este repo y escribí:

```
/nuevo-codelab Filtros y etiquetas en Gmail para ordenar la bandeja de entrada, para principiantes
```

El comando hace todo el recorrido y se detiene en el paso 2 para que apruebes el temario. Cuanto más contexto le des (público, nivel, plan de Workspace de los alumnos, material de referencia), mejor sale.

También podés usar cada agente por separado, por ejemplo: "usá topic-researcher para investigar Gemini en Google Sheets" o "usá codelab-reviewer para revisar codelabs/mi-codelab.md".

### Con otra herramienta de IA

Los agentes son archivos Markdown normales en `.claude/agents/`. Cualquier asistente (Codex, Cursor, Gemini…) puede leerlos y seguir sus instrucciones; ver [AGENTS.md](../AGENTS.md).

### A mano

Copiá `vilton-demo.md`, seguí la guía de formato y estilo de [`.claude/agents/codelab-author.md`](../.claude/agents/codelab-author.md) y exportá para probar (ver más abajo).

## Los agentes

| Agente | Qué hace | Qué entrega | No hace |
|---|---|---|---|
| [`topic-researcher`](../.claude/agents/topic-researcher.md) | Investiga el tema en fuentes oficiales y actuales | Informe con fuentes, fechas, dudas e ideas de codelabs | Diseñar ni escribir |
| [`curriculum-designer`](../.claude/agents/curriculum-designer.md) | Convierte la investigación en un temario | Temario pendiente de aprobación | Escribir el codelab |
| [`codelab-author`](../.claude/agents/codelab-author.md) | Escribe el codelab en Markdown de claat | `codelabs/<id>.md` que exporta sin errores | Publicar ni commitear |
| [`codelab-reviewer`](../.claude/agents/codelab-reviewer.md) | Revisa exportación, contenido, voseo y estilo | Veredicto con cambios concretos | Editar el codelab |
| [`codelab-elements-expert`](../.claude/agents/codelab-elements-expert.md) | Conoce el parser, el render y los componentes | Respuestas con evidencia del código, cambios en claat | Escribir contenido |

El comando que los encadena está en [`.claude/skills/nuevo-codelab/SKILL.md`](../.claude/skills/nuevo-codelab/SKILL.md).

## Estructura de carpetas

```
codelabs/
├── README.md                  ← este archivo
├── <id>.md                    ← los codelabs (uno por archivo; el nombre es el id)
├── img/                       ← imágenes de los codelabs (siempre locales)
├── investigaciones/<tema>.md  ← informes de topic-researcher
└── temarios/<id>.md           ← temarios, con su estado de aprobación
```

## Reglas que siguen todos los codelabs

Las reglas de negocio completas, con fecha, están en [REGLAS-DE-NEGOCIO.md](https://github.com/viltontech/codelabs-app/blob/main/REGLAS-DE-NEGOCIO.md) de la app. Las que afectan al contenido:

- **R14.** Enseñan cómo funciona la herramienta y funcionalidades específicas: cada paso tiene acciones concretas y un resultado que el alumno puede comprobar.
- **R15.** Los temas son abiertos y se investigan antes de diseñar.
- **R16.** Una persona aprueba el temario antes de escribir el codelab.
- **R17.** Español rioplatense con voseo bien aplicado: "abrí", "tenés", pero "**tu** empresa", "**te** enviamos".
- **R18.** Todo en español, incluidos los nombres de menús y botones.

Y del lado técnico:

- El último paso es el cierre: en la app, llegar a él marca el codelab como completado.
- Las imágenes van en `codelabs/img/`. Una imagen remota (`https://…`) hace fallar a claat.
- Mientras se trabaja, `status: Draft`. Se pasa a `Published` solo cuando se decide publicar.

## Previsualizar un codelab

```bash
# Una sola vez: compilar claat
cd claat && go build -o bin/claat . && cd ..

# Exportar y servir
./claat/bin/claat export -o build codelabs/<id>.md
python3 -m http.server 9090 --directory build
```

Abrí `http://localhost:9090/<id>/`. La carpeta `build/` no se commitea.

## Publicar

Hoy los codelabs se copian a mano a la app. Está planeado un GitHub Action que exporte con claat, suba el resultado a Firebase Storage y registre los metadatos en Firestore. Ese pipeline tiene que exportar solo los codelabs (`codelabs/*.md` excepto este README), no las carpetas `investigaciones/` ni `temarios/`.

## Cambiar el sistema

- **Estilo o formato de los codelabs:** editá `.claude/agents/codelab-author.md`. Si cambia lo que el revisor tiene que controlar, actualizá también `codelab-reviewer.md`.
- **Una regla de negocio nueva:** agregala con fecha en `REGLAS-DE-NEGOCIO.md` de la app y reflejala en el agente que corresponda.
- **Una construcción nueva de Markdown** (un tipo de bloque, un dominio para iframes): pedíselo a `codelab-elements-expert`, que toca el parser de claat y actualiza la guía del autor.
