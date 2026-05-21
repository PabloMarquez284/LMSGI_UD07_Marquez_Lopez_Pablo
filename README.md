# Explotación Tecnológica en ERP/CRM


## Entregable 1: Generación del Informe Dinámico (QWeb XML)
Para empezar el primer entregable, hay que activar el **modo desarrollador** en **Odoo** y una vez dentro consultar las **vistas**. Para buscar la que queremos, filtramos en el buscador “report\_invoice” por **clave** y pinchamos en el **report\_invoice\_document**.

<img width="1919" height="913" alt="image" src="https://github.com/user-attachments/assets/0f89432b-cfbe-4585-85c4-ebb77950ee1f" />

Ahora hago este documento mío, es decir, lo convierto a **español** y **comento** lo que hace para que sea más legible y entenderlo a la perfección.

Al investigar un poco sobre la estructura, instrucciones, datos y demás de este código, voy a hacer un breve análisis de las diferentes directivas o instrucciones de plantilla del motor QWeb que se utilizan:
- Directivas de iteración (t-foreach)  
- Condición (t-if)  
- Directiva de campos (t-field)

En este ejemplo podemos ver tanto directivas de iteración como una condición.

La directiva **t-foreach** funciona como un bucle que recorre una lista de elementos uno por uno. En el template de la factura, se usa **t-foreach="payments\_vals"** para recorrer todos los pagos asociados a esa factura, y con **t-as="payment\_vals"** se le da nombre a cada elemento individual dentro del bucle. Esto significa que Odoo repetirá automáticamente el bloque HTML interior tantas veces como pagos existan, generando una fila en la tabla por cada uno de ellos.  

La directiva **t-if** controla si un bloque se muestra o no dependiendo de si una condición es verdadera. Por ejemplo, **t-if="payment\_vals\['is\_refund'\]"** comprueba si el pago es un reembolso y, si lo es, muestra el texto **"Invertido en"**; en caso contrario, el **t-else** muestra **"Pagado en"**. Aquí muestro el ejemplo en la siguiente imagen:

<img width="1099" height="273" alt="image" src="https://github.com/user-attachments/assets/59ccb14c-b666-4d9a-9a47-77338fbcc30a" />


En este otro ejemplo podemos ver directivas de campos. 
La directiva **t-field** es la encargada de mostrar en el PDF el valor real de un campo del registro de Odoo, sustituyendo en tiempo de ejecución cualquier valor de previsualización que aparezca en el XML. En la imagén anterior hemos podemos ver que se hace uso de **t-out,** una directiva algo similar a t-field pero se usa cuando el dato no viene directamente de un campo ORM, sino de una expresión o diccionario Python, como **payment\_vals\['amount'\]**. Aquí muestro el ejemplo en la siguiente imagen:  

<img width="776" height="635" alt="image" src="https://github.com/user-attachments/assets/c1b661e9-000e-410d-927d-e0b1ba189208" />



## Entregable 2: Interoperabilidad de Datos (Extracción JSON/XML)
En este apartado se pide un fragmento de factura electrónica simplificado que cumpla con las especificaciones del estándar internacional **UBL (Universal Business Language)**, incluyendo obligatoriamente los namespaces de componentes agregados (cac) y componentes básicos (cbc), así como el ID de personalización europeo compatible con la red PEPPOL.

Una vez realizada una pequeña **investigación**, empezamos con el código. Primero hay que declarar los tres ***namespaces*** obligatorios de UBL 2.1 en el elemento raíz. Luego añadimos el id que identifica al perfil PEPPOL, el id de la factura y la fecha (en este caso 2026-05-21) en elementos **cbc**. Luego, anidamos dentro del **cac** el nombre del emisor usando una jerarquía propia. Finalmente, queda un resultado reconocible por cualquier **sistema europeo de facturación electrónica**.


## CONSULTAS IA:
**AGENTE:**  
Claude

**PROMPT:**

Estoy analizando el archivo "report\_invoice\_document", exportado de Odoo, y quiero que me expliques que uso y función tienen las siguientes estructuras en las dos imágenes que te he adjuntado:

