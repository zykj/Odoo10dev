[源链接](https://www.odoo.com/documentation/10.0/howtos/backend.html)

注意：本文中的代码可能粘贴不全，有些地方 还需要对照原文。


## 目录

1. [启动/停止Odoo服务](#启动停止服务)
2. [创建Odoo模块](#创建模块)
3. [ORM对象关系映射](#对象关系映射)
4. [继承](#继承)

##　启动停止服务
Odoo以Web浏览器作为客户端，通过RPC访问Odoo服务。
业务逻辑和扩展通常在服务器端执行，也能在客户端得到支持（例如，交互式地图的数据呈现）。
只需要在shell中使用 `odoo-bin` （可能需要完整路径）就可以启动Odoo，通过在终端点击Ctrl+C两次来停止，或者中止kill对应的系统进程。



## 创建模块


服务器和客户端的代码都会打包为模块，可以任意加载到数据库中。
Odoo的模块不仅可以为系统增加新的业务逻辑，还可以改变或扩展已有的业务逻辑：可以增加您国家的会计规则，在Odoo中都通过模块来实现。

###　模块的组成

一个Odoo模块会包含下列元素：

Business objects 业务对象
以Python类的方式声明，这些资源会根据Odoo的配置自动保留。
Data files 数据文件
XML或CSV文件声明元数据metadata (视图或工作流), 配置数据 (模块参数), 演示数据和从web浏览器处理请求的Web控制器
静态web数据
web界面或者网站模块结构使用的图像, CSS或javascript文件
每个模块是一个目录包含了模块自己的目录。模块目录 可以通过在启动命令中增加 --addons-path 的选项来指定。

提示

大部分命令行的选项可以在配置文件中设置，模块通过它自身的manifest证明文件来声明。可以参考证明文件的文档

模块还是Python的package包，包含了__init__.py文件,导入模块中不同的Python文件。

例如如果模块有一个mymodule.py文件，在__init__.py中会有这样的内容:

```python
from . import mymodule
```

Odoo有个帮助我们创建新模块的机制，odoo-bin有一个 subcommand scaffold来创建空的新模块：

```
$ odoo-bin scaffold <module name> <where to put it>
```

上述命令会为您的模块创建子目录，并自动创建一串模块的标准文件。包含了注释代码或XML. 本指南会解释这些文件的用法。

练习

创建模块
使用命令行创建一个空的模块：Open Academy,并安装它
odoo-bin scaffold openacademy addons. （会创建一个openacadmy的模块，位于addons目录）
调整模块中的manifest证明文件.不要打扰其它文件。

openacademy/__manifest__.py

```python

# -*- coding: utf-8 -*-
{
    'name': "Open Academy",

    'summary': """Manage trainings""",

    'description': """
        Open Academy module for managing trainings:
            - training courses
            - training sessions
            - attendees registration
    """,

    'author': "My Company",
    'website': "http://www.yourcompany.com",

    # 分类用于在模块列表中筛选模块
    # 完整分类列表： https://github.com/odoo/odoo/blob/master/odoo/addons/base/module/module_data.xml
    # 
    'category': 'Test',
    'version': '0.1',

    # 用于保证此模块正常工作的必要模块
    'depends': ['base'],

    # 安装时加载的模块
    'data': [
        # 'security/ir.model.access.csv',
        'templates.xml',
    ],
    # 只会在勾选演示时加载
    'demo': [
        'demo.xml',
    ],
}
```
openacademy/__init__.py

```python
# -*- coding: utf-8 -*-
from . import controllers
from . import models
```
openacademy/controllers.py

```python
# -*- coding: utf-8 -*-
from odoo import http

# class Openacademy(http.Controller):
#     @http.route('/openacademy/openacademy/', auth='public')
#     def index(self, **kw):
#         return "Hello, world"

#     @http.route('/openacademy/openacademy/objects/', auth='public')
#     def list(self, **kw):
#         return http.request.render('openacademy.listing', {
#             'root': '/openacademy/openacademy',
#             'objects': http.request.env['openacademy.openacademy'].search([]),
#         })

#     @http.route('/openacademy/openacademy/objects/<model("openacademy.openacademy"):obj>/', auth='public')
#     def object(self, obj, **kw):
#         return http.request.render('openacademy.object', {
#             'object': obj
#         })
```
openacademy/demo.xml

<odoo>
    <data>
        <!--  -->
        <!--   <record id="object0" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 0</field> -->
        <!--   </record> -->
        <!--  -->
        <!--   <record id="object1" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 1</field> -->
        <!--   </record> -->
        <!--  -->
        <!--   <record id="object2" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 2</field> -->
        <!--   </record> -->
        <!--  -->
        <!--   <record id="object3" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 3</field> -->
        <!--   </record> -->
        <!--  -->
        <!--   <record id="object4" model="openacademy.openacademy"> -->
        <!--     <field name="name">Object 4</field> -->
        <!--   </record> -->
        <!--  -->
    </data>
</odoo>

openacademy/models.py

```python
# -*- coding: utf-8 -*-

from odoo import models, fields, api

# class openacademy(models.Model):
#     _name = 'openacademy.openacademy'

#     name = fields.Char()
```

openacademy/security/ir.model.access.csv

```
id,name,model_id/id,group_id/id,perm_read,perm_write,perm_create,perm_unlink
access_openacademy_openacademy,openacademy.openacademy,model_openacademy_openacademy,,1,0,0,0
```

openacademy/templates.xml

```xml
<odoo>
    <data>
        <!-- <template id="listing"> -->
        <!--   <ul> -->
        <!--     <li t-foreach="objects" t-as="object"> -->
        <!--       <a t-attf-href="{{ root }}/objects/{{ object.id }}"> -->
        <!--         <t t-esc="object.display_name"/> -->
        <!--       </a> -->
        <!--     </li> -->
        <!--   </ul> -->
        <!-- </template> -->
        <!-- <template id="object"> -->
        <!--   <h1><t t-esc="object.display_name"/></h1> -->
        <!--   <dl> -->
        <!--     <t t-foreach="object._fields" t-as="field"> -->
        <!--       <dt><t t-esc="field"/></dt> -->
        <!--       <dd><t t-esc="object[field]"/></dd> -->
        <!--     </t> -->
        <!--   </dl> -->
        <!-- </template> -->
    </data>
</odoo>
```

**[⬆ 回到目录](#目录)**

## 对象关系映射

Odoo中有个关键组件是ORM层。它让我们不用去写大部分SQL并提供了扩展性和安全性。

业务对象以Pyhon类声明，扩展模型将它们集成到自动持久的系统中。Business objects are declared as Python classes extending Model which integrates them into the automated persistence system.

可以通过在定义中设置多个属性来配置模型。最重要的属性是 _name 它是必填项，用来定义在系统中的模型名。下面是定义一个完整模型的最小化代码 :

```python
from odoo import models
class MinimalModel(models.Model):
    _name = 'test.model'
```


### 模型字段
字段用来定义模型的存储及位置。以模型类上的属性定义字段。Fields are used to define what the model can store and where. Fields are defined as attributes on the model class:

```python
from odoo import models, fields

class LessMinimalModel(models.Model):
    _name = 'test.model2'

    name = fields.Char()
```

### 常见属性
和模型一样，通过以参数传递配置属性，字段也可以被配置，

```python
name = field.Char(required=True)
```

有些属性能用于所有字段，下面是最常见的一些字段可用的属性：

- string (unicode, default: field's name)
UI用户界面中的字段标注（）The label of the field in UI (visible by users).
- required (bool, default: False)
如果为真，此字段不能为空，当创建记录时，必须给一个值，或有一个默认值。
- help (unicode, default: '')
Long-form, 提供UI用户界面中的帮助提示 .
- index (bool, default: False)
为此列在数据库上创建索引。Requests that Odoo create a database index on the column.

简单字段Simple fields
字段有2个大类： "简单simple" 字段将原始值直接存储在模型的表中，"relational关系" 字段链接记录 (在同一个模型或不同的模型中

简单字段的例子有布尔，日期，文本。

保留字段Reserved fields
Odoo会为所有模型创建一些字段，这些字段通过系统管理，如果有必要可以被读出但不应被写入。 

id (Id)
用于模型中记录的唯一标识The unique identifier for a record in its model.
create_date (Datetime)
记录的创建时间Creation date of the record.
create_uid (Many2one)
创建记录的人User who created the record.
write_date (Datetime)
记录最新的更新日期Last modification date of the record.
write_uid (Many2one)
最新更新纪录的人user who last modified the record.

特殊字段Special fields
By default,在所有模型中Odoo需要一个name名称字段用于显示和搜索。可以通过设置_rec_name 来覆盖这个名称字段.

练习

定义一个模型
在openacademy模块中定义一个新的数据模型Course. course有标题和描述. 其中标题是必须的。
编辑 openacademy/models/models.py 以包括类Course.

```python
from odoo import models, fields, api

class Course(models.Model):
    _name = 'openacademy.course'

    name = fields.Char(string="Title", required=True)
    description = fields.Text()
```

数据文件

Odoo是高度数据驱动的系统. 虽然行为通过Python代码定义，模块值的部分则在加载时的数据文件中。

提示

some modules exist solely to add data into Odoo
Module data is declared via data files, XML 文件的 `<record>` 元素. 每个 `<record> ` 元素都会创建或更新数据记录 。

```xml
<odoo>
    <data>
        <record model="{model name}" id="{record identifier}">
            <field name="{a field name}">{a value}</field>
        </record>
    </data>
</odoo>
```

model模型是Odoo中记录所属的模型，id是外部标识, 它允许referring到记录 (无需知道它的数据库中的标识).
`<field> ` 元素有个name名称,是字段在模型中的名称 (e.g. 例如description描述). Their body主体是字段的值.
数据文件需要在证明文件中声明才会被加载, 在 'data' list列表中声明 (data键中的数据总是会被加载) 或者在'demo' list (只会在勾选了演示数据的数据库实例中加载).

练习

定义演示数据文件
为Courses模型创建演示文件包含一些演示课程.
编辑 openacademy/demo/demo.xml 文件：

```xml
<odoo>
    <data>
        <record model="openacademy.course" id="course0">
            <field name="name">Course 0</field>
            <field name="description">Course 0's description

Can have multiple lines
            </field>
        </record>
        <record model="openacademy.course" id="course1">
            <field name="name">Course 1</field>
            <!-- no description for this one -->
        </record>
        <record model="openacademy.course" id="course2">
            <field name="name">Course 2</field>
            <field name="description">Course 2's description</field>
        </record>
    </data>
</odoo>
```

> 效果是创建了3条记录，记录的name值分别为Course 0，Course 1，Course 2，0和2，还有描述值。

动作和菜单
动作和菜单是数据库中常见的记录，常通过数据文件声明。动作可以通过三种方法触发 :

- 通过点击菜单 (链接到指定的动作)
- 通过点击视图上的按钮 (连接到动作)
- 作为对象的上下文动作
由于菜单相对复杂 因此有一个 `<menuitem> `快捷键， 声明用于ir.ui.menu 模型，并连接到对应的动作 。

```xml
<record model="ir.actions.act_window" id="action_list_ideas">
    <field name="name">Ideas</field>
    <field name="res_model">idea.idea</field>
    <field name="view_mode">tree,form</field>
</record>
<menuitem id="menu_ideas" parent="menu_root" name="Ideas" sequence="10"
          action="action_list_ideas"/>
```

提醒

在XML文件中动作必须在其对应的菜单之前声明，
数据文件按会按先后顺序执行, 在数据库中动作的id要在菜单之前呈现。

练习

定义新的菜单项
在OpenAcademy菜单下定义新的菜单来访问课程. 用户能显示所有课程的列表
创建/修改课程
创建 openacademy/views/openacademy.xml 包含菜单以及菜单触发的动作
增加它到 openacademy/__manifest__.py 的data键中

openacademy/__manifest__.py

```python
    'data': [
        # 'security/ir.model.access.csv',
        'templates.xml',
        'views/openacademy.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
],
```

openacademy/views/openacademy.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<odoo>
    <data>
        <!-- 窗口动作 -->
        <!--
            下面的标签是用于The following tag is an action definition for a "窗口动作",
            这个动作会打开一个或一组视图
        -->
        <record model="ir.actions.act_window" id="course_list_action">
            <field name="name">Courses</field>
            <field name="res_model">openacademy.course</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
            <field name="help" type="html">
                <p class="oe_view_nocontent_create">Create the first course
                </p>
            </field>
        </record>

        <!-- 顶级菜单: 没有父级 -->
        <menuitem id="main_openacademy_menu" name="Open Academy"/>
        <!-- A first level in the left side menu is needed
             before using action= attribute -->
        <menuitem id="openacademy_menu" name="Open Academy"
                  parent="main_openacademy_menu"/>
        <!-- the following menuitem should appear *after*
             its parent openacademy_menu and *after* its
             action course_list_action -->
        <menuitem id="courses_menu" name="Courses" parent="openacademy_menu"
                  action="course_list_action"/>
        <!-- Full id location:
             action="openacademy.course_list_action"
             It is not required when it is the same module -->
    </data>
</odoo>
```

基础视图

视图定义了显示模型记录的方式。 每种类型的视图都表示可视化的模式（记录列表，其聚合图，...）。 视图可以通过其类型（例如，合作伙伴列表）或特别通过其ID来一般请求。 对于通用请求，将使用具有正确类型和最低优先级的视图（因此每种类型的最低优先级视图是该类型的默认视图）。

视图继承允许更改其他地方声明的视图（添加或删除内容）。

通用视图声明
视图被声明为ir.ui.view模型的记录。 视图类型由arch字段的根元素默示：

```xml

<record model="ir.ui.view" id="view_id">
    <field name="name">view.name</field>
    <field name="model">object_name</field>
    <field name="priority" eval="16"/>
    <field name="arch" type="xml">
        <!-- view content: <form>, <tree>, <graph>, ... -->
    </field>
</record>
```

提示：

视图的内容是XML.
arch字段必须以type="xml"声明才能被正确解析。 

列表视图
树形视图，也成为列表视图Tree views, also called list views,以表格显示记录

根元素为`<tree>` . 最简单的树形视图只是列出要在表中显示的所有字段（每个字段为一列）:

```xml
<tree string="Idea list">
    <field name="name"/>
    <field name="inventor_id"/>
</tree>
```

表单视图

表单用于创建和编辑单条记录。

根元素为 `<form>` . 它们由高级结构元素（组，notebooks）和交互元素（按钮和字段）组成：

```xml
<form string="Idea form">
    <group colspan="4">
        <group colspan="2" col="2">
            <separator string="General stuff" colspan="2"/>
            <field name="name"/>
            <field name="inventor_id"/>
        </group>

        <group colspan="2" col="2">
            <separator string="Dates" colspan="2"/>
            <field name="active"/>
            <field name="invent_date" readonly="1"/>
        </group>

        <notebook colspan="4">
            <page string="Description">
                <field name="description" nolabel="1"/>
            </page>
        </notebook>

        <field name="state"/>
    </group>
</form>
```

练习

使用XML自定义表单视图
为Course对象创建表单视图。显示的数据是：课程的名称和描述.

openacademy/views/openacademy.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<odoo>
    <data>
        <record model="ir.ui.view" id="course_form_view">
            <field name="name">course.form</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <form string="Course Form">
                    <sheet>
                        <group>
                            <field name="name"/>
                            <field name="description"/>
                        </group>
                    </sheet>
                </form>
            </field>
        </record>

        <!-- window action -->
        <!--
            The following tag is an action definition for a "window action",
-->

```

练习

Notebooks

在Course课程的表单视图中，把描述字段放到tab中，这样便于日后增加包含额外信息的其它tab.:

修改 openacademy/views/openacademy.xml 文件：

```xml
                    <sheet>
                        <group>
                            <field name="name"/>
                        </group>
                        <notebook>
                            <page string="Description">
                                <field name="description"/>
                            </page>
                            <page string="About">
                                This is an example of notebooks
                            </page>
                        </notebook>
                    </sheet>
                </form>
            </field>
```

Form views can also use plain HTML for more flexible layouts:

```xml
<form string="Idea Form">
    <header>
        <button string="Confirm" type="object" name="action_confirm"
                states="draft" class="oe_highlight" />
        <button string="Mark as done" type="object" name="action_done"
                states="confirmed" class="oe_highlight"/>
        <button string="Reset to draft" type="object" name="action_draft"
                states="confirmed,done" />
        <field name="state" widget="statusbar"/>
    </header>
    <sheet>
        <div class="oe_title">
            <label for="name" class="oe_edit_only" string="Idea Name" />
            <h1><field name="name" /></h1>
        </div>
        <separator string="General" colspan="2" />
        <group colspan="2" col="2">
            <field name="description" placeholder="Idea description..." />
        </group>
    </sheet>
</form>
```

搜索视图

搜索视图自定义与列表视图（和其他聚合视图）关联的搜索字段 。它们的根元素是 `<search>`  由定义可搜索那些字段组成:

```xml
<search>
    <field name="name"/>
    <field name="inventor_id"/>
</search>
```

如果没有定义搜索视图，则只能搜索名称字段。

练习

搜索courses
允许按标题或者描述搜索courses课程。

openacademy/views/openacademy.xml


```xml
        <record model="ir.ui.view" id="course_search_view">
            <field name="name">course.search</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <search>
                    <field name="name"/>
                    <field name="description"/>
                </search>
            </field>
        </record>

        <!-- window action -->
        <!--
            The following tag is an action definition for a "window action",
-->
```

模型间关系

来自模型的记录可能与另一个模型的记录有关。 例如，销售订单记录与包含客户端数据的客户端记录有关; 它也与其销售订单行记录有关。

练习

创建会话模型session model
在Open Academy中,我们考虑到: 在一定时间内为一定的听众教授课程而产生会话。
创建会话模型。会话有名称，开始日期，持续时间和席位. 并增加动作和菜单。
在 openacademy/models/models.py 创建class Session.
在 openacademy/view/openacademy.xml 增加对session会话对象的访问 

openacademy/models.py文件：

```python
    name = fields.Char(string="Title", required=True)
    description = fields.Text()


class Session(models.Model):
    _name = 'openacademy.session'

    name = fields.Char(required=True)
    start_date = fields.Date()
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")
```

openacademy/views/openacademy.xml 文件

```xml
        <!-- Full id location:
             action="openacademy.course_list_action"
             It is not required when it is the same module -->

        <!-- session form view -->
        <record model="ir.ui.view" id="session_form_view">
            <field name="name">session.form</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <form string="Session Form">
                    <sheet>
                        <group>
                            <field name="name"/>
                            <field name="start_date"/>
                            <field name="duration"/>
                            <field name="seats"/>
                        </group>
                    </sheet>
                </form>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
                  parent="openacademy_menu"
                  action="session_list_action"/>
    </data>
</odoo>

```

备注：

digits=(6, 2) 指定浮点数的精度: 6 是总位数, 而2是逗号后面的位数。 请注意，这样设置，逗号之前的最大位数就为4。

关系字段Relational fields
关系字段链接相同模型中或者不同模型中的记录，Relational fields link records, either of the same model (hierarchies) or between different models.

关系字段的类型有:

多对一Many2one(other_model, ondelete='set null')
到其它模型的简单链接 A simple link to an other object:

```python
print foo.other_id.name
```

其他

外键foreign keys
一对多One2many(other_model, related_field)
虚拟关系，Many2one的对应关系。A virtual relationship, inverse of a Many2one.  One2many表现为记录的容器，访问它相当于访问一组记录（可能为空） behaves as a container of records, accessing it results in a (possibly empty) set of records:

```python
for other in foo.other_ids:
    print other.name
Danger
```

由于One2many一对多是虚拟关系，必须在other_model中有一个Many2one多对一的字段，它的名称必须是related_field Many2many(other_model)
双向多重关系，一方的任何记录可以与另一方的任何数量的记录相关。 作为记录的容器，访问它也会导致一组可能为空的记录集：Bidirectional multiple relationship, any record on one side can be related to any number of records on the other side. Behaves as a container of records, accessing it also results in a possibly empty set of records:

```
for other in foo.other_ids:
    print other.name
Exercise
```

多对一关系Many2one relations
使用many2one多对一字段, 修改Course课程和Session会话模型反映它们和其他模型之间的关系:
一个课程有一个责任用户；此责任用户字段的值是系统内建模型res.users的一条记录。
一个会话有一个导师; 此字段的值是内建模型res.partner的一条记录。
一个会话和一个课程关联；其值是模型openacademy.course的一条记录，而且此字段是必填项.

调整视图.

为模型增加相关的Many2one字段并增加他们到视图中。

openacademy/models.py

```python
    name = fields.Char(string="Title", required=True)
    description = fields.Text()

    responsible_id = fields.Many2one('res.users',
        ondelete='set null', string="Responsible", index=True)


class Session(models.Model):
    _name = 'openacademy.session'
    start_date = fields.Date()
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")

    instructor_id = fields.Many2one('res.partner', string="Instructor")
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
```

openacademy/views/openacademy.xml

```xml
                    <sheet>
                        <group>
                            <field name="name"/>
                            <field name="responsible_id"/>
                        </group>
                        <notebook>
                            <page string="Description">
            </field>
        </record>

        <!-- override the automatically generated list view for courses -->
        <record model="ir.ui.view" id="course_tree_view">
            <field name="name">course.tree</field>
            <field name="model">openacademy.course</field>
            <field name="arch" type="xml">
                <tree string="Course Tree">
                    <field name="name"/>
                    <field name="responsible_id"/>
                </tree>
            </field>
        </record>

        <!-- window action -->
        <!--
            The following tag is an action definition for a "window action",
                <form string="Session Form">
                    <sheet>
                        <group>
                            <group string="General">
                                <field name="course_id"/>
                                <field name="name"/>
                                <field name="instructor_id"/>
                            </group>
                            <group string="Schedule">
                                <field name="start_date"/>
                                <field name="duration"/>
                                <field name="seats"/>
                            </group>
                        </group>
                    </sheet>
                </form>
            </field>
        </record>

        <!-- session tree/list view -->
        <record model="ir.ui.view" id="session_tree_view">
            <field name="name">session.tree</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <tree string="Session Tree">
                    <field name="name"/>
                    <field name="course_id"/>
                </tree>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
        </record>
```

练习Exercise

one2many关系的对应
使用对应关系字段one2many,Using the inverse relational field one2many,反映课程和会话直接的关系.修改课程Course class增加字段到course课程的表单视图。

openacademy/models.py

```python
    responsible_id = fields.Many2one('res.users',
        ondelete='set null', string="Responsible", index=True)
    session_ids = fields.One2many(
        'openacademy.session', 'course_id', string="Sessions")


class Session(models.Model):
```

openacademy/views/openacademy.xml

```xml
                        
                    <sheet>
                        <notebook>
                            <page string="Description">
                                <field name="description"/>
                            </page>
                            <page string="Sessions">
                                <field name="session_ids">
                                    <tree string="Registered sessions">
                                        <field name="name"/>
                                        <field name="instructor_id"/>
                                    </tree>
                                </field>
                            </page>
                        </notebook>
                    </sheet>
```

练习

Multiple many2many relations

使用关系字段many2many, 修改Session model模型关联将每个会话与一组与会者相关联. 与会者用res.partner的记录表示，并调整对应的视图.
修改Session class, 增加字段到表单视图。

openacademy/models.py

```python
    instructor_id = fields.Many2one('res.partner', string="Instructor")
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
```
openacademy/views/openacademy.xml  （注意，有些代码可能要参考看下官方原文，这里贴的不一定完整）

```xml
                                <field name="seats"/>
                            </group>
                        </group>
                        <label for="attendee_ids"/>
                        <field name="attendee_ids"/>
                    </sheet>
                </form>
            
```

**[⬆ 回到目录](#目录)**

## 继承
模型继承Model inheritance
Odoo提供了2种继承机制以模块化的方式来扩展现有模型。

第一种继承机制允许模块修改其他模块中的模型定义:


为模型增加字段,
覆盖模型中的字段定义,
增加模型约束,
为模型增加方法,
覆盖模型已有的方法.

第二种继承机制 (delegation) 能链接一个模型中所有的记录到父模型的记录，并提供对父记录的字段的透明访问。


其他

_inherit
_inherits

视图继承

替代直接修改现有的视图（覆盖他们）， Odoo提供了视图继承的方案， 其中子视图“扩展”视图应用于根视图之上, 可以从它们的父级增加或移除内容.

扩展视图通过使用inherit_id字段来引用它们的父级，它的arch字段由任意数量的xpath元素组成，选择和更改其父视图的内容:

```xml
<!-- improved idea categories list -->
<record id="idea_category_list2" model="ir.ui.view">
    <field name="name">id.category.list2</field>
    <field name="model">idea.category</field>
    <field name="inherit_id" ref="id_category_list"/>
    <field name="arch" type="xml">
        <!-- find field description and add the field
             idea_ids after it -->
        <xpath expr="//field[@name='description']" position="after">
          <field name="idea_ids" string="Number of ideas"/>
        </xpath>
    </field>
</record>
```

expr

XPath表达式选择父视图中的单一元素。如果没有元素匹配或者有多个位置则会引发错误。

Operation to apply to the matched element:

inside
增加 xpath的主体 到匹配元素的结尾
replace
将匹配的元素替换为xpath的正文，使用原始元素替换新主体中的$ 0节点
before
在匹配的元素之前插入xpath中的主体
after
在匹配的元素之后插入xpath中的主体
attributes
使用xpath的主体中的指定属性元素来更改匹配元素的属性
alters the attributes of the matched element using special attribute elements in the xpath's body

提示

当匹配了单一的元素后，position属性可以直接在发现的元素上设置.两种继承会有相同的结果。

```xml
<xpath expr="//field[@name='description']" position="after">
    <field name="idea_ids" />
</xpath>

<field name="description" position="after">
    <field name="idea_ids" />
</field>
```

练习

改变现有内容

使用模型继承, 修改已有Partner模型增加一个instructor布尔字段,和一个many2many字段对应 session-partner关系。
使用视图继承，在partner业务伙伴表单视图，显示这些字段。

提示

是时机介绍开发者模式来检查视图，找到它的external ID外部ID,以及放置新字段的位置。
创建 openacademy/models/partner.py 并在 __init__.py 中import它，
创建 openacademy/views/partner.xml 并把它增加到 __manifest__.py 中
openacademy/__init__.py

```python
# -*- coding: utf-8 -*-
from . import controllers
from . import models
from . import partner
```

openacademy/__manifest__.py

```python
        # 'security/ir.model.access.csv',
        'templates.xml',
        'views/openacademy.xml',
        'views/partner.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
    ],
```
openacademy/partner.py

```python
# -*- coding: utf-8 -*-
from odoo import fields, models

class Partner(models.Model):
    _inherit = 'res.partner'

    # Add a new column to the res.partner model, by default partners are not
    # instructors
    instructor = fields.Boolean("Instructor", default=False)

    session_ids = fields.Many2many('openacademy.session',
        string="Attended Sessions", readonly=True)
```


openacademy/views/partner.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <odoo>
    <data>
        <!-- Add instructor field to existing view -->
        <record model="ir.ui.view" id="partner_instructor_form_view">
            <field name="name">partner.instructor</field>
            <field name="model">res.partner</field>
            <field name="inherit_id" ref="base.view_partner_form"/>
            <field name="arch" type="xml">
                <notebook position="inside">
                    <page string="Sessions">
                        <group>
                            <field name="instructor"/>
                            <field name="session_ids"/>
                        </group>
                    </page>
                </notebook>
            </field>
        </record>

        <record model="ir.actions.act_window" id="contact_list_action">
            <field name="name">Contacts</field>
            <field name="res_model">res.partner</field>
            <field name="view_mode">tree,form</field>
        </record>
        <menuitem id="configuration_menu" name="Configuration"
                  parent="main_openacademy_menu"/>
        <menuitem id="contact_menu" name="Contacts"
                  parent="configuration_menu"
                  action="contact_list_action"/>
    </data>
</odoo>
```

域Domains

在Odoo里, Domains域是记录 条件的值  are values that encode conditions on records. domain域是用于选择模型记录子集的标准列表.每个条件是一个包含字段名以及操作和值的triple元祖.

例如：在产品模型中，要选择所有的单价超过1000的服务类：

```
[('product_type', '=', 'service'), ('unit_price', '>', 1000)]
```

默认条件为AND.By default criteria are combined with an implicit AND. 可以在domain里使用逻辑运算符 & (AND和), | (OR或) 以及 ! (NOT否) . 它们会用于Domain的开始位置。 (操作符被插入到其参数之前而不是之间). 比如要选择 "是服务类的产品或者 单价不是在 1000到2000之间的产品” :

```
['|',
    ('product_type', '=', 'service'),
    '!', '&',
        ('unit_price', '>=', 1000),
        ('unit_price', '<', 2000)]
```

domain参数可用于关系型字段来筛选对应的记录。 parameter can be added to relational fields to limit valid records for the relation when trying to select records in the client interface.

练习

Domains on relational fields

为会话选择导师时，限制选择instructor设置为True的partners ：

openacademy/models.py

```python
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")

    instructor_id = fields.Many2one('res.partner', string="Instructor",
        domain=[('instructor', '=', True)])
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
```

Note

A domain declared as a literal list is evaluated server-side and can't refer to dynamic values on the right-hand side, a domain declared as a string is evaluated client-side and allows field names on the right-hand side
Exercise

More complex domains
Create new partner categories Teacher / Level 1 and Teacher / Level 2. The instructor for a session can be either an instructor or a teacher (of any level).
Modify the Session model's domain
Modify openacademy/view/partner.xml to get access to Partner categories:
openacademy/models.py
    seats = fields.Integer(string="Number of seats")

    instructor_id = fields.Many2one('res.partner', string="Instructor",
        domain=['|', ('instructor', '=', True),
                     ('category_id.name', 'ilike', "Teacher")])
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
openacademy/views/partner.xml
        <menuitem id="contact_menu" name="Contacts"
                  parent="configuration_menu"
                  action="contact_list_action"/>

        <record model="ir.actions.act_window" id="contact_cat_list_action">
            <field name="name">Contact Tags</field>
            <field name="res_model">res.partner.category</field>
            <field name="view_mode">tree,form</field>
        </record>
        <menuitem id="contact_cat_menu" name="Contact Tags"
                  parent="configuration_menu"
                  action="contact_cat_list_action"/>

        <record model="res.partner.category" id="teacher1">
            <field name="name">Teacher / Level 1</field>
        </record>
        <record model="res.partner.category" id="teacher2">
            <field name="name">Teacher / Level 2</field>
        </record>
    </data>
</odoo>
Computed fields and default values
So far fields have been stored directly in and retrieved directly from the database. Fields can also be computed. In that case, the field's value is not retrieved from the database but computed on-the-fly by calling a method of the model.

To create a computed field, create a field and set its attribute compute to the name of a method. The computation method should simply set the value of the field to compute on every record in self.

Danger

self is a collection
The object self is a recordset, i.e., an ordered collection of records. It supports the standard Python operations on collections, like len(self) and iter(self), plus extra set operations like recs1 + recs2.
Iterating over self gives the records one by one, where each record is itself a collection of size 1. You can access/assign fields on single records by using the dot notation, like record.name.
import random
from odoo import models, fields, api

class ComputedModel(models.Model):
    _name = 'test.computed'

    name = fields.Char(compute='_compute_name')

    @api.multi
    def _compute_name(self):
        for record in self:
            record.name = str(random.randint(1, 1e6))
Dependencies
The value of a computed field usually depends on the values of other fields on the computed record. The ORM expects the developer to specify those dependencies on the compute method with the decorator depends(). The given dependencies are used by the ORM to trigger the recomputation of the field whenever some of its dependencies have been modified:

from odoo import models, fields, api

class ComputedModel(models.Model):
    _name = 'test.computed'

    name = fields.Char(compute='_compute_name')
    value = fields.Integer()

    @api.depends('value')
    def _compute_name(self):
        for record in self:
            record.name = "Record with value %s" % record.value
Exercise

Computed fields
Add the percentage of taken seats to the Session model
Display that field in the tree and form views
Display the field as a progress bar
Add a computed field to Session
Show the field in the Session view:
openacademy/models.py
    course_id = fields.Many2one('openacademy.course',
        ondelete='cascade', string="Course", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

    taken_seats = fields.Float(string="Taken seats", compute='_taken_seats')

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
        for r in self:
            if not r.seats:
                r.taken_seats = 0.0
            else:
                r.taken_seats = 100.0 * len(r.attendee_ids) / r.seats
openacademy/views/openacademy.xml
                                <field name="start_date"/>
                                <field name="duration"/>
                                <field name="seats"/>
                                <field name="taken_seats" widget="progressbar"/>
                            </group>
                        </group>
                        <label for="attendee_ids"/>
                <tree string="Session Tree">
                    <field name="name"/>
                    <field name="course_id"/>
                    <field name="taken_seats" widget="progressbar"/>
                </tree>
            </field>
        </record>
Default values
Any field can be given a default value. In the field definition, add the option default=X where X is either a Python literal value (boolean, integer, float, string), or a function taking a recordset and returning a value:

name = fields.Char(default="Unknown")
user_id = fields.Many2one('res.users', default=lambda self: self.env.user)
Note

The object self.env gives access to request parameters and other useful things:
self.env.cr or self._cr is the database cursor object; it is used for querying the database
self.env.uid or self._uid is the current user's database id
self.env.user is the current user's record
self.env.context or self._context is the context dictionary
self.env.ref(xml_id) returns the record corresponding to an XML id
self.env[model_name] returns an instance of the given model
Exercise

Active objects – Default values
Define the start_date default value as today (see Date).
Add a field active in the class Session, and set sessions as active by default.
openacademy/models.py
    _name = 'openacademy.session'

    name = fields.Char(required=True)
    start_date = fields.Date(default=fields.Date.today)
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")
    active = fields.Boolean(default=True)

    instructor_id = fields.Many2one('res.partner', string="Instructor",
        domain=['|', ('instructor', '=', True),
openacademy/views/openacademy.xml
                                <field name="course_id"/>
                                <field name="name"/>
                                <field name="instructor_id"/>
                                <field name="active"/>
                            </group>
                            <group string="Schedule">
                                <field name="start_date"/>
Note

Odoo has built-in rules making fields with an active field set to False invisible.
Onchange
The "onchange" mechanism provides a way for the client interface to update a form whenever the user has filled in a value in a field, without saving anything to the database.

For instance, suppose a model has three fields amount, unit_price and price, and you want to update the price on the form when any of the other fields is modified. To achieve this, define a method where self represents the record in the form view, and decorate it with onchange() to specify on which field it has to be triggered. Any change you make on self will be reflected on the form.

<!-- content of form view -->
<field name="amount"/>
<field name="unit_price"/>
<field name="price" readonly="1"/>
# onchange handler
@api.onchange('amount', 'unit_price')
def _onchange_price(self):
    # set auto-changing field
    self.price = self.amount * self.unit_price
    # Can optionally return a warning and domains
    return {
        'warning': {
            'title': "Something bad happened",
            'message': "It was very bad indeed",
        }
    }
For computed fields, valued onchange behavior is built-in as can be seen by playing with the Session form: change the number of seats or participants, and the taken_seats progressbar is automatically updated.

Exercise

Warning
Add an explicit onchange to warn about invalid values, like a negative number of seats, or more participants than seats.
openacademy/models.py
                r.taken_seats = 0.0
            else:
                r.taken_seats = 100.0 * len(r.attendee_ids) / r.seats

    @api.onchange('seats', 'attendee_ids')
    def _verify_valid_seats(self):
        if self.seats < 0:
            return {
                'warning': {
                    'title': "Incorrect 'seats' value",
                    'message': "The number of available seats may not be negative",
                },
            }
        if self.seats < len(self.attendee_ids):
            return {
                'warning': {
                    'title': "Too many attendees",
                    'message': "Increase seats or remove excess attendees",
                },
            }
Model constraints
Odoo provides two ways to set up automatically verified invariants: Python constraints and SQL constraints.

A Python constraint is defined as a method decorated with constrains(), and invoked on a recordset. The decorator specifies which fields are involved in the constraint, so that the constraint is automatically evaluated when one of them is modified. The method is expected to raise an exception if its invariant is not satisfied:

from odoo.exceptions import ValidationError

@api.constrains('age')
def _check_something(self):
    for record in self:
        if record.age > 20:
            raise ValidationError("Your record is too old: %s" % record.age)
    # all records passed the test, don't return anything
Exercise

Add Python constraints
Add a constraint that checks that the instructor is not present in the attendees of his/her own session.
openacademy/models.py
# -*- coding: utf-8 -*-

from odoo import models, fields, api, exceptions

class Course(models.Model):
    _name = 'openacademy.course'
                    'message': "Increase seats or remove excess attendees",
                },
            }

    @api.constrains('instructor_id', 'attendee_ids')
    def _check_instructor_not_in_attendees(self):
        for r in self:
            if r.instructor_id and r.instructor_id in r.attendee_ids:
                raise exceptions.ValidationError("A session's instructor can't be an attendee")
SQL constraints are defined through the model attribute _sql_constraints. The latter is assigned to a list of triples of strings (name, sql_definition, message), where name is a valid SQL constraint name, sql_definition is a table_constraint expression, and message is the error message.

Exercise

Add SQL constraints
With the help of PostgreSQL's documentation , add the following constraints:
CHECK that the course description and the course title are different
Make the Course's name UNIQUE
openacademy/models.py
    session_ids = fields.One2many(
        'openacademy.session', 'course_id', string="Sessions")

    _sql_constraints = [
        ('name_description_check',
         'CHECK(name != description)',
         "The title of the course should not be the description"),

        ('name_unique',
         'UNIQUE(name)',
         "The course title must be unique"),
    ]


class Session(models.Model):
    _name = 'openacademy.session'
Exercise

Exercise 6 - Add a duplicate option
Since we added a constraint for the Course name uniqueness, it is not possible to use the "duplicate" function anymore (Form ‣ Duplicate).
Re-implement your own "copy" method which allows to duplicate the Course object, changing the original name into "Copy of [original name]".
openacademy/models.py
    session_ids = fields.One2many(
        'openacademy.session', 'course_id', string="Sessions")

    @api.multi
    def copy(self, default=None):
        default = dict(default or {})

        copied_count = self.search_count(
            [('name', '=like', u"Copy of {}%".format(self.name))])
        if not copied_count:
            new_name = u"Copy of {}".format(self.name)
        else:
            new_name = u"Copy of {} ({})".format(self.name, copied_count)

        default['name'] = new_name
        return super(Course, self).copy(default)

    _sql_constraints = [
        ('name_description_check',
         'CHECK(name != description)',
Advanced Views
Tree views
Tree views can take supplementary attributes to further customize their behavior:

decoration-{$name}
allow changing the style of a row's text based on the corresponding record's attributes.

Values are Python expressions. For each record, the expression is evaluated with the record's attributes as context values and if true, the corresponding style is applied to the row. Other context values are uid (the id of the current user) and current_date (the current date as a string of the form yyyy-MM-dd).

{$name} can be bf (font-weight: bold), it (font-style: italic), or any bootstrap contextual color (danger, info, muted, primary, success or warning).

<tree string="Idea Categories" decoration-info="state=='draft'"
    decoration-danger="state=='trashed'">
    <field name="name"/>
    <field name="state"/>
</tree>
editable
Either "top" or "bottom". Makes the tree view editable in-place (rather than having to go through the form view), the value is the position where new rows appear.
Exercise

List coloring
Modify the Session tree view in such a way that sessions lasting less than 5 days are colored blue, and the ones lasting more than 15 days are colored red.
Modify the session tree view:
openacademy/views/openacademy.xml
            <field name="name">session.tree</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <tree string="Session Tree" decoration-info="duration&lt;5" decoration-danger="duration&gt;15">
                    <field name="name"/>
                    <field name="course_id"/>
                    <field name="duration" invisible="1"/>
                    <field name="taken_seats" widget="progressbar"/>
                </tree>
            </field>
Calendars
Displays records as calendar events. Their root element is <calendar> and their most common attributes are:

color
The name of the field used for color segmentation. Colors are automatically distributed to events, but events in the same color segment (records which have the same value for their @color field) will be given the same color.
date_start
record's field holding the start date/time for the event
date_stop (optional)
record's field holding the end date/time for the event
field (to define the label for each calendar event)

<calendar string="Ideas" date_start="invent_date" color="inventor_id">
    <field name="name"/>
</calendar>
Exercise

Calendar view
Add a Calendar view to the Session model enabling the user to view the events associated to the Open Academy.
Add an end_date field computed from start_date and duration

Tip

the inverse function makes the field writable, and allows moving the sessions (via drag and drop) in the calendar view
Add a calendar view to the Session model
And add the calendar view to the Session model's actions
openacademy/models.py
# -*- coding: utf-8 -*-

from datetime import timedelta
from odoo import models, fields, api, exceptions

class Course(models.Model):
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

    taken_seats = fields.Float(string="Taken seats", compute='_taken_seats')
    end_date = fields.Date(string="End Date", store=True,
        compute='_get_end_date', inverse='_set_end_date')

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
                },
            }

    @api.depends('start_date', 'duration')
    def _get_end_date(self):
        for r in self:
            if not (r.start_date and r.duration):
                r.end_date = r.start_date
                continue

            # Add duration to start_date, but: Monday + 5 days = Saturday, so
            # subtract one second to get on Friday instead
            start = fields.Datetime.from_string(r.start_date)
            duration = timedelta(days=r.duration, seconds=-1)
            r.end_date = start + duration

    def _set_end_date(self):
        for r in self:
            if not (r.start_date and r.end_date):
                continue

            # Compute the difference between dates, but: Friday - Monday = 4 days,
            # so add one day to get 5 days instead
            start_date = fields.Datetime.from_string(r.start_date)
            end_date = fields.Datetime.from_string(r.end_date)
            r.duration = (end_date - start_date).days + 1

    @api.constrains('instructor_id', 'attendee_ids')
    def _check_instructor_not_in_attendees(self):
        for r in self:
openacademy/views/openacademy.xml
            </field>
        </record>

        <!-- calendar view -->
        <record model="ir.ui.view" id="session_calendar_view">
            <field name="name">session.calendar</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <calendar string="Session Calendar" date_start="start_date"
                          date_stop="end_date"
                          color="instructor_id">
                    <field name="name"/>
                </calendar>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form,calendar</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
Search views
Search view <field> elements can have a @filter_domain that overrides the domain generated for searching on the given field. In the given domain, self represents the value entered by the user. In the example below, it is used to search on both fields name and description.

Search views can also contain <filter> elements, which act as toggles for predefined searches. Filters must have one of the following attributes:

domain
add the given domain to the current search
context
add some context to the current search; use the key group_by to group results on the given field name
<search string="Ideas">
    <field name="name"/>
    <field name="description" string="Name and description"
           filter_domain="['|', ('name', 'ilike', self), ('description', 'ilike', self)]"/>
    <field name="inventor_id"/>
    <field name="country_id" widget="selection"/>

    <filter name="my_ideas" string="My Ideas"
            domain="[('inventor_id', '=', uid)]"/>
    <group string="Group By">
        <filter name="group_by_inventor" string="Inventor"
                context="{'group_by': 'inventor_id'}"/>
    </group>
</search>
To use a non-default search view in an action, it should be linked using the search_view_id field of the action record.

The action can also set default values for search fields through its context field: context keys of the form search_default_field_name will initialize field_name with the provided value. Search filters must have an optional @name to have a default and behave as booleans (they can only be enabled by default).

Exercise

Search views
Add a button to filter the courses for which the current user is the responsible in the course search view. Make it selected by default.
Add a button to group courses by responsible user.
openacademy/views/openacademy.xml
                <search>
                    <field name="name"/>
                    <field name="description"/>
                    <filter name="my_courses" string="My Courses"
                            domain="[('responsible_id', '=', uid)]"/>
                    <group string="Group By">
                        <filter name="by_responsible" string="Responsible"
                                context="{'group_by': 'responsible_id'}"/>
                    </group>
                </search>
            </field>
        </record>
            <field name="res_model">openacademy.course</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
            <field name="context" eval="{'search_default_my_courses': 1}"/>
            <field name="help" type="html">
                <p class="oe_view_nocontent_create">Create the first course
                </p>
Gantt
Horizontal bar charts typically used to show project planning and advancement, their root element is <gantt>.

<gantt string="Ideas"
       date_start="invent_date"
       date_stop="date_finished"
       progress="progress"
       default_group_by="inventor_id" />
Exercise

Gantt charts
Add a Gantt Chart enabling the user to view the sessions scheduling linked to the Open Academy module. The sessions should be grouped by instructor.
Create a computed field expressing the session's duration in hours
Add the gantt view's definition, and add the gantt view to the Session model's action
openacademy/models.py
    end_date = fields.Date(string="End Date", store=True,
        compute='_get_end_date', inverse='_set_end_date')

    hours = fields.Float(string="Duration in hours",
                         compute='_get_hours', inverse='_set_hours')

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
        for r in self:
            end_date = fields.Datetime.from_string(r.end_date)
            r.duration = (end_date - start_date).days + 1

    @api.depends('duration')
    def _get_hours(self):
        for r in self:
            r.hours = r.duration * 24

    def _set_hours(self):
        for r in self:
            r.duration = r.hours / 24

    @api.constrains('instructor_id', 'attendee_ids')
    def _check_instructor_not_in_attendees(self):
        for r in self:
openacademy/views/openacademy.xml
            </field>
        </record>

        <record model="ir.ui.view" id="session_gantt_view">
            <field name="name">session.gantt</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <gantt string="Session Gantt" color="course_id"
                       date_start="start_date" date_delay="hours"
                       default_group_by='instructor_id'>
                    <field name="name"/>
                </gantt>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form,calendar,gantt</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
Graph views
Graph views allow aggregated overview and analysis of models, their root element is <graph>.

Note

Pivot views (element <pivot>) a multidimensional table, allows the selection of filers and dimensions to get the right aggregated dataset before moving to a more graphical overview. The pivot view shares the same content definition as graph views.
Graph views have 4 display modes, the default mode is selected using the @type attribute.

Bar (default)
a bar chart, the first dimension is used to define groups on the horizontal axis, other dimensions define aggregated bars within each group.

By default bars are side-by-side, they can be stacked by using @stacked="True" on the <graph>

Line
2-dimensional line chart
Pie
2-dimensional pie
Graph views contain <field> with a mandatory @type attribute taking the values:

row (default)
the field should be aggregated by default
measure
the field should be aggregated rather than grouped on
<graph string="Total idea score by Inventor">
    <field name="inventor_id"/>
    <field name="score" type="measure"/>
</graph>
Warning

Graph views perform aggregations on database values, they do not work with non-stored computed fields.
Exercise

Graph view
Add a Graph view in the Session object that displays, for each course, the number of attendees under the form of a bar chart.
Add the number of attendees as a stored computed field
Then add the relevant view
openacademy/models.py
    hours = fields.Float(string="Duration in hours",
                         compute='_get_hours', inverse='_set_hours')

    attendees_count = fields.Integer(
        string="Attendees count", compute='_get_attendees_count', store=True)

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
        for r in self:
        for r in self:
            r.duration = r.hours / 24

    @api.depends('attendee_ids')
    def _get_attendees_count(self):
        for r in self:
            r.attendees_count = len(r.attendee_ids)

    @api.constrains('instructor_id', 'attendee_ids')
    def _check_instructor_not_in_attendees(self):
        for r in self:
openacademy/views/openacademy.xml
            </field>
        </record>

        <record model="ir.ui.view" id="openacademy_session_graph_view">
            <field name="name">openacademy.session.graph</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <graph string="Participations by Courses">
                    <field name="course_id"/>
                    <field name="attendees_count" type="measure"/>
                </graph>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form,calendar,gantt,graph</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
Kanban
Used to organize tasks, production processes, etc… their root element is <kanban>.

A kanban view shows a set of cards possibly grouped in columns. Each card represents a record, and each column the values of an aggregation field.

For instance, project tasks may be organized by stage (each column is a stage), or by responsible (each column is a user), and so on.

Kanban views define the structure of each card as a mix of form elements (including basic HTML) and QWeb.

Exercise

Kanban view
Add a Kanban view that displays sessions grouped by course (columns are thus courses).
Add an integer color field to the Session model
Add the kanban view and update the action
openacademy/models.py
    duration = fields.Float(digits=(6, 2), help="Duration in days")
    seats = fields.Integer(string="Number of seats")
    active = fields.Boolean(default=True)
    color = fields.Integer()

    instructor_id = fields.Many2one('res.partner', string="Instructor",
        domain=['|', ('instructor', '=', True),
openacademy/views/openacademy.xml
        </record>

        <record model="ir.ui.view" id="view_openacad_session_kanban">
            <field name="name">openacad.session.kanban</field>
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <kanban default_group_by="course_id">
                    <field name="color"/>
                    <templates>
                        <t t-name="kanban-box">
                            <div
                                    t-attf-class="oe_kanban_color_{{kanban_getcolor(record.color.raw_value)}}
                                                  oe_kanban_global_click_edit oe_semantic_html_override
                                                  oe_kanban_card {{record.group_fancy==1 ? 'oe_kanban_card_fancy' : ''}}">
                                <div class="oe_dropdown_kanban">
                                    <!-- dropdown menu -->
                                    <div class="oe_dropdown_toggle">
                                        <i class="fa fa-bars fa-lg"/>
                                        <ul class="oe_dropdown_menu">
                                            <li>
                                                <a type="delete">Delete</a>
                                            </li>
                                            <li>
                                                <ul class="oe_kanban_colorpicker"
                                                    data-field="color"/>
                                            </li>
                                        </ul>
                                    </div>
                                    <div class="oe_clear"></div>
                                </div>
                                <div t-attf-class="oe_kanban_content">
                                    <!-- title -->
                                    Session name:
                                    <field name="name"/>
                                    <br/>
                                    Start date:
                                    <field name="start_date"/>
                                    <br/>
                                    duration:
                                    <field name="duration"/>
                                </div>
                            </div>
                        </t>
                    </templates>
                </kanban>
            </field>
        </record>

        <record model="ir.actions.act_window" id="session_list_action">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form,calendar,gantt,graph,kanban</field>
        </record>

        <menuitem id="session_menu" name="Sessions"
                  parent="openacademy_menu"
Workflows
Workflows are models associated to business objects describing their dynamics. Workflows are also used to track processes that evolve over time.

Exercise

Almost a workflow
Add a state field to the Session model. It will be used to define a workflow-ish.
A sesion can have three possible states: Draft (default), Confirmed and Done.
In the session form, add a (read-only) field to visualize the state, and buttons to change it. The valid transitions are:
Draft -> Confirmed
Confirmed -> Draft
Confirmed -> Done
Done -> Draft
Add a new state field
Add state-transitioning methods, those can be called from view buttons to change the record's state
And add the relevant buttons to the session's form view
openacademy/models.py
    attendees_count = fields.Integer(
        string="Attendees count", compute='_get_attendees_count', store=True)

    state = fields.Selection([
        ('draft', "Draft"),
        ('confirmed', "Confirmed"),
        ('done', "Done"),
    ], default='draft')

    @api.multi
    def action_draft(self):
        self.state = 'draft'

    @api.multi
    def action_confirm(self):
        self.state = 'confirmed'

    @api.multi
    def action_done(self):
        self.state = 'done'

    @api.depends('seats', 'attendee_ids')
    def _taken_seats(self):
        for r in self:
openacademy/views/openacademy.xml
            <field name="model">openacademy.session</field>
            <field name="arch" type="xml">
                <form string="Session Form">
                    <header>
                        <button name="action_draft" type="object"
                                string="Reset to draft"
                                states="confirmed,done"/>
                        <button name="action_confirm" type="object"
                                string="Confirm" states="draft"
                                class="oe_highlight"/>
                        <button name="action_done" type="object"
                                string="Mark as done" states="confirmed"
                                class="oe_highlight"/>
                        <field name="state" widget="statusbar"/>
                    </header>

                    <sheet>
                        <group>
                            <group string="General">
Workflows may be associated with any object in Odoo, and are entirely customizable. Workflows are used to structure and manage the lifecycles of business objects and documents, and define transitions, triggers, etc. with graphical tools. Workflows, activities (nodes or actions) and transitions (conditions) are declared as XML records, as usual. The tokens that navigate in workflows are called workitems.

Warning

A workflow associated with a model is only created when the model's records are created. Thus there is no workflow instance associated with session instances created before the workflow's definition
Exercise

Workflow
Replace the ad-hoc Session workflow by a real workflow. Transform the Session form view so its buttons call the workflow instead of the model's methods.
openacademy/__manifest__.py
        'templates.xml',
        'views/openacademy.xml',
        'views/partner.xml',
        'views/session_workflow.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
openacademy/models.py
        ('draft', "Draft"),
        ('confirmed', "Confirmed"),
        ('done', "Done"),
    ])

    @api.multi
    def action_draft(self):
openacademy/views/openacademy.xml
            <field name="arch" type="xml">
                <form string="Session Form">
                    <header>
                        <button name="draft" type="workflow"
                                string="Reset to draft"
                                states="confirmed,done"/>
                        <button name="confirm" type="workflow"
                                string="Confirm" states="draft"
                                class="oe_highlight"/>
                        <button name="done" type="workflow"
                                string="Mark as done" states="confirmed"
                                class="oe_highlight"/>
                        <field name="state" widget="statusbar"/>
openacademy/views/session_workflow.xml
<odoo>
    <data>
        <record model="workflow" id="wkf_session">
            <field name="name">OpenAcademy sessions workflow</field>
            <field name="osv">openacademy.session</field>
            <field name="on_create">True</field>
        </record>

        <record model="workflow.activity" id="draft">
            <field name="name">Draft</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="flow_start" eval="True"/>
            <field name="kind">function</field>
            <field name="action">action_draft()</field>
        </record>
        <record model="workflow.activity" id="confirmed">
            <field name="name">Confirmed</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="kind">function</field>
            <field name="action">action_confirm()</field>
        </record>
        <record model="workflow.activity" id="done">
            <field name="name">Done</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="kind">function</field>
            <field name="action">action_done()</field>
        </record>

        <record model="workflow.transition" id="session_draft_to_confirmed">
            <field name="act_from" ref="draft"/>
            <field name="act_to" ref="confirmed"/>
            <field name="signal">confirm</field>
        </record>
        <record model="workflow.transition" id="session_confirmed_to_draft">
            <field name="act_from" ref="confirmed"/>
            <field name="act_to" ref="draft"/>
            <field name="signal">draft</field>
        </record>
        <record model="workflow.transition" id="session_done_to_draft">
            <field name="act_from" ref="done"/>
            <field name="act_to" ref="draft"/>
            <field name="signal">draft</field>
        </record>
        <record model="workflow.transition" id="session_confirmed_to_done">
            <field name="act_from" ref="confirmed"/>
            <field name="act_to" ref="done"/>
            <field name="signal">done</field>
        </record>
    </data>
</odoo>
Tip

In order to check if instances of the workflow are correctly created alongside sessions, go to Settings ‣ Technical ‣ Workflows ‣ Instances
Exercise

Automatic transitions
Automatically transition sessions from Draft to Confirmed when more than half the session's seats are reserved.
openacademy/views/session_workflow.xml
            <field name="act_to" ref="done"/>
            <field name="signal">done</field>
        </record>

        <record model="workflow.transition" id="session_auto_confirm_half_filled">
            <field name="act_from" ref="draft"/>
            <field name="act_to" ref="confirmed"/>
            <field name="condition">taken_seats &gt; 50</field>
        </record>
    </data>
</odoo>
Exercise

Server actions
Replace the Python methods for synchronizing session state by server actions.
Both the workflow and the server actions could have been created entirely from the UI.
openacademy/views/session_workflow.xml
            <field name="on_create">True</field>
        </record>

        <record model="ir.actions.server" id="set_session_to_draft">
            <field name="name">Set session to Draft</field>
            <field name="model_id" ref="model_openacademy_session"/>
            <field name="code">
model.search([('id', 'in', context['active_ids'])]).action_draft()
            </field>
        </record>
        <record model="workflow.activity" id="draft">
            <field name="name">Draft</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="flow_start" eval="True"/>
            <field name="kind">dummy</field>
            <field name="action"></field>
            <field name="action_id" ref="set_session_to_draft"/>
        </record>

        <record model="ir.actions.server" id="set_session_to_confirmed">
            <field name="name">Set session to Confirmed</field>
            <field name="model_id" ref="model_openacademy_session"/>
            <field name="code">
model.search([('id', 'in', context['active_ids'])]).action_confirm()
            </field>
        </record>
        <record model="workflow.activity" id="confirmed">
            <field name="name">Confirmed</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="kind">dummy</field>
            <field name="action"></field>
            <field name="action_id" ref="set_session_to_confirmed"/>
        </record>

        <record model="ir.actions.server" id="set_session_to_done">
            <field name="name">Set session to Done</field>
            <field name="model_id" ref="model_openacademy_session"/>
            <field name="code">
model.search([('id', 'in', context['active_ids'])]).action_done()
            </field>
        </record>
        <record model="workflow.activity" id="done">
            <field name="name">Done</field>
            <field name="wkf_id" ref="wkf_session"/>
            <field name="kind">dummy</field>
            <field name="action"></field>
            <field name="action_id" ref="set_session_to_done"/>
        </record>

        <record model="workflow.transition" id="session_draft_to_confirmed">
Security
Access control mechanisms must be configured to achieve a coherent security policy.

Group-based access control mechanisms
Groups are created as normal records on the model res.groups, and granted menu access via menu definitions. However even without a menu, objects may still be accessible indirectly, so actual object-level permissions (read, write, create, unlink) must be defined for groups. They are usually inserted via CSV files inside modules. It is also possible to restrict access to specific fields on a view or object using the field's groups attribute.

Access rights
Access rights are defined as records of the model ir.model.access. Each access right is associated to a model, a group (or no group for global access), and a set of permissions: read, write, create, unlink. Such access rights are usually created by a CSV file named after its model: ir.model.access.csv.

id,name,model_id/id,group_id/id,perm_read,perm_write,perm_create,perm_unlink
access_idea_idea,idea.idea,model_idea_idea,base.group_user,1,1,1,0
access_idea_vote,idea.vote,model_idea_vote,base.group_user,1,1,1,0
Exercise

Add access control through the Odoo interface
Create a new user "John Smith". Then create a group "OpenAcademy / Session Read" with read access to the Session model.
Create a new user John Smith through Settings ‣ Users ‣ Users
Create a new group session_read through Settings ‣ Users ‣ Groups, it should have read access on the Session model
Edit John Smith to make them a member of session_read
Log in as John Smith to check the access rights are correct
Exercise

Add access control through data files in your module
Using data files,
Create a group OpenAcademy / Manager with full access to all OpenAcademy models
Make Session and Course readable by all users
Create a new file openacademy/security/security.xml to hold the OpenAcademy Manager group
Edit the file openacademy/security/ir.model.access.csv with the access rights to the models
Finally update openacademy/__manifest__.py to add the new data files to it
openacademy/__manifest__.py

    # always loaded
    'data': [
        'security/security.xml',
        'security/ir.model.access.csv',
        'templates.xml',
        'views/openacademy.xml',
        'views/partner.xml',
openacademy/security/ir.model.access.csv
id,name,model_id/id,group_id/id,perm_read,perm_write,perm_create,perm_unlink
course_manager,course manager,model_openacademy_course,group_manager,1,1,1,1
session_manager,session manager,model_openacademy_session,group_manager,1,1,1,1
course_read_all,course all,model_openacademy_course,,1,0,0,0
session_read_all,session all,model_openacademy_session,,1,0,0,0
openacademy/security/security.xml
<odoo>
    <data>
        <record id="group_manager" model="res.groups">
            <field name="name">OpenAcademy / Manager</field>
        </record>
    </data>
</odoo>
Record rules
A record rule restricts the access rights to a subset of records of the given model. A rule is a record of the model ir.rule, and is associated to a model, a number of groups (many2many field), permissions to which the restriction applies, and a domain. The domain specifies to which records the access rights are limited.

Here is an example of a rule that prevents the deletion of leads that are not in state cancel. Notice that the value of the field groups must follow the same convention as the method write() of the ORM.

<record id="delete_cancelled_only" model="ir.rule">
    <field name="name">Only cancelled leads may be deleted</field>
    <field name="model_id" ref="crm.model_crm_lead"/>
    <field name="groups" eval="[(4, ref('sales_team.group_sale_manager'))]"/>
    <field name="perm_read" eval="0"/>
    <field name="perm_write" eval="0"/>
    <field name="perm_create" eval="0"/>
    <field name="perm_unlink" eval="1" />
    <field name="domain_force">[('state','=','cancel')]</field>
</record>
Exercise

Record rule
Add a record rule for the model Course and the group "OpenAcademy / Manager", that restricts write and unlink accesses to the responsible of a course. If a course has no responsible, all users of the group must be able to modify it.
Create a new rule in openacademy/security/security.xml:
openacademy/security/security.xml
        <record id="group_manager" model="res.groups">
            <field name="name">OpenAcademy / Manager</field>
        </record>
    
        <record id="only_responsible_can_modify" model="ir.rule">
            <field name="name">Only Responsible can modify Course</field>
            <field name="model_id" ref="model_openacademy_course"/>
            <field name="groups" eval="[(4, ref('openacademy.group_manager'))]"/>
            <field name="perm_read" eval="0"/>
            <field name="perm_write" eval="1"/>
            <field name="perm_create" eval="0"/>
            <field name="perm_unlink" eval="1"/>
            <field name="domain_force">
                ['|', ('responsible_id','=',False),
                      ('responsible_id','=',user.id)]
            </field>
        </record>
    </data>
</odoo>
Wizards
Wizards describe interactive sessions with the user (or dialog boxes) through dynamic forms. A wizard is simply a model that extends the class TransientModel instead of Model. The class TransientModel extends Model and reuse all its existing mechanisms, with the following particularities:

Wizard records are not meant to be persistent; they are automatically deleted from the database after a certain time. This is why they are called transient.
Wizard models do not require explicit access rights: users have all permissions on wizard records.
Wizard records may refer to regular records or wizard records through many2one fields, but regular records cannot refer to wizard records through a many2one field.
We want to create a wizard that allow users to create attendees for a particular session, or for a list of sessions at once.

Exercise

Define the wizard
Create a wizard model with a many2one relationship with the Session model and a many2many relationship with the Partner model.
Add a new file openacademy/wizard.py:
openacademy/__init__.py
from . import controllers
from . import models
from . import partner
from . import wizard
openacademy/wizard.py
# -*- coding: utf-8 -*-

from odoo import models, fields, api

class Wizard(models.TransientModel):
    _name = 'openacademy.wizard'

    session_id = fields.Many2one('openacademy.session',
        string="Session", required=True)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
Launching wizards
Wizards are launched by ir.actions.act_window records, with the field target set to the value new. The latter opens the wizard view into a popup window. The action may be triggered by a menu item.

There is another way to launch the wizard: using an ir.actions.act_window record like above, but with an extra field src_model that specifies in the context of which model the action is available. The wizard will appear in the contextual actions of the model, above the main view. Because of some internal hooks in the ORM, such an action is declared in XML with the tag act_window.

<act_window id="launch_the_wizard"
            name="Launch the Wizard"
            src_model="context.model.name"
            res_model="wizard.model.name"
            view_mode="form"
            target="new"
            key2="client_action_multi"/>
Wizards use regular views and their buttons may use the attribute special="cancel" to close the wizard window without saving.

Exercise

Launch the wizard
Define a form view for the wizard.
Add the action to launch it in the context of the Session model.
Define a default value for the session field in the wizard; use the context parameter self._context to retrieve the current session.
openacademy/wizard.py
class Wizard(models.TransientModel):
    _name = 'openacademy.wizard'

    def _default_session(self):
        return self.env['openacademy.session'].browse(self._context.get('active_id'))

    session_id = fields.Many2one('openacademy.session',
        string="Session", required=True, default=_default_session)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")
openacademy/views/openacademy.xml
                  parent="openacademy_menu"
                  action="session_list_action"/>

        <record model="ir.ui.view" id="wizard_form_view">
            <field name="name">wizard.form</field>
            <field name="model">openacademy.wizard</field>
            <field name="arch" type="xml">
                <form string="Add Attendees">
                    <group>
                        <field name="session_id"/>
                        <field name="attendee_ids"/>
                    </group>
                </form>
            </field>
        </record>

        <act_window id="launch_session_wizard"
                    name="Add Attendees"
                    src_model="openacademy.session"
                    res_model="openacademy.wizard"
                    view_mode="form"
                    target="new"
                    key2="client_action_multi"/>
    </data>
</odoo>
Exercise

Register attendees
Add buttons to the wizard, and implement the corresponding method for adding the attendees to the given session.
openacademy/views/openacademy.xml
                        <field name="attendee_ids"/>
                    </group>
                    <footer>
                        <button name="subscribe" type="object"
                                string="Subscribe" class="oe_highlight"/>
                        or
                        <button special="cancel" string="Cancel"/>
                    </footer>
                </form>
            </field>
        </record>
openacademy/wizard.py
    session_id = fields.Many2one('openacademy.session',
        string="Session", required=True, default=_default_session)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

    @api.multi
    def subscribe(self):
        self.session_id.attendee_ids |= self.attendee_ids
        return {}
Exercise

Register attendees to multiple sessions
Modify the wizard model so that attendees can be registered to multiple sessions.
openacademy/views/openacademy.xml
                <form string="Add Attendees">
                    <group>
                        <field name="session_ids"/>
                        <field name="attendee_ids"/>
                    </group>
                    <footer>
                        <button name="subscribe" type="object"
openacademy/wizard.py
class Wizard(models.TransientModel):
    _name = 'openacademy.wizard'

    def _default_sessions(self):
        return self.env['openacademy.session'].browse(self._context.get('active_ids'))

    session_ids = fields.Many2many('openacademy.session',
        string="Sessions", required=True, default=_default_sessions)
    attendee_ids = fields.Many2many('res.partner', string="Attendees")

    @api.multi
    def subscribe(self):
        for session in self.session_ids:
            session.attendee_ids |= self.attendee_ids
        return {}
Internationalization
Each module can provide its own translations within the i18n directory, by having files named LANG.po where LANG is the locale code for the language, or the language and country combination when they differ (e.g. pt.po or pt_BR.po). Translations will be loaded automatically by Odoo for all enabled languages. Developers always use English when creating a module, then export the module terms using Odoo's gettext POT export feature (Settings ‣ Translations ‣ Import/Export ‣ Export Translation without specifying a language), to create the module template POT file, and then derive the translated PO files. Many IDE's have plugins or modes for editing and merging PO/POT files.

Tip

The Portable Object files generated by Odoo are published on Transifex, making it easy to translate the software.
|- idea/ # The module directory
   |- i18n/ # Translation files
      | - idea.pot # Translation Template (exported from Odoo)
      | - fr.po # French translation
      | - pt_BR.po # Brazilian Portuguese translation
      | (...)
Tip

By default Odoo's POT export only extracts labels inside XML files or inside field definitions in Python code, but any Python string can be translated this way by surrounding it with the function odoo._() (e.g. _("Label"))
Exercise

Translate a module
Choose a second language for your Odoo installation. Translate your module using the facilities provided by Odoo.
Create a directory openacademy/i18n/
Install whichever language you want ( Administration ‣ Translations ‣ Load an Official Translation)
Synchronize translatable terms (Administration ‣ Translations ‣ Application Terms ‣ Synchronize Translations)
Create a template translation file by exporting ( Administration ‣ Translations -> Import/Export ‣ Export Translation) without specifying a language, save in openacademy/i18n/
Create a translation file by exporting ( Administration ‣ Translations ‣ Import/Export ‣ Export Translation) and specifying a language. Save it in openacademy/i18n/
Open the exported translation file (with a basic text editor or a dedicated PO-file editor e.g. POEdit and translate the missing terms
In models.py, add an import statement for the function odoo._ and mark missing strings as translatable
Repeat steps 3-6
openacademy/models.py
# -*- coding: utf-8 -*-

from datetime import timedelta
from odoo import models, fields, api, exceptions, _

class Course(models.Model):
    _name = 'openacademy.course'
        default = dict(default or {})

        copied_count = self.search_count(
            [('name', '=like', _(u"Copy of {}%").format(self.name))])
        if not copied_count:
            new_name = _(u"Copy of {}").format(self.name)
        else:
            new_name = _(u"Copy of {} ({})").format(self.name, copied_count)

        default['name'] = new_name
        return super(Course, self).copy(default)
        if self.seats < 0:
            return {
                'warning': {
                    'title': _("Incorrect 'seats' value"),
                    'message': _("The number of available seats may not be negative"),
                },
            }
        if self.seats < len(self.attendee_ids):
            return {
                'warning': {
                    'title': _("Too many attendees"),
                    'message': _("Increase seats or remove excess attendees"),
                },
            }

    def _check_instructor_not_in_attendees(self):
        for r in self:
            if r.instructor_id and r.instructor_id in r.attendee_ids:
                raise exceptions.ValidationError(_("A session's instructor can't be an attendee"))
Reporting
Printed reports
Odoo 8.0 comes with a new report engine based on QWeb, Twitter Bootstrap and Wkhtmltopdf.

A report is a combination two elements:

an ir.actions.report.xml, for which a <report> shortcut element is provided, it sets up various basic parameters for the report (default type, whether the report should be saved to the database after generation,…)

<report
    id="account_invoices"
    model="account.invoice"
    string="Invoices"
    report_type="qweb-pdf"
    name="account.report_invoice"
    file="account.report_invoice"
    attachment_use="True"
    attachment="(object.state in ('open','paid')) and
        ('INV'+(object.number or '').replace('/','')+'.pdf')"
/>
A standard QWeb view for the actual report:

<t t-call="report.html_container">
    <t t-foreach="docs" t-as="o">
        <t t-call="report.external_layout">
            <div class="page">
                <h2>Report title</h2>
            </div>
        </t>
    </t>
</t>

the standard rendering context provides a number of elements, the most
important being:

``docs``
    the records for which the report is printed
``user``
    the user printing the report
Because reports are standard web pages, they are available through a URL and output parameters can be manipulated through this URL, for instance the HTML version of the Invoice report is available through http://localhost:8069/report/html/account.report_invoice/1 (if account is installed) and the PDF version through http://localhost:8069/report/pdf/account.report_invoice/1.

Danger

If it appears that your PDF report is missing the styles (i.e. the text appears but the style/layout is different from the html version), probably your wkhtmltopdf process cannot reach your web server to download them.
If you check your server logs and see that the CSS styles are not being downloaded when generating a PDF report, most surely this is the problem.
The wkhtmltopdf process will use the web.base.url system parameter as the root path to all linked files, but this parameter is automatically updated each time the Administrator is logged in. If your server resides behind some kind of proxy, that could not be reachable. You can fix this by adding one of these system parameters:
report.url, pointing to an URL reachable from your server (probably http://localhost:8069 or something similar). It will be used for this particular purpose only.
web.base.url.freeze, when set to True, will stop the automatic updates to web.base.url.
Exercise

Create a report for the Session model
For each session, it should display session's name, its start and end, and list the session's attendees.
openacademy/__manifest__.py
        'views/openacademy.xml',
        'views/partner.xml',
        'views/session_workflow.xml',
        'reports.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
openacademy/reports.xml
<odoo>
<data>
    <report
        id="report_session"
        model="openacademy.session"
        string="Session Report"
        name="openacademy.report_session_view"
        file="openacademy.report_session"
        report_type="qweb-pdf" />

    <template id="report_session_view">
        <t t-call="report.html_container">
            <t t-foreach="docs" t-as="doc">
                <t t-call="report.external_layout">
                    <div class="page">
                        <h2 t-field="doc.name"/>
                        <p>From <span t-field="doc.start_date"/> to <span t-field="doc.end_date"/></p>
                        <h3>Attendees:</h3>
                        <ul>
                            <t t-foreach="doc.attendee_ids" t-as="attendee">
                                <li><span t-field="attendee.name"/></li>
                            </t>
                        </ul>
                    </div>
                </t>
            </t>
        </t>
    </template>
</data>
</odoo>
Dashboards
Exercise

Define a Dashboard
Define a dashboard containing the graph view you created, the sessions calendar view and a list view of the courses (switchable to a form view). This dashboard should be available through a menuitem in the menu, and automatically displayed in the web client when the OpenAcademy main menu is selected.
Create a file openacademy/views/session_board.xml. It should contain the board view, the actions referenced in that view, an action to open the dashboard and a re-definition of the main menu item to add the dashboard action

Note

Available dashboard styles are 1, 1-1, 1-2, 2-1 and 1-1-1
Update openacademy/__manifest__.py to reference the new data file
openacademy/__manifest__.py
    'version': '0.1',

    # any module necessary for this one to work correctly
    'depends': ['base', 'board'],

    # always loaded
    'data': [
        'views/openacademy.xml',
        'views/partner.xml',
        'views/session_workflow.xml',
        'views/session_board.xml',
        'reports.xml',
    ],
    # only loaded in demonstration mode
openacademy/views/session_board.xml
<?xml version="1.0"?>
<odoo>
    <data>
        <record model="ir.actions.act_window" id="act_session_graph">
            <field name="name">Attendees by course</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">graph</field>
            <field name="view_id"
                   ref="openacademy.openacademy_session_graph_view"/>
        </record>
        <record model="ir.actions.act_window" id="act_session_calendar">
            <field name="name">Sessions</field>
            <field name="res_model">openacademy.session</field>
            <field name="view_type">form</field>
            <field name="view_mode">calendar</field>
            <field name="view_id" ref="openacademy.session_calendar_view"/>
        </record>
        <record model="ir.actions.act_window" id="act_course_list">
            <field name="name">Courses</field>
            <field name="res_model">openacademy.course</field>
            <field name="view_type">form</field>
            <field name="view_mode">tree,form</field>
        </record>
        <record model="ir.ui.view" id="board_session_form">
            <field name="name">Session Dashboard Form</field>
            <field name="model">board.board</field>
            <field name="type">form</field>
            <field name="arch" type="xml">
                <form string="Session Dashboard">
                    <board style="2-1">
                        <column>
                            <action
                                string="Attendees by course"
                                name="%(act_session_graph)d"
                                height="150"
                                width="510"/>
                            <action
                                string="Sessions"
                                name="%(act_session_calendar)d"/>
                        </column>
                        <column>
                            <action
                                string="Courses"
                                name="%(act_course_list)d"/>
                        </column>
                    </board>
                </form>
            </field>
        </record>
        <record model="ir.actions.act_window" id="open_board_session">
          <field name="name">Session Dashboard</field>
          <field name="res_model">board.board</field>
          <field name="view_type">form</field>
          <field name="view_mode">form</field>
          <field name="usage">menu</field>
          <field name="view_id" ref="board_session_form"/>
        </record>

        <menuitem
            name="Session Dashboard" parent="base.menu_reporting_dashboard"
            action="open_board_session"
            sequence="1"
            id="menu_board_session" icon="terp-graph"/>
    </data>
</odoo>
WebServices
The web-service module offer a common interface for all web-services :

XML-RPC
JSON-RPC
Business objects can also be accessed via the distributed object mechanism. They can all be modified via the client interface with contextual views.

Odoo is accessible through XML-RPC/JSON-RPC interfaces, for which libraries exist in many languages.

XML-RPC Library
The following example is a Python program that interacts with an Odoo server with the library xmlrpclib:

import xmlrpclib

root = 'http://%s:%d/xmlrpc/' % (HOST, PORT)

uid = xmlrpclib.ServerProxy(root + 'common').login(DB, USER, PASS)
print "Logged in as %s (uid: %d)" % (USER, uid)

# Create a new note
sock = xmlrpclib.ServerProxy(root + 'object')
args = {
    'color' : 8,
    'memo' : 'This is a note',
    'create_uid': uid,
}
note_id = sock.execute(DB, uid, PASS, 'note.note', 'create', args)
Exercise

Add a new service to the client
Write a Python program able to send XML-RPC requests to a PC running Odoo (yours, or your instructor's). This program should display all the sessions, and their corresponding number of seats. It should also create a new session for one of the courses.
import functools
import xmlrpclib
HOST = 'localhost'
PORT = 8069
DB = 'openacademy'
USER = 'admin'
PASS = 'admin'
ROOT = 'http://%s:%d/xmlrpc/' % (HOST,PORT)

# 1. Login
uid = xmlrpclib.ServerProxy(ROOT + 'common').login(DB,USER,PASS)
print "Logged in as %s (uid:%d)" % (USER,uid)

call = functools.partial(
    xmlrpclib.ServerProxy(ROOT + 'object').execute,
    DB, uid, PASS)

# 2. Read the sessions
sessions = call('openacademy.session','search_read', [], ['name','seats'])
for session in sessions:
    print "Session %s (%s seats)" % (session['name'], session['seats'])
# 3.create a new session
session_id = call('openacademy.session', 'create', {
    'name' : 'My session',
    'course_id' : 2,
})
Instead of using a hard-coded course id, the code can look up a course by name:
# 3.create a new session for the "Functional" course
course_id = call('openacademy.course', 'search', [('name','ilike','Functional')])[0]
session_id = call('openacademy.session', 'create', {
    'name' : 'My session',
    'course_id' : course_id,
})
JSON-RPC Library
The following example is a Python program that interacts with an Odoo server with the standard Python libraries urllib2 and json:

import json
import random
import urllib2

def json_rpc(url, method, params):
    data = {
        "jsonrpc": "2.0",
        "method": method,
        "params": params,
        "id": random.randint(0, 1000000000),
    }
    req = urllib2.Request(url=url, data=json.dumps(data), headers={
        "Content-Type":"application/json",
    })
    reply = json.load(urllib2.urlopen(req))
    if reply.get("error"):
        raise Exception(reply["error"])
    return reply["result"]

def call(url, service, method, *args):
    return json_rpc(url, "call", {"service": service, "method": method, "args": args})

# log in the given database
url = "http://%s:%s/jsonrpc" % (HOST, PORT)
uid = call(url, "common", "login", DB, USER, PASS)

# create a new note
args = {
    'color' : 8,
    'memo' : 'This is another note',
    'create_uid': uid,
}
note_id = call(url, "object", "execute", DB, uid, PASS, 'note.note', 'create', args)
Here is the same program, using the library jsonrpclib:

import jsonrpclib

# server proxy object
url = "http://%s:%s/jsonrpc" % (HOST, PORT)
server = jsonrpclib.Server(url)

# log in the given database
uid = server.call(service="common", method="login", args=[DB, USER, PASS])

# helper function for invoking model methods
def invoke(model, method, *args):
    args = [DB, uid, PASS, model, method] + list(args)
    return server.call(service="object", method="execute", args=args)

# create a new note
args = {
    'color' : 8,
    'memo' : 'This is another note',
    'create_uid': uid,
}
note_id = invoke('note.note', 'create', args)
Examples can be easily adapted from XML-RPC to JSON-RPC.

Note

There are a number of high-level APIs in various languages to access Odoo systems without explicitly going through XML-RPC or JSON-RPC, such as:
https://github.com/akretion/ooor
https://github.com/syleam/openobject-library
https://github.com/nicolas-van/openerp-client-lib
http://pythonhosted.org/OdooRPC
https://github.com/abhishek-jaiswal/php-openerp-lib
[1] it is possible to disable the automatic creation of some fields
[2] writing raw SQL queries is possible, but requires care as it bypasses all Odoo authentication and security mechanisms.