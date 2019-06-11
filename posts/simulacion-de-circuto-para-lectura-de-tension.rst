.. title: Simulación de circuto para lectura de tensión
.. slug: simulacion-de-circuto-para-lectura-de-tension
.. date: 2019-06-11 13:29:55 UTC+02:00
.. tags: electronica,spice,kicad,robotica,motor
.. category: electrónica
.. link: 
.. description: 
.. type: text
.. has_math: true

##############################
Circuito de lectura de tensión
##############################

Para un proyecto de robótica que estoy comenzando, basado en versiones de los rover marcianos de NASA, necesitaba diseñar un circuito para hacer la lectura de la corriente que circula por un motor controlado mediante un driver `Allegro A4953 <https://www.allegromicro.com/~/media/Files/Datasheets/A4952-3-Datasheet.pdf>`_. Este driver tiene un pin LSS en el que se puede conectar una resistencia :math:`R_S` para medir la corriente del motor, que se obtiene simplemente dividiendo la tensión :math:`V_S` en la resistencia por el valor de la misma:

.. math::
    I_{motor} = \frac{V_S}{R_S}

Las conexiones del chip se muestran en la hoja de datos:

.. image:: /images/a4953_conexiones.png
    :width: 300px
    :align: center
    :alt: Conexiones del A4953


El esquema del circuito se hizo en `KiCad <http://www.kicad-pcb.org/>`_ y se simuló con `ngspice <http://ngspice.sourceforge.net/>`_. La tensión en la resistencia :math:`R_S`, :math:`V_S`, se simula usando una fuente de tensión (:math:`V_1` en el esquema) cuyo valor se hace variar entre los valores máximo y mínimo.

.. image:: /images/a4953_current_sensing.png
    :width: 70%
    :align: center
    :alt: Esquema

********
Cálculos
********

Resistencia :math:`R_S` 
=======================

En la hoja de datos del driver se especifica que el valor de la tensión en la :math:`R_S` debe ser menor, en valor absoluto, que :math:`0.5V`. Para calcular esta resistencia, se da una expresión en función de :math:`I_{max}`, la corriente máxima que puede circular por el motor, :math:`A`, una ganancia interna que se define con el valor :math:`10`, y :math:`V_{ref}`, la tensión que se aplique en el pin VREF:

.. math::
    I_{max} = \frac{V_{ref}}{A R_S}

Para una corriente máxima de :math:`1.1A` y :math:`V_{ref} = 5V`:

.. math::
    R_S = \frac{V_{ref}}{A  I_{max}} = \frac{5V}{10 \times 1.1A} = 0.45 \Omega

Eligiendo  :math:`R_S = 0.4 \Omega`, la tensión máxima que se tendrá en LSS es, en valor absoluto, :math:`V_S = 0.4 \Omega \times 1.1A = 0.44V < 0.5V`, por lo que la tensión en LSS estará comprendida entre :math:`-0.44V` y :math:`+0.44V`.



Adaptación de tensiones 
=======================

Este rango de tensiones será leído mediante el `conversor analógico/digital (ADC) <https://es.wikipedia.org/wiki/Conversor_de_se%C3%B1al_anal%C3%B3gica_a_digital>`_ de un `Arduino Nano <https://store.arduino.cc/arduino-nano>`_, que registra tensiones entre :math:`0` y :math:`5V` con una resolución de 10 bit (1024 cuentas), por lo que es necesario adaptar estas tensiones a valores adecuados para tener una resolución decente en la lectura. Idealmente, se intentaría usar todo el rango :math:`(0V, 5V)` del ADC, pero conviene dejar márgenes de seguridad para asegurar que no se alcanzan los máximos y para evitar que el amplificador operacional que hará la adaptación trabaje en los extremos (:math:`0V` y :math:`VCC`), lo que puede producir valores erróneos.

La adaptación de tensiones se realiza usando un `amplificador operacional LM358 <http://www.ti.com/lit/ds/symlink/lm358.pdf>`_ configurado como `amplificador no inversor <https://es.wikipedia.org/wiki/Amplificador_operacional#Amplificador_no_inversor>`_. En esta configuración, la ganancia es positiva, por lo que el primer obstáculo está en transformar el rango de tensiones en LSS para que todas sean positivas. Para ello se coloca en serie con la tensión :math:`V_S` un diodo alimentado desde el pin de :math:`3.3V` del Arduino que sumará su tensión de codo, unos :math:`0.66V` de promedio, al valor de :math:`V_S`, por lo que se pasa a tener un rango de tensiones :math:`(Vd-Vs, Vd+Vs) = (0.23V, 1.09V)`, todas positivas. Estas tensiones, :math:`V_M` en el esquema y en la gráfica, se pasan como entrada a la etapa amplificadora para adaptarlas al rango :math:`(0V, 5V)` del ADC.

