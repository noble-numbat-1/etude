# When Objects Are Alike

## Basic Inheritance

Every class in Python are subclasses of the special build-in *object* class.

```python
class SubClass(object):
    ''' explicitly specify object as superclass '''
    pass
```

Class variable:

```python
class Contact:
    all_contacts: List["Contact"] = []
    def __init__(self, name: str, email: str) -> None:
        self.name = name
        self.email = email
        Contact.all_contacts.append(self)
    def __repr__(self) -> str:
        return (
            f"{self.__class__.__name__}("
            f"{self.name!r}, {self.email!r}"
            f")"
        )
```

Inheritance:

```python
class Supplier(Contact):
    def order(self, order: "Order") -> None:
        print(
            "If this were a real system we would send "
            f"'{order}' order to '{self.name}'"
        )
```

## Extending built-in

```python
from __future__ import annotations
class ContactList(list["Contact"]):
    def search(self, name: str) -> list["Contact"]:
        matching_contacts: list["Contact"] = []
        for contact in self:
            if name in contact.name:
                matching_contacts.append(contact)
        return matching_contacts
class Contact:
    all_contacts = ContactList()
    def __init__(self, name: str, email: str) -> None:
        self.name = name
        self.email = email
        Contact.all_contacts.append(self)
    def __repr__(self) -> str:
        return (
            f"{self.__class__.__name__}(" 
            f"{self.name!r}, {self.email!r}" f")"
        )

>>> [c.name for c in Contact.all_contacts.search('John')]
```

## Overriding and super

```python
class Friend(Contact):
    def __init__(self, name: str, email: str, phone: str) -> None:
        super().__init__(name, email)
        self.phone = phone
```

## Multiple inheritance

```python
class EmailableContact(Contact, MailSender):
    pass
```

Method Resolution Order (MRO):

__mro__

## Polymorphism

TODO Case study
