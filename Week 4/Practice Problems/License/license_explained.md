```C
#include <stdio.h>

int main(int argc, char *argv[])
{
    // Check for command line args
    if (argc != 2)
    {
        printf("Usage: ./read infile\n");
        return 1;
    }
```
- **Include Header**: The code starts by including the standard input/output library (stdio.h), which is necessary for input and output operations.
main Function: This is the main function where the program execution begins.
- **Command Line Arguments Check**:
    - `int argc` represents the number of command-line arguments passed to the program (argument count).
    - `char *argv[]` is an array of strings that contains the command-line arguments (./license plates.txt).
- `if (argc != 2)`: The program then checks if there are exactly 2 command-line arguments (the program name itself and the name of the input file). If not, it prints a usage message and exits with a return code of 1, closing the program.

```C
// Create buffer to read into
    char buffer[7];

// Create array to store plate numbers
    char *plates[8];
```
**Buffer and Array Declaration**: 
- `char buffer[7];`: This declares an array of characters named buffer with a size of 7. It will be used to temporarily store the license plate numbers.
- `char *plates[8];`: This declares an array of character pointers named plates, capable of storing 8 pointers. These pointers will be used to store references to the license plates.

_Notice the difference? One is an array of characters and the other is an array of character pointers._

An array of characters stores character by character inside the array. `char a[] = { 'H', 'e', 'l', 'l', 'o' }`.
An array of character pointers (or strings) each element is a string (technically, each element is a pointer to the first character of a "string"),  for example: 
`char *b[] = {"Hello", "Hey", "Yo"}`.

A char array `char buffer[x]` is fully controlled by the scope. It will allocate the needed memory correctly and automatically release the memory when goes out of the scope.  But the situation with the char pointer `char *plates[y]` is different if you allocate the dynamic memory, you have to deallocate it manually (using `free`) otherwise it introduces memory leaks.

Some external fonts to help you with this topic: 
- https://aticleworld.com/what-is-the-difference-between-char-array-and-char-pointer-in-c/
- https://www.geeksforgeeks.org/array-of-strings-in-c/

```C
FILE *infile = fopen(argv[1], "r");

    int idx = 0;

while (fread(buffer, 1, 7, infile) == 7)
    {
        // Replace '\n' with '\0'
        buffer[6] = '\0';

        // Save plate number in array
        plates[idx] = buffer;
        idx++;
    }
```

**Open Input File:**

- `FILE *infile = fopen(argv[1], "r");`: This line opens the file specified as the second command-line argument `(argv[1])` for reading (r). It assigns a file pointer (infile) to this opened file.

**Read License Plates from File:**

- `int idx = 0;`: This initializes an index variable to keep track of the position in the plates array.

- `while (fread(buffer, 1, 7, infile) == 7)`: This loop reads 7 characters at a time (license plates) from the input file (infile) into the buffer. The loop continues until it can read 7 characters (the assumption here is that each license plate is 7 characters long).

- `buffer[6] = '\0';`: This line replaces the newline character (\n) at the end of the license plate with a nul terminator (\0) to ensure the string is properly terminated. Since the file imported and saved into buffer is external, its lines end in '\n' and not '\0'.

> Here is the wrong line in the code. 

- `plates[idx] = buffer;`: This line assigns the address of the buffer to the plates array at index `idx`. However, this is where the problem lies (the answer lies here, I'll show it further down the text). All plates elements will point to the same buffer, so you'll end up with the same license plate repeated in the array, which is what you'd get if you had executed the program as it was given to us.

**Print License Plates:**

- `for (int i = 0; i < 8; i++)`: This loop iterates through the plates array.

- `printf("%s\n", plates[i]);`: It prints each license plate from the plates array. However, due to the earlier issue, it will print the same license plate 8 times.

<!-- PAREI AQUI -->

> # Spoiler Alert!

The problem with the code is that it uses the same buffer to read and store license plates, and all elements in the plates array end up pointing to the same buffer. To fix this, you should allocate memory for each license plate individually.

1. **Array of Pointers Issue:** In this code, they are using an array of character pointers or strings (`char *plates[8];`) to store license plates. However, you are using the same character buffer `buffer` to read and store the license plates repeatedly. This results in all the pointers in the `plates` array pointing to the same `buffer`, which means they all contain the same value. You need to allocate memory for each plate individually.

2. **Pointer Assignment Issue:** When you do `plates[idx] = buffer;`, you are assigning the address of `buffer` to each element in the `plates` array. As a result, all elements in the `plates` array will point to the same memory location (`buffer`). So, when you modify `buffer`, all elements in the `plates` array will reflect the same value.

To fix these issues, you need to allocate memory for each license plate individually and copy the content of `buffer` into each allocated memory. Here's a corrected version of your code:

```c
#include <stdio.h>
#include <stdlib.h> // For memory allocation functions


    while (fread(buffer, 1, 7, infile) == 7)
    {
        // Replace '\n' with '\0'
        buffer[6] = '\0';

        // Allocate memory for the plate and copy the content of buffer
        plates[idx] = (char *)malloc(7); // Allocate memory for the plate
        if (plates[idx] == NULL)
        {
            perror("Memory allocation failed");
            return 1;
        }
        strcpy(plates[idx], buffer); // Copy the content of buffer to the allocated memory
        idx++;
    }

    for (int i = 0; i < 8; i++)
    {
        printf("%s\n", plates[i]);
        free(plates[i]); // Free the allocated memory when done
    }

    fclose(infile); // Close the file when done

    return 0;
}
```

In this corrected code, we use `malloc` to allocate memory for each license plate, and then we use `strcpy` to copy the content of `buffer` into the allocated memory. Additionally, we free the allocated memory when we are done with it using `free`. This way, each license plate is stored in its own separate memory location, and you should see different license plates printed.