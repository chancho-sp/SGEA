# Módulos Odoo

Guía paso a paso para crear módulos en Odoo.

---

## 1. Manifest.py 

Definir el módulo, ejemplo:

```bash
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

```

---

## 2. Init.py

nombre_modulo/__init__.py (importar la carpeta)

```bash
from . import models
```

nombre_modulo/models/__init__.py (importar el archivo o archivos)

```bash
from . import models
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

class Clase(models.Model):
    _name = 'modulo.clase'
    _description = 'Clase'

    name = fields.Char(string='Nombre', required=True)
    precio = fields.Float(string='Precio')

    Clase_id = fields.One2many(
        comodel_name='modulo.Clase',
        inverse_name='Clase_id',
        string='Clase'
    )

    @api.depends('Clase_id.coste')
    def _compute_coste(self):
        for Clase in self:
            if modulo.Clase_id:
                modulo.Clase = Clase.Clase_id.precio * Clase.Clase_id.cantidad
            else:
                Clase.coste = 0

```

---

## 4. Crear permisos

security/ir.model.access.csv

```bash
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
access_modulo_clase_user,access.modulo.clase.user,model_modulo_clase,base.group_user,1,1,1,1
access_modulo_clase2_user,access.modulo.clase2.user,model_modulo_clase,base.group_user,1,1,1,1
```

model_modulo_modelo (por ejemplo model_videojuego_juego) debe llamarse obligatoriamente así, coincidiendo con el
nombre del modelo (videojuego.juego en este caso), si no no funcionará.

---

## 5. Crear vistas

views/vistas.xml

vista lista

```bash
<record id="view_modulo_clase_list" model="ir.ui.view">
    <field name="name">modulo.clase.list</field>
    <field name="model">modulo.clase</field>
    <field name="arch" type="xml">
        <list>
            <field name="name"/>
            <field name="precio"/>
        </list>
    </field>
</record>
```

vista formulario

```bash
<record id="view_modelo_form" model="ir.ui.view">
    <field name="name">modulo.clase.form</field>
    <field name="model">modulo.clase</field>
    <field name="arch" type="xml">
        <form>
            <sheet>
                <group>
                    <field name="name"/>
                    <field name="precio"/>
                    <field name="cantidad"/>
                    <field name="total" readonly="1"/>
                </group>
            </sheet>
        </form>
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
    <field name="res_model">modulo.clase</field>
    <field name="view_mode">list,form</field>
</record>

<menuitem id="menu_modulo_root"
          name="Módulo"
          sequence="10"
          groups="base.group_user"/>

<menuitem id="menu_clase"
          name="Clase"
          parent="menu_modulo_root"
          action="action_clase"
          sequence="10"
          groups="base.group_user"/>
```

Los menús pueden no verse si no tienen grupo. Sequence es el orden en que aparecen en el menú, cuanto más bajo el número antes aparece, 
si no ponemos nada aparece en el orden del XML. Grupos tambien se puede dejar vacío y funciona, normalmente.

---
