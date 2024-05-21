# SARS Coronavirus Genome Sequence Analysis

This script processes a FASTA file containing SARS coronavirus genome sequences. It filters sequences by their length, checks for a specific subsequence, and calculates pairwise differences between sequences to find the most similar sequences.

## Requirements

- Python 3.x
- Pandas
- NumPy

## Installation

Before running the script, ensure you have the necessary Python packages installed. You can install them using pip:

```bash
pip install pandas numpy
```

## Usage

1. **Place the FASTA file in the same directory as the script.**

   Ensure your FASTA file is named `SARS_CORONAVIRUS_NC_045512_sequence.fasta`.

2. **Run the script.**

   You can run the script using a Python interpreter:

   ```bash
   python your_script_name.py
   ```

## Script Details

### Steps:

1. **Read the FASTA File:**

   The script reads the FASTA file, skips the header row, and extracts the genome sequences.

   ```python
   pn = pd.read_csv('SARS_CORONAVIRUS_NC_045512_sequence.fasta', header = None)[1:][0]
   ```

2. **Convert to DataFrame:**

   The extracted sequences are converted to a DataFrame with a column named 'Genome Sequence'.

   ```python
   pn = pd.DataFrame(pn)
   pn.columns = ['Genome Sequence']
   ```

3. **Calculate Length and Filter by Median Length:**

   The script calculates the length of each sequence, determines the median length, and filters the sequences to keep only those with the median length.

   ```python
   pn['Length'] = pn['Genome Sequence'].apply(lambda x: len(x))
   mid_length = np.median(pn['Length'])
   pn = pn[pn.Length == mid_length]
   ```

4. **Check for Specific Subsequence:**

   Each sequence is checked for the presence of the subsequence 'TAATTTAGGCATGCCTT'. A new column 'Match?' is added to indicate whether the subsequence is present (1) or not (0).

   ```python
   pn['Match?'] = pn['Genome Sequence'].apply(lambda x: x.find('TAATTTAGGCATGCCTT'))
   pn['Match?'] = pn['Match?'].apply(lambda x: 0 if x == -1 else 1)
   ```

5. **Calculate Pairwise Differences:**

   The script calculates the number of different letters between each pair of sequences and finds the pair with the minimum number of differences.

   ```python
   def diff_letters(a,b):
       return sum(a[i] != b[i] for i in range(len(a)))

   differences = []
   for name, row in pn.iterrows():
       temp_diff = []
       for j in range(len(pn)):
           temp_diff.append(diff_letters(row[0], pn.iloc[j]['Genome Sequence']))
       differences.append(temp_diff) 

   min_val = 1000
   similar = 0
   for i in range(len(differences)):
       for j in range(len(differences[0])):
           if differences[i][j] < min_val and differences[i][j] != 0:
               min_val = differences[i][j]
               similar = (i,j)
   ```

## Author

Your Name

## License

This project is licensed under the MIT License - see the LICENSE file for details.
