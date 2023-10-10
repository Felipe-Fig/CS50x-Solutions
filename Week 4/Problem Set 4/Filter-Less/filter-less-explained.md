# Week 4, Problem Set 4, Filter (less confortable)

## Full Explanation

### BMP.H
One thing I noticed and it wasn't covered by CS50's explanation is the `__attribute__((packed))` at the end of the `struct`. So...

In C, the `__attribute__((packed))` variable attribute is used to indicate that a structure or union should be packed, which means that the compiler should not insert any padding bytes between the members of the structure or union. Padding bytes are typically added by the compiler to ensure that each member is aligned to a memory address that is suitable for its data type. However, in certain situations, you may want to eliminate this padding to conserve memory or when interfacing with hardware or binary data structures that expect a specific memory layout.

Notice how they used it in the file:

```C
typedef struct
{
    WORD   bfType;
    DWORD  bfSize;
    WORD   bfReserved1;
    WORD   bfReserved2;
    DWORD  bfOffBits;
} __attribute__((__packed__))
BITMAPFILEHEADER;
```

This tells the compiler to remove any padding between the structure members. Without this attribute, the compiler may insert padding to ensure that all the elements of this struct are correctly aligned in memory.

It's important to note that using `__attribute__((packed))` can have implications:

- Performance: Accessing unaligned data can be slower on some architectures.
- Portability: The use of packed structures can make your code less portable, as it relies on specific memory layouts that may vary between compilers and platforms.
- Alignment Issues: Some architectures may not support unaligned memory access, leading to alignment exceptions or errors.
- Readability: Packed structures can make your code less readable, as the structure layout no longer matches the natural alignment of data types.

Therefore, it's essential to use `__attribute__((packed))` only when necessary, such as when dealing with hardware registers or binary data formats where you need precise control over memory layout.

### Grayscale

As per the CS50 explanation:
> So to convert a pixel to grayscale, we just need to make sure the red, green, and blue values are all the same value. But how do we know what value to make them? Well, it’s probably reasonable to expect that if the original red, green, and blue values were all pretty high, then the new value should also be pretty high. And if the original values were all low, then the new value should also be low.\
\
In fact, to ensure each pixel of the new image still has the same general brightness or darkness as the old image, we can take the average of the red, green, and blue values to determine what shade of grey to make the new pixel.\
\
If you apply that to each pixel in the image, the result will be an image converted to grayscale.

So, for the steps: each pixel in the provided image has values of red, green and blue. We will have to look into each of those pixels, verify the values, calculate the mean of the three and make them all the same value (the mean).

This "look into" the pixel is meant by accessing the RGB values at each pixel of the image. So, if we have a image that is a 2d grid, each pixel could be accessed with image[x][y].

Since the RGB values are inside a _struct_ called `RGBTRIPLE`, we will have to access each of those values inside it. 

In order to access a value inside a struct, we would have to `struct.value` to reach it. In this case it would be `RGBTRIPLE.rgbtRed/Green/Blue[x][y]`

> When implementing the grayscale function, you’ll need to average the values of 3 integers. Why might you want to divide the sum of these integers by 3.0 and not 3?\
\
When you divide two integers, the result will be an integer. In this case, it's best to divide by 3.0 (a float) and then round it. If you do this, the value will be more precise.

```C
int a;
int b;
int c;
float avg;

for (int i=0, i>[height], i++)
{
    for (int j=0, j>[width], j++)
    {
        a = image[i][j].rgbtBlue;
        b = image[i][j].rgbtGreen;
        c = image[i][j].rgbtRed;

        avg = (a+b+c)/3.0;
        round(avg) // has to include math.h

        image[i][j].rgbtBlue = *avg;
        image[i][j].rgbtGreen = *avg;
        image[i][j].rgbtRed = *avg;
    }
}
```

### Sepia
> An image can be converted to sepia by taking each pixel, and computing new red, green, and blue values based on the original values of the three.\
\
There are a number of algorithms for converting an image to sepia, but for this problem, we’ll ask you to use the following algorithm. For each pixel, the sepia color values should be calculated based on the original color values per the below.

