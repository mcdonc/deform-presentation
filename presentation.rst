.. include:: <s5defs.txt>

================================
Deform: A Form Generation System
================================

:Authors:  Chris McDonough, Agendaless Consulting
:Date: 7/6/2010

Features
========

- Generates forms which are capable of capturing complex data
  structures (lists and mappings, recursively).

- Form validation and errors

- AJAX (actually AJAH) forms

- Client side field filters (masks for SSN, etc)

- Widgets for most common tasks

Comparable To
=============

- z3c.form

- zope.formlib

- ToscaWidgets

- Django newforms

- Formish (heavily influenced by)

- WTForm

Superset Of
===========

- Formencode

Notables
========

- Deform not concerned with "model" data.  Only with "form" data.  The
  schema you use in Deform is unrelated to your model's schema.

- Compatible with any web framework (or non-framework).  No
  assumptions made about request/response library, etc.

Process
=======

- 100% statement coverage via unit tests

- Each widget has a functional selenium test that uses the application
  at `http://deformdemo.repoze.org <http://deformdemo.repoze.org>`_ as
  the application under test.

- Well-documented at `http://docs.repoze.org/deform
  <http://docs.repoze.org/deform>`_.

Dependencies
============

- Colander schema library

- Peppercorn form marshalling library

- Chameleon templating library

- Translationstring i18n library

- ISO8601 date parsing library

Generating a Form
=================

This will generate a form with a single text input widget::

  import colander, deform
  class Schema(colander.Schema):
      text = colander.SchemaNode(
          colander.String(),
          validator=colander.Length(max=100),
          description='Enter some text')
  schema = Schema()
  form = deform.Form(schema, buttons=('submit',))
  html = form.render()

Schema Nodes
============

Schema nodes in a schema can provide:

- Its type (string, int, mapping, etc).

- A validator function.

- The initial value and the "missing" value.

- A name, a title, and a description, used during rendering.

- Schema nodes are the domain of the Colander package.

Widgets
=======

- Many prebaked widgets.

- 1-to-1 relationship between widget and schema node.

- Widgets are written in a combination of Python and the Chameleon ZPT
  templating language

- Any other templating language may be used; hook points exist to use
  a new one.

- Different widgets may be used for the same type of schema field
  (e.g. checkbox vs. radio choice).

- Widgets can be created as necessary.

- Widgets are the domain of the Deform package.

Widget Template Example
=======================

Here's an example of a widget template that uses Chameleon::

  <textarea tal:attributes="rows field.widget.rows;
                            cols field.widget.cols;"
            id="${field.oid}"
            name="${field.name}">${cstruct}</textarea>

Validators
==========

- Deform ships with a number of prebaked validators for common cases
  (length, one-of, email-address, in-range, etc).

- New validators can be added.

- Validators are the domain of both the Colander and Deform packages.

Validator Example
=================

An example of a Deform validator (return value unimportant)::

    def is_int(node, value):
        try:
            int(value)
        except (ValueError, OverflowError, TypeError):
            raise Invalid(node,
                          '%r is not a valid integer' % 
                          value)

Edit Forms
==========

Forms allowing you to edit existing data are similar; you just pass in
the data structure you'd like to edit (it must match the schema)::

  import colander, deform
  class Schema(colander.Schema):
      text = colander.SchemaNode(
          colander.String(),
          validator=colander.Length(max=100),
          description='Enter some text')
  schema = Schema()
  form = deform.Form(schema, buttons=('submit',))
  html = form.render({'text':'Hello!'})

Validation and Re-Rendering
===========================

Your code is in charge of doing validation and re-rendering::

  # form = deform.Form(schema, buttons=('submit',))
  try:
      controls = self.request.POST.items()
      captured = form.validate(controls)
      html = form.render(captured)
  except deform.ValidationFailure, e:
      # the submitted values could not be validated
      html = e.render()

Editing Structure
=================

Often we need to edit or add structure:

- "Multi-file" upload widget

-  Multiple people in a list

Let's see some of that live.

Miscellaneous Features
======================

- Internationalization: Deform error messages are internationalizable.

- Multiple Forms on the Same Page: possible.

- Inter-field Validation: Validator attached to a container node.

- AJAX Forms: Built-in.

- Input Masks:  Built-in.

Futures
=======

- Deform is "done".

- Systems are being built atop Deform (e.g. a "CRUD" system which
  conflates the model schema with the form schema).

End
===

- Get it via ``easy_install deform``

- Docs at http://docs.repoze.org/deform
