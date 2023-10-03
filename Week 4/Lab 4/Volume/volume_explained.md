# Solution with explanation

## Link to the exercise
https://cs50.harvard.edu/x/2023/labs/4/volume/

## Basic knowledge
> [fread function](https://manual.cs50.io/3/fread)

> [fwrite function](https://manual.cs50.io/3/fwrite)

## Explaining the code
`const int HEADER_SIZE = 44;`\
The constant HEADER_SIZE is defined with a value of 44. In a WAV file, the first 44 bytes represent the header information, which contains metadata about the audio file.

```C
// Check command-line arguments
    if (argc != 4)
    {
        printf("Usage: ./volume input.wav output.wav factor\n");
        return 1;
    }
```
The program checks whether it has received the correct number of command-line arguments. It expects 4 arguments: the program name itself (argv[0]), the input audio file name (argv[1]), the output audio file name (argv[2]), and the scaling factor (argv[3]). If the number of arguments is not equal to 4, it prints a usage message and returns with an error code of 1.

```C
FILE *input = fopen(argv[1], "r");
    if (input == NULL)
    {
        printf("Could not open file.\n");
        return 1;
    }

FILE *output = fopen(argv[2], "w");
    if (output == NULL)
    {
        printf("Could not open file.\n");
        return 1;
    }
```
The program attempts to open two files:\
_input_: It opens the file specified by `argv[1]` (the input audio file) for reading (r).\
_output_: It opens the file specified by `argv[2]` (the output audio file) for writing (w).\
If either of these file operations fails, an error message is printed, and the program returns with an error code of 1.

`float factor = atof(argv[3]);`

It converts the scaling factor provided as a command-line argument `(argv[3])` from a string to a floating-point number using the [`atof`](https://manual.cs50.io/3/atof) function. This factor will be used to adjust the volume of the audio data.

## Spoiler
```C
uint8_t header[HEADER_SIZE];

fread(header, HEADER_SIZE, 1, input);

fwrite(header, HEADER_SIZE, 1, output);

int16_t buffer;
// loop through the samples, multiply by the factor, add them to the buffer
while(fread(&buffer, sizeof(int16_t), 1, input))
{
    buffer = buffer * factor;
    fwrite(&buffer, sizeof(int16_t), 1, output);
}
```
The first 44 bytes of the input file (the header) are read using `fread` and stored in the newly created header array. Notice the size of the header is [HEADER_SIZE] and not "44" because we had declared [HEADER_SIZE] as 44 in the beginning of the code.

Then, the same header data is written to the **output** file using `fwrite`. This step ensures that the header information is preserved in the modified (output) audio file.

The loop reads 16-bit (2 bytes) audio samples from the **input** file (input) and modifies them based on the **scaling factor.** The loop continues until there are no more samples to read from the input file.

Here's how this part works:

`fread(&buffer, sizeof(int16_t), 1, input)` reads a single 16-bit audio sample from the input file into the buffer variable.

The value of **buffer** is multiplied by the scaling factor `factor` to adjust the volume.

The modified sample is then written to the **output** file, from the buffer, using `fwrite`.

```C
fclose(input);
fclose(output);
```

After processing all the audio samples, both the input and output files are closed using fclose to ensure that any pending data is written and the resources are released.