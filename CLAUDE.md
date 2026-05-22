# Reglas del proyecto

Este repositorio contiene la tesis de grado de Luis Alejandro sobre RCG extendida para SAT. Las siguientes reglas son obligatorias en cualquier intervención de Claude.

## Escritura de .tex

- **Obligatorio**: cualquier intervención sobre un archivo `.tex` debe tener presentes las reglas de `notes/indicaciones-para-la-escritura-2026.pdf` y respetarlas al pie de la letra. No hace falta releer el documento en cada turno, pero su contenido debe estar incorporado al criterio con que se redacta o se proponen ediciones.
- Todo lo que se escriba en archivos `.tex` (capítulos, introducción, notas, etc.) debe cumplir las reglas de redacción definidas en `notes/indicaciones-para-la-escritura-2026.pdf`. Esto incluye:
  - No introducir hermengildos (términos sin definir).
  - No usar voz pasiva participial (p. ej. *"es denotada"*, *"es reconocida"*).
  - No usar palabras comadreja (vagas o evasivas, p. ej. *"el punto de referencia obligado"*).
  - Convenciones de párrafo, transiciones y registro académico que indique el documento.
- Antes de proponer ediciones basadas en revisiones del tutor (Fernando), leer la versión `.tex` anotada en `notes/` (no solo el PDF) para no invertir la dirección de los `\cambio{actual}{sugerido}` ni confundir el fragmento marcado con la sugerencia.
- Los borradores se editan únicamente en `sketch/`, nunca en `notes/`.

## Referencias bibliográficas

- No deben existir entradas duplicadas en `sketch/bib/referencias.bib`. Antes de añadir una entrada nueva, verificar que el trabajo no esté ya registrado bajo otra clave.
- No introducir citas redundantes: si una afirmación ya está respaldada por una `\cite{...}` en el mismo párrafo, no añadir otra cita al mismo trabajo.
- Usar `[REF]` como placeholder cuando no se conozca aún la referencia exacta (sin etiqueta dentro de los corchetes).

## Compilación

- Cada vez que se termine de modificar un `.tex`, recompilar el archivo correspondiente con el ciclo completo de LaTeX (`pdflatex → bibtex → pdflatex → pdflatex`) para que el `.pdf` quede sincronizado con la fuente.
- Verificar que la compilación termine sin warnings de citas o referencias indefinidas (salvo los cross-chapter `\ref` pendientes de resolver con el documento maestro).
