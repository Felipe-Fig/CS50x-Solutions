# CS50x
## Week 5, Lab 5, Inheritance
Link to the exercise: https://cs50.harvard.edu/x/2023/labs/5/

### As per the explanation given on the official CS50 guide:
Notice the definition of a type called person. Each person has an array of two parents, each of which is a pointer to another person struct. Each person also has an array of two alleles, each of which is a char (either 'A', 'B', or 'O').
```C
// Each person has two parents and two alleles
typedef struct person
{
    struct person *parents[2];
    char alleles[2];
}
person;
```

Now, take a look at the `main` function. The function begins by “seeding” (i.e., providing some initial input to) a random number generator, which we’ll use later to generate random alleles.

`srand(time(0));`

The main function then calls the `create_family` function to simulate the creation of `person` structs for a family of 3 generations (i.e. a person, their parents, and their grandparents).

`person *p = create_family(GENERATIONS);`

We then call print_family to print out each of those family members and their blood types.

`print_family(p, 0);`

Finally, the function calls free_family to free any memory that was previously allocated with malloc.

`free_family(p);`

### Extra code explanation
```C
// Randomly chooses a blood type allele.
char random_allele()
{
    int r = rand() % 3;
    if (r == 0)
    {
        return 'A';
    }
    else if (r == 1)
    {
        return 'B';
    }
    else
    {
        return 'O';
    }
}
```

The function `rand()` is used for random number generator in C in a certain range that can take values from [0, Range_max]. The `%` operator is used to limit the random integer to the range [0, 2], as `rand() % 3` will produce values 0, 1, or 2.

```C
// Print each family member and their alleles.
void print_family(person *p, int generation)
{
    // Handle base case
    if (p == NULL)
    {
        return;
    }

    // Print indentation
    for (int i = 0; i < generation * INDENT_LENGTH; i++)
    {
        printf(" ");
    }

    // Print person
    if (generation == 0)
    {
        printf("Child (Generation %i): blood type %c%c\n", generation, p->alleles[0], p->alleles[1]);
    }
    else if (generation == 1)
    {
        printf("Parent (Generation %i): blood type %c%c\n", generation, p->alleles[0], p->alleles[1]);
    }
    else
    {
        for (int i = 0; i < generation - 2; i++)
        {
            printf("Great-");
        }
        printf("Grandparent (Generation %i): blood type %c%c\n", generation, p->alleles[0], p->alleles[1]);
    }

    // Print parents of current generation
    print_family(p->parents[0], generation + 1);
    print_family(p->parents[1], generation + 1);
}
```

This part of the code defines the function `print_family` that recursively prints information about a family tree of individuals and their alleles. It prints the generation, blood type, and relationship of each family member.

1. `void print_family(person *p, int generation)`
   - This function takes two arguments: a pointer to the `person` struct (`person *p`) and an integer representing the generation.

2. `if (p == NULL)`
   - This is the base case of the recursion. It checks if the pointer `p` is `NULL`. If `p` is `NULL`, it means there are no more family members to print, and the function returns, effectively ending the recursion for that branch.

3. Printing Indentation:
   - The `for` loop prints indentation based on the generation level. It multiplies `generation` by `INDENT_LENGTH`, and for each iteration, it prints a space character to indent the output.

4. Printing Person Information:
   - Depending on the generation level (`generation`), the function prints different information for the person:
     - If `generation` is 0, it prints "Child" and their blood type.
     - If `generation` is 1, it prints "Parent" and their blood type.
     - If `generation` is greater than 1, it prints the appropriate prefix (e.g., "Great-Grandparent") and their blood type.

5. Printing Parent Information:
   - The function then recursively calls itself for each of the current person's parents (stored in the `parents` array). It increments the `generation` by 1 to indicate the next generation.

### create_family

> First, you should allocate memory for a new person. Recall that you can use `malloc` to allocate memory, and `sizeof(person)` to get the number of bytes to allocate.
- Remember, to allocate memory for a particular person, we can use `malloc(n)`, which takes a size as argument and will allocate `n` bytes of memory.
Remember, to access a variable via a pointer, we can use arrow notation.
For example, if `p` is a pointer to a person, then a pointer to this person’s first parents can be accessed by `p->parents[0]`.

```C
person *p = malloc(sizeof(person));
```

> If generations > 1, then there are more generations that still need to be allocated. We’ve already created two new parents, `parent0` and `parent1`, by recursively calling `create_family`. Your create_family function should then set the parents pointers of the new person you created.

```C
p->parents[0] = *parent0;
p->parents[1] = *parent1;
```

