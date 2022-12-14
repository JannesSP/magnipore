# Magnipore

If you find a bug please add it to the issues on github with a detailed desciption. :)

## Description

Magnipore is used to compare two ONT samples with each other on a signal level to find differential signals between these samples on single base resolution.
These differences occur majorly due to molecular base changes hinting to mutations or modifications.
Magnipore classifies these differences and provides the user with a positionwise comparison together with quality values like a bayesian p-value.

## Usage

### Conda

>Magnipore conda coming soon

For now create a conda environment using the [conda_env.yml](conda/conda_env.yml).
If you want to basecall your ONT data you also need a Guppy version from [Oxford Nanopore Technologies](https://community.nanoporetech.com).

#### Simplest use case:

Without basecalling:
```
magnipore.py path_to_fast5_first_sample path_to_reference_first_sample first_sample_label path_to_fast5_sec_sample path_to_reference_sec_sample sec_sample_label working_dir --path_to_first_basecalls PATH_TO_FIRST_BASECALLS --path_to_sec_basecalls PATH_TO_SEC_BASECALLS
```

With basecalling
```
magnipore.py path_to_fast5_first_sample path_to_reference_first_sample first_sample_label path_to_fast5_sec_sample path_to_reference_sec_sample sec_sample_label working_dir --guppy_bin PATH --guppy_model PATH
```

#### Help:
```
usage: magnipore.py [-h] [--guppy_bin GUPPY_BIN] [--guppy_model GUPPY_MODEL] [--guppy_device GUPPY_DEVICE] [--path_to_first_basecalls PATH_TO_FIRST_BASECALLS]
                    [--path_to_sec_basecalls PATH_TO_SEC_BASECALLS] [--calculate_data_density] [-t THREADS] [-f5] [-fr] [--strict] [-r2] [-mx {map-ont,splice,ava-ont}] [-mk MINIMAP2K] [--timeit]
                    path_to_fast5_first_sample path_to_reference_first_sample first_sample_label path_to_fast5_sec_sample path_to_reference_sec_sample sec_sample_label working_dir

positional arguments:
  path_to_fast5_first_sample
                        FAST5 file of first sample
  path_to_reference_first_sample
                        reference FASTA file of first sample
  first_sample_label    Name of the sample or pipeline run
  path_to_fast5_sec_sample
                        FAST5 file of second sample
  path_to_reference_sec_sample
                        reference FASTA file of second sample
  sec_sample_label      Name of the sample or pipeline run
  working_dir           Path to write all output files

optional arguments:
  -h, --help            show this help message and exit
  --guppy_bin GUPPY_BIN
                        Guppy binary (default: None)
  --guppy_model GUPPY_MODEL
                        Guppy model used for basecalling (default: None)
  --guppy_device GUPPY_DEVICE
                        Use the GPU to basecall "cuda:0" to use the GPU with ID 0 (default: cuda:0)
  --path_to_first_basecalls PATH_TO_FIRST_BASECALLS
                        FASTQ file to use: <first_sample_label>.fastq Will skip basecalling for first sample (default: None)
  --path_to_sec_basecalls PATH_TO_SEC_BASECALLS
                        FASTQ file to use: <sec_sample_label>.fastq Will skip basecalling for second sample (default: None)
  --calculate_data_density
                        Will calculate data density after building the models. Will increase runtime! (default: False)
  -t THREADS, --threads THREADS
                        Number of threads to use (default: 1)
  -f5, --fast5_out      Guppy generates FAST5 output (workspace folder) of Guppy (default: False)
  -fr, --force_rebuild  Run commands regardless if files are already present (default: False)
  --strict              Do not write positions with a mutational context into .magnipore files (default: False)
  -r2, --range2         Use range 2 instead of range 3 for the mutational context check (default: False)
  -mx {map-ont,splice,ava-ont}, --minimap2x {map-ont,splice,ava-ont}
                        -x parameter for minimap2 (default: splice)
  -mk MINIMAP2K, --minimap2k MINIMAP2K
                        -k parameter for minimap2 (default: 14)
```
<!-- #### positional arguments:
- path_to_fast5_first_sample : FAST5 directory of first sample
- path_to_reference_first_sample : reference FASTA file of first sample
- first_sample_label : Name of the sample or pipeline run
- path_to_fast5_sec_sample : FAST5 file of second sample
- path_to_reference_sec_sample : reference FASTA file of second sample
- sec_sample_label : Name of the sample or pipeline run
- working_dir : Path to write all output files

#### required arguments:
use either the basecalling arguments or provide basecalls
- basecalling arguments:
    - guppy_bin : Path to guppy binary
    - guppy_model : Path to guppy model used for basecalling
    - (optional) guppy_device : Device used for basecalling (cpu or gpu cuda:0)
- provided basecalls (FASTQ)
    - path_to_first_basecalls : Path
    - path_to_sec_basecalls : Path

For optional arguments see magnipore.py --help. Includes small number of mapping parameters and the option to skip basecalling. -->

## Output

The .magnipore file is a tsv containing the following columns.

- strand : on which strand the comparison took place
- td_score : threshold distance score for the signal comparison
- kl_divergence : kullback leibler divergence for the signal comparison
- bayesian_p : p-value for the signal comparison
- signal_type : classification into "mod" for modification and "mut" for mutation
- ref_1 : contig name of sample 1
- pos_1 : position in contig of sample 1
- base_1 : base at the position of sample 1
- motif_1 : motif around the base at the position of sample 1
- signal_mean_1 : mean of the signal distribution at the position of sample 1
- signal_std_1 : standard deviation of the signal distribution at the position of sample 1
- n_datapoints_1 : number of data points that formed the signal distribution
- contained_datapoints_1 : number of data points withtin 3 standard deviations around the mean
- n_segments_1 : number of segments from nanopolish eventalign that formed the signal distribution
- contained_segments_1 : number of segments within 3 standard deviations around the mean
- n_reads_1 : number of reads (coverage) that formed the signal distribution

same for second sample:
- ref_2, pos_2, base_2, motif_2, signal_mean_2, signal_std_2, n_datapoints_2, contained_datapoints_2, n_segments_2, contained_segments_2, n_reads_2