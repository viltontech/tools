# Instrucciones para agentes de IA

Este archivo es para cualquier asistente de IA que trabaje en el repo (Claude Code, Codex, Cursor, Copilot, Gemini…).

Este repo es el fork de Vilton de [googlecodelabs/tools](https://github.com/googlecodelabs/tools): `claat` (en Go) convierte codelabs escritos en Markdown en HTML estático, y la app [viltontech/codelabs-app](https://github.com/viltontech/codelabs-app) los consume. Lo que no es de Vilton (README.md, FORMAT-GUIDE.md, `site/`, `sample/`) viene del proyecto original.

## Crear o editar codelabs

Antes de tocar `codelabs/`, leé [codelabs/README.md](codelabs/README.md): explica el flujo, los agentes y las reglas.

- Cada etapa tiene un agente especialista en `.claude/agents/<nombre>.md`. Aunque tu herramienta no ejecute subagentes, leé el archivo del agente de esa etapa y seguí sus instrucciones: son Markdown normal.
- Nunca escribas un codelab sin un temario aprobado por una persona (`codelabs/temarios/<id>.md` con **Estado:** aprobado). Es una regla de negocio (R16).
- Contenido en español rioplatense con voseo bien aplicado (R17) y todo en español (R18). La guía está en `.claude/agents/codelab-author.md`.

## Cambiar claat

- Código en `claat/`. Tests: `cd claat && go test ./...`. Binario: `cd claat && go build -o bin/claat .`.
- Para cambios en el parser, el render o `codelab-elements/`, seguí `.claude/agents/codelab-elements-expert.md`: incluye lo que la app necesita de los codelabs y no se puede romper.

## Comandos

```bash
cd claat && go build -o bin/claat . && cd ..        # compilar claat
./claat/bin/claat export -o build codelabs/<id>.md  # exportar un codelab
python3 -m http.server 9090 --directory build       # previsualizar en http://localhost:9090/<id>/
```

No commitees `build/`. No cambies `status` a `Published` ni hagas commits salvo que te lo pidan.
