# Fuzzy Matching Made Fun (and Functional!)

## Overview

Welcome to the world of **fuzzy matching**, where names get scrubbed, massaged, and compared to find their long-lost twins (or at least their closest lookalikes). This Python script helps match names between two CSV files, using both **exact** and **fuzzy** matching techniques, so you can spot similarities—even when typos or formatting quirks try to get in the way!

![fuzzy_matching](https://github.com/user-attachments/assets/9658fb60-8f38-45a7-8576-f81e0eac6357)

## Setup

Before you dive into the wonderful world of near-identical text, you need to set up your environment. Follow these steps:

1. **Create a virtual environment** (optional, but recommended):
   ```bash
   virtualenv venv
   ```
2. **Activate your virtual environment**:
   ```bash
   source venv/bin/activate
   ```
3. **Install dependencies**:
   ```bash
   pip install fuzzywuzzy python-Levenshtein
   ```

Now you're ready to rock and roll!

## Running the Script

Once set up, you can run the script like this:

```bash
python fuzzyMatch.py ~/Downloads/Matching\ Directory\ Combined\ -\ EP.csv 0 ~/Downloads/BankBillPayFile.csv 0
```

Here’s what’s happening:
- The first argument is the **first CSV file**.
- The second argument is the **column index** (zero-based) to match from that file.
- The third argument is the **second CSV file**.
- The fourth argument is the **column index** (zero-based) to match from that file.

## What’s Under the Hood?

### The Magic Formula (aka How It Works)

1. **Read CSV files**: We load the given column from each CSV and store it as a key-value pair, where the key is a cleaned-up version of the name.
2. **Exact Matching**: We check for names that match exactly across both files.
3. **Fuzzy Matching**: When exact matching fails, we use **Levenshtein distance** (via `fuzzywuzzy`) to find similar names.
4. **Export the results**: The script writes a `matches.csv` file containing:
   - Exact matches
   - Fuzzy matches with a similarity score

### The Cleanup Crew (aka Preprocessing)

Before comparing, names are **cleaned up** by:
- Removing **punctuation**
- Stripping **trailing digits** (because who needs those?)
- Converting to **lowercase**
- Removing **spaces**

### What You’ll See in the Output

The script will proudly announce:
- The number of items in each file
- How many exact matches were found
- How many near-matches were discovered
- A final *victory message* when the matching is done!

## Example Output

```plaintext
File 1 has  150  items.
File 2 has  120  items.
Great news! We found 75 exact matches.
Bad news :( We found 30 in file2 that are not in file1 and still have 45 to compare against.
We have 20 fuzzy matches.
Done, check the output file.
```

## Things to Keep in Mind

- **Higher scores (out of 100) mean better matches**.
- If the top match has a score <90, we add a few empty placeholders to visually separate weaker suggestions.
- The fuzzy matching threshold is set at **65**, so anything below that is ignored (we don’t want garbage matches!).

## Wrapping Up

With this script, you can now:

✅ Identify duplicate or nearly identical names across two CSV files.
✅ Catch those sneaky variations in spelling and formatting.
✅ Save tons of time instead of manually comparing files.

Go forth and match like a pro! 🎯

