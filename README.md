# Explotación Tecnológica en ERP/CRM


## Entregable 1: Generación del Informe Dinámico (QWeb XML)
Para empezar el primer entregable, hay que activar el **modo desarrollador** en **Odoo** y una vez dentro consultar las **vistas**. Para buscar la que queremos, filtramos en el buscador “report\_invoice” por **clave** y pinchamos en el **report\_invoice\_document**.
<img width="1919" height="913" alt="image" src="https://github.com/user-attachments/assets/0f89432b-cfbe-4585-85c4-ebb77950ee1f" />

Ahora hago este documento mío, es decir, lo convierto a **español** y **comento** lo que hace para que sea más legible y entenderlo a la perfección.


## Entregable 2: Interoperabilidad de Datos (Extracción JSON/XML)
En este apartado se pide un fragmento de factura electrónica simplificado que cumpla con las especificaciones del estándar internacional **UBL (Universal Business Language)**, incluyendo obligatoriamente los namespaces de componentes agregados (cac) y componentes básicos (cbc), así como el ID de personalización europeo compatible con la red PEPPOL.

Una vez realizada una pequeña **investigación**, empezamos con el código. Primero hay que declarar los tres ***namespaces*** obligatorios de UBL 2.1 en el elemento raíz. Luego añadimos el id que identifica al perfil PEPPOL, el id de la factura y la fecha (en este caso 2026-05-21) en elementos **cbc**. Luego, anidamos dentro del **cac** el nombre del emisor usando una jerarquía propia. Finalmente, queda un resultado reconocible por cualquier **sistema europeo de facturación electrónica**.
