---
name: nuevo-codelab
description: Crea un codelab de Vilton de punta a punta a partir de un tema - investiga y diseña el temario, lo somete a aprobación humana, escribe el codelab y lo revisa hasta aprobarlo.
argument-hint: <tema del codelab y, opcionalmente, público o enfoque>
---

Crea un codelab nuevo sobre: **$ARGUMENTS**

Si no se indicó un tema, pregunta cuál antes de empezar.

Sigue estas fases en orden. La aprobación humana del temario (fase 2) es obligatoria por la regla de negocio R16: nunca escribas el codelab sin ella.

## 1. Temario

Lanza el agente `curriculum-designer` con el tema y cualquier contexto que haya dado la persona (público, nivel, plan de Workspace, material de referencia). Espera a que entregue `codelabs/temarios/<id>.md`.

## 2. Aprobación humana

Muestra a la persona, en el chat:
- la ruta del temario;
- el objetivo, y cada paso en una línea con su duración;
- la duración total;
- las "Decisiones y preguntas para quien aprueba".

Luego pregunta con AskUserQuestion si aprueba el temario, con las opciones:
- **Aprobar**: seguir a la fase 3.
- **Pedir cambios**: la persona escribe qué cambiar (opción "Other"). Vuelve a lanzar `curriculum-designer` con el temario actual y los cambios pedidos, y repite esta fase.
- **Cancelar**: detente y deja el temario como está.

Al aprobar, cambia en el temario `**Estado:** pendiente de aprobación` por `**Estado:** aprobado (<fecha de hoy>)` y anota las respuestas que dio la persona a las preguntas abiertas.

## 3. Escritura

Lanza `codelab-author` con la ruta del temario aprobado. Debe seguirlo paso a paso, escribir `codelabs/<id>.md` con `status: Draft` y validar que exporte.

## 4. Revisión

Lanza `codelab-reviewer` con las rutas del codelab y del temario.
- **CAMBIOS NECESARIOS:** pasa la lista de bloqueantes y mejoras a `codelab-author` para que corrija, y vuelve a revisar. Máximo 3 vueltas; si sigue sin aprobarse, detente y muestra lo pendiente a la persona.
- **APROBADO:** sigue a la fase 5.

## 5. Entrega

Informa a la persona:
- rutas del temario y del codelab;
- pasos y duración total;
- capturas de pantalla pendientes (marcadores `TODO`) que alguien debe tomar;
- cómo previsualizarlo: `./claat/bin/claat export -o build codelabs/<id>.md` y `python3 -m http.server 9090 --directory build`, luego abrir `http://localhost:9090/<id>/`.

No cambies `status` a `Published` ni hagas commits salvo que la persona lo pida.
