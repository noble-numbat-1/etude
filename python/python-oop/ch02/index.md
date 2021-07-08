# Objects in Python

Installing third-party libraries:

`python -m pip install mypy`

or using *the conda tool*:

`conda install mypy`

Special variable **__name__**: specifies the name of the module when it was imported.

## Working with Object and Class

Constructor method:

__new__()

Initializer method:

__init__()

Example:

```python
import math
class Point:
    def __init__(self, x: float = 0, y: float = 0) -> None:
        self.move(x, y)
    def move(self, x: float, y: float) -> None:
        self.x = x
        self.y = y
    def calc_distance(self, other: "Point") -> float:
        return math.hypot(self.x - other.x, self.y - other.y)
```

Representation:

__repr__(): is used to create a string representation of the object in the form of Python expression to rebuild the object.

```python
class Sample:
    def __init__(
        self,
        sepal_length: float,
        sepal_width: float,
        petal_length: float,
        petal_width: float,
        species: Optional[str] = None,
    ) -> None:
        self.sepal_length = sepal_length
        self.sepal_width = sepal_width
        self.petal_length = petal_length
        self.petal_width = petal_width
        self.species = species
        self.classification: Optional[str] = None
    def __repr__(self) -> str:
        if self.species is None:
            known_unknown = "UnknownSample"
        else:
            known_unknown = "KnownSample"
        if self.classification is None:
            classification = ""
        else:
            classification = f", {self.classification}"
        return (
            f"{known_unknown}("
            f"sepal_length={self.sepal_length}, "
            f"sepal_width={self.sepal_width}, "
            f"petal_length={self.petal_length}, "
            f"petal_width={self.petal_width}, "
            f"species={self.species!r}"
            f"{classification}"
            f")"
        )
```

## Modules and Packages

The *import* statement:

- import mudule (import database): import the database module, creating a database namespace
- from...import (from database import Database): import only the Database class from the database module
- from...import...as (from database import Database as DB): import Database class and rename as DB from the database module
- import multiple items (from database import Database, Query)
- from database import * (avoid this): import all classes and functions from the database module

### Absolute imports

Specify the complete path to the module.
A package (folder) must contain an empty file name **__init__.py** to mark as package.

eg:

- import folder.module
- or var = folder.module.[Class, Function,...]

Import a single class

- from folder.module import Class

Import an entire module

- from folder import module

### Relative imports

Import other module from the same package

- from .siblingModule import Class

Import other module from parent package

- from ..parentModule import Class
- from ..parentPackage.module import Class

### Packages as a whole

The module in a package could be export as a whole by importing in the *__init__.py* file.

eg:

```python
# __init__.py
from .database import db
```

## Third-party Libraries

List of library can be found at [Python Package Index: PyPI](http://pypi.python.org/)

- venv: **Virtual Environment**

```bash
$ cd project_directory
$ python -m venv env

$ source env/bin/activate
# on Linux or macOs

$ env/Scripts/activate.bat
# on Windows

$ deactivate
# to deactivate the environment
```

There are several third-party tools for virtual environment:

- virtualenv
- pyenv
- virtualenvwrapper
- conda (if working with data scrience, it can install more complex package)