```C
sepiaRed = .393 * originalRed + .769 * originalGreen + .189 * originalBlue 

sepiaGreen = .349 * originalRed + .686 * originalGreen + .168 * originalBlue

sepiaBlue = .272 * originalRed + .534 * originalGreen + .131 * originalBlue
``````

> Of course, the result of each of these formulas may not be an integer, but each value could be rounded to the nearest integer. It’s also possible that the result of the formula is a number greater than 255, the maximum value for an 8-bit color value. In that case, the red, green, and blue values should be capped at 255. As a result, we can guarantee that the resulting red, green, and blue values will be whole numbers between 0 and 255, inclusive.

> How might a function that returns the lesser of two integers come in handy while implementing sepia, particularly when you need to make sure a color’s value is no higher than 255?

First of all, let's better understand how this calculation of the _sepia_ should be done:

```C
sepiaRed = (0.393*image[i][j].rgbtRed)+(0.769*image[i][j].rgbtGreen)+(0.189*image[i][j].rgbtBlue);

sepiaGreen = (0.349*image[i][j].rgbtRed)+(0.686*image[i][j].rgbtGreen)+(0.168*image[i][j].rgbtBlue);

sepiaBlue = (0.272*image[i][j].rgbtRed)+(0.534*image[i][j].rgbtGreen)+(0.131*image[i][j].rgbtBlue);
```

But no value can be greater than 255, so: 

Final solution for Sepia:

```C
for (int i=0, i<[height], i++)
{
    for (int j=0, j<[width], j++)
    {
        sepiaRed = (0.393*image[i][j].rgbtRed)+(0.769*image[i][j].rgbtGreen)+(0.189*image[i][j].rgbtBlue);

        sepiaGreen = (0.349*image[i][j].rgbtRed)+(0.686*image[i][j].rgbtGreen)+(0.168*image[i][j].rgbtBlue);

        sepiaBlue = (0.272*image[i][j].rgbtRed)+(0.534*image[i][j].rgbtGreen)+(0.131*image[i][j].rgbtBlue);
    }
}

if (sepiaRed > 255)
    {
        sepiaRed = 255;
    }
if (sepiaGreen > 255)
    {
        sepiaGreen = 255;
    }
if (sepiaBlue > 255)
    {
        sepiaBlue = 255
    }
