# Python
# Python Interview Questions & Solutions

## 1. Find Second Largest Element

### Way 1: Using `set()` + `sort()`
```python
def second_largest(lst):
    unique = list(set(lst))
    unique.sort()
    return unique[-2]

nums = [10, 20, 4, 45, 99, 99]
print(second_largest(nums))  # 45
```
*Using `set()` removes duplicates first, so "second largest" means second largest distinct value.*

### Way 2: Single pass, no sorting (O(n) instead of O(n log n))
```python
def second_largest_v2(lst):
    first = second = float('-inf')
    for num in lst:
        if num > first:
            second = first
            first = num
        elif first > num > second:
            second = num
    return second

print(second_largest_v2([10, 20, 4, 45, 99, 99]))  # 45
```

### Way 3: Using `heapq`
```python
import heapq

def second_largest_v3(lst):
    return heapq.nlargest(2, set(lst))[-1]

print(second_largest_v3([10, 20, 4, 45, 99, 99]))  # 45
```

### Way 4: One-liner with `sorted()` + `set()`
```python
nums = [10, 20, 4, 45, 99, 99]
print(sorted(set(nums))[-2])  # 45
```

---

## 2. Find Duplicates in a List

### Way 1: Using two sets
```python
def find_duplicates(lst):
    seen = set()
    duplicates = set()
    for item in lst:
        if item in seen:
            duplicates.add(item)
        else:
            seen.add(item)
    return list(duplicates)

nums = [1, 2, 3, 2, 4, 5, 1, 6]
print(find_duplicates(nums))  # [1, 2]
```

### Way 2: Using `collections.Counter`
```python
from collections import Counter

def find_duplicates_v2(lst):
    counts = Counter(lst)
    return [item for item, freq in counts.items() if freq > 1]

print(find_duplicates_v2([1, 2, 3, 2, 4, 5, 1, 6]))  # [1, 2]
```

### Way 3: List comprehension with `.count()` (simple but slower — O(n²))
```python
def find_duplicates_v3(lst):
    return list(set([x for x in lst if lst.count(x) > 1]))

print(find_duplicates_v3([1, 2, 3, 2, 4, 5, 1, 6]))  # [1, 2]
```

### Way 4: Set difference trick
```python
def find_duplicates_v4(lst):
    seen, dups = set(), set()
    [dups.add(x) if x in seen else seen.add(x) for x in lst]
    return list(dups)
```

---

## 3. Create a Class (Name & Age using self)

### Way 1: Standard class with `__init__`
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def display(self):
        print(f"Name: {self.name}, Age: {self.age}")

p1 = Person("Rajitha", 25)
p1.display()  # Name: Rajitha, Age: 25
```

### Way 2: Using `dataclass` (modern, concise)
```python
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int

p1 = Person("Rajitha", 25)
print(p1)  # Person(name='Rajitha', age=25)
```

### Way 3: Using `namedtuple` (immutable, lightweight)
```python
from collections import namedtuple

Person = namedtuple("Person", ["name", "age"])
p1 = Person("Rajitha", 25)
print(p1.name, p1.age)  # Rajitha 25
```

### Way 4: Using `__slots__` (memory-efficient class)
```python
class Person:
    __slots__ = ['name', 'age']
    def __init__(self, name, age):
        self.name = name
        self.age = age

p1 = Person("Rajitha", 25)
print(p1.name, p1.age)
```

---

## 4. Update Object Attribute (Rajitha → Raji)

### Way 1: Direct attribute assignment
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

p1 = Person("Rajitha", 25)
print("Before:", p1.name)   # Before: Rajitha

p1.name = "Raji"            # updating the attribute
print("After:", p1.name)    # After: Raji
```

### Way 2: Using `setattr()`
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

p1 = Person("Rajitha", 25)
setattr(p1, "name", "Raji")
print(p1.name)  # Raji
```

### Way 3: Using a dedicated setter method
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def set_name(self, new_name):
        self.name = new_name

p1 = Person("Rajitha", 25)
p1.set_name("Raji")
print(p1.name)  # Raji
```

### Way 4: Using `@property` for controlled updates
```python
class Person:
    def __init__(self, name, age):
        self._name = name
        self.age = age

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        self._name = value

p1 = Person("Rajitha", 25)
p1.name = "Raji"   # goes through the setter
print(p1.name)     # Raji
```

---

## 5. String Frequency: aaabbbccc → a-3 b-3 c-3

### Way 1: Manual dictionary with `.get()`
```python
def char_frequency(s):
    freq = {}
    for ch in s:
        freq[ch] = freq.get(ch, 0) + 1
    return freq

s = "aaabbbccc"
result = char_frequency(s)
for ch, count in result.items():
    print(f"{ch}-{count}", end=" ")
# Output: a-3 b-3 c-3
```

### Way 2: Using `collections.Counter` (cleanest)
```python
from collections import Counter
freq = Counter("aaabbbccc")
print(" ".join(f"{ch}-{count}" for ch, count in freq.items()))
# a-3 b-3 c-3
```

### Way 3: Using `itertools.groupby` (works on consecutive/grouped chars)
```python
from itertools import groupby

s = "aaabbbccc"
result = [(ch, len(list(group))) for ch, group in groupby(s)]
print(" ".join(f"{ch}-{count}" for ch, count in result))
# a-3 b-3 c-3
```

### Way 4: Manual dictionary with `defaultdict`
```python
from collections import defaultdict

def char_frequency_v4(s):
    freq = defaultdict(int)
    for ch in s:
        freq[ch] += 1
    return dict(freq)

print(char_frequency_v4("aaabbbccc"))  # {'a': 3, 'b': 3, 'c': 3}
```

### Way 5: Using `set()` + `.count()`
```python
s = "aaabbbccc"
result = {ch: s.count(ch) for ch in set(s)}
print(result)  # {'a': 3, 'b': 3, 'c': 3} (order may vary)
```

> **Note:** `groupby` (Way 3) only groups *consecutive* identical characters — it would give wrong results for something like `"abcabc"`. `Counter` and `dict.get()` count occurrences anywhere in the string, so they're safer general-purpose choices.
