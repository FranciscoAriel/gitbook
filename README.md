---
description: Capítulo 1
---

# Propiedades deseables de los estimadores

En la vida real quisiéramos estimar valores aproximados de características de interes de una población, por ejemplo la temperatura promedio de cierto lugar.

Sería conveniente tener fórmulas generales para poder estimar algo desconocido únicamente utilizando información obtenida a partir de la observación de fenómenos; quisiéramos que esas fórmulas generales sean buenas y que se pudieran utilizar con otros datos. 

Por ello en este documento se detalla la forma en obtener estimadores de los parámetros poblacionales que son de interés


Supóngase que $\mathbf{X}=( X_1,X_2,\ldots,X_n )$ es una muestra aleatoria de una densidad $f_X(x;\theta)$ conocida, la cual depende de un parámetro desconocido $\theta$, aunque sabemos que $\theta$ pertenece a un espacio de parámetros $\Theta$. Si únicamente conocemos la muestra aleatoria (observaciones), $\mathbf{x}$ que representan una realización de $\mathbf{X}$, ¿Cuál es el valor aproximado de $\theta$?

{% hint style="info" %}
Por ahora se considerará que $\theta$ es de dimensión uno, aunque podría ser un vector de parámetros.
{% endhint %}
