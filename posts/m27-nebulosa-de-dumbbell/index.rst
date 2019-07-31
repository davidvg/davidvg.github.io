.. title: M27 - Nebulosa de Dumbbell
.. slug: m27-nebulosa-de-dumbbell
.. date: 2019-07-31 20:45:44 UTC+02:00
.. tags: nebulosa,dumbbell,messier,m27
.. category: astrofotografía
.. link: 
.. description: 
.. type: text
.. status:


El pasado fín de semana, aprovechando que daban buena noche, me escapé a León para probar la nueva cámara CCD, una `Atik 383L+ <https://www.atik-cameras.com/product/atik-383l-plus/>`_, de 8.3 Mpx y que con el C8 y la reductora de focal x0.63 me da un campo aproximado de :math:`48' \times 36'` (`minutos de arco <https://es.wikipedia.org/wiki/Minuto_de_arco>`_), bastante grande para una CCD comparado con los :math:`62' \times 42'` que obtenía con la Canon 50D.

Decidí probar con `M27 <https://duckduckgo.com/?q=nebulosa+dumbbell&ia=images&iax=images&atb=v136-1>`_, la `nebulosa planetaria <https://es.wikipedia.org/wiki/Nebulosa_planetaria>`_ Dumbbell, una "pelota" grande y luminosa que suele salir bien en las fotos.

La cámara es monocromo, para poder usar en el futuro `filtros de banda estrecha <https://duckduckgo.com/?q=astrophotography+narrow+band&atb=v136-1&iar=images&iax=images&ia=images>`_ para capturar emisiones de :math:`\text{H}_{\alpha}` (hidrógeno) o el :math:`\text{OIII}` (oxígeno)

|

.. image:: /images/m27_190728.jpg
    :width: 800px
    :align: center
    :alt: M27, nebulosa de Dumbbell.

Equipo
------

- Telescopio: Celestron C8 + reductora de focal x0.63.
- Montura SkyWatcher EQ6.
- Guiado: QHY5-IIM en tubo Lunático (60mm diámetro, 230mm focal)
- Cámara: Atik 383L+, refrigerada a :math:`-20 ^\circ C`.
- `Filtro de contaminación lumínica IDAS <https://sciencecenter.net/hutech/idas/lps.htm>`_.
- RaspberryPi con `INDI <https://www.indilib.org/>`_ para control de todo el equipo.

Datos de la imagen
------------------

- 12 tomas de 10 minutos de exposición (total acumulado: 2h)
- Bias: :math:`30 \times 0.001 \; \text{s}`
- Dark: :math:`10 \times 10 \; \text{min}`
- Flat: :math:`30 \times 1 \; \text{s}`, colocando un difusor en el tubo y disparando un flash.

Software
--------

- Control del telescopio, guiado y captura de imágenes: `KStars <https://edu.kde.org/kstars/>`_.
- Preprocesado, alineado, apilado y procesado básico de imágenes: `Siril <https://www.siril.org/>`_.
- Post procesado en `GIMP <https://www.gimp.org/>`_.
