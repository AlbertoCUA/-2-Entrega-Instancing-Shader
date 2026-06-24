# 🐠 Enjambre de Peces — Simulación 3D con Babylon.js

Este proyecto es una simulación interactiva en 3D de un enjambre o banco de peces (*fish school*) que utiliza el algoritmo de bandadas o enjambres **Boids** de Craig Reynolds. La simulación está implementada en **Babylon.js** utilizando técnicas avanzadas de renderizado como sombreadores (shaders) personalizados en GLSL, instanciación de mallas para optimización de rendimiento y físicas de comportamiento para simular el movimiento colectivo de los peces.

---

## Características Principales

*   **Algoritmo Boids Completo:** Implementación de las tres reglas clásicas del comportamiento de bandadas (Separación, Alineación y Cohesión) más dos reglas adicionales:
    *   **Seguimiento del Líder:** Los peces normales tienden a seguir a peces líderes designados.
    *   **Confinamiento Espacial:** Fuerzas de rebote suaves que mantienen al enjambre dentro de los límites simulados.
*   **Sombreadores GLSL Personalizados (Custom Shaders):**
    *   **Vertex Shader (Wobble Effect):** Deformación de vértices en tiempo real para simular la ondulación del pez al nadar. La cabeza se mantiene estable, mientras que la oscilación se intensifica hacia la cola.
    *   **Fragment Shader (Toon/Cel Shading):** Renderizado con estilo sombreado plano de cuatro niveles discretos, especularidad de estilo *toon*, y un efecto de contorno (*rim light/outline*) para resaltar los bordes.
*   **Optimización por Instanciación (InstancedMesh):** Renderizado de más de 150 peces de manera eficiente en un único *draw call* a través de buffers instanciados (`instancedBuffers`) para desincronizar los tiempos de nado (`timeOffset`) y asignar colores según el grupo.
*   **Sistema de Iluminación y Sombras:**
    *   `HemisphericLight` para simular la luz ambiental del océano (azul en el cielo, oscura en el fondo).
    *   `DirectionalLight` dinámica con un generador de sombras exponenciales suavizadas (`ShadowGenerator` con *blur kernel*).
*   **Entorno Marino:** Suelo oceánico receptor de sombras, niebla exponencial para dar sensación de profundidad marina (`BABYLON.Scene.FOGMODE_EXP2`), y burbujas de aire que ascienden continuamente y reaparecen en el fondo de manera aleatoria.
*   **Interfaz de Usuario (HUD):**
    *   Pantalla de carga personalizada mientras se inicializa la escena.
    *   Información técnica en tiempo real en la interfaz (cámaras, luces, materiales).
    *   Contador de FPS para medir el rendimiento.

---

## Tecnologías Utilizadas

1.  **Core:** HTML5, CSS3, JavaScript (ES6).
2.  **Motor 3D:** [Babylon.js](https://www.babylonjs.com/) (cargado vía CDN oficial).
3.  **Lenguaje de Sombreado:** GLSL (OpenGL Shading Language) para los shaders de deformación y coloreado de los peces.

---

## Controles de la Simulación

La cámara utilizada es una `ArcRotateCamera` que permite navegar libremente por el espacio tridimensional:

*   **Rotar Cámara (Órbita):** Clic izquierdo + arrastrar el ratón.
*   **Desplazar Cámara (Pan):** Clic derecho + arrastrar el ratón.
*   **Zoom:** Rueda del ratón (scroll).
*   **Adaptabilidad:** La aplicación se adapta automáticamente al cambiar el tamaño de la ventana (*auto-resize*).

---

## Estructura del Proyecto

```bash
├── index.html            # Archivo principal con HTML, CSS, JavaScript y Shaders
├── README.md             # Instrucciones generales y documentación para GitHub
└── documentacion/        # Carpeta de documentación formal
    └── documentacion.tex # Documento LaTeX con la explicación técnica detallada
```

---

## Cómo Ejecutar la Aplicación

Debido a que el proyecto está autocontenido en un solo archivo HTML y utiliza recursos externos vía CDN, no requiere de una instalación compleja:

### Método Directo
1.  Descarga o clona el repositorio.
2.  Haz doble clic sobre el archivo [index.html](file:///c:/Users/marti/Documents/Optativas/Repo/Proyecto%20P2/index.html) para abrirlo en cualquier navegador web moderno (Chrome, Firefox, Edge, Safari, etc.).

### Servidor Local (Recomendado)
Para asegurar que los navegadores carguen correctamente los recursos sin restricciones de políticas CORS, puedes servirlo mediante un servidor local simple:

**Con Python 3:**
```bash
python -m http.server 8000
```
Luego, accede en tu navegador a `http://localhost:8000`.

**Con Node.js (http-server):**
```bash
npx http-server -p 8000
```

---

## Detalles del Algoritmo de Enjambre (Boids)

Cada pez de la simulación se comporta como un agente autónomo que calcula su aceleración basándose en su entorno local mediante las siguientes reglas:

1.  **Separación (Separation):** Evitar chocar con los compañeros de enjambre cercanos.
2.  **Alineación (Alignment):** Intentar igualar la velocidad y dirección de los compañeros cercanos de su mismo grupo.
3.  **Cohesión (Cohesion):** Dirigirse hacia el centro de masa (posición media) de los compañeros cercanos de su grupo.
4.  **Búsqueda del Líder (Leader Follow):** Los peces normales aplican una fuerza de atracción hacia el pez líder que tienen asignado en su respectivo grupo.
5.  **Fuerza de Confinamiento:** Cuando un boid se aproxima a los límites de la caja virtual de tamaño $BOUNDS$, se aplica una fuerza de desaceleración y redirección proporcional a la distancia de rebasamiento para mantenerlo dentro del área.
