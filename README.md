# Sudoku

Sudoku is a logic-based puzzle game played on a 9x9 grid, divided into nine 3x3 subgrids. The objective is to fill the entire grid with numbers from 1 to 9, following these rules:

1. Each row must contain the numbers 1 to 9 without repetition.
2. Each column must contain the numbers 1 to 9 without repetition.
3. Each 3x3 subgrid must also contain the numbers 1 to 9 without repetition.

Some numbers are pre-filled as clues, and the challenge is to deduce the placement of the remaining numbers based on the rules. It’s a game of logic, not math, and requires reasoning to solve.

![image](https://github.com/user-attachments/assets/14f3b284-99c3-42b6-95ad-f51117e13a6a)
![image](https://github.com/user-attachments/assets/01154bfd-7ad3-4f97-a2e5-87188debc30f)


# code

```
// Sudoku board (9x9 grid)
[
  5 3 0 0 7 0 0 0 0
  6 0 0 1 9 5 0 0 0
  0 9 8 0 0 0 0 6 0
  8 0 0 0 6 0 0 0 3
  4 0 0 8 0 3 0 0 1
  7 0 0 0 2 0 0 0 6
  0 6 0 0 0 0 2 8 0
  0 0 0 4 1 9 0 0 5
  0 0 0 0 8 0 0 7 9
] b!

// Function to print the Sudoku board
:P
  9(          // Loop through rows
    9(        // Loop through columns
      b i 9 * j + ? // Get value at (i,j)
      0 = ( ` `) /E ( . ) // Print value or space for 0
      j 2 % 2 = ( ` `)    // Add space every 3 columns
    )
    /N                  // New line after each row
    i 2 % 2 = ( /N )    // Extra line every 3 rows
  )
;

// Function to check if a number is valid in a given position
:V n! j! i!
  /T $          // Assume valid unless proven otherwise
  // Check row
  9( b i 9 * k + ? n = ( /F ) ) $  // If match in row, fail
  // Check column
  9( b k 9 * j + ? n = ( /F ) ) $  // If match in column, fail
  // Check 3x3 box
  3( 3(
    b (i 3 / 3 * l +) 9 * (j 3 / 3 * m +) + ? n =
    ( /F )  // If match in 3x3 box, fail
  ))
;

// Function to find an empty cell
:E
  81(
    b i ? 0 = (
      i 9 / i 9 % /T  // Return row and column of empty cell
    )
  )
  /F  // Return false if no empty cell
;

// Recursive function to solve Sudoku
:S
  E (         // Find empty cell
    i! j!     // Store row and column
    9(        // Try digits 1 to 9
      1+      // Increment to get 1-9
      " i j V ( // If valid
        b i 9 * j + !  // Place number
        S (    // Recursively solve
          /T $ // If solved, return true
        ) /E (
          0 b i 9 * j + ! // If not solved, backtrack
        )
      ) '     // Drop the number if not valid
    )
    /F $      // If no number works, return false
  ) /E (
    /T        // If no empty cell, puzzle is solved
  )
;

// Main program
`Initial Sudoku board:` /N P
S ( 
  `Solved Sudoku board:` /N P 
) /E ( 
  `No solution exists.` 
)
```



1. First, let's look at the array definition `:A`:
```forth
:A [5 3 0 0 7 0 0 0 0 6 0 0 1 9 5 0 0 0 0 9 8 0 0 0 0 6 0 8 0 0 0 6 0 0 0 3 4 0 0 8 0 3 0 0 1 7 0 0 0 2 0 0 0 6 0 6 0 0 0 0 2 8 0 0 0 0 4 1 9 0 0 5 0 0 0 0 8 0 0 7 9] b ! ;
```
This creates an array of 81 numbers (9x9 grid) and stores it in variable `b`.

2. The print function `:P`:
```forth
:P 9(9(b i 9 * j + ? 0 = (``) /E(.) j 2 % 0 = (` `)) /N i 2 % 0 = (/N)) ;
```
Let's break this down:
- Outer loop `9(...)` iterates 9 times (rows), using counter `/i`
- Inner loop `9(...)` iterates 9 times (columns), using counter `/j`
- `b i 9 * j + ?` gets value from array:
  - `i 9 *` multiplies row number by 9
  - `j +` adds column number
  - `?` gets value at that index
- `0 = (``) /E(.)` checks if value is 0:
  - If zero, prints empty space
  - If non-zero, prints the number with `.`
- `j 2 % 0 = (` `)` adds space every other column
- `/N` adds newline
- `i 2 % 0 = (/N)` adds extra newline every other row

3. The solve function `:S`:
```forth
:S E(i ! j ! 9(1 + n ! n i j V(b i 9 * j + n ! S(/T)) /E(0 b i 9 * j + !)) /F) /E(/T) ;
```
This implements backtracking:
- `E` finds empty cell
- For each empty cell, tries numbers 1-9
- `V` validates the number
- If valid, places number and recursively continues
- If no solution found, backtracks

4. The main function `:M`:
```forth
:M `Initial Sudoku board:` /N P A S(`Solved Sudoku board:` /N P) /E(`No solution exists.`) ;
```
- Prints "Initial Sudoku board:"
- Displays initial board using P
- Tries to solve using S
- If solved, prints solution
- If unsolvable, prints error message

The issue we're seeing appears to be in the print function. Let's modify it to be simpler:

```forth
:P 9(9(b i 9 * j + ? .) ` ` /N) ;
```

This will:
- Print 9x9 grid
- Add space between numbers
- Add newline after each row
