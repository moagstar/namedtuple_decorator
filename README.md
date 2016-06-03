# namedtuple_decorator

A drop in replacement for the standard function `collections.namedtuple` that
can be used as a class and function decorator, while still supporting the
current standard usage:

```python
from namedtuple_decorator import namedtuple

# The basic function decorator uses the function argument names as field names
@namedtuple
def Point3d(x, y, z):
    """an element of some set called a space."""

# However you may want to generate the field names dynamically, in this case
# you can return field_names as expected by collections.namedtuple from the
# function.
@namedtuple
def Point3d():
    """an element of some set called a space."""
    return (chr(i) for i in range(120, 123))

# It is also possible to use as a class decorator, in which case your class
# should define a _fields member which gives field_names as expected by
# collections.namedtuple
@namedtuple
class Point3d:
    """an element of some set called a space."""
    _fields = 'x y z'

# The function can still be used like collections.namedtuple
Point3d = namedtuple('Point3d', 'x y z')
```

Using as a decorator avoids having to repeat the name. It is also possible to
set the docstring of the namedtuple using the decorator syntax.

The replace and verbose parameters are supported as parameters to both the
class and function decorator, for example:

```python
@namedtuple(replace=True)
def Point(x, y): pass
```

## Installation

Installing from PyPI using pip:

```bash
$ pip install table2dicts
```

Installing from source:

```bash
$ python setup.py install
```

## Motivation

The main motivation for this is to provide an improved syntax for defining a
named tuple, as well as offering the ability to set the docstring on the newly
created type.

## How it works

The function namedtuple selects an implementation based on the parameters that
are passed:

* when given a class we assume that a plain class decorator is intended
* when given a callable we assume that a plain function decorator is intended
    - the function should return either the field_names as expected in
      collections.namedtuple
    - or None in which case the function argument names are used as the
      field_names
* when fields_names is present in keyword arguments, or the second positional
  argument is iterable we assume the classic form of namedtuple is intended
* otherwise we assume a decorator factory is desired with the verbose and
  replace flags passed as arguments.

## TODO

# TODO : Better docstrings (by monkey patching _class_template)
# TODO : Only rename > 2.7
# TODO : Signature in python3 instead of getargspec
# TODO : Sphinx, readthedocs
# TODO : travis, appveyor, circle
# TODO : setup.py pypi
# TODO : test with tox
# TODO : Add test for memoize function
# TODO : Find some way of only displaying the output when verbose==True
# TODO : Don't lose additional methods in class decorator? Maybe create a class that is a child of the namedtuple
# TODO : Add some documentation info about the philosophy behind define the signature
...
# TODO : change dynamic field name syntax so that field names are parameters to the namedtuple function
# TODO : change class decorator to use __init__ signature as field names

For example I prefer this syntax for dynamic field names and class decorator:

```python

# Function decorator with dynamic field names - constructor uses star and
# double star args for the signature and the dynamic field names are passed to
# the namedtuple function
@namedtuple('Field_%d' % x for x in range(10))
def Fields_10(*args, **kwargs): pass

# Class decorator basic usage - you define the constructor signature.
@namedtuple
class Person:
    def __init__(self, name, age, gender): pass
```