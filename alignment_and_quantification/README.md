# Disclaimer

This is designed for users of the HTCF cluster at Washington University in St. Louis. No guarantees are made about other systems, setups, configurations, etc. 

# Prerequisites

The scripts in this folder require `python3`. If this is not your default `python`, change all instance of `python` in `bulkRNACallingCards.sh` to `python3`. In addition, you will need to install the following modules:
- `pysam`
- `numpy`
- `pandas`
- `matplotlib`
- `twobitreader`

The easiest way to install them is:
- If `python3` is the default:
`pip install pysam numpy pandas matplotlib twobitreader`
- Otherwise:
`pip3 install pysam numpy pandas matplotlib twobitreader`

To figure out which version of `python` is installed by default:
- `python -V`

# Instructions

Copy these files into your working directory.

Make a folder called `logs` in this directory.

Edit the following files:
- `5prime_barcodes_R1.txt`
One sample per line, each line lists the primer barcode used for that sample
- `manifest_R1.txt`
One sample per line corresponding to 5prime_barcodes_R1.txt, each line listing the file containing the read1 sequences for that sample
- `manifest_I7.txt`
One sample per line corresponding to 5prime_barcodes_R1.txt and manifest_R1.txt, each line listing the i7 index (index 1) sequence used to demultiplex this sample
- `manifest_I5.txt`
Same instruction as per manifest_I7.txt, if the library was dual-indexed. Thus, each line lists the i5 index (index 2) sequence used to demultiplex this sample
- `bulkRNACallingCards.sh`
Edit the following lines:
- Line 6: set `--array=` to be the number of samples you are analyzing. Example: if you are analyzing 5 samples, you would write: `--array=1-5`
- Line 10: set `--mail-user=` to your e-mail address, for notifications about your job submission
- Line 41: set `transposase=` to the transposase used in the experiment. Example: if you used piggyBac, you would write: `transpoase="PB"`
- Line 42: set `genome=` to the one appropriate for your experiment. Recommend values are `hg38` for human and `mm10` for mouse
- Line 46: set the terminal repeat sequence used in this experiment. This line should, collectively, specify the barcode and terminal repeat sequences expected at the start of every read1. Note that the barcode will be automatically stored in the `$barcode` variable. Therefore, the only thing to specify is the terminal repeat sequence expected to be found. A list of commonly-used terminal repeats is found in lines 36-39. Example: if you are expecting to find the long PB LTR sequence, you would write: `-g "^"$barcode$Long_PB_LTR`

# Running the Scripts

As a sanity check, ensure that `5prime_barcodes_R1.txt` and `manifest_R1.txt` (and `manifest_I7.txt` and `manifest_I5.txt`, if used) have the same number of lines, and that this number is equal to the range specified in line 6 (`--array=`). To run the scripts, enter `sbatch bulkRNACallingCards.sh`

# Output

The major output of the scripts are aligned BAM files of insertions as well as CCF files for statistical analysis and visualization. These will be written to `../output_and_analysis/`. The `logs` folder contains stdout and stderr files from the scripts. The `*.out` files contain information from the adapter trimming step, including the number and percent of reads in the read1 file containing the correct barcode-terminal repeat combination. The `*.err` files contain mapping information, specifically what fraction of the trimmed read1's mapped to the genome.
