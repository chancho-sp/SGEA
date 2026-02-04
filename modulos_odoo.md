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
access_academia_curso_user,access.academia.curso.user,model_academia_curso,base.group_user,1,1,1,1
access_academia_alumno_user,access.academia.alumno.user,model_academia_alumno,base.group_user,1,1,1,1
```

model_modulo_modelo (por ejemplo model_academia_curso) debe llamarse obligatoriamente así, coincidiendo con el
nombre del modelo (academia.curso en este caso), si no no funcionará.

---

## 5. Crear vistas

views/vistas.xml

vista lista

```bash
<record id="view_academia_curso_list" model="ir.ui.view">
    <field name="name">academia.curso.list</field>
    <field name="model">academia.curso</field>
    <field name="arch" type="xml">
        <tree>
            <field name="name"/>
            <field name="precio"/>
        </tree>
    </field>
</record>
```

vista formulario

```bash
<record id="view_modelo_form" model="ir.ui.view">
    <field name="name">modulo.modelo.form</field>
    <field name="model">modulo.modelo</field>
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

Ejemplo completo:

```bash
<?xml version="1.0" encoding="UTF-8"?>
<odoo>

    <!-- ===================== -->
    <!-- VISTAS CURSO -->
    <!-- ===================== -->

    <!-- Lista Curso -->
    <record id="view_academia_curso_list" model="ir.ui.view">
        <field name="name">academia.curso.list</field>
        <field name="model">academia.curso</field>
        <field name="arch" type="xml">
            <tree>
                <field name="name"/>
                <field name="precio"/>
            </tree>
        </field>
    </record>

    <!-- Formulario Curso -->
    <record id="view_academia_curso_form" model="ir.ui.view">
        <field name="name">academia.curso.form</field>
        <field name="model">academia.curso</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <group>
                        <field name="name"/>
                        <field name="precio"/>
                        <field name="alumno_ids">
                            <tree>
                                <field name="name"/>
                                <field name="edad"/>
                                <field name="coste"/>
                            </tree>
                        </field>
                    </group>
                </sheet>
            </form>
        </field>
    </record>

    <!-- Acción Curso -->
    <record id="action_academia_curso" model="ir.actions.act_window">
        <field name="name">Cursos</field>
        <field name="res_model">academia.curso</field>
        <field name="view_mode">tree,form</field>
    </record>

    <!-- ===================== -->
    <!-- VISTAS ALUMNO -->
    <!-- ===================== -->

    <!-- Lista Alumno -->
    <record id="view_academia_alumno_list" model="ir.ui.view">
        <field name="name">academia.alumno.list</field>
        <field name="model">academia.alumno</field>
        <field name="arch" type="xml">
            <tree>
                <field name="name"/>
                <field name="edad"/>
                <field name="coste"/>
                <field name="curso_id"/>
            </tree>
        </field>
    </record>

    <!-- Formulario Alumno -->
    <record id="view_academia_alumno_form" model="ir.ui.view">
        <field name="name">academia.alumno.form</field>
        <field name="model">academia.alumno</field>
        <field name="arch" type="xml">
            <form>
                <sheet>
                    <group>
                        <field name="name"/>
                        <field name="edad"/>
                        <field name="curso_id"/>
                        <field name="coste"/>
                    </group>
                </sheet>
            </form>
        </field>
    </record>

    <!-- Acción Alumno -->
    <record id="action_academia_alumno" model="ir.actions.act_window">
        <field name="name">Alumnos</field>
        <field name="res_model">academia.alumno</field>
        <field name="view_mode">tree,form</field>
    </record>

    <!-- ===================== -->
    <!-- MENÚ PRINCIPAL -->
    <!-- ===================== -->

    <menuitem id="menu_academia_root" name="Academia"/>

    <menuitem id="menu_academia_curso"
              name="Cursos"
              parent="menu_academia_root"
              action="action_academia_curso"/>

    <menuitem id="menu_academia_alumno"
              name="Alumnos"
              parent="menu_academia_root"
              action="action_academia_alumno"/>

</odoo>

```

vista de cursos con alumnos

```bash
<record id="view_academia_curso_form" model="ir.ui.view">
    <field name="name">academia.curso.form</field>
    <field name="model">academia.curso</field>
    <field name="arch" type="xml">
        <form>
            <sheet>
                <group>
                    <field name="name"/>
                    <field name="precio"/>
                </group>
                <notebook>
                    <page string="Alumnos">
                        <field name="alumno_ids">
                            <tree>
                                <field name="name"/>
                                <field name="edad"/>
                                <field name="coste"/>
                            </tree>
                        </field>
                    </page>
                </notebook>
            </sheet>
        </form>
    </field>
</record>

```
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