* Directivas de iteración (t-foreach)  
* Condición (t-if)  
* Directiva de campos (t-field)

**RESPUESTA:**

# "**Análisis del template `report_invoice_document` de Odoo**

Estas estructuras son **directivas QWeb**, el motor de plantillas XML de Odoo. Te explico cada una con los ejemplos exactos de tus imágenes:

---

## **1\. `t-foreach` — Directiva de iteración**

\<t t-foreach="payments\_vals" t-as="payment\_vals"\>

**¿Qué hace?** Itera sobre una colección de datos, como un bucle `for`. Siempre acompaña a `t-as`, que define el nombre de la variable en cada iteración.

| Atributo | Valor | Significado |
| ----- | ----- | ----- |
| `t-foreach` | `payments_vals` | La lista a recorrer (todos los pagos de la factura) |
| `t-as` | `payment_vals` | Variable que representa cada pago individual |

**En contexto:** por cada pago registrado en la factura, Odoo renderiza una fila `<tr>` con su fecha y su importe. Si hay 3 pagos, se generan 3 filas.

---

## **2\. `t-if` / `t-else` — Condicionales**

Aparecen en ambas imágenes con dos patrones distintos:

### **Patrón A — Condicional sobre un valor (imagen 1\)**

\<t t-if="payment\_vals\['is\_refund'\]"\>Invertido en\</t\>

\<t t-else=""\>Pagado en\</t\>

Muestra **"Invertido en"** si el pago es un reembolso, y **"Pagado en"** en caso contrario.

### **Patrón B — Condicional sobre campos del objeto `o` (imagen 2\)**

\<div t-if="o.invoice\_date\_due and o.move\_type \== 'out\_invoice' 

     and o.state \== 'posted'" name="due\_date"\>

Este bloque solo se renderiza si se cumplen **tres condiciones simultáneas**:

* Existe fecha de vencimiento  
* Es una factura de cliente (`out_invoice`)  
* La factura está confirmada (`posted`)

Odoo usa `o` como objeto principal del reporte — en este caso, el registro `account.move` (la factura).

Otros ejemplos de la imagen 2 y su lógica:

| Condición | Bloque visible cuando... |
| ----- | ----- |
| `o.taxable_supply_date and o.show_taxable_supply_date` | Hay fecha de oferta sujeta a impuestos |
| `o.delivery_date` | Existe fecha de entrega |
| `o.invoice_origin` | La factura tiene un origen (ej. pedido de venta) |
| `o.partner_id.ref` | El cliente tiene código de referencia |
| `o.invoice_incoterm_id` | Se ha definido un Incoterm |

---

## **3\. `t-field` — Directiva de campo**

\<div t-field="o.invoice\_date\_due"\>2023-10-31\</div\>

\<div t-field="o.delivery\_date"\>2023-09-25\</div\>

\<div t-field="o.invoice\_origin"\>SO123\</div\>

**¿Qué hace?** Recupera y renderiza el valor de un campo del modelo directamente desde la base de datos de Odoo. El valor hardcodeado visible en el XML (`2023-10-31`, `SO123`…) es solo el **valor de previsualización** en el editor — en producción se sustituye dinámicamente.

### **Diferencia clave con `t-out`**

En la imagen 1 aparece `t-out`, que es similar pero con un matiz importante:

\<span t-out="payment\_vals\['amount'\]" 

      t-options="{'widget': 'monetary', 'display\_currency': o.currency\_id}"\>

      20.00

\</span\>

| Directiva | Uso típico | Aplica widgets |
| ----- | ----- | ----- |
| `t-field` | Campos de un objeto ORM (`o.campo`) | ✅ Sí |
| `t-out` | Expresiones o diccionarios Python | ✅ Sí (con `t-options`) |

En este caso, `t-options` le indica a Odoo que formatee el importe como **valor monetario** con la **moneda de la factura**.

---

## **Resumen visual del flujo**

t-foreach  →  itera los pagos de la factura

    └── t-if  →  decide si mostrar cada bloque según condiciones

            └── t-field / t-out  →  imprime el valor del campo formateado

Juntas, estas tres directivas permiten que una sola plantilla genere facturas completamente distintas según los datos de cada registro."