Amplificador operacional
========================

La ganancia del amplificador no inversor viene dada, en este circuito, por

.. math::
    A_v = 1 + \frac{R_4}{R_2+R_3}

y la tensión en la salida será

.. math::
    V_{OUT} = A_v   V_M

Según las especificaciones del LM358, en la salida de este operacional se pueden obtener tensiones entre :math:`0V` y :math:`VCC-1.5V`, por lo que si fijamos un valor máximo aproximado de tensión en el ADC de :math:`4.5V` se tiene que VCC debe ser igual a :math:`6V`, que es una de las tensiones que se va a usar en la placa definitiva. Ese valor máximo también da un margen de seguridad en la tensión medida en el ADC.

Para obtener :math:`4.5V` en la salida cuando se tienen :math:`1.09V` enla entrada necesitamos una ganancia de tensión igual a

.. math::
    \frac{4.5V}{1.09V} = 4.13 = 1 + \frac{R_4}{R_2+R_3}

por lo que la relación :math:`\frac{R_4}{R_2+R_3}` tiene que ser menor o igual a :math:`3.13` para no sobrepasar los :math:`4.5V`.  Seleccionando :math:`R_2+R_3 = 110 \: k \Omega` y :math:`R_4 = 330 \:k \Omega` se obtiene una relación de :math:`3` y una ganancia :math:`A_v = 4`.

Con esta ganancia el rango de tensiones en la salida del amplificador será

.. math::
    V_{OUT} = (A_v \times 0.23, \: Av \times 1.09) = (0.90V, \: 4.35V)

que se corresponden con valores medidos en el ADC de :math:`(184, 890)`, utilizando un :math:`69 \%` del rango del ADC.


Simulación
==========

La gráfica siguiente muestra el resultado de la simulación para los parámetros :math:`V_S`, :math:`V_M` y :math:`V_{OUT}` cuando :math:`V_S` se hace variar entre :math:`-0.44V` y :math:`+0.44V`.

.. image:: /images/output.jpg
    :width: 50%
    :align: center
    :alt: Plot

La simulación devuelve los siguientes valores de las tensiones :math:`V_M`, tras sumar la tensión de codo del diodo, y :math:`V_{OUT}`, a la salida del amplificador, para los casos en que :math:`V_S` toma los valores mínimo, :math:`0` y máximo:


+---------------+--------------+-----------------+
| :math:`V_S`   | :math:`V_M`  | :math:`V_{OUT}` |
+===============+==============+=================+
| :math:`-0.44` | :math:`0.23` | :math:`0.90`    |
+---------------+--------------+-----------------+
| :math:`0.00`  | :math:`0.66` | :math:`2.63`    |
+---------------+--------------+-----------------+
| :math:`+0.44` | :math:`1.09` | :math:`4.35`    |
+---------------+--------------+-----------------+

Se puede ver cómo :math:`V_M` toma valores siempre positivos, por lo que no hay problemas en el amplificador. También se ve que la tensión a la salida está en el rango del conversor ACD.


*******************************
Resolución en corriente 
*******************************

Se puede calcular la resolución de la medida de corriente por el motor, es decir cuánto cambia la corriente por cada cuenta del ADC.

La tensión de salida del amplificador es:

.. math::
    V_{OUT} = A_v(R_S i_S + V_d)

siendo :math:`i_S` la corriente que circula por :math:`R_S` (se desprecia la corrientes que circula por el diodo, del orden de :math:`1 mA`) y :math:`V_d` la tensión de codo en el diodo.

Expresado como cuentas del ADC:

.. math::
    N = \frac{1024}{5}  V_{OUT}

Si tomamos dos medidas consecutivas del ADC, :math:`N1` y :math:`N2 = N1 + 1`:

.. math::
    N2 - N1 = \frac{1024}{5} Av (R_S i_2 + V_d) - \frac{1024}{5} Av (R_S i_1 + V_d)

Operando:

.. math::
    1 = \frac{1024}{5} Av R_S (i_2 - i_1)

y se tiene:

.. math::
    i2 - i1 = \Delta i = \frac{5}{1024 \: A_v R_S} = 3.05 \dfrac{mA}{cuenta}

Es decir, se tiene una resolución en la medida de corriente que circula por el motor de unos :math:`3 \: mA`.
