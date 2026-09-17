
# Automated Software Testing Examples

This project is a collection of small Python programs and `pytest` tests. It
is designed for learning how software is checked automatically instead of
being tested only by manually running a program and looking at the output.

The examples cover four important testing ideas:

1. **Assertions** - checking lists, dictionaries, sets, and decimal numbers.
2. **Bank testing** - testing a bank account class and a grading function.
3. **Positive and negative testing** - checking both accepted and rejected
	 input.
4. **Roman numeral testing** - validating and converting Roman numerals.

Each topic is kept in its own folder. The folders are independent examples,
so you can study or run one topic at a time.

## What you need

- Python 3.8 or newer
- `pytest`
- A terminal such as PowerShell, Command Prompt, or the VS Code terminal

There is currently no `requirements.txt` file. Install `pytest` with one of
these Windows commands:

```powershell
py -m pip install pytest
```

If the `py` command is not available, use:

```powershell
python -m pip install pytest
```

The `py` command is the usual Python launcher on Windows. The examples below
use `py -m pytest`; `python -m pytest` is an equivalent alternative when
`python` is configured on your PATH.

## Running the tests

Open a terminal in this project folder. Run tests from the folder that contains
the code and test files:

```powershell
cd Assertions_Testing
py -m pytest -q
```

Repeat the same process for the other folders:

```powershell
cd ..\Bank_Testing
py -m pytest -q

cd '..\Postive&Negative_Testing'
py -m pytest -q

cd ..\Roman_Testing
py -m pytest -q
```

The folder name `Postive&Negative_Testing` is intentionally kept as it exists
in this project. The word "Positive" is misspelled in the folder name, so use
the exact spelling when typing the command.

The `-q` option means "quiet": pytest prints a short summary. Remove `-q` if
you want more detail about each test.

To run every test from the project root, you can also use:

```powershell
py -m pytest -q Assertions_Testing Bank_Testing 'Postive&Negative_Testing' Roman_Testing
```

## Understanding pytest

Pytest discovers functions whose names start with `test_` in files whose names
start with `test_` or end with `_test.py`. A test normally uses Python's
`assert` statement:

```python
assert actual_value == expected_value
```

If the condition is true, the test passes. If it is false, the test fails and
pytest shows the values that were different.

Tests can also check that bad input raises the correct error:

```python
with pytest.raises(ValueError):
		function_that_should_reject_input()
```

This is important because a program is not correct merely because it handles
valid input. It should also reject invalid input in a predictable way.

## Project structure

```text
automated_software_testing/
|-- README.md
|-- Assertions_Testing/
|   |-- README.md
|   |-- test_collections.py
|   `-- test_floats.py
|-- Bank_Testing/
|   |-- README.md
|   |-- bank.py
|   |-- grade.py
|   |-- test_bank.py
|   |-- test_dependent.py
|   `-- test_independent.py
|-- Postive&Negative_Testing/
|   |-- README.md
|   |-- validators.py
|   |-- test_positivevalidators.py
|   `-- test_negativevalidators.py
`-- Roman_Testing/
		|-- README.md
		|-- roman.py
		`-- test_roman.py
```

The smaller README files provide topic-specific notes. This file is the main
guide for the whole project.

## 1. Assertions testing

Folder: `Assertions_Testing`

This folder demonstrates how assertions compare common Python data types.

### `test_collections.py`

- `test_list_equality` checks that two lists contain the same values in the
	same order.
- `test_list_contents` sorts a list before comparing it, so the original order
	does not matter for this example.
- `test_dict_equality` checks dictionaries. Dictionary key order does not
	affect equality.
- `test_set_operations` checks the intersection of two sets, which means the
	values that appear in both sets.

### `test_floats.py`

Decimal calculations can contain tiny rounding differences in computers. For
example, `0.1 + 0.2` is not represented exactly in binary floating-point
format. The first test uses `pytest.approx(0.3)` to compare values within a
small acceptable tolerance.

The second test intentionally confirms that the exact comparison
`0.1 + 0.2 != 0.3` is true. Together, the tests show why `approx` is safer
when the expected result is a decimal calculation.

## 2. Bank and grade testing

Folder: `Bank_Testing`

### `bank.py`

`BankAccount` stores a balance and provides two operations:

```python
from bank import BankAccount

