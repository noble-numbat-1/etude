# Objects in Python

Installing third-party libraries:

`python -m pip install mypy`

or using *the conda tool*:

`conda install mypy`

Special variable **__name__**: specifies the name of the module when it was imported.

## Working with Object and Class

Constructor method:

__new__()

initializer method:

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

Stop At: Who can access my data?