# Expressions
As of version 0.7 the assembler follows the normal BODMAS/PEMDAS operator precedence rules. Operators at the same precedence are evaluated left to right, sub-expressions within parenthesis are evaluated before all other operators.

|Precedence|Operator          |Description                         |
|----------|------------------|------------------------------------|
|     0    | (...)            | Parenthesized sub-expressions      |
|     1    | ~,!,+,-          | Complement, Logical NOT, Unary plus/minus|
|     2    | *,/,%            | Multiplication, division, remainder|
|     3    | +, -             | Addition, subtraction              |
|     4    | <<,>>            | Bitwise shift                      |
|     5    | <,<=,>,>=        | Relational operators               |
|     6    | =, <>            | Equal, not equal                   |
|     7    | &                | Bitwise AND                        |
|     8    | ^                | Bitwise XOR                        |
|     9    | \|               | Bitwise OR                         |
|     10   | &&               | Logical AND                        |
|     11   | \|\|             | Logical OR                         |
