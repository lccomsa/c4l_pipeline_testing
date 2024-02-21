# Mitogenome Pipeline


## 1. Dependencies

All dependencies are already present on amarel as a module or will become 
accessible once you follow the instructions in section 1.1.

- Python
- perl
- FastQC
- Trimmomatic
- Samtools
- BWA
- Stampy
- NOVOplasty
<br>

### 1.1 Adding community modules and Geneva lab shared files

First, your amarel account and environment should have some configurations 
made so that you can access the necessary dependencies. You must have access 
to the community module files, as well as the Geneva lab’s shared apps. You 
can do this by following the following instructions:
<br>

To use shared modules you will need to add these lines to your path.

Edit your bash profile in your home directory
```
cd
nano .bash_profile
```
In this file, there is a line labeled: `# User specific environment and startup programs`

Paste these commands **underneath** that line
```
module use /projects/community/modulefiles
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$HOME/last/bin:/projects/f_geneva_1/.shared_apps/bin
export PATH
```
Save and exit the file, then run this command
```
source .bash_profile
```
<br>

**Note: The above changes will be permanent to your amarel account. 
If you do not want that to be the case, reverse the second step to 
delete the added text from your .bash_profile and everything will 
revert back to previous settings.**  
<br>

### 1.2 Variables and file locations

While following the instructions under **2. Setup**, you will need 
several files and pieces of information, which will be listed below:

For section 2.1:

- The files that must be copied over are located on amarel in:
		/projectsc/f_geneva_1/caden/mtGenomes/mitogenome_pipeline
- The reference genome file is located at the following location. 
Copy it into your project’s “references” folder
		/projectsc/f_geneva_1/caden/mtGenomes/references/sagrei.fasta
<br>

For section 2.2, use the following data to fill out your configuration file, as per the instructions:

- Forward illumina reads:  
/projectsc/f_geneva_1/caden/mtGenomes/assemblies/tropidolepis-tc/tropidolepis-tc-SRR7240910_1.fastq.gz
- Reverse illumina reads:  
/projectsc/f_geneva_1/caden/mtGenomes/assemblies/tropidolepis-tc/tropidolepis-tc-SRR7240910_2.fastq.gz
- Read length:  
450
- Insert length:  
125
- Illumina adaptor file location:  
/projectsc/f_geneva_1/programs/trimmomatic/adapters/TruSeq3-PE-2.fa:2:30:10:4
- Kmer: (leave the default of 33)
- Species name:  
anolis_tropidolepis
- Name of reference:  
sagrei
- Name of reference file:  
sagrei.fasta
<br>

## 2. Setup

### 2.1 Preliminary file and folder setup
The first step necessary to prepare to run the pipeline is to select 
or create a folder in which the project will be conducted. It is 
important that the name of the folder does not contain any SPACES! 
From here on out in these instructions, this folder will be referred 
to as your "project folder".

Next, within your project folder, create the following two folders 
(named EXACTLY as listed below, including identical capitalization):
- species-fetch
- references
- mitogenome\_pipeline

Now, you will copy the following files into the "mitogenome\_pipeline" folder:
- novoplasty_template.txt
- pipe_config_template.txt
- pipeline.sh
- sam_depth.sh

A reference mitochondrial genome is required to run the pipeline. Move 
or copy your reference genome (in .fasta format) into the "references" folder.  
<br>

### 2.2 Filling out the configuration file

Determine the name or ID you will to use for the read data that you will be 
assembling. This can either be a species name, or some other unique identifier, 
as long as there are NO SPACES or special characters outside of dashes and 
underscores in the name (eg. homo_sapiens , AP-run321). For our example, we 
will use _yourName_ as a place-holder. Wherever you see _yourName_ , replace 
it with the ID you have chosen.

Next, you will save a copy of the pipe_config_template.txt file in the 
"species-fetch" folder. Rename this file to _yourName_.txt

Open this file with a text editor (recommended is using Nano via the 
unix shell). You should see something that looks like the following:

<details>
<summary>Blank Configuration File</summary>

  ```
  ## Directory and file name pointing to forward illumina reads

  ## Directory and file name pointing to reverse illumina reads

  ## Length of reads from illumina run (integer)

  ## Insert length from illumina run (integer)

  ## Directory and file name pointing to the illumina adaptor file for trimmomatic to use
  /projectsc/f_geneva_1/programs/trimmomatic/adapters/TruSeq3-PE-2.fa:2:30:10:4
  ## Kmer to be used for novoPlasty assembly (integer)
  33
  ## Name/ID of the species that will be the reference mt-genome

  ## Name of the reference genome file with file extension

  #### If you would like for a sub-section of the pipeline to run, set that value to 1. To not have a subsection run, set to 0 (zero). 
  ## to run FastQC, Trimmomatic, and post-trim FastQC
  1
  ## to run BWA mapping and post-bwa sorting
  1
  ## to run Stampy mapping
  1
  ## to run samtools filtering of stampy mapped reads
  1
  ## to run generation of novoplasty configuration file and assembly
  1
```
</details>

