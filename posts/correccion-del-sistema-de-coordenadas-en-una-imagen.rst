.. title: Corrección del sistema de coordenadas en una imagen
.. slug: correccion-del-sistema-de-coordenadas-en-una-imagen
.. date: 2019-08-07 11:27:24 UTC+02:00
.. tags: astronomía,fotografía,wcs,coordenadas,astropy,python
.. category: astrofotografía
.. link: 
.. description: 
.. type: text
.. status: draft

En la **última sesión de astrofotografía** fueron surgiendo problemas que hicieron que sólo pudiera tomar 1 hora de exposición total en lugar de las 3 que quería tener. Uno de ellos tenía que ver con el **sistema de coordenadas**.

El formato **FITS** se emplea en astrofotografía para almacenar datos, sean imágenes o de otro tipo, junto con información referente a dichos datos. Esta información se almacena en la **cabecera** del archivo. Entre esos datos se incluyen el telescopio usado, la cámara, las coordenadas del lugar de observación, la fecha, las coordenadas celestes de la imagen, etc. El problema surgió con estas últimas, la **ascensión recta** y la **declinación**, que definen la posición en el cielo del centro de la imagen. Descubrí el problema al día siguiente de la captura, revisando las imágenes en **DS9**, cuando superpuse la rejilla de coordenadas para descargar datos de los servidores de astronomía a partir de las coordenadas:

|

.. image:: /images/stephan_wcs_orig.jpeg
    :width: 800px
    :align: center
    :alt: Sistema de coordenadas en la imagen original

|

Primera sorpresa: el sistema de coordenadas coloca el polo celeste dentro del encuadre, cuando el centro del mismo está a unos 55 grados de separación.

En seguida recordé que esa misma referencia de coordenadas era la que obtuvo **KStars** cuando alineé la montura a la estrella polar, para obtener el norte. La única explicación que se me ocurre es que, por algún motivo, **KStars** haya mezclado las calibraciones de coordenadas y haya asignado la referencia del alineamiento a la polar a las imágenes.

Toca revisar algunos datos de la cabecera::

    OBJCTRA = '23 59 30.04'        / Object J2000 RA in Hours                       
    OBJCTDEC= '89 53 29.45'        / Object J2000 DEC in Degrees                    
    RA      =         3.598752E+02 / Object J2000 RA in Degrees                     
    DEC     =         8.989151E+01 / Object J2000 DEC in Degrees                    
    CRVAL1  =     3.5987517284E+02 / CRVAL1                                         
    CRVAL2  =     8.9891513977E+01 / CRVAL1                                         
    CTYPE1  = 'RA---TAN'           / CTYPE1                                         
    CTYPE2  = 'DEC--TAN'           / CTYPE2                                         
    CRPIX1  =     1.6770000000E+03 / CRPIX1                                         
    CRPIX2  =     1.2645000000E+03 / CRPIX2                                         

Efectivamente, las coordenadas de la imagen son incorrectas, correspondiendo al polo celeste:

- **Ascensión recta**: se muestra en varios campos: ``RA``, ``OBJCTRA``, ``CRVAL1``, con valores _math:`23h59'30"` y :math:`359.8752 ^{\circ}`, siendo ambos equivalentes.
- **Declinación**: se muestra en los campos ``DEC``, ``OBJCTDEC`` y ``CRVAL2``, con valores :math:`89^\{circ}53'29"` y :math:`89.8915`.
- ``CRPIX1, CRPIX2``: define los píxeles que se toman como referencia para las coordenadas.
- ``CRVAL1, CRVAL2``: contiene el valor correspondiente de ``RA`` y ``DEC`` para los píxeles ``CRPIX1`` y ``CRPIX2`` respectivamente.
- ``CTYPE1, CTYPE2``: define qué coordenada corresponde a cada eje.

Para solucionarlo probé a modificar los valores de estos campos, usando el paquete de **Astropy** para Python, que incluye un módulo para visualizar y editar archivos FITS y sus cabeceras.

Para obtener las coordenadas reales con la máxima precisión posible subí una de las imágenes a la web de `Astrometry.net <nova.astrometry.net>`_, obteniendo el resultado siguiente:

**Captura Astrometry**

Con un sencillo script de Python se realizan las modificaciones::

    OBJCTRA = '23 59 30.04'        / Image center R.A. (hms)                        
    OBJCTDEC= '89 53 29.45'        / Image center declination (dms)                 
    RA      =           339.069444                                                  
    DEC     =           34.4466412                                                  
    CRVAL1  =           339.069444 / Axis1 reference value                          
    CRVAL2  =           34.4466412 / Axis2 reference value                          
    CTYPE1  = 'RA---TAN'           / Coordinate type for the first axis             
    CTYPE2  = 'DEC--TAN'           / Coordinate type for the second axis            
    CRPIX1  =           761.608477 / Axis1 reference pixel                          
    CRPIX2  =           857.687279 / Axis2 reference pixel                          

Con estas modificaciones el sistema de coordenadas debería ser correcto:

|

.. image:: /images/stephan_wcs_corr.jpeg
    :width: 800px
    :align: center
    :alt: Sistema de coordenadas corregido

|

Ahora sólo falta investigar por qué se produjo el error.
