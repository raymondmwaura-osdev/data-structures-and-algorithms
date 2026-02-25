# Fisher-Yates Shuffle Algorithm

The Fisher-Yates shuffle is an efficient and unbiased method for generating a random permutation of a finite sequence. It ensures that every possible arrangement of the sequence is equally likely.

## Core Logic

The algorithm works by moving through a list and swapping each element with another randomly selected element that has not yet been processed. In the modern version, this is done "in-place" by partitioning the list into two parts: a shuffled portion and an unshuffled portion.

### The Step-by-Step Process

+ Start at the last element of the list.
+ Generate a random integer `j` such that `0 ≤ j ≤ i`, where `i` is the current index.
+ Swap the element at index `i` with the element at index `j`.
+ Repeat the process for index `i-1`, continuing until you reach the beginning of the list.

---

## Efficiency and Complexity

This algorithm is the standard for shuffling because it provides maximum efficiency without sacrificing randomness.

+ **Time Complexity**: `O(n)` The list is traversed exactly once.
+ **Space Complexity**: `O(1)` The shuffle is performed in-place, requiring no extra storage.

---

## Why It Is Unbiased

A shuffle is "unbiased" only if every possible permutation of the list has an equal probability of occurring.

The Fisher-Yates shuffle achieves this by ensuring that for every position `i`, andy of the remaining `i` elements has an equal chance of being selected. Many other shuffling methods fail because they do not give every element an equal mathematical probability of ending up in every position.

---

## Pseudocode

```text
To shuffle an array 'a' of n elements:
  For i from n - 1 down to 1:
    j = random integer such that 0 <= j <= i
    Exchange a[i] and a[j]
```

---

## Code Examples

```javascript
function shuffle(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]]; // Swap using destructuring
  }
  return array;
}
```

```python
import random

def fisher_yates_shuffle(arr):
    for i in range(len(arr) - 1, 0, -1):
        j = random.randint(0, i)
        arr[i], arr[j] = arr[j], arr[i]
```

---
