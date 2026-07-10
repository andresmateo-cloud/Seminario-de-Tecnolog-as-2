# Seminario-de-Tecnologías-2
# Proyecto Animación 3D: Enjambre de Luciérnagas Autónoma 
# Integrantes del equipo:
 * Mateo Dorantes Andrés
 * Pérez Alvarado Adán

# Documentación del Proyecto: Enjambre de Luciérnagas Autónoma

## 1. Introducción
El proyecto consiste en una simulación gráfica en tiempo real de un enjambre de 200 luciérnagas, impulsada por el algoritmo de "Boids" (flocking). Está desarrollado integralmente en HTML5, CSS3, JavaScript y hace uso del motor gráfico 3D WebGL Babylon.js en su versión 6.26.0 . La escena resultante incluye renderizado avanzado con iluminación dinámica, efectos ambientales y una interfaz gráfica (UI) para interactuar con los parámetros del enjambre.

## 2. Arquitectura y Optimización 
Dado que la simulación incluye cientos de agentes en movimiento, la arquitectura se diseñó con un enfoque en el rendimiento computacional y de renderizado:
* **InstancedMesh:** Para evitar el cuello de botella que implica realizar múltiples llamadas de dibujo, los 200 agentes se instancian a partir de una única malla base (`baseMesh`), logrando que toda la población de luciérnagas se renderice en un solo *draw call*.
* **Luz Directa Selectiva:** Aunque todas las luciérnagas aparentan emitir luz (mediante shaders especiales y glow), las fuentes de luz real que afectan la escena (`PointLight`) están limitadas a un agente de cada seis (aproximadamente 34 en total) para evitar colapsar los cálculos de iluminación de WebGL.
* **Spatial Hashing (Cuadrícula Espacial):** Para calcular el comportamiento del enjambre de manera eficiente, el algoritmo de búsqueda de vecinos evita la complejidad tradicional de O(n²). En su lugar, utiliza un sistema que divide el espacio 3D en celdas basadas en el radio de percepción, permitiendo localizar rápidamente qué agentes están interactuando entre sí (Spatial Hashing).

## 3. Entorno 3D y Composición de Escena
El mundo virtual cuenta con múltiples componentes atmosféricos y topográficos:
* **Cielo y Sistema de Iluminación:** Se construyó un *skydome* infinito sobre el cual se dibuja un cielo nocturno estrellado y una luna brillante. La escena cuenta con iluminación ambiental y una luz direccional principal alineada al astro lunar.
* **Vegetación y Utilería:** Sobre el terreno se esparcen árboles generados algorítmicamente (troncos cilíndricos y copas esféricas), arbustos deformados al azar y rocas poliédricas. Se incluye también un estanque circular translúcido y reflexivo.
* **Atmósfera y Efectos Visuales:** El motor renderiza una capa base de niebla con decaimiento exponencial (`EXP2`) combinada con un plano translúcido simulando humedad a ras del suelo. Esto se refuerza mediante un sistema de partículas (`ParticleSystem`) que proyecta polvo fosforescente flotante y un post-proceso `GlowLayer` para potenciar las luces de los agentes.

## 4. Algoritmo de Enjambre (Flocking / Boids)
La inteligencia artificial del vuelo colectivo se basa en la iteración de tres reglas fundamentales aplicadas sobre cada agente:
* **Separación:** Calcula un vector de rechazo cuando la distancia hacia otro boid cae por debajo de un umbral (`sepRadius`), evitando que las luciérnagas colisionen o se aglomeren excesivamente.
* **Alineación:** Cada luciérnaga sondea el vector de velocidad de las compañeras en su rango de percepción e intenta promediar e igualar dicha velocidad y dirección.
* **Cohesión:** Determina el centro de masa de los agentes cercanos y aplica un vector de atracción suave para mantener a la bandada unida.
* **Especies:** Existen 10 variaciones fenotípicas programadas en el objeto `SPECIES` que diversifican el tamaño, velocidad base y el color de luminiscencia del abdomen.

## 5. Implementación de Shaders Personalizados (GLSL)
El proyecto renuncia a los materiales estándar para los agentes, e implementa un `ShaderMaterial` nativo. Esto permite efectos sofisticados acelerados por hardware:
* **Vertex Shader:** Se integra física visual al manipular la posición de los vértices (un efecto de oscilación o *wobble*). Utilizando atributos personalizados como la fase (`phase`), cada agente ondula independientemente simulando un aleteo en el aire.
* **Fragment Shader:** Controla cómo la luz se emite localmente. Analiza el canal rojo (`color.r`) configurado en los vértices de la malla. Aquellas partes pertenecientes al abdomen tienen valor 1.0 (convirtiéndolas en emisoras brillantes), mientras que el torso, alas y cabeza poseen valores bajos y quedan opacos. Adicionalmente, el shader procesa efectos Fresnel y absorbe variables del entorno como la niebla de la escena.

## 6. Interfaz de Usuario y Controles (HUD)
El proyecto contiene un panel superpuesto diseñado en HTML/CSS y tipografía *monospace*, estilizado para integrarse orgánicamente en un ambiente oscuro:
* **Panel Informativo (HUD):** Localizado arriba a la izquierda, muestra métricas en tiempo real de la simulación, incluyendo los agentes activos, una barra visual de *Frames Per Second* (FPS) y el contador de tiempo.
* **Panel de Parámetros Globales:** Situado a la derecha, contiene deslizadores (sliders) para modificar al vuelo la lógica del enjambre. Permite ajustar el peso de la Separación, Alineación y Cohesión, alterar el rango de percepción de los agentes, su velocidad y los límites de las fronteras virtuales (X, Y, Z). También incluye un menú desplegable para alternar la interacción con el borde del mundo entre un efecto de "Rebote" o de bucle "Wraparound".
