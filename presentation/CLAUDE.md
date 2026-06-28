# Reglas de la presentación

Este directorio contiene la presentación para la defensa de la tesis de Luis Alejandro sobre RCG extendida para SAT. Las siguientes reglas son obligatorias en cualquier intervención de Claude sobre la presentación.

## Herramienta

- **Usar Beamer** (LaTeX para diapositivas). Es el formato elegido porque consume menos tokens de Claude y de cualquier IA que otras alternativas.
- Compilar con el ciclo de LaTeX habitual para mantener el `.pdf` sincronizado con la fuente.

## Duración y extensión

- La exposición dura **20 minutos**. Ir a lo esencial: no profundizar de más en cada detalle.
- Ritmo medio: **1 diapositiva por minuto**. Apuntar a **entre 20 y 25 diapositivas**.
- Menos de 20 es mejor; se admite acercarse a 25 porque la tesis es extensa.

## Contenido de cada diapositiva

- La presentación sirve para **guiarse**, no para leerla. **Nada de párrafos.**
- **Máximo 7 ítems por diapositiva.**
- **Máximo 7 palabras por ítem**, aunque el texto parezca telegráfico.
- Ideal: que cada ítem ocupe **una sola línea**.
- Óptimo: que el bloque de texto forme un **polígono convexo** (las líneas no deben "entrar y salir" de forma irregular). El tribunal incluye a Fernando, que agradece este orden visual.

## Aparición progresiva

- Los ítems deben **aparecer uno a uno** (overlays de Beamer, p. ej. `\pause`, `\item<+->`, `\onslide`).
- Motivo: si el oyente ve todo el contenido de golpe y lee el punto 7 mientras se habla del punto 1, pierde el foco.

## Tablas y datos

- **Evitar tablas con muchos números.** El ojo humano no procesa muchos datos rápido.
- Usar una tabla densa solo si hay que resaltar algo **muy importante**.

## Énfasis y legibilidad

- Para resaltar un resultado importante (o una celda de una tabla), poner ese texto en **negrita, subrayado y de color rojo o azul intenso**.
- Razón: los proyectores suelen mostrar mal los colores tenues; el contraste fuerte garantiza que se vea.

## Coherencia con la tesis

- El contenido debe alinearse con la tesis ya redactada en `sketch/` y `docs/`. Respetar los nombres, definiciones y resultados tal como aparecen allí.
- Aplican también, en lo pertinente, las reglas de redacción de la tesis (sin hermengildos, sin voz pasiva participial, sin palabras comadreja).
