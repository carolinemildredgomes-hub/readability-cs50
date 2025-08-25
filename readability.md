#include <ctype.h>
#include <cs50.h>
#include <math.h>
#include <stdio.h>
#include <string.h>

// Function prototypes
int count_letters(string text);
int count_words(string text);
int count_sentences(string text);

int main(void)
{
    // Prompt user for text
    string text = get_string("Text: ");

    // Count letters, words, sentences
    int letters = count_letters(text);
    int words = count_words(text);
    int sentences = count_sentences(text);

    // Compute L and S for the Coleman-Liau formula
    // L = avg letters per 100 words, S = avg sentences per 100 words
    float L = 0.0f;
    float S = 0.0f;
    if (words > 0)
    {
        L = ((float) letters / (float) words) * 100.0f;
        S = ((float) sentences / (float) words) * 100.0f;
    }

    // Coleman-Liau index
    float index = 0.0588f * L - 0.296f * S - 15.8f;
    int grade = (int) roundf(index);

    // Print according to spec
    if (grade < 1)
    {
        printf("Before Grade 1\n");
    }
    else if (grade >= 16)
    {
        printf("Grade 16+\n");
    }
    else
    {
        printf("Grade %i\n", grade);
    }

    return 0;
}

// Returns number of alphabetic letters in text
int count_letters(string text)
{
    int count = 0;
    for (int i = 0, n = strlen(text); i < n; i++)
    {
        if (isalpha((unsigned char) text[i]))
        {
            count++;
        }
    }
    return count;
}

// Returns number of words in text.
// This version is robust to multiple spaces and counts words
// by detecting a transition from non-word to word characters.
int count_words(string text)
{
    int words = 0;
    bool in_word = false;
    for (int i = 0, n = strlen(text); i < n; i++)
    {
        if (!isspace((unsigned char) text[i]) && !in_word)
        {
            // starting a new word
            in_word = true;
            words++;
        }
        else if (isspace((unsigned char) text[i]) && in_word)
        {
            // left a word
            in_word = false;
        }
    }
    return words;
}

// Returns number of sentences in text ('.', '!', '?')
int count_sentences(string text)
{
    int count = 0;
    for (int i = 0, n = strlen(text); i < n; i++)
    {
        char c = text[i];
        if (c == '.' || c == '!' || c == '?')
        {
            count++;
        }
    }
    return count;
}




# Readability — CS50 Problem Set 2

**Problem:** Compute the Coleman–Liau readability index for a given text and print the approximate U.S. grade level required to comprehend it.

**Language:** C (CS50 library)

## How to compile & run

On cs50.dev, run:

```bash
make readability
./readability
# or pipe text directly:
echo "One fish. Two fish. Red fish. Blue fish." | ./readability


Sample outputs

Interactive:

$ ./readability
Text: One fish. Two fish. Red fish. Blue fish.
Before Grade 1


Non-interactive:

$ echo "Would you like them here or there? I would not like them here or there. I would not like them anywhere." | ./readability
Grade 2

$ echo "Congratulations! Today is your day. You're off to Great Places! You're off and away!" | ./readability
Grade 3