> Finally, assign both `alleles` for the new person by randomly choosing one allele from each parents.

```C
p->alleles[0] = p->parents[0]->alleles[rand () % 2];
p->alleles[1] = p->parents[1]->alleles[rand () % 2];
```

> Otherwise (if generations == 1), then there will be no parents data for this person. Both parents of your new person should be set to NULL, and each allele should be generated randomly. 
- You might find the `rand()` function useful for randomly assigning alleles. This function returns an integer between 0 and RAND_MAX, or 2147483647.
In particular, to generate a pseudorandom number that is either 0 or 1, you can use the expression `rand() % 2`.

```C
p->parents[0] = NULL;
p->parents[1] = NULL;

p->alleles[0] = random_allele();
p->alleles[1] = random_allele();
```

> Finally, your function should return a pointer for the person that was allocated.

### free_family
The free_family function should accept as input a pointer to a person, free memory for that person, and then recursively free memory for all of their ancestors.

- Since this is a recursive function, you should first handle the base case. If the input to the function is `NULL`, then there’s nothing to `free`, so your function can `return` immediately.

```C
if (p == NULL)
{
    return;
}
```

- Otherwise, you should recursively free both of the person’s `parents` before freeing the child.

```C
free_family(p->parents[0]);
free_family(p->parents[1]);

free(p);
```

#### Spoiler Code
```C
// Simulate genetic inheritance of blood type

#include <stdbool.h>
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// Each person has two parents and two alleles
typedef struct person
{
    struct person *parents[2];
    char alleles[2];
}
person;

const int GENERATIONS = 3;
const int INDENT_LENGTH = 4;

person *create_family(int generations);
void print_family(person *p, int generation);
void free_family(person *p);
char random_allele();

int main(void)
{
    // Seed random number generator
    srand(time(0));

    // Create a new family with three generations
    person *p = create_family(GENERATIONS);

    // Print family tree of blood types
    print_family(p, 0);

    // Free memory
    free_family(p);
}

// Create a new individual with `generations`
person *create_family(int generations)
{
    // TODO: Allocate memory for new person
    person *p = malloc(sizeof(person));

    // If there are still generations left to create
    if (generations > 1)
    {
        // Create two new parents for current person by recursively calling create_family
        person *parent0 = create_family(generations - 1);
        person *parent1 = create_family(generations - 1);

        // TODO: Set parents
     pointers for current person
        p->parents[0] = parent0;
        p->parents[1] = parent1;

        // TODO: Randomly assign current person's alleles based on the alleles of their parents
        p->alleles[0] = p->parents[0]->alleles[rand () % 2];
        p->alleles[1] = p->parents[1]->alleles[rand () % 2];
    }

    // If there are no generations left to create
    else
    {
        // TODO: Set parents
     pointers to NULL
        p->parents[0] = NULL;
        p->parents[1] = NULL;

        // TODO: Randomly assign alleles
        p->alleles[0] = random_allele();
        p->alleles[1] = random_allele();
    }

    // TODO: Return newly created person
    return p;
}

// Free `p` and all ancestors of `p`.
void free_family(person *p)
{
    // TODO: Handle base case
    if (p == NULL)
    {
        return;
    }

    // TODO: Free parents recursively
    free_family(p->parents[0]);
    free_family(p->parents[1]);

    // TODO: Free child
    free(p);
}

// Print each family member and their alleles.
void print_family(person *p, int generation)
{
    // Handle base case
    if (p == NULL)
    {
        return;
    }

    // Print indentation
    for (int i = 0; i < generation * INDENT_LENGTH; i++)
    {
        printf(" ");
    }

    // Print person
    if (generation == 0)
    {
        printf("Child (Generation %i): blood type %c%c\n", generation, p->alleles[0], p->alleles[1]);
    }
    else if (generation == 1)
    {
        printf("Parent (Generation %i): blood type %c%c\n", generation, p->alleles[0], p->alleles[1]);
    }
    else
    {
        for (int i = 0; i < generation - 2; i++)
        {
            printf("Great-");
        }
        printf("Grandparent (Generation %i): blood type %c%c\n", generation, p->alleles[0], p->alleles[1]);
    }

    // Print parents of current generation
    print_family(p->parents[0], generation + 1);
    print_family(p->parents[1], generation + 1);
}

// Randomly chooses a blood type allele.
char random_allele()
{
    int r = rand() % 3;
    if (r == 0)
    {
        return 'A';
    }
    else if (r == 1)
    {
        return 'B';
    }
    else
    {
        return 'O';
    }
}
```