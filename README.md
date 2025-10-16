This is an example of running ANNEXA with public data provided by PacBio from human Kinnex full-length RNA sequencing (https://downloads.pacbcloud.com/public/dataset/Kinnex-full-length-RNA/DATA-EXAMPLE/). See [here](https://downloads.pacbcloud.com/public/dataset/Kinnex-full-length-RNA/README.txt) for more information on this dataset and [here](https://github.com/IGDRion/ANNEXA) for information on ANNEXA.

# Preparing data
1. The .bam file was converted back to fastq with [samtools](https://www.htslib.org/) using:
 ```samtools fastq human_80k_Sreads.segmented.bam > human_80k_Sreads.segmented.fastq```
 2. Reads were aligned to the [GRCh38 genome](https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_47/GRCh38.p14.genome.fa.gz) using [minimap2](github.com/lh3/minimap2): 
 ```minimap2 -ax splice -k14 -uf -t 4 GRCh38.p14.genome.fa human_80k_Sreads.segmented.fastq > human_80k_Sreads.segmented.fastq.sam```
 3. The .sam file was converted to .bam and sorted using samtools:
```
samtools view -b -h -O BAM -@ 6 -o human_80k_Sreads.segmented.fastq.bam human_80k_Sreads.segmented.fastq.sam
samtools sort -@ 6 -o human_80k_Sreads.segmented.fastq.sorted.bam -T human_80k_Sreads.segmented.fastq.sorted human_80k_Sreads.segmented.fastq.bam
```
4. The .bam file was downsampled to only the chromosome 1 using:
```
samtools view -b human_80k_Sreads.segmented.fastq.sorted.bam chr1 > chr1.bam
```

# Running ANNEXA
For this example, we downsampled the reads, the reference annotation ([GENCODE](https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_47/gencode.v47.chr_patch_hapl_scaff.annotation.gtf.gz)) and the reference genome to only chr1.

ANNEXA was run using:
```
nextflow run IGDRion/ANNEXA \
-profile docker \
--input sample.txt \
--gtf chr1.gtf \
--fa chr1.fa \
--maxCpu 2 \
--maxMemory 8 \
-resume
```

```results``` contains the output of ANNEXA and ```nextflow.log``` a log of the Nextflow run. 