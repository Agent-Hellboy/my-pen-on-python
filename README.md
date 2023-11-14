# my-pen-on-python

# 14 NOV 2023 

# Descriptors vs. __getattr__ and __getattribute__

Descriptors and special methods like `__getattr__` and `__getattribute__` offer control over attribute access in Python. Understanding their differences and capabilities is crucial for managing attribute behaviors within classes.

## Descriptors:

- **Data Descriptors:**
  - Define `__get__()`, `__set__()`, or `__delete__()` methods to control attribute access and modification.
  - Offer precise control over specific attributes within a class.
  - Useful for computed attributes, read-only attributes, and custom access patterns.

- **Non-Data Descriptors:**
  - Define only `__get__()` method and primarily control attribute access without allowing modification.
  - Create read-only attributes that cannot be directly modified after instantiation.

## `__getattr__` and `__getattribute__`:

- **`__getattr__`:**
  - Invoked when an attribute is not found through normal lookup.
  - Acts as a fallback to handle attribute access when the attribute is not directly available.

- **`__getattribute__`:**
  - Called for every attribute access within a class.
  - Intercepts all attribute lookups and impacts all attribute accesses.

### Example illustrating descriptors:

Consider the following Python code:

```python
class DataDescriptor:
    def __init__(self, initial_val):
        self.value = initial_val
    
    def __get__(self, instance, owner):
        print("Getting value")
        return self.value
    
    def __set__(self, instance, value):
        print("Setting value")
        self.value = value

class NonDataDescriptor:
    def __init__(self, initial_val):
        self.value = initial_val
    
    def __get__(self, instance, owner):
        print("Getting value")
        return self.value

class A:
    data = DataDescriptor(5)  # Data descriptor
    nondata = NonDataDescriptor(10)  # Non-data descriptor

a = A()

# Scenario 1: Using Data Descriptor for Controlled Assignment
print(a.data)  # Output: Getting value
a.data = 8  # Output: Setting value
print(a.data)  # Output: Getting value

# Scenario 2: Non-Data Descriptor for Read-Only Attribute
print(a.nondata)  # Output: Getting value
a.nondata = 15  # This won't modify the value, as it's a non-data descriptor
print(a.nondata)  # Output: Getting value
```

## Limitations of Using `__getattribute__` Alone

### No Inherent Attribute Differentiation:

- `__getattribute__` lacks inherent differentiation between different attributes.
- This absence makes it challenging to selectively apply distinct behaviors to specific attributes.
- most of the codebase has to rely on descriptors because of this only

### Impacts All Attribute Accesses:

- Overriding `__getattribute__` affects the behavior of all attribute accesses within the class.
- This wide-reaching impact potentially makes the codebase less maintainable over time.

### Challenges in Read-Only Attribute Behavior:

- Enforcing read-only attribute behavior solely within `__getattribute__` without additional conditional logic or checks can be cumbersome.
- Achieving specific read-only attribute behavior becomes complex and less intuitive.

In summary, while `__getattribute__` provides a potent mechanism for intercepting attribute accesses, achieving the same level of precision and readability as offered by descriptors, especially data and non-data descriptors, might be challenging when exclusively relying on `__getattribute__`


## Example from real world code base 

### Descriptors in ORM: Fine-Grained Control over Every Attribute

In Object-Relational Mapping (ORM), descriptors are essential for providing fine-grained control over every attribute of a table. Each attribute in a database table may have distinct data types and associated properties. Utilizing descriptors allows for preprocessing and implementing specialized behaviors that cannot be accomplished solely with `__getattr__` and `__getattribute__` methods. Descriptors enable ORM systems to handle attribute-specific operations, ensuring data integrity, handling diverse data types, and performing necessary preprocessing before data persistence.
