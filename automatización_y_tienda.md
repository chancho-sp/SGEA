# Memoria del Proyecto Odoo

Este documento recoge de forma detallada el trabajo realizado con **Odoo**, en particular como crear plantillas de correo personalizadas y automatizar acciones por un lado, 
y la creación de una tienda digital completa por el otro. 
---

# PARTE 1 – Crear Correos Personalizados y Automatizarlos

## 1. Automatización de Correos Electrónicos en Odoo

Los correos automatizados permiten enviar mensajes cuando ocurre un evento determinado dentro del sistema:

| Evento                    | Acción            | Destinatario  |
| ------------------------- | ----------------- | ------------- |
| Confirmación de pedido    | Enviar correo     | Cliente       |
| Registro de nuevo usuario | Enviar bienvenida | Usuario       |
| Cambio de estado de venta | Notificación      | Administrador |

---

## 2. Plantillas de correo

Cada plantilla define:

| Campo     | Descripción                                                        |
| --------- | ------------------------------------------------------------------ |
| Asunto    | Título del correo, puede incluir variables dinámicas               |
| Contenido | Mensaje principal con formato HTML o texto plano                   |
| Variables | Permiten insertar datos del sistema, por ejemplo {{ object.name }} |

Ejemplo conceptual:

```
📧 Asunto: Confirmación de pedido {{ object.name }}

Hola {{ object.partner_id.name }},

Tu pedido ha sido confirmado correctamente.
Gracias por confiar en nuestra tienda.
```
En este caso, los campos dinámicos dependen del modelo al que se aplique la plantilla de correo, por ejemplo el modelo producto, cliente, etc.

---

## 3. Automatización mediante acciones

Se utilizan **acciones automatizadas** para que los correos se envíen al producirse un evento:

```
[ Pedido confirmado ]
          ↓
[ Acción automática ]
          ↓
[ Enviar correo al cliente ]
```

Estas acciones pueden aplicarse a múltiples eventos y se configuran desde el menú técnico de Odoo, para que aparezcan hay que instalar el módulo de automatización,
de lo contrario no aparecerá la opción.

---

# PARTE 2 – Crear Tienda Digital con Odoo

## 1. Activación de aplicaciones necesarias

| Aplicación           | Función                                |
| -------------------- | -------------------------------------- |
| Sitio Web            | Base para crear páginas web            |
| Comercio Electrónico | Añade funcionalidades de tienda online |
| Inventario           | Gestión de stock                       |
| Ventas / Facturación | Gestión de pedidos y pagos             |

---

## 2. Creación del sitio web

![Ejemplo de editor de Odoo](https://odoocdn.com/openerp_website/static/src/img/apps/website/hero_image.webp)

El editor visual permite cambiar los estilos intuitivamente, sin tener que tocar el CSS de la página manualmente: texto, imágenes, botones y productos. Se pueden personalizar colores, tipografía y diseño de la página.
También se puede acceder al código CSS para mayor personalización, pero si cambiamos cosas ahí es posible que el editor visual no funcione correctamente después.

---

## 3. Creación de productos

| Campo       | Descripción                     |
| ----------- | ------------------------------- |
| Nombre      | Nombre del producto             |
| Precio      | Precio en la moneda configurada |
| Imagen      | Imagen principal del producto   |
| Descripción | Detalle de características      |
| Categoría   | Organización en la tienda       |
| Stock       | Cantidad disponible             |

Cada categoría puede tener sus subcategorías, y estas a su vez otras, haciéndo la organización tan compleja como queramos.
---

## 4. Gestión del carrito y proceso de compra

El flujo de compra se puede representar con un diagrama simple:

```
[ Cliente añade producto ]
          ↓
[ Carrito ]
          ↓
[ Checkout ]
          ↓
[ Confirmación de pedido ]
```

Hay que configurar los métodos de pago individualmente, nosotros solo hemos probado a configurar el método de pago de prueba en clase.

---
