# Informal Okameron Syntax Reference


## Syntax used in this document

Angle brackets indicate that the contents are a description of what should go 
there, not literal text.

Curly brackets (except when used as the outer brackets of an array literal) 
indicate that something is optional.

An ellipsis (...) indicates that something may repeat.


## Program Structure

Programs must begin with a module header of the form
```
MODULE <module name>;
```
and end with the keyword
```
END.
```


## Primitive Types

The primitive types in Okameron are

- `Char`
- `Short`
- `Int`
- `Long`


## Literals

A decimal numeric literal can be entered as
```
1234567890
```
Hexadecimal numeric literals have an 'H' suffix. For example,
```
3A33H
```
or
```
04d2H
```
The 'H' must be capitalized. Hexadecimal literals cannot start with a letter,
so prefix them with a '0' if necessary.


There are no character literals in Okameron.


## Constants

Constants are declared in the `CONST` section, which may appear only once,
outside of any procedure. It takes the form
```
CONST
    <constant name> = <constant value>;
    ...
```
where <constant value> is a numeric expression containing no parentheses.

*TODO: figure out what the exact requirements are for constant value
expressions*

E.g.

```
CONST
    CURRENT_YEAR = 2023;
    SIXTY_FOUR_K = 64 * 1024;
```


## Operators

### Unary Operators

| Symbol    | Name      |
|-----------|-----------|
| ~         | not       |
| -         | negate    |

### Binary Operators

| Symbol    | Name                              |
|-----------|-----------------------------------|
| +         | plus                              |
| -         | minus                             |
| *         | multiply                          |
| *\|       | unsigned multiply                 |
| /         | divide                            |
| DIV       |                                   |
| /\|       | unsigned divide                   |
| UDIV      |                                   |
| MOD       | modulus                           |
| UMOD      | unsigned modulus                  |
| &         | and                               |
| XOR       | exclusive or                      |
| OR        | or                                |
| =         | equals                            |
| #         | not equals                        |
| <         | less than                         |
| >         | greater than                      |
| <=        | less than or equal to             |
| >=        | greater than or equal to          |
| <\|       | unsigned less than                |
| >\|       | unsigned greated than             |
| <\|=      | unsigned less than or equal to    |
| >\|=      | unsigned greater than or equal to |


## Variables

Variables are declared in a `VAR` section, which may appear between the
procedure header and the `BEGIN` keyword, or outside of any procedure.
This section takes the form
```
VAR
    <variable name>: <type>;
    ...
```
E.g.
```
VAR
    favoriteNumber: INT;
    inputCharacter: CHAR;
    reallyLongNumber: LONG;
```

Inside a procedure, they can be assigned values using the `:=` operator. For
example,
```
    favoriteNumber := 37;
    inputCharacter := 41H;
    reallyLongNumber := 1234567890;
```


## Procedures
Procedures are declared by the `PROCEDURE` keyword. They take the form
```
PROCEDURE <procedure name>({<param>{, <param>}...: <type>;}...){: <return type>};
{<VAR section, see above>}
BEGIN
    <statement>
    ...
END;
```
They may return values using the `RETURN()` statement.
E.g.
```
PROCEDURE Add3(a, b, c: INT;): INT;
VAR result: INT;
BEGIN
    result := a + b + c;
    RETURN(result);
END;
```
The `RETURN()` statement may also be used without arguments, if the procedure
does not return a value.


## Arrays
Array types can be created using the following syntax:
```
ARRAY <length> OF <element type>;
```
The array's length must be either a constant or a literal.
For example,
```
    numbers: ARRAY 10 OF INT;
    name: ARRAY 32 OF CHAR;
```
Array elements can be accessed using square brackets. E.g.
```
    aNumber := numbers[3];
```
In Okameron, array indexing starts at 0.
