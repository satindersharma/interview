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
**Explanation:**
- `set(lst)` removes duplicate values, so `99` appearing twice becomes just one `99`.
- `.sort()` arranges the unique values in ascending order.
- `unique[-2]` picks the second-from-last element — i.e., the second largest.
- **Time Complexity:** O(n log n) due to sorting.
- **When to use:** Simple, readable, good for small-to-medium lists where clarity matters more than speed.

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
**Explanation:**
- We keep track of the largest (`first`) and second largest (`second`) seen so far, starting both at negative infinity.
- As we loop through the list once, whenever we find a number bigger than `first`, the old `first` becomes the new `second`, and the number becomes the new `first`.
- If a number is between `second` and `first` (but not equal to `first`), it becomes the new `second`.
- **Time Complexity:** O(n) — only one pass through the list, no sorting needed.
- **When to use:** Best for large lists or performance-critical code, since it avoids the O(n log n) cost of sorting.

### Way 3: Using `heapq`
```python
import heapq

def second_largest_v3(lst):
    return heapq.nlargest(2, set(lst))[-1]

print(second_largest_v3([10, 20, 4, 45, 99, 99]))  # 45
```
**Explanation:**
- `heapq.nlargest(2, set(lst))` efficiently finds the 2 largest unique values without fully sorting the list.
- `[-1]` grabs the second item in that top-2 list (the smaller of the two, i.e., the second largest overall).
- **Time Complexity:** O(n log k) where k=2, so effectively O(n) — very efficient for finding just the "top few" values.
- **When to use:** Great when you need the "top N" values from a very large dataset, since heaps avoid sorting everything.

### Way 4: One-liner with `sorted()` + `set()`
```python
nums = [10, 20, 4, 45, 99, 99]
print(sorted(set(nums))[-2])  # 45
```
**Explanation:**
- Same idea as Way 1, just written as a compact one-liner instead of a function.
- `sorted(set(nums))` creates a new sorted list of unique values, and `[-2]` picks the second largest.
- **Time Complexity:** O(n log n).
- **When to use:** Quick and readable for scripts or interviews where you want to show a concise solution.

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
**Explanation:**
- We walk through the list once, keeping a `seen` set of everything we've encountered so far.
- If an item is already in `seen`, it means we've seen it before — so it's a duplicate, and we add it to the `duplicates` set.
- Using sets (instead of lists) makes the "have I seen this before?" check very fast.
- **Time Complexity:** O(n) — a single pass with O(1) average set lookups.
- **When to use:** The most efficient general-purpose approach; good default choice.

### Way 2: Using `collections.Counter`
```python
from collections import Counter

def find_duplicates_v2(lst):
    counts = Counter(lst)
    return [item for item, freq in counts.items() if freq > 1]

print(find_duplicates_v2([1, 2, 3, 2, 4, 5, 1, 6]))  # [1, 2]
```
**Explanation:**
- `Counter(lst)` builds a dictionary-like object that counts how many times each element appears.
- We then filter for items whose count (`freq`) is greater than 1 — those are the duplicates.
- **Time Complexity:** O(n) — Counter builds the frequency map in one pass.
- **When to use:** Very readable and idiomatic Python; especially handy if you also want the actual counts, not just which items are duplicated.

### Way 3: List comprehension with `.count()` (simple but slower — O(n²))
```python
def find_duplicates_v3(lst):
    return list(set([x for x in lst if lst.count(x) > 1]))

print(find_duplicates_v3([1, 2, 3, 2, 4, 5, 1, 6]))  # [1, 2]
```
**Explanation:**
- For every element `x` in the list, `lst.count(x)` counts how many times it appears in the *entire* list.
- If it appears more than once, we keep it. Wrapping in `set()` removes repeated duplicates from the result itself.
- **Time Complexity:** O(n²) — because `.count()` re-scans the whole list for every single element.
- **When to use:** Only for small lists or quick prototyping; avoid for large datasets since it's much slower than the other methods.

