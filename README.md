Objective: Generate a lookup table which converts Vibrio cholerae genome assemblies with classical locus tag (VC395) notation to El Tor notation (VC).

Materials Needed: 
1)	Windows Subsystem for Linux aka WSL (for use with Conda and Conda-dependent packages)
2)	Ubuntu
3)	Git (https://git-scm.com/downloads)
4)	Github repository (https://github.com/)
5)	Bioconda on WSL (https://bioconda.github.io/)
6)	gffread and BLAST Bioconda packages

https://anaconda.org/bioconda/gffread
https://anaconda.org/bioconda/blast

7)	Genomic sequence files for Reference and Target genome assemblies (in our case, we use ASM2162v1 as the reference assembly and ASM674v1 as the target assembly).

https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000021625.1/
https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000006745.1/

Download Genome sequences (FASTA), Annotation Features (GTF and GFF), Sequence and Annotation (GBFF), and Genomic Coding Sequences (FASTA) to generate .CSV file with correct format for use with gffread. Extract these files to a folder with the assembly name e.g. ASM2162v1 or ASM674v1.

Steps:

1.	Open WSL terminal by searching “Ubuntu” in the Windows start menu, or right clicking on a destination folder and clicking “open Linux shell”
2.	Install Bioconda in the Linux shell by following the directions on the Bioconda usage site
3.	Set up Bioconda channel for installing packages and set channel priority:

conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
conda config –-set channel_priority strict

4.	Install gffread and BLAST Bioconda packages by entering the code below:

conda install -c bioconda gffread
conda install -c bioconda BLAST

You now have the necessary packages installed to perform sequence-based mapping using two or more assemblies.

5.	Install git on your Ubuntu terminal:

sudo apt update
sudo apt install git

6.	Create a public repository on Github (you can name this repository whatever you like), and add the folders containing the genome assemblies downloaded from NCBI
7.	On your Ubuntu terminal, navigate to the folder that you want to install your newly created Github repository with the genome assemblies using the cd command (NOTE: you can use the mkdir command to create a new directory in Ubuntu)
8.	Clone the Github repository to the desired location on your Ubuntu terminal:

git clone <repository_url>

#for example,

git clone https://github.com/viberio/VCassemblies
git init

9.	Commit the changes to your local (Ubuntu) repository, then push the changes to your remote repository on Github:

git commit -m “Adding genome assemblies”
git remote add origin <repository_url> #as above
git push -u origin main
git pull origin main 
#this updates local repository based on changes made to remote
10.	 Navigate to the folder containing the assemblies on your Ubuntu terminal using the cd command
11.	 Navigate to either of the assembly folders using the cd command
12.	 Use gffread to extract the CDS sequences from the .gff and .fna files of Assembly 1:

gffread genome.gff -g genome.fna -x cds1.fasta
#here we are making cds1.fasta the output file that contains the CDS sequences for Assembly 1

13.	 Navigate to the folder containing the other assembly
14.	 Use gffread to extract the CDS sequences from the .gff and .fna files of Assembly 2:

gffread genome.gff -g genome.fna -x cds2.fasta
#here we are making cds2.fasta the output file that contains the CDS sequences for Assembly 2

15.	 Use BLAST to map the genes (based on CDS sequence similarity) between the two assemblies:

makeblastdb -in cds2.fasta -dbtype nucl -out cds1vscds2_db
blastn -query cds1.fasta -db cds1vscds2_db -outfmt 6 -out blast_results.tsv

#line 1 creates a nucleotide database using CDS sequences from Assembly 2 (target assembly)
#line 2 performs a nucleotide BLAST against the new database with Assembly 1 as the query, and outputs blast_results.tsv which can be opened using Excel to display genes mapped based on sequence similarity in columns 1 and 2

