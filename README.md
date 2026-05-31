# Seminario-de-Tecnolog-as-2
# Proyecto Animación 3D: Enjambre de Luciérnagas Autónoma (Avance 1)

## Descripción
Este proyecto presenta la integración funcional de las etapas T1, T2 y T3. Consiste en un sistema de partículas biológicas articuladas (luciérnagas) que patrullan un espacio virtual tridimensional de manera independiente.

## Estructura del Proyecto
* `index.html`: Punto de entrada de la aplicación. Configura la escena base (T1), iluminación de baja intensidad y niebla para simular un ambiente nocturno, además de gestionar el bucle de renderizado principal.
* `Firefly.js`: Módulo ES6 que contiene la clase del Agente (T3). Construye la geometría jerárquica (T2) (Tórax -> Cabeza, Alas, Abdomen funcional) y calcula las físicas de avance y colisión perimetral autónoma.

## Conceptos Aplicados
1.  **Jerarquías de Transformación (T2):** Las alas están anidadas a pivotes descentralizados con respecto al tórax. Esto permite que el aleteo (`Math.sin(t)`) sea relativo a su articulación y no deforme el cuerpo del insecto.
2.  **Movimiento Autónomo Local (T3):** Cada agente se desplaza utilizando vectores de velocidad tridimensionales aleatorios propios y rota orgánicamente hacia su vector de dirección mediante cálculo trigonométrico en tiempo real.
3.  **Iluminación Dinámica:** Cada luciérnaga posee un material emisivo acoplado a una `PointLight` interna, simulando bioluminiscencia real que afecta al entorno cercano al volar.
