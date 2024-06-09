# Expressions
As of version 0.7 the assembler follows the normal BODMAS/PEMDAS operator precedence rules. Operators at the same precedence are evaluated left to right, sub-expressions within parenthesis are evaluated before all other operators.

|Precedence|Operator          |Description                         |
|----------|------------------|------------------------------------|
|     0    | (...)            | Parenthesized sub-expressions      |
|     1    | !,+,-            | Logical NOT, Unary plus/minus      |
|     2    | *,/,%            | Multiplication, division, remainder|
|     3    | +, -             | Addition, subtraction              |
|     4    | <<,>>            | Bitwise shift                      |
|     5    | <,<=,>,>=, =, <> | Relational operators               |
|     6    | &                | Bitwise AND                        |
|     7    | \|               | Bitwise OR                         |
|     8    | &&               | Logical AND                        |
|     9    | \|\|             | Logical OR                         |
