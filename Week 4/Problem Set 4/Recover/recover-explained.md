# CS50x
## Week 4, Problem Set 4, Recover

Link to the activity: https://cs50.harvard.edu/x/2023/psets/4/recover/

### Thought process
```pseudocode
Open the memory card file

Create an empty buffer to store data

Repeat until the end of the card:
    Read 512 bytes of data into the buffer
    
    If the first 4 bytes in the buffer represent the start of a JPEG:
        If you're already writing to a JPEG file:
            Close the current JPEG file
        Create a new JPEG file with a unique name
        Write the 512 bytes of data into the new JPEG file
    
    Else if you're already writing to a JPEG file:
        Write the 512 bytes of data into the current JPEG file

Close any remaining open files
```

This pseudocode outlines the main steps:

1. Open the memory card file.
2. Create a buffer to store data.
3. Loop through the card file in 512-byte chunks.
4. Check if the current chunk represents the start of a JPEG file (by checking its header).
5. If it's the start of a new JPEG, close the previous file (if any), create a new JPEG file, and write the data to it.
6. If you're in the middle of a JPEG, keep writing data to the current JPEG file.
7. Close any remaining open files.

### Step-by-step coding
#### Including new library
In order to use the `uint8_t` file type, this library is nedded: `#include <stdint.h>`. Will come in handy later in the code.

#### Accepting exactly one command-line argument
In order to check if we got only one command line argument, the `argc[]` value should be evaluated. Remember that the name of the program also counts as 1 for the `argc`, so if the correct program usage is: `$ ./recover card.raw`, we then have 2 CL arguments. Now for the code implementation.
```C
if (argc != 2)
{
    printf("Usage: ./recover IMAGE");
    return 1;
}
```
#### If the forensic image cannot be opened for reading
```C
FILE *file = fopen(argv[1], "r");
    if (file == NULL)
    {
        printf("Could not open %s.\n", argv[1]);
        return 1;
    }
```
#### Open the memory card file.
For this we'll use the `fopen` function. The last code shows this function in use. If the file at `argv[1]` gets opened correctly, it will be called `file` for the rest of the program.

#### 2. Create a buffer to store data.
```C
int BLOCK_SIZE = 512;
typedef uint8_t BYTE;
BYTE *buffer = malloc(BLOCK_SIZE);
```
In this portion we needed to create a temporary buffer to store the data. The buffer is created as per the last line and to this buffer, it is allocated memory os size `BLOCK_SIZE`.

We had define just prior that `BLOCK_SIZE` = 512. This is because the original problem gave this data to us. Each block has size of 512 bytes.

Note that I also created a variable type named `BYTE`. This type is `uint8_t` because creating an array (buffer) with this data type would prevent errors later in the code. The reason for this is as follows:

> In the given code, the `buffer` variable is declared as an array of `uint8_t` data type because it's intended to hold binary data from a file. `uint8_t` is an 8-bit unsigned integer data type, and it's often used for handling binary data because it's one of the smallest data types available in C and doesn't have any associated sign bit.\
\
Here's why `uint8_t` (or `unsigned char`) is a common choice for `buffer`:\
\
**Size and Range:** `uint8_t` is exactly 8 bits in size, which means it can represent values in the range of 0 to 255. Since each element of the buffer is meant to store a byte of binary data (ranging from 0x00 to 0xFF), `uint8_t` is a precise and compact choice.\
\
**Unsigned:** It's an unsigned data type, which means it doesn't represent negative numbers. When working with binary data or raw file data, you typically don't want to interpret the bytes as signed values, as it might lead to unexpected results.\
\
**Portability:** Using `uint8_t` ensures that the code is more portable because you explicitly specify an 8-bit unsigned data type. This is especially useful when dealing with binary file formats that require exact byte-level representation.\
\
While `uint8_t` is a common choice, you can technically use other data types, like `char` or `unsigned char`, for `buffer` as long as you handle the data appropriately. However, using `uint8_t` makes the intent of the code very clear and avoids potential issues with signed/unsigned conversions or portability. It's a good practice to use `uint8_t` when dealing with raw binary data in C.

#### Loop through the card file in 512-byte chunks.
`while (fread(buffer, 1, BLOCK_SIZE, file) == BLOCK_SIZE)`

This code reads from `file` (the one we opened earlier from `argv[1]`) to the `buffer` (array of type uint8_t and size 512). As it's usage manual shows, it will read files the size of `BLOCK_SIZE`, which are 512 as we declared earlier.

All of this is done `while` a condition is met. The condition is that `fread` will output the number of bytes read (512). While it reads 512 bytes from the `file` to the `buffer`, the while loop is True. If, by any means, it read less than that, the while loop becomes False and stops iterating.

#### Check if the current chunk represents the start of a JPEG file (by checking its header).
`if (buffer[0] == 0xff && buffer[1] == 0xd8 && buffer[2] == 0xff && (buffer[3] & 0xf0) == 0xe0)`

This line does exactly that, provided the info in the CS50 official page. The video provided by them explains this quite well. One thing we have to notice here is the *bitwise arithmetic* which is a new concept and not so much explained. There are plenty of info about this on the internet. 