### Way 4: Set difference trick
```python
def find_duplicates_v4(lst):
    seen, dups = set(), set()
    [dups.add(x) if x in seen else seen.add(x) for x in lst]
    return list(dups)
```
**Explanation:**
- Functionally identical to Way 1, but written as a list comprehension with a side effect (adding to sets) instead of a `for` loop with `if/else`.
- **Time Complexity:** O(n).
- **When to use:** Mostly a style choice — some prefer the compactness, though a plain `for` loop (Way 1) is usually considered more readable/Pythonic since this comprehension is used only for its side effects, not to build a list.

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
**Explanation:**
- `__init__` is the constructor — it runs automatically when a new `Person` object is created.
- `self` refers to the specific object being created/used; `self.name = name` stores the passed-in value as an attribute *on that object*.
- `display()` is a regular method that can access those stored attributes via `self`.
- **When to use:** The standard, most explicit way to define a class in Python — good for understanding OOP fundamentals and for classes that need custom methods/behavior.

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
**Explanation:**
- The `@dataclass` decorator automatically generates `__init__`, `__repr__`, and `__eq__` methods for you based on the type-annotated fields (`name: str`, `age: int`).
- You get a nice printable representation (`Person(name='Rajitha', age=25)`) for free, without manually writing `__init__` or `display()`.
- **When to use:** Ideal for classes that are mainly containers for data (like structs), reducing boilerplate significantly.

### Way 3: Using `namedtuple` (immutable, lightweight)
```python
from collections import namedtuple

Person = namedtuple("Person", ["name", "age"])
p1 = Person("Rajitha", 25)
print(p1.name, p1.age)  # Rajitha 25
```
**Explanation:**
- `namedtuple` creates a lightweight, tuple-based class where fields can be accessed by name (`p1.name`) instead of just by index (`p1[0]`).
- Unlike a regular class or dataclass, instances are **immutable** — once created, `p1.name = "Raji"` would raise an error.
- **When to use:** Good for simple, fixed data records that shouldn't change after creation, and where memory efficiency matters.

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
**Explanation:**
- Normally, Python objects store their attributes in a flexible `__dict__`, which uses extra memory.
- `__slots__` tells Python to pre-allocate space only for the listed attributes (`name`, `age`), skipping the `__dict__` — this saves memory and can slightly speed up attribute access.
- The tradeoff: you can't add new attributes dynamically (e.g., `p1.email = "x"` would fail).
- **When to use:** When creating *many* instances of a class (e.g., thousands/millions) and memory usage matters.

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
**Explanation:**
- Since `name` is a public attribute, you can simply reassign it with `p1.name = "Raji"`.
- This directly overwrites the value stored on the object.
- **When to use:** The simplest and most common way to update an attribute in Python — use this unless you have a specific reason for validation/control (see Way 4).

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
**Explanation:**
- `setattr(object, attribute_name, value)` does the same thing as direct assignment, but the attribute name is passed as a **string**.
- This is useful when the attribute name isn't known until runtime (e.g., it comes from user input or a config file).
- **When to use:** Dynamic scenarios — e.g., looping through a dictionary of `{field_name: new_value}` pairs and updating each one programmatically.

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
**Explanation:**
- Instead of updating the attribute from outside the class, we define a method (`set_name`) that handles the update internally.
- This keeps the "how do I update this" logic inside the class, which is useful if later you want to add logging, validation, or side effects when the name changes.
- **When to use:** Common in more traditional OOP codebases (similar to Java-style getters/setters) or when you anticipate needing extra logic later.

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
**Explanation:**
- `_name` (with underscore) is treated as the "internal" storage for the attribute.
- The `@property` decorator lets `p1.name` be read like a normal attribute, even though it's actually calling the `name()` getter method behind the scenes.
- The `@name.setter` decorator lets `p1.name = "Raji"` look like a normal assignment, but it actually runs the `name(self, value)` method — so you could add validation (e.g., "reject empty names") without changing how the attribute is used from outside.
- **When to use:** When you want the clean syntax of direct attribute access (`p1.name = ...`) but still need control/validation behind the scenes — the best of both worlds between Way 1 and Way 3.

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
**Explanation:**
- We loop through each character in the string.
- `freq.get(ch, 0)` looks up the current count for that character, defaulting to `0` if it hasn't been seen yet, then we add 1.
- This builds up a dictionary mapping each character to how many times it appears anywhere in the string.
- **Time Complexity:** O(n).
- **When to use:** Good for understanding the core logic from scratch; useful when `collections` isn't available or when teaching the underlying concept.

