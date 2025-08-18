---
trigger: always_on
---

# Tipografía

## Familias y stacks

- **Encabezados y títulos:** `Outfit`, respaldo: `Manrope`, luego sistema: `ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`.
- **Cuerpo / párrafos / enlaces:** `Inter`, respaldo: `Source Sans 3`, luego el mismo stack de sistema.

## Pesos y estilos

- **Títulos (h1–h3):** Outfit 700 (alternar 600 si el contraste visual con el fondo es muy alto).
- **Subtítulos (h4–h6):** Outfit 600.
- **Párrafos:** Inter 400 (usar 500 para llamados o notas).
- **Menú de navegación:** Outfit 600 **en mayúsculas** con `letter-spacing: 0.04em`.  
  > Si el menú tiene textos largos, usa “Title Case” sin upper para legibilidad.
- **Botones:** Outfit 700 **en mayúsculas** con `letter-spacing: 0.06em`.

## Tallas y ritmo (sugerencia)

- Base: `16px`  
- Párrafo: `1rem/1.65`  
- H1: `clamp(2rem, 1.2rem + 2.5vw, 3rem)`  
- H2: `clamp(1.5rem, 1rem + 1.5vw, 2.25rem)`  
- H3: `clamp(1.25rem, 1rem + 0.8vw, 1.5rem)`  
- Menú/botón: `0.95rem` (upper + tracking)

## Accesibilidad y microtipografía

- `font-display: swap` (Google Fonts)  
- `text-wrap: balance;` para títulos, `text-wrap: pretty;` en párrafos donde aplique  
- `hyphens: auto;` y `lang="es"` en `<html>`  
- Evitar “faux bold”: `font-synthesis-weight: none;`  
- Enlaces: `text-decoration-thickness: .08em; text-underline-offset: .15em;`
