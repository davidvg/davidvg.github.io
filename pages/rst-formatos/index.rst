.. title: ReStructuredText - Formatos
.. slug: rst-formatos
.. date: 2019-09-23 20:00:23 UTC+02:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text


Formatos
########


Texto formateado
****************

- *Cursiva*: ``*cursiva*``
- **Negrita**: ``**negrita**``


Encabezados
***********

.. code-block:: rst

    ****** 
    título
    ****** 

    subtítulo
    #########

    sub-subtítulo
    *************


Enlaces
*******

Enlaces externos
----------------

- Url: ```<https://davidvg.github.io>`_``
- Url con etiqueta: ```Mi web <https://davidvg.github.io>`_``


Enlaces internos
----------------

Se pueden crear enlaces a puntos del documento usando etiquetas. Para crear una etiqueta:

::

    .. _etiqueta:


Posteriormente se pueden crear enlaces a esa etiqueta, de dos maneras:

::

    etiqueta_

    :ref:`etiqueta`