Note that some values may be pre-filled with recommended values for those 
parameters.

We will now go over how to fill in the configuration file.

<details>
<summary>WARNINGS:</summary>

  - First and foremost, it is important to remember that using SPACES 
  for any of the inputs without special actions taken will cause issues. If you 
  are just starting out using the unix shell, please ensure that any folder 
  names or file names are changed so that they do not contain spaces! This also
  applies to when you are entering parameters into the configuration file. Ensure
  no spaces are added by accident to the beginning or end of your entry, else errors 
  may occur when running the pipeline.
  
  - Next, it is also important not to tamper with the spacing of each line.
  The pipeline is designed to pull important data and variables from the entry
  lines based on line numbering/spacing, so adding extra lines or deleting them
  will cause the pipeline to not function.

</details>

The general procedure for filling in the configuration file is that the relevant 
parameter goes into the blank line below the instructions written starting with \#\#.

Each dropdown below expands inctructions for filling out each respective line in 
the configuration file:

<details> 
<summary>## Directory and file name pointing to (forward/reverse) illumina reads</summary>
  Below each of these lines, write the full file path to either the forward or reverse read 
  file for the species' whose mitochodrial genome you wish to assemble.
</details>

<details> 
<summary>## Length of reads from illumina run (integer)</summary>
  This is the integer value representing the average read length generated during the 
  sequencing run. Do not write bp or anything else, just the INTEGER.
</details>

<details> 
<summary>## Insert length from illumina run (integer)</summary>
  This is the integer value representing the average insert length generated during the 
  sequencing run. Do not write bp or anything else, just the INTEGER.
</details>

<details> 
<summary>## Directory and file name pointing to the illumina adaptor file for trimmomatic to use</summary>
  Write the full file path to the adaptor you wish to use when trimming the reads.
</details>

<details> 
<summary>## Kmer to be used for novoPlasty assembly (integer)</summary>
  Enter the integer value for the Kmer you wish NovoPlasty to use when assembling 
  the genome. We recommend starting with a value of 33.
</details>

<details> 
<summary>## Name/ID of the species that will be the reference mt-genome</summary>
  Enter a name or ID that allows you to know what reference you used when generating 
  this assembly. This does not need to be identical to the name of the reference 
  file (see below), but it should be a NAME ONLY with NO FILE EXTENSION. DO NOT USE SPACES.
  - Good example: potato_3
  - Bad examples: potato_3_r445.fasta , potato 3 r445
</details>

<details> 
<summary>## Name of the reference genome file with file extension</summary>
  Enter the FILE NAME of the reference file that will be used for the intended assembly, 
  INCLUDING FILE EXTENSION. (ie. this is when you would enter potato_3_r445.fasta ). DO 
  NOT INCLUDE FILE PATH.
</details>
<br>

### 2.3 Before running the pipeline

Congratulations! You are almost at the point where you can run the pipeline!!

There are two last steps that must happen before you can run it.

First, you must make a few modifications the the pipeline.sh file:
- Open the pipeline.sh file with a text editor (Using Nano via the Unix shell
  recommended).
- Scroll down to approximately line 31. You should see something like this:
```
  #File directory where the project will be run
  project=""
```
- where it says ``project=""`` , delete anything that is between the quotes
  and then WITHIN THE QUOTES type the directory path to your
  project folder that you selected or created at the beginning of this
  series of instructions. Make sure that you DO NOT put a "/" at the end of
  the directory path!! You should end up with something like this:
```
  #File directory where the project will be run
  project="/home/users/me/genetics_projects/mito-genome-project"
```
- REMINDER: If any of your file directory names from above have spaces in them,
  and you do not know how to deal with spaces in file and directory names in the Unix
  Shell, you should consider changing your folder names to remove or replace the
  spaces with characters such as underscores or dashes.
<br>

The second step only applies if you are trying to rerun the pipeline on a data 
sample that previously failed. If this is your first time trying to assemble this 
unique genome, then you can skip forward to the next section "Running the pipeline"

If the pipeline fails at an intermediate step, the steps after that one will still 
generate files, but they will be empty. So, any time after a run of the pipeline is 
complete, you must check the output log file to see how the run proceeded and if 
any errors occurred. After the site of an error is identified, all failed output 
files should be deleted before a rerun. Additionally, the user should determine whether 
they wish to run the pipeline from the beginning (in which case all output files should 
be deleted), or if they wish to rerun it from the last successful step. If the latter is 
desired, the user should edit the last part of the configuration file such that only 
the relevant subsections of the pipeline run.  
<br>

## 3. Running the pipeline

To run the pipeline is relatively straightforward. For each of the instructions below, 
where it says _yourName_ you should type the Name or ID that you chose during the 
"Filling out The Configuration File" instructions above.

- If running on the unix shell terminal locally, the pipeline can be run using
```
$  source pipeline.sh "yourName"
```
- If running on a computing system that utilizes slurm, then use
```
$  sbatch pipeline.sh "yourName"
```

