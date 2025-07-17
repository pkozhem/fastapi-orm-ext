# FastApi ORM Extensions

[![PyPI - Version](https://img.shields.io/pypi/v/fastapi-orm-ext.svg)](https://pypi.org/project/fastapi-orm-ext)
[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/fastapi-orm-ext.svg)](https://pypi.org/project/fastapi-orm-ext)

-----

## Table of Contents
- [About](#About)
- [Installation](#installation)
- [License](#license)
- [Contribution](#contribution)

## About
This library provides preset base class for your tables and repositories.
#### Use TableBase like so:
```python
from fastapi_orm_ext.table import TableBase
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column


class Table(TableBase, DeclarativeBase):
    # you can use this class in alembic's env.py file
    # to specify target_metadata for example
    __abstract__: bool = True


class User(Table, DeclarativeBase):
    name: Mapped[str] = mapped_column(nullable=False)
    surnname: Mapped[str] = mapped_column(nullable=True)
```

TableBase consists of four mixins:
- NameConventionMixin: handle name convention;
- TableNameMixin: takes model's class name and convert it to snake case, use this name while creating table in DB;
- TimestampsMixin: handles when record was created and updated;
- UUIDPrimaryKeyMixin: makes PK of UUID4 type.

#### Use Repository like so:
```python
from fastapi_orm_ext.repository import RepositoryBase
from pydantic import BaseModel

from app.tables import User
# the variant to get async session
from app.utils import get_async_session()


class CreateUserSchema(BaseModel):
    name: str
    surname: str | None


class UserRepository(RepositoryBase[User]):
    # specify the model to interact with
    model = User
    # choose flush or commit
    auto_flush = True
    auto_commit = False

# initialize UserRepository
repo = UserRepository(async_session)
# create new record in user table with name Bob and surname NULL
data = CreateUserSchema(name="Bob")
await repo.create(data)

# get all records in User table - in our case only Bob
res: list[User] = await repo.all()
print(res)
```

To see what else RepositoryBase can do, visit the source code of interface RepositoryBase inheriting from

## Installation

```console
pip install fastapi-orm-ext
```
or
```console
uv add fastapi-orm-ext
```
or
```
poetry add fastapi-orm-ext
```
etc


## License

`fastapi-orm-ext` is distributed under the terms of the [MIT](https://spdx.org/licenses/MIT.html) license.


## Contribution
Install repository:
```console
https://github.com/pkozhem/fastapi-orm-ext.git
```

Create virtual environment, activate it and install dependencies:
```console
uv venv
source .venv/bin/activate
uv sync
```

Create new branch from actual tag:
```console
git checkout <tag>
git branch pull-<fix, feat, impr>: Short branch desc
```

Pull your changes and create pull request:
```console
git pull origin <your_branch_name>
```
