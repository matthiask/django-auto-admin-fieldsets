# Django Auto Admin Fieldsets

A Django utility for automatically handling unspecified fields in ModelAdmin fieldsets.

## Installation

```bash
pip install django-auto-admin-fieldsets
```

## Features

- Automatically add unspecified model fields to a designated placeholder in Django admin fieldsets
- Works with all field types, including many-to-many fields
- Respects `exclude` and `readonly_fields` settings
- Supports custom placeholders
- Provides both a mixin and a standalone function for maximum flexibility

## Usage

### Using the Mixin

```python
from django.contrib import admin
from django_auto_admin_fieldsets.admin import AutoFieldsetsMixin
from . import models

@admin.register(models.MyModel)
class MyModelAdmin(AutoFieldsetsMixin, admin.ModelAdmin):
    # Define fieldsets as usual with a placeholder
    fieldsets = [
        ("Basic Information", {"fields": ["title", "slug"]}),
        ("Content", {"fields": ["__remaining__"]}),  # All other fields will appear here
    ]

    # Optional: customize the placeholder (default is "__remaining__")
    remaining_fields_placeholder = "__remaining__"
```

### Using the Convenience ModelAdmin

```python
from django.contrib import admin
from django_auto_admin_fieldsets.admin import AutoFieldsetsModelAdmin
from . import models

@admin.register(models.MyModel)
class MyModelAdmin(AutoFieldsetsModelAdmin):
    # Define fieldsets as usual with a placeholder
    fieldsets = [
        ("Basic Information", {"fields": ["title", "slug"]}),
        ("Content", {"fields": ["__remaining__"]}),  # All other fields will appear here
    ]
```

### Using the Standalone Function

```python
from django.contrib import admin
from django_auto_admin_fieldsets.admin import auto_add_fields_to_fieldsets
from . import models

@admin.register(models.MyModel)
class MyModelAdmin(admin.ModelAdmin):
    fieldsets = [
        ("Basic Information", {"fields": ["title", "slug"]}),
        ("Content", {"fields": ["__remaining__"]}),
    ]

    def get_fieldsets(self, request, obj=None):
        fieldsets = super().get_fieldsets(request, obj)
        return auto_add_fields_to_fieldsets(
            model=self.model,
            fieldsets=fieldsets,
            exclude=self.exclude or [],
            placeholder="__remaining__",
        )

admin.site.register(MyModel, MyModelAdmin)
```

## Configuration Options

- `remaining_fields_placeholder`: The placeholder string to look for in your fieldsets (default: `"__remaining__"`)

The function also respects the standard Django admin configuration options. If
you do not want a field to appear in the resulting `fieldsets`, add it to the
standard `exclude` list.

## Development

### Running Tests

Tests should be run using tox, which tests against multiple Python and Django versions:

```bash
tox
```

This will run the test suite against all supported Python and Django combinations as defined in tox.ini.

### Code Quality

This project uses pre-commit for code quality checks. After cloning the repository, install the pre-commit hooks:

```bash
pre-commit install
```

The pre-commit configuration includes ruff for linting and formatting. Configuration can be found in `pyproject.toml`.

## Compatibility

- Python: 3.10 and above
- Django: 4.2, 5.1, and 5.2 (also tested with Django main)

## License

MIT
