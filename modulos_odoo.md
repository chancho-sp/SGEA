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

class Curso(models.Model):
    _name = 'academia.curso'
    _description = 'Curso'

    name = fields.Char(string='Nombre', required=True)
    precio = fields.Float(string='Precio')

    alumno_ids = fields.One2many(
        comodel_name='academia.alumno',
        inverse_name='curso_id',
        string='Alumnos'
    )


class Alumno(models.Model):
    _name = 'academia.alumno'
    _description = 'Alumno'

    name = fields.Char(string='Nombre', required=True)
    edad = fields.Integer(string='Edad')

    curso_id = fields.Many2one(
        comodel_name='academia.curso',
        string='Curso',
        required=True,
        ondelete='cascade'
    )

    coste = fields.Float(
        string='Coste del curso',
        compute='_compute_coste',
        store=True
    )

    @api.depends('curso_id.precio')
    def _compute_coste(self):
        for alumno in self:
            if alumno.curso_id:
                alumno.coste = alumno.curso_id.precio + 100
            else:
                alumno.coste = 0

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
