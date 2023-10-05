# Setup

There are different ways of installing blast. The easiest one in my view is to do it via conda. First, you need to [install conda](https://conda.io/projects/conda/en/latest/user-guide/install/index.html) on your computer. Then, you can create an environment as follows:

```
conda create -n blast_env -c conda-forge -c bioconda blast
```

The environment name is arbitrary `blast_env`. After it is installed, you can activate the environment via:

```
conda activate blast_env
```

There are different flavours of blast that answer different questions. If you are not familiar with blastp, blastn, tblastn, blastx, psiblast, rpsblast, rpstblastn and tblastx you can use blast documentation or google to learn them. In this tutorial, we are going to perform a demo via blastp which searched a database of proteins to find the best hit for a protein query.

# Make a local database

If you are not going to use a publicly available database, you have to prepare your database locally. In order to do that, you need to have all protein sequences of interest (for your organism or organisms) in one fasta file. Then, you can use the following command:

```
makeblastdb -in FASTA_FILENAME.fa -blastdb_version 5 -dbtype prot -title "my_local_protein_database"
```

If you have more than one fasta file to create your database, you have to first concatenate all sequences into one file. I put 4 proteome sequences for our demo into `database_fasta_input` folder.  You can do it like this:

```
cat database_fasta_input/*.fasta >> input_database.fa
```

Now, we have a new fasta file that contains all needed sequences. We create the database:
```
makeblastdb -in input_database.fa -blastdb_version 5 -dbtype prot -title "my_local_protein_database"
```

Input file name `input_database.fa` and title `my_local_protein_database` are arbitrary. After this task is done, there will be more files in your directory with different extensions and same filename as your input file.

If you want to perform a build a database based on nucleotide you should change `-dbtype` to `nucl`. For further options please use the help function as follows.

```
makeblastdb -h
```

# Perform the search

Now, that we have a blast database, we can perform a sequence similarity search. Let's say we keep all of our query sequences in a file called `my_query.fasta` (this name is arbitrary). Then, you can perform a blastp via:

```
blastp -db input_database.fa -query my_query.fasta -outfmt 6 -evalue 1e-8 -max_target_seqs 100 -out blastp_my_query.outfmt6
```

Output file name is arbitrary (`blastp_my_query.outfmt6`), and you can also change the other variables if you know what you are doing. Here, we set multiple optional settings. If you want to know more about them, please read the help section:

```
blastp -h
```

Now, the output file `blastp_my_query.outfmt6` contains the results of our sequence similarity search. You can open this file via a text editor. The first column is the ID of a query and the second column is the hit. For each sequence there could be more than one hit with specified criteria. You can search for best blast hit (but we do not need that for now). The columns represent:

```
qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore
```

Read [this website](https://www.metagenomics.wiki/tools/blast/blastn-output-format-6) or [blast documentation](https://www.ncbi.nlm.nih.gov/books/NBK279684/table/appendices.T.options_common_to_all_blast/) for more details.

## Best blast hit

You can use this command to get the best blast hit:

```
awk: awk '!x[$1]++' blastp_my_query.outfmt6
```

I learned it from a website that is not available anymore but this is the archive page:

```
https://web.archive.org/web/20210515150234/https://nbis.se/blog/2018-02-22-nylander-awkblast.html
```

# Online database

You can download and build databases from NCBI, UNIPROT, and any other website of your choice. Please refer to their documentation for more information.

# References

In this demo, I used four proteome sequences which are downloaded and I like to cite them at the end:
1. Sekimoto, H. et al. A divergent RWP‐RK transcription factor determines mating type in heterothallic Closterium. N. Phytol. https://doi.org/10.1111/nph.18662 (2023).
2. Dadras, Armin, et al. "Environmental gradients reveal stress hubs pre-dating plant terrestrialization." Nature Plants (2023): 1-20.
3. Feng, Xuehuan, et al. "Chromosome-level genomes of multicellular algal sisters to land plants illuminate signaling network evolution." bioRxiv (2023): 2023-01.
4. Cheng, S. et al. Genomes of subaerial Zygnematophyceae provide insights into land plant evolution. Cell 179, 1057–1067.e14 (2019).
