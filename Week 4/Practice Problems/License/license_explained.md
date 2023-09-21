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

- `buffer[6] = '\0';`: This line replaces the newline character (\n) at the end of the license plate with a NUL terminator (\0) to ensure the string is properly terminated. Since the file imported and saved into buffer is external, its lines end in '\n' and not '\0'.

> Here is the wrong line in the code. 

- `plates[idx] = buffer;`: This line assigns the address of the buffer to the plates array at index `idx`. However, this is where the problem lies (the answer is here, I'll show it further down the text). All plates elements will point to the same buffer, so you'll end up with the same license plate repeated in the array, which is what you'd get if you had executed the program as it was given to us.

**Print License Plates:**

- `for (int i = 0; i < 8; i++)`: This loop iterates through the plates array.

- `printf("%s\n", plates[i]);`: It prints each license plate from the plates array. However, due to the earlier issue, it will print the same license plate 8 times.

> # Hint before the spoiler:

The issue is with the line `plates[idx] = buffer;`.

In C, when you assign one array (in this case, `buffer`) to another (in this case, `plates[idx]`), you are not copying the content of the array. Instead, you are copying the memory address or pointer to the original array. This means that all elements in the `plates` array end up pointing to the same memory location as the `buffer`.

Here's a step-by-step explanation of why this is problematic:

1. When you read a license plate from the file into the `buffer`, the characters are stored in a contiguous block of memory, and `buffer` contains the address of the start of that block.

2. When you assign `plates[idx] = buffer;`, you are essentially saying, "Make `plates[idx]` point to the same memory location as `buffer`."

3. Since the loop continues, the `idx` variable is incremented, and the same assignment happens for the next license plate. This means that both `plates[0]` and `plates[1]` (and so on) all point to the same memory location as `buffer`.

4. When you modify the `buffer` to read a new license plate, all the pointers in the `plates` array still point to the same memory location, so they all see the same value, which is the last license plate read.

In essence, all elements in the `plates` array end up pointing to the same memory location (the `buffer`), so any changes made to `buffer` affect all elements in the array. This is why you see the same license plate repeated in the array when you print it.

To store different license plates correctly, you need to allocate memory for each license plate individually, so that each element in the `plates` array points to a separate memory location containing the respective license plate. This ensures that modifications to one license plate don't affect the others.


> # Spoiler Alert!

The problem with the code is that it uses the same buffer to read and store license plates, and all elements in the `plates` array end up pointing to the same buffer. To fix this, you should allocate memory for each license plate individually.

1. **Array of Pointers Issue:** In this code, they are using an array of character pointers or strings (`char *plates[8];`) to store license plates. However, you are using the same character buffer `buffer` to read and store the license plates repeatedly. This results in all the pointers in the `plates` array pointing to the same `buffer`, which means they all contain the same value. You need to allocate memory for each plate to be stored individually.

2. **Pointer Assignment Issue:** When you do `plates[idx] = buffer;`, you are assigning the address of `buffer` to each element in the `plates` array. As a result, all elements in the `plates` array will point to the same memory location (`buffer`). So, when you modify `buffer`, all elements in the `plates` array will reflect the same value, which is the last one assigned.

To fix these issues, you need to allocate memory for each license plate individually and copy the content of `buffer` into each allocated memory. 

```c
#include <stdio.h>
// Two new libraries included
#include <stdlib.h>
#include <string.h> 


    while (fread(buffer, 1, 7, infile) == 7)
    {
        // Replace '\n' with '\0'
        buffer[6] = '\0';

        // Allocate memory for the plate and copy the content of buffer
        plates[idx] = (char *)malloc(7);

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

In this corrected code, we use `malloc` to allocate memory for each license plate, and then we use `strcpy` to copy the content of `buffer` into the allocated memory. Additionally, we free the allocated memory when we are done with it using `free`. This way, each license plate is stored in its own separate memory location, and you should see different license plates printed at the end.

# Second solution

Once you understand that `buffer` is copied as an address into the `plates[]` array, you'll understand why it printed only the last plate in hte first program execution. But there's a catch. The "print" function is separate from the array value assignement function and when the code "prints", the value at `plates[idx]` is already the last plate copied to `buffer`.

With this in mind, I introduced the possibility to `printf` the `plates[idx]` from inside the loop, right before the `buffer` gets it's value reassigned.

Here's what I've done (and it worked)!

```C
while (fread(buffer, 1, 7, infile) == 7)
    {
        // Replace '\n' with '\0'
        buffer[6] = '\0';

        // Save plate number in array
        plates[idx] = buffer;
        // Added as test to see if it runs properly. Since the problem is 
        // buffer changing and the plates array only saving the last
        //address, will try this.
        printf("%s\n", plates[idx]);
        idx++;
    }
```

Notice that in this case there's no need to `malloc`.