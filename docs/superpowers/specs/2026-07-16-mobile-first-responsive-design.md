# Mobile-first responsive fix — Pump&Flow site

## Problema

El sitio (`index.html`, `clases.html`, `curso.html`, `personales.html`) no tiene
ninguna media query. Todos los layouts usan grids fijos (`grid-template-columns:
1.1fr 1fr`, `repeat(3,1fr)`, etc.) pensados para escritorio, y el nav mete logo +
3 links + botón de WhatsApp en una sola fila. En un celular el contenido se
desborda y el nav no cabe.

## Alcance

Las 4 páginas HTML de la raíz del repo. No incluye `pump-flow-design/` (el
generador de clases y el sistema de diseño quedan fuera).

## Arquitectura CSS

**Mobile-first real:** el CSS base se escribe para pantalla chica; se amplía
con `min-width` media queries para pantallas más grandes. Un solo breakpoint,
acorde a la complejidad del sitio: **680px**.

**Estilos compartidos → `pump-flow-design/diseño-marca-pump-flow/styles.css`**
(ya importado por las 4 páginas). Se mueven ahí, porque hoy están duplicados
copy-paste en cada `<style>` inline:
- Reglas de `nav`, `.btn`/`.btn-primary`/`.btn-outline`/`.btn-white`, `.wrap`,
  `footer` y su comportamiento responsive.
- Una media query que redeclara las variables de tipografía grande
  (`--text-display-xl`, `--text-display`, `--text-h1`) a tamaños menores en
  mobile. Como todas las páginas consumen estas variables vía `var()`, un solo
  cambio ajusta todos los títulos grandes de las 4 páginas a la vez.

**Estilos específicos de cada página** (hero, grids de tarjetas, etc.) se
quedan en el `<style>` inline de cada HTML, con sus propias media queries para
pasar de multi-columna a una columna.

## Nav mobile

- Fila superior: logo (izq) + botón "Reserva tu clase ✨" (der), siempre
  visibles.
- Ícono ☰ junto al logo. Al tocarlo despliega un panel debajo con los 3 links
  (Clases, Curso en línea, Clases personales) en columna.
- Sin dependencias nuevas: checkbox oculto + CSS `:checked`, o un script
  inline mínimo que togglea una clase. Sin frameworks.
- Los links NO se muestran en la fila superior en mobile — solo dentro del
  menú desplegado.

## Componentes compartidos — comportamiento en mobile (<680px)

- `.wrap`: padding lateral de 32px → 20px.
- `.btn`: sin cambios de tamaño (ya cumple 48px de alto, buen target táctil).
  Cuando dos botones están en la misma fila (ej. hero de index), se apilan a
  ancho completo.
- `footer .inner`: de `flex row space-between` a columna centrada.
- Padding vertical de secciones grandes (72px/64px) baja a ~40-48px en mobile
  para acortar el scroll.

## Ajustes por página (<680px, todo a una columna)

- **`index.html`**: hero (foto+texto) → foto arriba, texto abajo. `.burbujas`
  se apilan centradas, sin el offset alternado izquierda/derecha. `.grid3` (3
  tarjetas) → 1 columna. `.reserva .inner` (texto + mapa) se apila, mapa con
  altura fija reducida.
- **`clases.html`**: `.horarios` (2 tarjetas) → 1 columna. `.intro .card`
  (texto + lista) se apila. `.pgrid` (2 precios) → 1 columna.
- **`curso.html`**: hero (texto + 2 fotos) se apila. `.modulos` (3 tarjetas) →
  1 columna.
- **`personales.html`**: ya es mayormente vertical; solo hereda el
  padding/tipografía reducidos, sin cambios estructurales de grid.

## Verificación

Levantar el sitio con un server local y revisar las 4 páginas en viewport de
375px (iPhone SE, el más angosto común):
- Nada se corta ni se desborda horizontalmente.
- El menú hamburguesa abre y cierra correctamente.
- Los botones de WhatsApp/reserva siguen siendo tocables y visibles sin
  scroll horizontal.

Revisar también en un viewport de escritorio (~1280px) que el layout actual
no haya cambiado visualmente.

## Fuera de alcance

- No se toca `pump-flow-design/` (sistema de diseño, generador de clases).
- No se agrega breakpoint intermedio para tablet — un solo breakpoint basta
  dado lo simple de los layouts.
- No se introduce ningún framework JS ni build step.
