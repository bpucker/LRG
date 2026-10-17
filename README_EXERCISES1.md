# Bachelor Exercises

## Connect to VM
```
ssh -i /your/private/key ubuntu@YOUR_IP_GOES_HERE -p YOUR_PORT_GOES_HERE
```

## Navigate to folder
```
cd /vol/data/LRG
```

## Calculate FASTQ file statistics
```
python3 /vol/data/LRG/FASTQ_stats3.py \
--in /vol/data/LRG/ERR3712254.fastq.gz
```

## User name
Every participant needs to create a subfolder and store all their result files in this subfolder. Please replace "USER1" in the following command by your name. Your name must not contain any spaces or special characters.
```
mkdir /vol/data/LRG/USER1
```


## Conduct long-read mapping
```
minimap2 -t 10 \
-a /vol/data/LRG/TAIR10.fa \
/vol/data/LRG/ERR3712254.fastq.gz \
> /vol/data/LRG/USER1/mapped_reads.sam
```

## Convert SAM to BAM and sort by genomic position
```
samtools view -b /vol/data/LRG/USER1/mapped_reads.sam | samtools sort -o /vol/data/LRG/USER1/mapped_reads.sorted.bam
```

## Index BAM file
```
samtools index /vol/data/LRG/USER1/mapped_reads.sorted.bam
```

## DOWNLOAD RESULTS FROM VM OR VIA SCIEBO
```
rsync --append-verify -avzhP \
-e "ssh -i /homes/bpucker/.ssh/cloud_id -p VM_PORT_GOES_HERE" \
ubuntu@VM_IP_GOES_HERE:/vol/data/LRG/USER1/mapped_reads.sorted.bam \
/your/local/folder/LRG/
```

```
rsync --append-verify -avzhP \
-e "ssh -i /homes/bpucker/.ssh/cloud_id -p VM_PORT_GOES_HERE" \
ubuntu@VM_IP_GOES_HERE:/vol/data/LRG/USER1/mapped_reads.sorted.bai \
/your/local/folder/LRG/
```

```
rsync --append-verify -avzhP \
-e "ssh -i /homes/bpucker/.ssh/cloud_id -p VM_PORT_GOES_HERE" \
ubuntu@VM_IP_GOES_HERE:/vol/data/LRG/USER1/TAIR10.fa \
/your/local/folder/LRG/
```

```
rsync --append-verify -avzhP \
-e "ssh -i /homes/bpucker/.ssh/cloud_id -p VM_PORT_GOES_HERE" \
ubuntu@VM_IP_GOES_HERE:/vol/data/LRG/USER1/araport11.gff \
/your/local/folder/LRG/
```


# Additional Master Exercises

## Navigate to user folder
```
cd /vol/data/LRG/USER1
```

## Run Shasta assembly
```
shasta \
--input /vol/data/LRG/ERR3712254.fastq \
--assemblyDirectory shasta_R9_assembly \
--config Nanopore-OldGuppy-Sep2020 \
--threads 16 \
> /vol/data/LRG/USER1/ERR3712254.assembly.doc.txt &
```

## Calculate assembly statistics
```
python3 /vol/data/LRG/contig_stats3.py \
--in /vol/data/LRG/USER1/shasta_R9_assembly/Assembly.fasta
```

## Check assembly statistics
```
cat /vol/data/LRG/USER1/shasta_R9_assembly/Assembly.fasta_stats.txt
```

## Check assembly completeness with BUSCO
```
sudo docker run --rm --user root -v /vol/data/:/vol/data/ ezlabgva/busco:v6.1.0_cv2 \
busco -i /vol/data/LRG/USER1/shasta_R9_assembly/Assembly.fasta \
-l embryophyta_odb12.2 \
-c 28 \
--out_path /vol/data/LRG/USER1/Assembly.busco/ \
-o Assembly.busco \
-m genome \
-f \
> /vol/data/LRG/USER1/Assembly.busco.txt &
```

## Check BUSCO results
```
cat /vol/data/LRG/USER1/Assembly.busco.txt
```


