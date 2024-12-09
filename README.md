# **FingerprintCheck Workflow**

## Version: 1.0

### Overview
The **FingerprintCheck** workflow compares two BAM or CRAM files using the **Somalier** tool to extract and evaluate their genomic fingerprints. This enables validation of sample identity and detection of potential cross-sample contamination or swaps.

### Workflow Structure
The workflow consists of the following steps:

1. **Extract Fingerprint**  
   Using Somalier, this step extracts fingerprint data from the input BAM/CRAM files for specific genomic sites provided.

2. **Check Fingerprint**  
   Compares the extracted fingerprints to calculate the relatedness between the two samples.

### Inputs
| Input Name      | Type   | Description                                                                          |
|------------------|--------|--------------------------------------------------------------------------------------|
| `bam1`          | File   | BAM/CRAM file for the first sample.                                                  |
| `bai1`          | File   | BAM/CRAM index file for the first sample.                                            |
| `prefix1`       | String | Prefix for naming the extracted fingerprint of the first sample. Default: `"f1_"`.   |
| `bam2`          | File   | BAM/CRAM file for the second sample.                                                 |
| `bai2`          | File   | BAM/CRAM index file for the second sample.                                           |
| `prefix2`       | String | Prefix for naming the extracted fingerprint of the second sample. Default: `"f2_"`.  |
| `reference`     | File   | Reference genome file in FASTA format.                                               |
| `sites`         | File   | BED file containing genomic sites for fingerprint extraction.                        |

### Outputs
| Output Name      | Type   | Description                                                |
|-------------------|--------|------------------------------------------------------------|
| `relatedness`     | String | Calculated relatedness score between the two samples.      |
| `fingerprint1`    | File   | Extracted fingerprint file for the first sample.           |
| `fingerprint2`    | File   | Extracted fingerprint file for the second sample.          |

### Workflow Steps
1. **ExtractFingerprint Task**
   - Extracts fingerprints from the BAM/CRAM files using Somalier.
   - Outputs `.somalier` files named with the specified prefixes.

2. **CheckFingerprint Task**
   - Takes the `.somalier` files and compares them using the Somalier `relate` command.
   - Generates a `somalier.pairs.tsv` file with detailed results and outputs the relatedness score.

### Runtime Attributes
Customizable runtime attributes can be passed to the tasks via `RuntimeAttr`. Default settings include:

- **Memory:** 2 GB  
- **Disk:** Calculated based on input size + 20 GB  
- **CPU:** 1 core  
- **Docker Image:** `brentp/somalier:v0.2.19`

### Usage
To execute this workflow, provide the required input files and customize optional parameters as needed. Below is an example configuration:

```json
{
  "FingerprintCheck.bam1": "sample1.bam",
  "FingerprintCheck.bai1": "sample1.bai",
  "FingerprintCheck.prefix1": "sample1_",
  "FingerprintCheck.bam2": "sample2.bam",
  "FingerprintCheck.bai2": "sample2.bai",
  "FingerprintCheck.prefix2": "sample2_",
  "FingerprintCheck.reference": "reference.fasta",
  "FingerprintCheck.sites": "fingerprint_sites.bed"
}
```

### Dependencies
- **Somalier:** Version `v0.2.19` (Docker image: `brentp/somalier:v0.2.19`)

### Output Example
After running the workflow, the following files will be generated:

1. `sample1_sample1.fingerprint.somalier`  
2. `sample2_sample2.fingerprint.somalier`  
3. `somalier.pairs.tsv`  
4. `relatedness.txt` (Contains the relatedness score)

### Notes
- Ensure the input BAM/CRAM files and reference FASTA are indexed.
- Sufficient disk space should be provisioned for large BAM files and their intermediate processing.  
