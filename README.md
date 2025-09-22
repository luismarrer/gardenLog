# MisPlantas - Mi Jardín Personal 🌱

Un sitio web personal dedicado a documentar y compartir mi colección de plantas, desde hierbas aromáticas hasta flores exóticas. Cada planta tiene su propia página con información detallada sobre cuidados, características y consejos de cultivo.

## 🌿 Descripción del Proyecto

MisPlantas es una aplicación web estática que presenta mi jardín personal de manera organizada y visualmente atractiva. El sitio incluye información detallada sobre 7 plantas diferentes, con fotografías reales y guías de cuidado completas.

### Plantas Incluidas

1. **Lechuga del País / Black Seeded Simpson** - Lechuga de hojas tiernas perfecta para ensaladas
2. **Tomate Cherry** - Tomatitos dulces ideales para picar o ensaladas
3. **Cosmos Rosita** - Flor rosada que atrae mariposas y abejas
4. **Nepenthes** - Planta carnívora exótica con jarritas atrapa-insectos
5. **Cilantro** - Hierba aromática de sabor fresco y cítrico
6. **Perejil** - Hierba verde versátil para la cocina
7. **Perennial Mix Wildflower** - Mezcla floral perenne que atrae polinizadores

## 🎨 Características de Diseño

- **Diseño Responsivo**: Adaptado para dispositivos móviles, tabletas y escritorio
- **Tipografía Moderna**: Uso de fuentes Outfit e Inter para una experiencia visual óptima
- **Paleta de Colores Natural**: Tonos verdes y tierra que reflejan la naturaleza
- **Navegación Intuitiva**: Menú de navegación claro con logo central
- **Galería de Imágenes**: Fotografías reales de alta calidad de cada planta

## 🏗️ Estructura del Proyecto

```bash
gardenLog/
├── index.html              # Página principal
├── cilantro.html           # Página detalle del cilantro
├── cosmos-rosita.html      # Página detalle del cosmos rosita
├── lechuga.html            # Página detalle de la lechuga
├── nepenthes.html          # Página detalle del nepenthes
├── perejil.html            # Página detalle del perejil
├── tomate-cherry.html      # Página detalle del tomate cherry
├── wildflower.html         # Página detalle de las flores silvestres
├── styles/
│   ├── main.css            # Estilos principales
│   └── plant-detail.css    # Estilos para páginas de plantas
├── images/                 # Imágenes de las plantas
└── mockup/                 # Recursos de diseño y mockups
```

## 🎯 Funcionalidades

### Página Principal (`index.html`)

- **Hero Section**: Presentación del jardín con imagen destacada
- **Contador de Plantas**: Muestra el número total de plantas (7)
- **Galería de Plantas**: Grid responsivo con todas las plantas
- **Navegación**: Enlaces a secciones específicas

### Páginas de Plantas Individuales

- **Información Detallada**: Nombre, descripción y características
- **Cuidados Básicos**: Guía de cuidado específica para cada planta
- **Tarjetas Informativas**:
  - Condiciones de cultivo
  - Problemas comunes
  - Consejos de cuidado
- **Galería de Imágenes**: Múltiples fotografías de cada planta
- **Navegación**: Botón para regresar a la sección principal

## 🛠️ Tecnologías Utilizadas

- **HTML5**: Estructura semántica con elementos como `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`
- **CSS3**:
  - Flexbox y CSS Grid para layouts responsivos
  - Variables CSS para consistencia en colores y tipografía
  - Media queries para adaptabilidad móvil
- **Google Fonts**: Outfit (títulos) e Inter (texto)
- **Diseño Responsivo**: Mobile-first approach

## 🎨 Paleta de Colores

- **Fondo Principal**: `#F8F5EC`
- **Verde Principal**: `#4D7B38` (navegación, botones, enlaces)
- **Verde Hover**: `#78A75A`
- **Verde Secundario**: `#9EB08C` (secciones destacadas)
- **Texto Principal**: `#6B4E37` (títulos), `#333333` (párrafos)
- **Texto Claro**: `#FFFFFF` (sobre fondos oscuros)
- **Enlace Hover**: `#C93F2D`

## 📱 Diseño Responsivo

El sitio está optimizado para:

- **Móviles**: < 768px
- **Tabletas**: 768px - 1024px  
- **Escritorio**: > 1024px

### Características Responsivas

- Grid adaptativo en la galería de plantas
- Navegación optimizada para móviles
- Imágenes responsivas con `object-fit`
- Tipografía escalable con `clamp()`

## 🚀 Cómo Usar

1. **Clonar el repositorio**:

   ```bash
   git clone [URL_DEL_REPOSITORIO]
   cd gardenLog
   ```

2. **Abrir en el navegador**:

   - Abrir `index.html` directamente en el navegador
   - O usar un servidor local como Live Server en VS Code

3. **Navegación**:

   - **Inicio**: Página principal con hero y galería
   - **Plantas**: Salta a la sección de plantas
   - **MisPlantas**: Logo que regresa al inicio
   - **Contacto**: Salta al footer con información de contacto

## 🔧 Desarrollo

### Requisitos de Desarrollo

- Editor de código (VS Code recomendado)
- Navegador moderno para pruebas
- Extensión Live Server (opcional)

### Estructura de Archivos CSS

- `styles/main.css`: Estilos globales, reset, navegación, hero, galería
- `styles/plant-detail.css`: Estilos específicos para páginas de plantas

### Convenciones de Código

- Indentación de 4 espacios
- Nombres de clases en kebab-case
- Comentarios descriptivos en CSS
- Estructura HTML semántica

## 📸 Recursos Visuales

Todas las imágenes son fotografías reales de las plantas del jardín, organizadas por planta:

- Múltiples ángulos y estados de crecimiento
- Resolución optimizada para web
- Formato JPEG para mejor compresión

## 🤝 Contribuciones

Este es un proyecto personal, pero si tienes sugerencias o encuentras algún problema:

1. Abre un issue describiendo el problema o sugerencia
2. Si quieres contribuir con código, haz un fork y crea un pull request

## 📄 Licencia

Este proyecto es de uso personal y educativo.

---

## TODO

- [ ] Exportar proyecto a Astro
  - [ ] Crear una rama de desarrollo para trabajar la exportación

---

Desarrollado con 💚 para documentar y monitorear mis plantas
