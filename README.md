# Mejora adaptada a cada imagen — Actividad 1

Actividad 1 de **Visión Artificial** — Máster Universitario en Inteligencia
Artificial, UNIR. Autor: **Andrés Hurtado Morón**.

## Resumen de la práctica

Se parte de **cuatro fotografías subexpuestas** (`81.png`, `87.png`, `156.png`
y `521.png`), cada una con un problema distinto: desde sombras con estructura
hasta ruido de croma, zonas muy brillantes o un histograma con un pico enorme
pegado a cero. Todas tienen `min = 0` y `max = 255`, por lo que el estiramiento
min-max clásico es la identidad y no aporta nada.

La idea central es que **no existe un pipeline universal**: cada histograma
pide una técnica de intensidad distinta. Siguiendo la metodología del
laboratorio, se trabaja **siempre en RGB** (sin convertir a grises ni a
LAB/HSV), aplicando las transformaciones de intensidad sobre los tres canales
y volviendo a unirlos.

| Imagen | Observación | Pipeline aplicado |
|---|---|---|
| `81.png` | media 13,7; mucha estructura | Corrección lineal (`α=2`, `β=2`) + CLAHE + mezcla 10 % |
| `87.png` | media 8,5; ruido/dominante roja, carteles luminosos | Limpieza de croma + balance de blancos suave + gamma 0,7 |
| `156.png` | sombras muy oscuras + escaparate brillante | Corrección lineal enmascarada a sombras (comparada con curva a trozos) |
| `521.png` | media 5,3; casi todo negro, pico en 0 | Ecualización global limitada del histograma (`clip_frac`, mezcla 50 %) |

### Técnicas empleadas

- **Corrección lineal** `g = α·f + β` (punto a punto), global o restringida a
  las zonas de sombra mediante una máscara.
- **CLAHE** por canal con `clipLimit` bajo para realzar contraste local sin
  amplificar el ruido.
- **Limpieza de croma** en sombras y **balance de blancos** tipo *gray-world*
  suave.
- **Corrección gamma** aplicada sobre la luminancia para proteger las luces.
- **Ecualización de histograma** global por canal, recortando el pico del
  histograma antes de calcular la CDF para no amplificar el suelo de ruido.
- **Curva a trozos** (interpolación) como comparación frente al método final.

### Evaluación de la calidad

Al ser las originales las imágenes **degradadas**, no existe una referencia de
calidad; por eso se usan **métricas sin referencia** (brillo, contraste,
porcentaje de píxeles subexpuestos/saturados, ruido en zonas planas y color).
La original se emplea únicamente como referencia de **estructura** mediante la
**correlación de gradientes**, para comprobar que no se inventa ni se borra
contenido.

## Requisitos y ejecución

```bash
pip install opencv-python numpy matplotlib jupyter
jupyter notebook actividad1.ipynb
```

Las imágenes de entrada deben estar en `archive/image/` (las cuatro
mencionadas arriba).

## Entregables

- `actividad1.ipynb` — notebook con el desarrollo y los resultados.
- `Act1_Andrés_Hurtado.pdf` — memoria de la actividad.