### Way 2: Using `collections.Counter` (cleanest)
```python
from collections import Counter
freq = Counter("aaabbbccc")
print(" ".join(f"{ch}-{count}" for ch, count in freq.items()))
# a-3 b-3 c-3
```
**Explanation:**
- `Counter` is a specialized dictionary built exactly for counting hashable items — passing it a string automatically counts each character's occurrences.
- `freq.items()` gives `(character, count)` pairs, which we format into `"a-3"` style strings and join with spaces.
- **Time Complexity:** O(n).
- **When to use:** The most Pythonic and readable solution — preferred in real-world code and most interviews unless you're asked to implement it manually.

### Way 3: Using `itertools.groupby` (works on consecutive/grouped chars)
```python
from itertools import groupby

s = "aaabbbccc"
result = [(ch, len(list(group))) for ch, group in groupby(s)]
print(" ".join(f"{ch}-{count}" for ch, count in result))
# a-3 b-3 c-3
```
**Explanation:**
- `groupby(s)` groups **consecutive** identical characters together as it scans left to right.
- For each group, we count how many characters are in it using `len(list(group))`.
- This happens to give the right answer for `"aaabbbccc"` because each letter's occurrences are all bunched together — but it is *not* a general character-frequency counter.
- **Time Complexity:** O(n).
- **⚠️ Caution:** Only use this if you know identical characters always appear next to each other. For a string like `"abcabc"`, this would incorrectly report each character as having a count of 1 in three separate groups, instead of 2 total.

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
**Explanation:**
- `defaultdict(int)` automatically creates a default value of `0` for any new key the first time it's accessed, so `freq[ch] += 1` works without needing `.get()` or an `if` check.
- Functionally the same result as Way 1, just slightly cleaner syntax.
- **Time Complexity:** O(n).
- **When to use:** Handy pattern any time you're counting/grouping items and want to avoid manually checking "does this key exist yet?"

### Way 5: Using `set()` + `.count()`
```python
s = "aaabbbccc"
result = {ch: s.count(ch) for ch in set(s)}
print(result)  # {'a': 3, 'b': 3, 'c': 3} (order may vary)
```
**Explanation:**
- `set(s)` gets each unique character in the string.
- For each unique character, `s.count(ch)` counts how many times it appears in the whole string.
- **Time Complexity:** O(n × k) where k is the number of unique characters — slower than the other approaches for large strings with many unique characters, since `.count()` rescans the string each time.
- **When to use:** Fine for short strings or quick one-off scripts, but avoid for large-scale text processing.

---

## Summary Table

| Problem | Most Efficient Approach | Time Complexity |
|---|---|---|
| Second Largest Element | Single pass (Way 2) | O(n) |
| Find Duplicates | Two sets or Counter (Way 1/2) | O(n) |
| Create a Class | Standard `__init__` or `dataclass` | N/A |
| Update Attribute | Direct assignment (Way 1) | O(1) |
| String Frequency | `Counter` (Way 2) | O(n) |




```python

Transactions = [
{"id": 1, "amount": 200, "type": "credit"},
{"id": 2, "amount": 100, "type": "debit"},
{"id": 3, "amount": 400, "type": "credit"},
{"id": 4, "amount": 150, "type": "debit"},
{"id": 5, "amount": 90, "type": "credit"},
]
 
# Sort:
# 1. Credit first
# 2. Within each type --> descending amount
#Use sorted() with lambda


Transactions = [
{"id": 1, "amount": 200, "type": "credit"},
{"id": 2, "amount": 100, "type": "debit"},
{"id": 3, "amount": 400, "type": "credit"},
{"id": 4, "amount": 150, "type": "debit"},
{"id": 5, "amount": 90, "type": "credit"},
]

sorted_transactions = sorted(
    Transactions,
    key=lambda x: (
        0 if x["type"] == "credit" else 1,  # credit first
        -x["amount"]                         # amount descending within type
    )
)

print(sorted_transactions)
```
