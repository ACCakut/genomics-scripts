**BWA + Pilon** is a widely used workflow for improving genome assemblies, particularly when working with Illumina short reads. Below is an overview of the process and how to implement it based on the provided search results:

---

## **Overview of BWA + Pilon Workflow**
This workflow involves two main steps:
1. **Mapping Illumina reads to the reference genome/assembly using BWA**.
2. **Polishing the assembly using Pilon to correct errors and improve quality**.

---

## **Step-by-Step Guide**

### **1. Index the Reference Genome**
Before mapping reads, you need to index the reference genome or assembly using BWA:
```bash
bwa index reference.fasta
```
This generates several index files (`.amb`, `.ann`, `.bwt`, `.pac`, `.sa`) that are required for alignment[1][3][6].

---

### **2. Map Reads Using BWA**
Use `bwa mem` to align paired-end Illumina reads to the reference genome. Adjust the number of threads (`-t`) as needed:
```bash
bwa mem -t 8 reference.fasta reads_R1.fastq.gz reads_R2.fastq.gz > alignment.sam
```
- This produces a SAM file containing the alignment information[3][6].
- If you are working with single-end reads, provide only one FASTQ file instead of two[3].

## use rather default penalty parameters B and O than our customized ones


---

### **3. Convert and Sort SAM File**
Convert the SAM file to BAM format and sort it using Samtools:
```bash
samtools view -Sb alignment.sam > alignment.bam
samtools sort -o sorted_alignment.bam alignment.bam
```
Index the sorted BAM file for faster access:
```bash
samtools index sorted_alignment.bam
```
These steps prepare the alignment file for use in Pilon[6].

---

### **4. Run Pilon for Polishing**
Run Pilon using the reference genome and the sorted BAM file:
```bash
pilon --genome reference.fasta --bam sorted_alignment.bam --output polished --threads 8
```
- Pilon will analyze discrepancies between the mapped reads and the input genome to correct errors such as single nucleotide polymorphisms (SNPs), small indels, and misassemblies[2][6].
- The output will include an improved FASTA file (`polished.fasta`) and optionally a VCF file detailing detected variants.

---

## **Optional: Iterate Polishing**
If necessary, you can perform multiple rounds of polishing by repeating Steps 2–4 with the updated assembly from Pilon as the new reference genome. However, multiple rounds are usually not recommended unless there are significant improvements after each round[5].

---

## **Applications and Benefits**
- This workflow is ideal for bacterial genomes like *Bacillus subtilis*, where high-quality assemblies are achievable with Illumina data.
- It ensures that small errors in base calls, indels, or structural misassemblies are corrected, leading to a highly accurate final assembly.

By combining BWA's efficient mapping with Pilon's powerful error correction, this approach provides a reliable method for refining genome assemblies.

Citations:
[1] https://mtbgenomicsworkshop.readthedocs.io/en/latest/material/day3/mapping.html
[2] https://denbi-nanopore-training-course.readthedocs.io/en/latest/artic/pilon/
[3] https://genomics.sschmeier.com/ngs-mapping/
[4] https://www.youtube.com/watch?v=z18BEKxB0M4
[5] https://bioinformaticsworkbook.org/dataAnalysis/GenomeAssembly/Iterate_Pilon_Genome_Polishing.html
[6] https://timkahlke.github.io/LongRead_tutorials/ECR_P.html
[7] https://www.ridom.de/seqsphere/ug/v40/BWA_Mapper.html
[8] https://angus.readthedocs.io/en/2013/bwa-tutorial.html
[9] https://www.biostars.org/p/90149/
[10] https://protocols.faircloth-lab.org/en/latest/protocols-computer/assembly/polishing-with-pilon.html
[11] https://genomics-fungi.sschmeier.com/ngs-mapping/index.html