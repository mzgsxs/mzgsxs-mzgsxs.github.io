## Python Google/NumPy-style docstrings
GPT generated examples, both supported by sphinx
### Google-Style
Function
```python
def add(a: int, b: int) -> int:
    """
    Add two numbers.

    Args:
        a (int): The first number.
        b (int): The second number.

    Returns:
        int: The sum of the two numbers.

    Raises:
        ValueError: If either `a` or `b` is not an integer.
    """
    if not isinstance(a, int) or not isinstance(b, int):
        raise ValueError("Both arguments must be integers.")
    return a + b
```
Class
```python
class Calculator:
    """
    A simple calculator class.

    Attributes:
        last_result (int): Stores the result of the last calculation.
    """

    def __init__(self):
        """
        Initialize the Calculator with a default result of 0.
        """
        self.last_result = 0

    def multiply(self, x: int, y: int) -> int:
        """
        Multiply two numbers.

        Args:
            x (int): The first number.
            y (int): The second number.

        Returns:
            int: The product of the two numbers.
        """
        self.last_result = x * y
        return self.last_result
```
Module
```python
"""
calculator.py

This module provides basic calculator operations such as addition,
subtraction, multiplication, and division.

Example:
    >>> from calculator import add
    >>> add(2, 3)
    5
"""
...
```



### NumPy-Style
Function
```python
def subtract(a: float, b: float) -> float:
    """
    Subtract one number from another.

    Parameters
    ----------
    a : float
        The first number.
    b : float
        The second number.

    Returns
    -------
    float
        The result of subtracting `b` from `a`.

    Raises
    ------
    ValueError
        If either `a` or `b` is not a number.
    """
    if not isinstance(a, (int, float)) or not isinstance(b, (int, float)):
        raise ValueError("Both arguments must be numbers.")
    return a - b
```
Class
```python
class Matrix:
    """
    A class to represent a mathematical matrix.

    Attributes
    ----------
    rows : int
        Number of rows in the matrix.
    cols : int
        Number of columns in the matrix.
    
    Methods
    -------
    transpose():
        Returns the transpose of the matrix.
    """

    def __init__(self, rows: int, cols: int):
        """
        Initialize a matrix with given dimensions.

        Parameters
        ----------
        rows : int
            Number of rows in the matrix.
        cols : int
            Number of columns in the matrix.
        """
        self.rows = rows
        self.cols = cols

    def transpose(self):
        """
        Transpose the matrix.

        Returns
        -------
        Matrix
            A new Matrix instance that is the transpose of this matrix.
        """
        # Implementation here...
```
Module
```python
"""
matrix_operations.py

This module provides functions for basic matrix operations such as addition,
multiplication, and inversion.

Examples
--------
>>> from matrix_operations import add_matrices
>>> A = [[1, 2], [3, 4]]
>>> B = [[5, 6], [7, 8]]
>>> add_matrices(A, B)
[[6, 8], [10, 12]]
"""
...
```
