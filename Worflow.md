# Workflow

## 1. Working directory setup

```
@143.107.54.134
cd /projects/augustold/CSHL/Hisat2_map_stats

# Create directories
mkdir R570 SP80-3280 AP85-441

# Genome files path
/projects/augustold/CSHL/Saccharum_genome_refs

# FASTQ files path
/projects/augustold/fastq/
```

## 2. Conda enviroment setup

```
# Creaye enviroment
conda create -n hisat2
conda activate hisat2

# Install softwares
conda install -c bioconda hisat2
conda install -c bioconda stringtie
```

## 3. Build genome index
```
# AP85-441
#screen
#conda activate hisat2
cd /projects/augustold/CSHL/Saccharum_genome_refs/Sspon
mkdir hisat2_index
cd hisat2_index
hisat2-build -p 40 /projects/augustold/CSHL/Saccharum_genome_refs/Sspon/Sspon.HiC_chr_asm.fasta hisat2_index

# SP80-3280
#screen
#conda activate hisat2
cd /projects/augustold/CSHL/Saccharum_genome_refs/R570
mkdir hisat2_index
cd hisat2_index
hisat2-build -p 20 /projects/augustold/CSHL/Saccharum_genome_refs/SP803280/sc.mlc.cns.sgl.utg.scga7.importdb.fa hisat2_index

# R570
#screen
#conda activate hisat2
cd /projects/augustold/CSHL/Saccharum_genome_refs/R570
mkdir hisat2_index
cd hisat2_index
hisat2-build -p 20 /projects/augustold/CSHL/Saccharum_genome_refs/R570/single_tiling_path_assembly.fna hisat2_index
```

## 4. Map RNAseq samples

```
# AP85-441
#screen
#cd /projects/augustold/CSHL/Hisat2_map_stats/AP85-441
conda activate hisat2

for SAMPLE in $(ls /projects/augustold/fastq/ | sed s/_[12].fq.gz// | sort -u); do
    hisat2 \
    -p 30 \
    -x /projects/augustold/CSHL/Saccharum_genome_refs/Sspon/hisat2_index/hisat2_index \
    -1 /projects/augustold/fastq/${SAMPLE}_1.fq.gz \
    -2 /projects/augustold/fastq/${SAMPLE}_2.fq.gz \
    -S ${SAMPLE}.sam \
    2> summary_${SAMPLE}.txt

    samtools view -Su ${SAMPLE}.sam | samtools sort -@ 30 -m 5G -o ${SAMPLE}.sorted.bam

    rm -rf ${SAMPLE}.sam
done

# SP80-3280
#screen
#cd /projects/augustold/CSHL/Hisat2_map_stats/SP80-3280
conda activate hisat2

for SAMPLE in $(ls /projects/augustold/fastq/ | sed s/_[12].fq.gz// | sort -u); do
    hisat2 \
    -p 30 \
    -x /projects/augustold/CSHL/Saccharum_genome_refs/SP803280/hisat2_index/hisat2_index \
    -1 /projects/augustold/fastq/${SAMPLE}_1.fq.gz \
    -2 /projects/augustold/fastq/${SAMPLE}_2.fq.gz \
    -S ${SAMPLE}.sam \
    2> summary_${SAMPLE}.txt

    samtools view -Su ${SAMPLE}.sam | samtools sort -@ 30 -m 5G -o ${SAMPLE}.sorted.bam

    rm -rf ${SAMPLE}.sam
done

# R570 (keep only "summary_sample.txt" files)
#screen
#cd /projects/augustold/CSHL/Hisat2_map_stats/R570
conda activate hisat2

for SAMPLE in $(ls /projects/augustold/fastq/ | sed s/_[12].fq.gz// | sort -u); do
    hisat2 \
    -p 20 \
    -x /projects/augustold/CSHL/Saccharum_genome_refs/R570/hisat2_index/hisat2_index \
    -1 /projects/augustold/fastq/${SAMPLE}_1.fq.gz \
    -2 /projects/augustold/fastq/${SAMPLE}_2.fq.gz \
    -S ${SAMPLE}.sam \
    2> summary_${SAMPLE}.txt

    rm -rf ${SAMPLE}.sam
done
```


