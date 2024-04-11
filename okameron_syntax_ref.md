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


## Comments

Comments are enclosed by the characters `(*` and `*)`. E.g.
```
(* This is a comment in Okameron *)
```


## Primitive Types

The primitive types in Okameron are

- `CHAR`
- `SHORT`
- `INT`
- `LONG`


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
The initial value of a variable is undefined; it must be assigned a value 
inside a procedure before use.


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


## Control Flow

The two primary control flow statements in Okameron are `IF` statements and 
`WHILE` loops.

### `IF` Statement

The basic `IF` statement takes the following form:
```
IF <condition> THEN
    <statement>
    ...
END;
```
If the condition is true, the code inside the body will run.  Otherwise, it 
will be skipped. Conditions do not need to be wrapped in parentheses.

The `IF` statement can also be followed by any number of `ELSIF` statements 
and/or an `ELSE` statement. The general form is
```
IF <condition> THEN
    <statement>
    ...
{ELSIF <condition> THEN
    <statement>
    ...
}
...
{ELSE
    <statement>
    ...
}
END;
```
The code inside the body of an `ELSIF` statement will only run if its 
corresponding condition is true and all conditions associated with the 
preceding `IF`/`ELSIF` statements were false. The code inside the body of an 
`ELSE` statement will only run if all conditions associated with the preceding 
`IF`/`ELSIF` statements were false.


### `WHILE` Loops

A `WHILE` loop can be created by using the `WHILE` statement as follows:
```
WHILE <condition> DO
    <statement>
    ...
END;
```
The code inside the body will be executed repeatedly. At the beginning of each 
iteration, the condition is checked. If it is false, the loop will terminate 
immediately. Similarly to `IF` statements, the condition does not require 
parentheses.

Okameron does not have for loops, but the functionality can be replicated using 
a `WHILE` loop. E.g,
```
PROCEDURE ForLoopUsingWhileExample();
VAR i: INT;
BEGIN
    i := 0;
    WHILE i <| 10 DO
        (* add some code here *)
        i := i + 1;
    END;
END;
```

The loop can also be terminated early by using the `BREAK()` statement at any 
point in the loop. Alternatively, the rest of the current iteration can be 
skipped using the `CONTINUE()` statement.

*TODO: Add an example for `IF` statement, and another example for `WHILE` 
loop.*


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

Similarly to C, array parameters to procedures are handled by automatically 
converting them to pointers to the array's first element, and they may be 
handled as such.


## Records

Record types contain multiple fields of other types that can be accessed 
independently. They can be declared using the following syntax:
```
TYPE <type name> = RECORD
    <field name>: <field type>;
    ...
END;
```
For example,
```
TYPE Player = RECORD
    name: ARRAY 16 OF CHAR;
    hp: INT;
    level: INT;
    xp: INT;
END;
```
Variables of this type can be created using the type name provided in the 
declaration.

The fields of a record can be accessed using the dot (`.`) operator as follows:
```
    <record name>.<field name>
```
E.g,
```
    p1.level := p1.level + 1;
```

Procedures may not have parameters of record types. They may, however, have 
parameters whose type is `POINTER TO <record type>`, or `ARRAY <size> of 
<record type>`.


## Pointers

Pointer types allow data in memory to be accessed by address. A variable of 
pointer type can be created by prefixing the type of the data with `POINTER 
TO`. For example,
```
    number: POINTER TO INT;
```
The data referenced by a pointer can be accessed using the postfix `^` 
operator. For example,
```
    (* Store the current value pointed to by number into a *)
    a := number^;
    (* Replace the value pointed to by number with '7' *)
    number^ := 7;
```
The address of a variable can be obtained using the `PTROF()` operator.

A pointer with no type can be created using the `PTR` type. The memory 
referenced by this pointer can be accessed using the `GET*()` and `PUT*()` 
functions:

| Data Size | `GET*()`      | `SET*()`      |
|-----------|---------------|---------------|
| `CHAR`    | `GETCHAR()`   | `PUTCHAR()`   |
| `SHORT`   | `GETSHORT()`  | `PUTSHORT()`  |
| `INT`     | `GETINT()`    | `PUTINT()`    |
| `LONG`    | `GETLONG()`   | `PUTLONG()`   |

The `GET*()` functions take one parameter, the address to be accessed, and 
return the data of the specified type at that address. The `SET*()` functions 
take two parameters; the first is the address, and the second is the data to 
write at that address.


## Procedure Pointers

In addition to accessing data, pointers can also be used to call procedures 
using the `CALL()` operator, which has the following syntax:
```
    CALL(<procedure address>{, <argument>}...);
```
This operator evaluates to the value returned by the procedure, meaning it can 
be used just like a normal procedure call.

Similarly to variables, the address of a procedure can also be obtained using 
the `PTROF()` operator.

*TODO: Add examples for procedure pointers*


## Imports

To use procedures and variables declared in other modules, the `IMPORT` 
statement must be used. It occurs just after the line containing the `MODULE` 
keyword, and has the following syntax:
```
    IMPORT {<module name>, }... <module name>;
```

## Using with Assembly Language

### Including Assembly Code

Assembly code can be included using the `ASM` statement. Its syntax is as 
follows:
```
    ASM "<name of assembly source file>";
```
*TODO: Determine where exactly this statement is valid*

### `EXTERN` Declarations

Variables that are defined in assembly files must be declared using the 
`EXTERN` statement, which has the following syntax:
```
    EXTERN {<variable name>, }... <variable name>: <type>;
```
The `EXTERN` statement can also be used to declare constants defined in 
assembly, with the side effect that the `PTROF()` operator must be used to 
access the constant's value.

Procedures declared in assembly files must be declared similarly, using `EXTERN 
PROCEDURE`:
```
    EXTERN PROCEDURE {<procedure name>, }... <procedure name>: <return type>;
```
A return type is required, even if it is unused.

### Calling Convention

#### fox32

Procedures can have a maximum of 8 parameters, which are passed in registers 
r0-r7. The return value is stored in register r0.


## Bugs

### Accessing members of records in arrays

Record members cannot be accessed if the record is an element of an array. For 
example,
```
    someArray[0].something := somethingElse;
```
would cause an error.

A work-around is to use a pointer to access the record, as shown below.
```
    localSomeArray0 := PTROF(someArray[0]);
    localSomeArray0^.something := somethingElse;
```

### Expression including procedure call as the argument of a return statement

If the argument to a return statement is an arithmetic expression involving a
function call, the returned value may be incorrect. For example, assuming
`Three()` is a procedure defined to always return 3,
```
    RETURN(4 + Three());
```
may not always return 7. To be safe, store the result of the expression to a
variable and return that.

### Register values after changing parameter inside function

If a function changes the value of one of its parameters, the register used to
pass the parameter will not be restored, and will instead be updated to match
the new value upon return. This should be kept in mind when writing assembly
code that calls Okameron functions.


## Platform-Specific Details

### fox32

#### Sizes of Primitive Types

| Type  | Size (bits)   |
|-------|---------------|
| CHAR  | 8             |
| SHORT | 16            |
| INT   | 32            |
| LONG  | 32            |
| PTR   | 32            |

#### Global Variable Initialization

Global variables are initialized to zero.
