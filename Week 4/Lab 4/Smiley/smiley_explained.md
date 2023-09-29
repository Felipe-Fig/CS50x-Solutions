# Solution with explanation

## Link to the exercise
https://cs50.harvard.edu/x/2023/labs/4/smiley/

## Thought question
How do you think you represent a black pixel when using a 24-bit color BMP file?

Is this the same or different when mixing paints to repesent various colors?

## Code explained
Our job here is to create the `colorize` function. The following was given to us:
```C
#include "helpers.h"

void colorize(int height, int width, RGBTRIPLE image[height][width])
{
    // Change all black pixels to a color of your choosing
}
```

As we can see, this function takes 3 arguments being 2 integers (height and width) and also a `RGBTRIPLE` type of array, wich was defined back in the `bmp.h` file.

One thing we notice as well is that the `height` and `width` provided will be size of the array of `RGBTRIPLE`.

So what we need to do is to manipulate this image (wich is given to us as an array) and change the black color.

As we've seen in `bmp.h`, the image array has 3 values that could be changed: `rgbtBlue`, `rgbtGreen`, `rgbtRed`. These are the values that make the image black. If the image is black, we would have the following:

```C
image[x][y].rgbtBlue = 0
image[x][y].rgbtGreen = 0
image[x][y].rgbtRed = 0
```
So, if changing from black to anything else is correct, then that's where we'll act. Pay attention to the function `colorize`, one of the arguments it receives is an RGBTRIPLE (type of variable) that's called `image` and is an array `[height][width]`. This `image` has the attributes `rgbtBlue/Green/Red` that we need to change.

In our code, we will have to check to see if the color on that specific cell of the array is black, and if it is, change it. So we'll have something like this: `if image[x][y].rgbtBlue/Green/Red = 0, then assign new value`. We will also need to iterate through the array in order to check all the cells. That will be done with 2 for loops.

## Spoiler Alert

```C
#include "helpers.h"

void colorize(int height, int width, RGBTRIPLE image[height][width])
{
    // Change all black pixels to a color of your choosing
    for (int i = 0; i < height; i++)
    {
        for (int j = 0; j < width; j++)
        {
            if (image[i][j].rgbtBlue == 0 && image[i][j].rgbtGreen == 0 && image[i][j].rgbtRed == 0)
            {
                image[i][j].rgbtBlue = 100;
                image[i][j].rgbtGreen = 100;
                image[i][j].rgbtRed = 100;
            }
        }
    }
}
```

I've chosen the color with value 100 for Red/Green/Blue, but you can choose whathever you want. It must not be 0 (because it's black). After running all the steps, you'll seee that the code is correct and it's done to upload.