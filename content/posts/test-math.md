+++
date = 2026-03-18
title = 'Math & Programming Test'
[params]
  math = true
+++

## Inline Math

The quadratic formula is $x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$, where $a \neq 0$.

Euler's identity: $e^{i\pi} + 1 = 0$.

## Block Math

The Gaussian integral:

$$\int_{-\infty}^{\infty} e^{-x^2} dx = \sqrt{\pi}$$

A matrix equation:

$$
A = \begin{pmatrix}
  a_{11} & a_{12} & a_{13} \\
  a_{21} & a_{22} & a_{23} \\
  a_{31} & a_{32} & a_{33}
\end{pmatrix}
$$

Taylor series expansion:

$$f(x) = \sum_{n=0}^{\infty} \frac{f^{(n)}(a)}{n!}(x - a)^n$$

## Programming

A classic binary search in Python:

```python
def binary_search(arr: list[int], target: int) -> int:
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
```

Its time complexity is $O(\log n)$ and space complexity is $O(1)$.

## Mixed Example

Given an array of $n$ elements, the expected number of comparisons for a successful search is:

$$C_{\text{avg}} = \frac{\log_2 n + 1}{2} \approx \log_2 n$$