account = BankAccount(100)
account.deposit(50)       # returns 150
account.withdraw(30)      # returns 120
account.balance           # is now 120
```

- `BankAccount(balance=0)` creates an account. The default balance is `0`.
- `deposit(amount)` adds the amount and returns the new balance. A zero or
	negative deposit raises `ValueError`.
- `withdraw(amount)` subtracts the amount and returns the new balance. A
	withdrawal greater than the current balance raises `ValueError` with an
	insufficient-funds message.

### `grade.py`

The `letter_grade(score)` function converts a score from 0 to 100 into a
letter:

| Score | Grade |
|---|---|
| 80-100 | A |
| 70-79 | B |
| 60-69 | C |
| 0-59 | F |

Scores below 0 or above 100 raise `ValueError`. The current folder contains
the grading implementation, but it does not currently contain a
`test_grades.py` file; the bank tests are the automated tests supplied in this
folder.

### Test independence

- `test_bank.py` checks that depositing 50 into an account containing 100
	produces 150.
- `test_dependent.py` contains two separate tests that each create their own
	account. Despite its name, the current code does not share an account
	between those tests.
- `test_independent.py` also creates a fresh account in every test and shows
	the preferred pattern: one test should not depend on state left behind by a
	different test.

Fresh test data makes failures easier to understand and allows tests to run in
any order.

## 3. Positive and negative testing

Folder: `Postive&Negative_Testing`

This example validates email addresses and ages. A **positive test** gives the
program input that should be accepted. A **negative test** gives input that
should be rejected and checks the error type.

### `validators.py`

`validate_email(email)` returns `True` for an email matching the project's
regular expression. It expects a username, an `@`, a domain, a dot, and a
top-level domain containing at least two letters. Examples accepted by the
tests include `student@siam.ac.th` and `user@mail.example.com`. Invalid input
raises `ValueError`.

`validate_age(age)` returns `True` when the value is an integer from 0 through
150, inclusive.

- A non-integer raises `TypeError`.
- An integer below 0 or above 150 raises `ValueError`.

### Test files

- `test_positivevalidators.py` checks valid emails, age 25, and the boundary
	ages 0 and 150.
- `test_negativevalidators.py` checks an email without `@`, an email without a
	domain, a negative age, and a text value used as an age.

The difference between `TypeError` and `ValueError` is deliberate: a string is
the wrong kind of data, while `-5` is the right kind of data but outside the
allowed range.

You can also run the small demonstration in `validators.py` directly:

```powershell
cd 'Postive&Negative_Testing'
py validators.py
```

The folder path is quoted because `&` has a special meaning in PowerShell.

## 4. Roman numeral testing

Folder: `Roman_Testing`

This example converts valid Roman numerals into ordinary integers and rejects
invalid formats.

### `roman.py`

`roman_to_integer(roman)` accepts uppercase or lowercase input and returns an
integer from 1 through 3999. It supports the standard symbols `I`, `V`, `X`,
`L`, `C`, `D`, and `M`.

The validation rules include:

- `V`, `L`, and `D` cannot be repeated.
- `I`, `X`, `C`, and `M` cannot appear four times in a row.
- Only `IV`, `IX`, `XL`, `XC`, `CD`, and `CM` are valid subtractive pairs.
- The complete numeral must be in canonical order, not merely produce a
	mathematically plausible number.
- Empty input, unknown characters, and values outside 1-3999 raise
	`ValueError`.

`integer_to_roman(number)` builds a Roman numeral using the largest possible
	symbol first. It is used internally to check that an input was written in
	standard form.

Run the interactive program with:

```powershell
cd Roman_Testing
py roman.py
```

Example session:

```text
Enter a Roman numeral: XIV
Integer: 14
Do you want to continue? (yes/no): no
Goodbye!
```

The program accepts `yes`/`y` to enter another numeral and `no`/`n` to exit.
For invalid input it prints the reason and asks again.

### `test_roman.py`

The tests cover ordinary values such as `I`, `VI`, and `MD`, subtractive
values such as `IV` and `IX`, lowercase input, and the upper boundary `MMMCMXCIX`
(3999). They also verify that invalid input raises `ValueError`, including
empty input, illegal repetition, unknown characters, invalid subtraction, bad
ordering, and a value above 3999.

## Testing lessons shown by this project

- Test normal cases and boundary cases, not just one happy-path example.
- Test invalid input and confirm the expected exception type.
- Keep tests independent by creating fresh objects inside each test.
- Use tolerant comparisons for floating-point calculations.
- Test both a function's returned value and its effects on an object, such as
	the changed bank balance.
- Parameterize similar tests when many inputs should follow the same rule.

## Troubleshooting

### `pytest` is not recognized

Run pytest through Python instead of calling it directly:

```powershell
py -m pytest -q
```

If that also fails, install Python and ensure the Python launcher is available,
then install pytest with `py -m pip install pytest`.

### An import fails

Run the command from the specific exercise folder, such as `Bank_Testing`,
because the test files import local modules such as `bank` and `roman`.

### PowerShell rejects the positive/negative folder command

The `&` character has a special meaning in PowerShell. Put the folder path in
single quotes:

```powershell
cd 'Positive&Negative_Testing'
```

## Summary

This project is a practical introduction to automated testing in Python. The
production code is intentionally small, while the tests demonstrate how to
prove that code works for valid values, rejects invalid values, handles edge
cases, and remains understandable to future developers.
