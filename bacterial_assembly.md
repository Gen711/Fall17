Lab 9: Bacterial Genome Assembly
--

> Step 1: Launch an instance on Jetstream. For this exercise, we will use a m1.xlarge instance.

> Update, upgrade, install...

```
sudo apt-get -y install ruby build-essential python python-pip default-jre
```

>Install LinuxBrew, then the following software..

```
brew install gcc gnuplot canu quast spades
```

> Install a python plotting tool

```
pip install matplotlib
```


> Download PacBio Data. This is 25x coverage of an E. coli genome.

```
curl -L -o pacbio.fastq http://gembox.cbcb.umd.edu/mhap/raw/ecoli_p6_25x.filtered.fastq
```

> Download an Illumina dataset for E. coli, along with a reference genome

```
curl -LO https://s3.amazonaws.com/gen711/ecoli_data.tar.gz
tar -zxf ecoli_data.tar.gz
curl -LO ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/005/845/GCF_000005845.2_ASM584v2/GCF_000005845.2_ASM584v2_genomic.fna.gz
```

> Assemble with canu (http://canu.readthedocs.io/en/latest/index.html). Canu is the leading long-read genome assembler as of 2017.

```
canu \
 -p ecoli -d ecoli-pacbio \
 genomeSize=4.8m \
 -pacbio-raw pacbio.fastq
 ```

> Assemble with SPAdes (http://cab.spbu.ru/software/spades/), using a kmer length of 95. This is the genome assembler you will use, if you assemble Illumina data.

```
 spades.py -t 24 -m 55 --mp1-rf -k 95 \
 --pe1-1 ecoli_pe.1.fq \
 --pe1-2 ecoli_pe.2.fq \
 --mp1-1 nextera.1.fq \
 --mp1-2 nextera.2.fq \
 -o Ecoli_all_data
```

> compare the genomes, using quast

```
quast Ecoli_all_data/scaffolds.fasta ecoli-pacbio/ecoli.contigs.fasta \
        -R GCF_000005845.2_ASM584v2_genomic.fna.gz \
        -o quast_output --threads 24 --gene-finding
```

> look at report, found at `quast_output/report.txt`

> Download the full report, with pretty graphics, optional.
```
scp -ri jetkey username@xxx.xxx.xxx.x:quast_output ~/Downloads
```

> Optional: Polish PacBio assembly using Quiver (https://github.com/PacificBiosciences/GenomicConsensus). This will take more time that we have in lab, but might be a good learning experience.

This lab uses code from ANGUS2017: https://angus.readthedocs.io/en/2017/visualizing-blast-scores-with-RStudio.html
