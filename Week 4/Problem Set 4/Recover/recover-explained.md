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
FILE *file = fopen(argc[1], r);
if (file == NULL)
{
    printf("Could not open %s./n", file);
    return 1;
}
```
#### Open the memory card file.
For this we'll use the `fopen` function. 

#### 2. Create a buffer to store data.
#### 3. Loop through the card file in 512-byte chunks.
#### 4. Check if the current chunk represents the start of a JPEG file (by checking its header).
#### 5. If it's the start of a new JPEG, close the previous file (if any), create a new JPEG file, and write the data to it.
#### 6. If you're in the middle of a JPEG, keep writing data to the current JPEG file.
#### 7. Close any remaining open files.

#### SPOILER
```C
if (argc != 2)
{
    printf("Usage: ./recover IMAGE");
    return 1;
}

FILE *file = fopen(argv[1], "r");
if (file == NULL)
{
    printf("Could not open %s./n", file);
    return 1;
}

char *buffer [sizeof(file)];

int BLOCK_SIZE = 512;
int jpeg_counter = 0;
FILE *img = NULL;

while (fread(buffer, 1, BLOCK_SIZE, file) == BLOCK_SIZE)
{
    if (buffer[0] == 0xff && buffer[1] == 0xd8 && buffer[2] == 0xff && buffer[3] == 0xe0 || buffer[3] == 0xe1 || buffer[3] == 0xe2 || buffer[3] == 0xe3 || buffer[3] == 0xe4 || buffer[3] == 0xe5 || buffer[3] == 0xe6 || buffer[3] == 0xe7 || buffer[3] == 0xe8 || buffer[3] == 0xe9 || buffer[3] == 0xea || buffer[3] == 0xeb || buffer[3] == 0xec || buffer[3] == 0xed || buffer[3] == 0xee || buffer[3] == 0xef)
    {
        if (jpeg_counter != 0)
        {
            fclose(outpu);
        }
        else
        {
            char filename[8];
            sprint(filename, "%03i.jpg", jpeg_counter);
            jpeg_counter++;
            img = fopen(filename, "w");
            fwrite(buffer, BLOCK_SIZE, 1, img);
        }
    }
    else
    {
        fwrite(buffer, BLOCK_SIZE, 1, img);
    }
}

fclose(img);
fclose(file);
```