```

### Reflect

In order to reflect the image we should iterate through the image array and do the following:
- save the far right pixel `image[i][width-j]` to a temporary variable (I called it buffer).
- copy the first (far left) pixel into the last (far right) pixel.
- copy from the temporary variable (buffer) to the first (far left) pixel.
- and iterate.

```C
for (int i=0, i<[height], i++)
{
    for (int j=0, j<[width], j++)
    {
        int buffer = image[i][width-j];
        image[i][width-j] = image[i][j];
        image[i][j] = buffer;
    }
}
```

### Blur
Assuming you have read and watched all the material available for the Blur function, we start with the following needs:
- Copy the original image to a new array
- Loop to calculate the average value of a given pixel
- Save this new value to the copied array
- Keep reading from the original array until it ends
- Copy from the copied array into the original array for the output

In terms of steps this should be easy, but we encounter a big problem when we try to calculate the average value of the pixels. This happens because the average value is calculated using the 9 nearest pixels (including the center pixel), but sometimes we get corner pixels, which don't have pixels to some of the sides.

This is solved easily by hand, calculating only the value of the nearest pixels inside the image. But try doing this with code. Remember that touching extra memory could cause segmentation faults.

So let's first understand how this image array is laid out and how we will calculate the average values:

![IMG001](https://github.com/Felipe-Fig/CS50x-Solutions/blob/ee2d293c0463a50053aa09028dd7816029f1edf2/Week%204/Problem%20Set%204/Filter-Less/IMG001.png)

As an array of bytes, each "cell" of this array can be accessed with `array[x][y]`. 

![IMG002](https://github.com/Felipe-Fig/CS50x-Solutions/blob/ee2d293c0463a50053aa09028dd7816029f1edf2/Week%204/Problem%20Set%204/Filter-Less/IMG002.png)

In order to calculate `img[1][2]` average values, all of the blue painted pixels should be measured by our algorithm. That's ok until now, but let's check the corner cases.

![IMG003](https://github.com/Felipe-Fig/CS50x-Solutions/blob/ee2d293c0463a50053aa09028dd7816029f1edf2/Week%204/Problem%20Set%204/Filter-Less/IMG003.png)

In this "corner case", we should only calculate the average of the yellow blocks (img[0][0] included). But the code we write should specify not to touch the data outside the array, which is marked with highlighter.

When we get to the corner case in the code, the easiest way I found was to create another nested loop. This new loop will use 2 new variables `a,b`. A and B will be the coordinates of a square that calculates all the pixels values around the designated pixel.
> To recap, we have a "designated pixel" which is the pixel at hand, the one we use as reference to calculate the average values of pixels around it. 

This new `a,b` loop will "look" to the pixels around the "designated pixel" in order to make calculations. If the average is calculated with a 9x9 square, A and B will have only 3 possible values, from -1 to 1 (the same as from 0 to 2).

Let's first look at `a`. "A" will designate the row (as in height). For the 9x9 box around the "designated pixel", we have 3 possibilities:
- `a=-1` this is the row immediatlely above the "designated pixel".
- `a=0` this is the row of the "designated pixel".
- `a-1` this is the row below the "designated pixel".

There are some cases where `a` cannot be used in the calculation (because it is out of bounds). These cases are:
- When the "designated pixel" is on the first row of the array. In this case `a=-1` should not be calculated.
- When the "designated pixel" is on the last row of the array. In this case `a=1` should not be calculated.

The cases where the "designate pixel" is on the first/last row is when `image[i][j] -> i = 0` and `image[i][j] -> i = [height]`.

To put this in code it would be `if i = 0 && a = -1 || i = [height] && a = 1`

Simplifying: `if i + a < 0 || i + a > [height]`.

Now let's look at `b`. "B" will designate the column (as in width). For the 9x9 box around the "designated pixel", we have 3 possibilities:
- `b = -1` this is the column to the left of the "designated pixel".
- `b = 0` this is the column of the "designated pixel".
- `b = 1` this is the column to the right the "designated pixel".

There are some cases where `b` cannot be used in the calculation (because it is out of bounds). These cases are:
- When the "designated pixel" is on the first (left) column of the array. In this case `b = -1` should not be calculated.
- When the "designated pixel" is on the last (right) column of the array. In this case `b = 1` should not be calculated.

The cases where the "designate pixel" is on the left/right row is when `image[i][j] -> j = 0` and `image[i][j] -> j = [width]`.

To put this in code it would be `if j = 0 && b = -1 || j = [width] && b = 1`

Simplifying: `if j + b < 0 || j + b > [width]`.

So, when iterating through the code, these cases should be specified as the cases where the calculation isn't done by the program in order to avoid accessing untouched memory (causing something like a segmentation fault). 

These conditions should be used inside the main iterative loop, that loops through the `image[i][j]` array. More specifically, these conditions should be used inside another for loop that iterates through `a,b`, making the right calculations.

In order to calculate the color value, we use the code `color = image[i][j].rgbtRed/Green/Blue`. Since the `a,b` loop goes through all the pixels in the array, we could sum the `a,b` values of all the pixels and colors. This sum will be save in the `sumRed/Green/Blue` variable.

### Spoiler alert - Blur Answers
```C
void blur(int height, int width, RGBTRIPLE image[height][width])
{
    // Here we declare all the variables we are going to use
    // The first is a new array to be the copy of the original
    // The other valus will store the sum of the values in each pixel
    // The counter will be used as a dividend, to calculate the average.

    RGBTRIPLE copy[height][width];
    double sumRed = 0;
    double sumGreen = 0;
    double sumBlue = 0;
    int count = 0;

    // Copies the image array into the copy array
    for (int i = 0; i < height; i++)
    {
        for (int j = 0; j < width; j++)
        {
            copy[i][j] = image[i][j];
        }
    }

    // Start of the loop to go through all the array, column by column, increasing the row as it ends.
    for (int i = 0; i < height; i++)
    {
        for (int j = 0; j < width; j++)
        {
            // sums values of the pixel and 8 neighboring ones, skips iteration if it goes outside the pic
            for (int a = -1; a < 2; a++)
            {
                if (i + a < 0 || i + a > [height])
                {
                    continue;
                }

                for (int b = -1; b < 2; b++)
                {
                    if (j + b < 0 || j + b > [width])
                    {
                        continue;
                    }

                    sumBlue += image[a][b].rgbtBlue;
                    sumGreen += image[a][b].rgbtGreen;
                    sumRed += image[a][b].rgbtRed;
                    count++;
                }
            }

            // Stores the average
            copy[i][j].rgbtRed = round((double) sumRed / count);
            copy[i][j].rgbtGreen = round((double) sumGreen / count);
            copy[i][j].rgbtBlue = round((double) sumBlue / count);
        }
    }

    // Copies the temporary image in the real image
    for (int i = 0; i < height; i++)
    {
        for (int j = 0; j < width; j++)
        {
            image[i][j] = tmp[i][j];
        }
    }

    return;
}
```