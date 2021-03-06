Addon Configuration
===================

If you want to provide configuration (and a nice form) for your app, you may add a file
``aldryn_config.py`` to the root of your app (next to setup.py). This configuration form will
then be displayed on the detail page of the installed addon on the controlpanel.

.. image:: ../../_static/addons/screenshot_addon_configuration.png

This file **must** contain a class named ``Form`` which **must** subclass ``aldryn_client.forms.BaseForm``.

The ``Form`` class may contain any number of form fields.

Available fields are:

* ``aldryn_client.forms.CharField``
* ``aldryn_client.forms.CheckboxField``
* ``aldryn_client.forms.SelectField``
* ``aldryn_client.forms.NumberField``
* ``aldryn_client.forms.StaticFileField``

All fields must provide a label as first argument and take a keyword argument named ``required`` to indicate
whether this field is required or not.
Additionally, all fields support ``help_text`` and ``initial`` keyword arguments.

``CharFields`` take optional ``max_length`` and ``min_length`` arguments.

``SelectField`` takes a list of tuples (Django style) as required second argument).

``NumberField`` has optional ``min_value`` and ``max_value`` arguments.

``StaticFileField`` takes an optional ``extensions`` argument which is a list of valid file extensions.


Generating settings
-------------------

To generate settings, define a ``to_settings`` method which takes two arguments:

* A cleaned data of your form
* A dictionary of existing settings (which contains ``MIDDLEWARE_CLASSES`` and ``DEBUG``).

Add the settings you want on to the settings dictionary and return it.

Example:

.. code-block:: python

    # -*- coding: utf-8 -*-
    from aldryn_client import forms


    class Form(forms.BaseForm):

        ...

        def to_settings(self, cleaned_data, settings_dict):
            if settings_dict.get('DEBUG'):
                settings_dict['EMAIL_BACKEND'] = 'django.core.mail.backends.console.EmailBackend'
            return settings_dict



Custom field validation
-----------------------

If you want to have custom field validation, subclass a field and overwrite it's ``clean`` method,
which takes a single argument (the value to clean) and should return a cleaned value or raise
``aldryn_client.forms.ValidationError`` with a nice message as to why the validation failed.

Example:

.. code-block:: python

    # -*- coding: utf-8 -*-
    from aldryn_client import forms


    class EvenNumberField(forms.NumberField):
        def clean(self, value):
            value = super(EvenNumberField, self).clean(value)
            if value % 2 != 0:
                raise forms.ValidationError('Please provide an even number')
            else:
                return value


Extending the Toolbar
---------------------

To create a better user experience, django CMS 3 provides a way of extending the toolbar so you could add entrues such
as **Block > Add new entry** to make it easier for the user to use your addon. A detailed guide can be found within
the django CMS documentation:
http://django-cms.readthedocs.org/en/develop/extending_cms/toolbar.html