`(buffer[3] & 0xf0) == 0xe0`:
It checks whether the fourth byte (buffer[3]) has the upper 4 bits (most significant bits) set to 0xf (1111 in binary) and the lower 4 bits set to 0 (0000 in binary). In other words, it checks if the first four bits of buffer[3] are 1111 and the last four bits are 0000. So, (buffer[3] & 0xf0) masks off the last four bits of buffer[3], ensuring that the first four bits are 1111 and the last four bits are ignored. Then, it checks if the result of this operation is equal to 0xe0.

You could also write exception by exception in your code, it should work fine as well, it would only take more time.

> The best explanation is as follows:\
\
The expression `(buffer[3] & 0xf0) == 0xe0` involves bitwise operations and comparisons. Let's break it down step by step:\
\
`buffer[3]`:
This refers to the value of the fourth byte (index 3) in the `buffer` array. In binary terms, this byte can be represented as a sequence of eight bits, for example, something like `b7 b6 b5 b4 b3 b2 b1 b0`, where `b7` is the most significant bit (leftmost) and `b0` is the least significant bit (rightmost).\
\
`0xf0`:
This is a hexadecimal number, and in binary, it is represented as `11110000`. It's a bitmask with the first four bits set to 1 and the last four bits set to 0.\
\
`(buffer[3] & 0xf0)`:
The `&` operator is a bitwise AND operation. When you perform a bitwise AND between `buffer[3]` and `0xf0`, it operates on each pair of corresponding bits. It sets the result bit to 1 if and only if both bits in the pair are 1.\
\
So, `(buffer[3] & 0xf0)` essentially masks off the last four bits of `buffer[3]`, retaining only the first four bits. This is done to isolate the most significant bits in `buffer[3]`.\
\
`0xe0`:
This is another hexadecimal number, represented in binary as `11100000`. It's a specific bit pattern you want to compare against. In the context of JPEG file headers, the first four bits are expected to be `1110`, and the last four bits can vary.\
\
`(buffer[3] & 0xf0) == 0xe0`:\
This is the final comparison. It checks if the result of masking the most significant bits of `buffer[3]` with `0xf0` (i.e., `(buffer[3] & 0xf0)`) is equal to `0xe0`.\
\
In the context of JPEG file headers, this comparison ensures that the first four bits of `buffer[3]` match `1110`, while the last four bits can be any value. It's a way to confirm that the header starts with the correct pattern for a JPEG file.\
\
So, to summarize, `(buffer[3] & 0xf0) == 0xe0` checks if the most significant four bits of the fourth byte in the `buffer` match the expected pattern for a JPEG file header.

#### If it's the start of a new JPEG, close the previous file (if any), create a new JPEG file, and write the data to it.
```C
if (buffer[0] == 0xff && buffer[1] == 0xd8 && buffer[2] == 0xff && (buffer[3] & 0xf0) == 0xe0)
        {
        if (jpeg_counter != 0)
        {
            fclose(img);
        }

        char filename[8];
        sprintf(filename, "%03i.jpg", jpeg_counter);
        jpeg_counter++;
        img = fopen(filename, "w");
```
The start of this code is the same as before. It will check the header to see if it's a JPEG. 

If it is, we go to the next if: `if (jpeg_counter != 0)`. If this `jpeg_counter` isn't zero, the image is closed. This explain why we created the `jpeg_counter` variable. We need to count how many images we have in order to name them and also if the counter isn't zero, it means some image is already being created.

To create a new JPEG we use the `sprintf` function. This function will write a string to `filename` and it's name will be the `jpeg_counter` in the specified formatting. 

If this happens, we can assure this image number is being created and increase the `jpeg_counter`. This increase will be used to tell the program that an image is already in creation and will also be used to name the next image to be created.

Lastly the line `img = fopen(filename, "w");` is used to open a file named filename for writing. `img` is a pointer to a FILE structure. The return value of fopen is the file pointer to the opened file. So, when you execute `img = fopen(filename, "w");`, you are assigning this file pointer to the img pointer you declared earlier. Now, img points to the newly opened or truncated file.

#### 6. If you're in the middle of a JPEG, keep writing data to the current JPEG file.
#### 7. Close any remaining open files.

#### SPOILER
```C
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>

int main(int argc, char *argv[])
{
    if (argc != 2)
    {
        printf("Usage: ./recover IMAGE");
        return 1;
    }

    FILE *file = fopen(argv[1], "r");
    if (file == NULL)
    {
        printf("Could not open %s.\n", argv[1]);
        return 1;
    }

    int BLOCK_SIZE = 512;
    typedef uint8_t BYTE;
    BYTE *buffer = malloc(BLOCK_SIZE);
    int jpeg_counter = 0;
    FILE *img = NULL;

    while (fread(buffer, 1, BLOCK_SIZE, file) == BLOCK_SIZE)
    {
        if (buffer[0] == 0xff && buffer[1] == 0xd8 && buffer[2] == 0xff && (buffer[3] & 0xf0) == 0xe0)
        {
            if (jpeg_counter != 0)
            {
                fclose(img);
            }

            char filename[8];
            sprintf(filename, "%03i.jpg", jpeg_counter);
            jpeg_counter++;
            img = fopen(filename, "w");
            
            if (img == NULL)
            {
                fclose(file);
                free(buffer);
                fprintf(stderr, "Could not create image file.\n");
                return 2;
            }
        }

        if (img != NULL)
        {
            fwrite(buffer, BLOCK_SIZE, 1, img);
        }
    }

    free(buffer);
    if (img != NULL)
    {
        fclose(img);
    }
    fclose(file);

    return 0;
}

```