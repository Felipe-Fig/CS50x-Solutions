# CS50x - Week 5
## Problem Set 5 - Trie

### Code explanation:
1. `root = malloc(sizeof(node));`:
   - `malloc` It allocates a block of memory of the specified size and returns a pointer to the first byte of the block.
   - In this case, `sizeof(node)` calculates the size of a `node` structure (specified before in the code) and allocates memory for it. This memory block will serve as the root of the trie.
   - The `root` pointer is then assigned the address of this allocated memory.

2. `if (root == NULL)`:
   - Error-checking step to ensure that the memory allocation was successful. If `malloc` fails to allocate memory for any reason, it returns a `NULL` pointer to indicate the failure.

3. `root->is_word = false;`:
   - The `is_word` field is a boolean flag associated with the `root` node. In this context, `root` is not associated with any specific word, so it is initialized as `false` to indicate that it's not a complete word.

4. `for (int i = 0; i < SIZE_OF_ALPHABET; i++)`:
   - This loop initializes the `children` array of the `root` node. The `children` array is an array of pointers to child nodes, one for each letter of the alphabet.

5. `root->children[i] = NULL;`:
   - Inside the loop, each element of the `children` array is initialized to `NULL`. This means that the `root` node initially has no children, as it represents the root of the trie. Also, note that `root->children[i]` goes until it reaches 26 or `[SIZE_OF_ALPHABET]`. So all of the 26 children will be pointing to `NULL`.

6. `while (fscanf(infile, "%s", name) == 1)`:
   - This `while` loop reads words from the `infile` (which will be dog_names.txt) using the `fscanf` function. It reads words using the format specifier `"%s"` and stores each word in the `name` character array.
   - The loop continues as long as `fscanf` returns 1, which means it successfully read a word from the file.

7. `node *cursor = root;`:
   - At the beginning of each iteration of the loop, a `cursor` pointer is initialized to point to the `root` of the trie. This pointer will be used to traverse the trie while inserting letters of the word.

8. `for (int i = 0, n = strlen(name); i < n; i++)`:
   - This `for` loop iterates over each character in the `name` array, representing the characters of the current word.

9. `int index = tolower(name[i]) - 'a';`:
   - For each character in the word, this line calculates an index based on the character. It uses `tolower(name[i])` to convert the character to lowercase (to ensure the trie is case-insensitive) and then subtracts 'a' to determine the index for the alphabet (0 for 'a', 1 for 'b', etc.).

10. `if (cursor->children[index] == NULL)`:
   - This condition checks if the current character (represented by `index`) does not have a corresponding child node in the `cursor` node's `children` array. If it's `NULL`, it means that this character has not been inserted into the trie yet.

11. Inside the `if` block, a new node is created for the character:
*The best analogy for this if statement is the "open door" example given in the extra classes by Doug Loyd.*
   - `node *new = malloc(sizeof(node));` allocates memory for a new node.
   - `new->is_word = false;` initializes the `is_word` flag for the new node to `false` because it's not a complete word yet.
   - A loop initializes the `children` array of the new node with `NULL` pointers, similar to the root node. This prepares the new node for any subsequent characters of the word.

12. `cursor->children[index] = new;`:
   - After creating the new node for the character, it's linked to the parent node's `children` array by setting `cursor->children[index]` to point to the new node.

13. `cursor = cursor->children[index];`:
   - The `cursor` is then updated to point to the newly created child node. This allows the program to continue adding the next character of the word to the trie.

14. After the `for` loop completes, the `cursor->is_word` is set to `true`:
   - This indicates that the current `cursor` node represents a complete word in the trie.

15. `if (check(get_string("Check word: ")))`:
   - This line prompts the user for a word to check using `get_string("Check word: ")`. It then calls the `check` function to determine if the entered word exists in the trie.
   - If the word is found in the trie (i.e., `check` returns `true`), the code inside the curly braces is executed.
   - If the word is not found, the code inside the `else` block is executed.

16. Inside the `if` block:
   - If the word is found in the trie, it prints "Found!" to indicate that the word exists in the vocabulary.

17. Inside the `else` block:
   - If the word is not found in the trie, it prints "Not Found." to indicate that the word is not in the vocabulary.

18. `if (!unload())`:
   - After checking the word, the code checks whether the process of unloading the trie from memory is successful. The `unload` function is called.
   - If unloading is successful (i.e., `unload` returns `true`), the code inside the `if` block is not executed.
   - If unloading encounters a problem (i.e., `unload` returns `false`), the code inside the `if` block is executed, which prints "Problem freeing memory!".

19. `fclose(infile);`:
   - Finally, the code closes the input file (`infile`) using the `fclose` function to release system resources associated with the file.

20. `bool unload(void)`:
   - The `unload` function is responsible for initiating the unloading process. It starts by calling the recursive function `unloader` with the root node as the starting point.

21. `void unloader(node* current)`:
   - The `unloader` function is a recursive function that takes a `current` node as a parameter.

22. Inside the `unloader` function:
   - It uses a loop to iterate over all the children of the current node (i.e., the letters of the alphabet).
   - For each child, it checks if it points to anything (i.e., if it's not `NULL`). If it's not `NULL, it calls the `unloader` function recursively for that child. This is the recursive step, as it goes deeper into the trie to free its child nodes.
   - Once it has checked all children, it proceeds to free the current node using `free(current)`. This is done after it has ensured that all child nodes are also freed. This way, it starts from the leaves of the trie and works its way up to the root.

23. `return true;`:
   - The `unload` function returns `true` to indicate that unloading was successful. However, it does not handle errors or return `false` in the event of a failure to unload.

### Creating the "check" function:
> Initialize a cursor node to the root of the trie.
```C
node *cursor = root
```
> Iterate through each character of the input word.

> Calculate the index corresponding to the current character in the trie (0 for 'a', 1 for 'b', etc.).

> Check if the child node at the calculated index is NULL. If it is, return false because the word is not found in the trie.

> Update the cursor to the child node at the calculated index.
```C
int index;

for (int i = 0; i < (strlen(word)); i++)
{
    index = tolower(word[i]) - 'a';
    if (cursor->children[index] == NULL)
    {
        printf("Not found!");
        return false;
    }

    cursor = cursor->children[index];
}
```
> After the loop, check if the cursor is at the end of a word in the trie by checking the `is_word` flag. 

> If it is, return `true` to indicate that the word exists in the trie. Otherwise, return `false`.

```C
if (cursor->is_word == true)
{
    printf("Found!");
    return true;
}
```
