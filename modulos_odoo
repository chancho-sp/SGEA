# Módulos Odoo

Guía paso a paso para crear módulos en Odoo.

---

## 1. Manifest.py 

Definir el módulo, ejemplo:

{
    'name': 'Nombre del módulo',
    'version': '1.0',
    'summary': 'Descripción breve',
    'category': 'Custom',
    'author': 'Autor',
    'depends': ['base'],
    'data': [
        'security/ir.model.access.csv',
        'views/vistas.xml',
        'views/menu.xml',
    ],
    'installable': True,
    'application': True,
}


---

## 2. Init.py

nombre_modulo/__init__.py

```bash
from . import models
```

nombre_modulo/models/__init__.py

```bash
from . import modelo
```

---

## 3. Crear el modelo

Crear un archivo por modelo:

```bash
models/modelo.py
```

Ejemplo:

```bash
from odoo import models, fields, api

class Modelo(models.Model):
    _name = 'modulo.modelo'
    _description = 'Modelo'

    name = fields.Char(string='Nombre', required=True)
    precio = fields.Float(string='Precio')
    cantidad = fields.Integer(string='Cantidad')

    total = fields.Float(
        string='Total',
        compute='_compute_total',
        store=True
    )

    @api.depends('precio', 'cantidad')
    def _compute_total(self):
        for record in self:
            record.total = record.precio * record.cantidad
```

---

## 4. Crear permisos

security/ir.model.access.csv

```bash
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_modelo_user,access.modelo.user,model_modulo_modelo,base.group_user,1,1,1,1
```

---

## 5. Crear vistas

views/vistas.xml

vista lista

```bash
<record id="view_modelo_list" model="ir.ui.view">
    <field name="name">modulo.modelo.list</field>
    <field name="model">modulo.modelo</field>
    <field name="arch" type="xml">
        <list>
            <field name="name"/>
            <field name="precio"/>
            <field name="cantidad"/>
            <field name="total"/>
        </list>
    </field>
</record>
```

vista formulario

```bash
<record id="view_modelo_list" model="ir.ui.view">
    <field name="name">modulo.modelo.list</field>
    <field name="model">modulo.modelo</field>
    <field name="arch" type="xml">
        <list>
            <field name="name"/>
            <field name="precio"/>
            <field name="cantidad"/>
            <field name="total"/>
        </list>
    </field>
</record>
```
Un solo nodo raíz (list, form, etc.)

---

## 6. Crear acciones y menús

views/menu.xml

```bash
<record id="action_modelo" model="ir.actions.act_window">
    <field name="name">Modelos</field>
    <field name="res_model">modulo.modelo</field>
    <field name="view_mode">list,form</field>
</record>

<menuitem id="menu_modulo_root"
          name="Mi Módulo"
          sequence="10"
          groups="base.group_user"/>

<menuitem id="menu_modelo"
          name="Modelos"
          parent="menu_modulo_root"
          action="action_modelo"
          sequence="10"
          groups="base.group_user"/>
```

Los menús pueden no verse si no tienen grupo.

---
