# Annotation_AAT
Structural genome annotation pipeline used in the CNAG AA Team for eukaryote genomes. 

It is based on Evidence-Modeler2 + PASA approach. It can annotate a genome using transcript evidence (optional but highly recommended), protein evidence (compulsory) and ab initio gene predictions. 

It needs a config file and a spec file (json file with instructions on which resources should slurm use for each of the jobs). Both files are created by the script "create_config_annotation.py" that is located in the bin directory. To check all the options accepted by the script, do:

```
bin/create_config_annotation.v2.py -h
```

The final command would look like this: 

```
bin/create_config_file.annotation.v2.py --configFile configs/annotation.config --specFile configs/annotation.spec --project-name TEST1 A --genome genome.fa --pasa-conf evidence/pipeline_inputs/alignAssembly.v2.5.2.pasa2.config --update-conf evidence/pipeline_inputs/annotCompare_v2.5.2.pasa2.config --illumina-dir Illumina_RNA/fastq/ --proteins evidence/proteins.fa --create-database --geneid-param human3isoU12.param --aug-species human --pb-dir isoseq/
```

# Tricks to obtain the different input options

1. If you do not know which **geneid parameters** to use, you can download the most appropriate ones from: https://genome.crg.es/software/geneid/index.html#parameters. Also, for Augustus species (--species option), check the available species  already trained for augustus or let the pipeline train your own).

2. Modify the annotCompare_v2.5.2.pasa2.config and alignAssembly.v2.5.2.pasa2.config config files with the name and path to tha pasa database that the pipeline will create. Examples of this files can be found under the "config_examples/" directory.

3. The project-name option is the prefix that will be assigned to all the annotated genes and the version of the annotation (eg. --project-name HSAP1 A)

Once the 2 config files are produced, the pipeline can be launched using snakemake (tested and developed for Snakemake v6.3.0) like this:

``snakemake --notemp -j 999 --snakefile annotation_AAT.v2.smk --configfile annotation.config --is --cluster-conf annotation.spec --use-conda --use-envmodules``

If you are using an HPC cluster, please check how should you run snakemake to launch the jobs to the cluster. 

Most of the tools used will be installed via conda using the environments of the "envs" directory after providing the "--use-conda" option to snakemake. However, a few tools cannot be installed via conda and will have to be available in your PATH, or as a module in the cluster. Those tools are:

- GeneMark 
- GeneID

# Description of all options
```
bin/create_config_file.annotation.v2.py -h
usage: create_configuration_file [-h] [--configFile configFile] [--specFile specFile] [--basename base_name] [--pipeline-workdir PIPELINE_WORKDIR] [--annotation-version ANNOTATION_VERSION]
                                 [--project-name project_name project_name] [--no-redmask] [--no-pasa] [--no-transdecoder] [--no-miniprot] [--no-augustus] [--no-augustus-hints] [--no-geneid]
                                 [--no-geneid-introns] [--no-genemark] [--no-genemark-ET] [--no-EVM] [--no-update] [--no-noncoding] [--no-jbrowse] [--keep-intermediate] [--scripts-dir SCRIPTS_DIR]
                                 [--genome genome] [--glen glen] [--illumina-dir ILLUMINA_DIR] [--cdna-dir CDNA_DIR] [--drna-dir DRNA_DIR] [--pb-dir PB_DIR] [--rna-samples-tsv rna_samples_tsv]
                                 [--transcripts transcripts] [--pasa-config pasa_config] [--update-config update_config] [--proteins proteins] [--genome-masked genome_masked]
                                 [--repeat-dir REPEAT_ANNOTATION_DIR] [--Rep-gff Repeat_gff] [--rna-outdir RNA_OUTDIR] [--TACO-dir TACO_dir] [--junctions junctions] [--gtf-models RNAmodels]
                                 [--dir-masked-chunks DIR_MASKED_CHUNKS] [--annot-dir ANNOTATION_BASEDIR] [--EVM-dir EVM_DIR] [--miniprot-cds MINIPROT_CDS] [--miniprot-gene MINIPROT_GENE]
                                 [--augustus-prediction AUGUSTUS_PREDICTION] [--augustus-preEVM AUGUSTUS_PREEVM] [--geneid-prediction GENEID_PREDICTION] [--geneid-preEVM GENEID_PREEVM]
                                 [--genemark-prediction GENEMARK_PREDICTION] [--genemark-preEVM GENEMARK_PREEVM] [--geneid-introns-prediction GENEID_INTRONS_PREDICTION]
                                 [--geneid-introns-preEVM GENEID_INTRONS_PREEVM] [--genemark-ET-prediction GENEMARK_ET_PREDICTION] [--genemark-ET-preEVM GENEMARK_ET_PREEVM]
                                 [--augustus-hints-prediction AUGUSTUS_HINTS_PREDICTION] [--augustus-hints-preEVM AUGUSTUS_HINTS_PREEVM] [--evm-out EVM_OUT] [--update-dir UPDATE_DIR]
                                 [--ncRNA-dir NCRNA_ANNOTATION_DIR] [--masked-chunks MASKED_CHUNKS] [--protein-chunks PROTEIN_CHUNKS] [--species-rdatabase species_rdatabase]
                                 [--repeat-library repeat_library] [--rmask-cores rmaskCores] [--red-wordlen red_wordlen] [--red-minkmer red_minkmer] [--add-red-option RED_ADD_OPTS] [--blastdb blastdb]
                                 [--blast-eval evalue] [--blast-cores blastCores] [--star-genome-dir star_genome_dir] [--no-pe] [--star-dir star_dir] [--star-cpu starCores]
                                 [--indexstar-opts indexstar_additional_options] [--star-opts star_additional_options] [--stringtie-illum-opts stringtie_illumina_opts]
                                 [--TACO-illum-opts TACO_illumina_opts] [--trim-galore-opts trim_galore_opts] [--trim-Illumina-cores Trim_Illumina_cores] [--cdna-mappings cdna_minimap_dir]
                                 [--minimap2-cdna-opts minimap2_cDNA_opts] [--stringtie-cdna-opts stringtie_cDNA_opts] [--TACO-cdna-opts TACO_cDNA_opts] [--drna-mappings drna_minimap_dir]
                                 [--minimap2-drna-opts minimap2_dRNA_opts] [--stringtie-drna-opts stringtie_dRNA_opts] [--TACO-drna-opts TACO_dRNA_opts] [--pb-mappings pb_minimap_dir]
                                 [--minimap2-pb-opts minimap2_pb_opts] [--stringtie-pb-opts stringtie_pb_opts] [--TACO-pb-opts TACO_pb_opts] [--minimap-cpu minimapCores] [--taco-cpu tacoCores]
                                 [--TACO-all-opts TACO_global_opts] [--espresso-cpu espressoCores] [--espresso-path espresso_path] [--espresso-outdir espresso_outdir]
                                 [--pasa-weights PASA_WEIGHTS [PASA_WEIGHTS ...]] [--create-database] [--aligners ALIGNERS] [--add-pasa-option ADD_OPTION] [--update-rounds UPDATE_ROUNDS]
                                 [--transdecoder-weights TRANSDECODER_WEIGHTS [TRANSDECODER_WEIGHTS ...]] [--miniprot-path MINIPROT_PATH] [--miniprot-cores MINIPROT_CORES]
                                 [--miniprot-weights MINIPROT_WEIGHTS [MINIPROT_WEIGHTS ...]] [--additional-miniprot-options ADDITIONAL_MINIPROT_OPTIONS] [--aug-species aug_species]
                                 [--aug-conf aug_config_path] [--optimize-aug-cores AUG_OPTIMIZE_THREADS] [--aug-alternatives-from-sampling {true,false}] [--aug-alternatives-from-evidence {true,false}]
                                 [--aug-uniqueGeneId {true,false}] [--aug-gff3 {ON,OFF,on,off}] [--aug-sample AUG_SAMPLE] [--aug-noInFrameStop {true,false}] [--aug-maxtracks AUG_MAXTRACKS]
                                 [--aug-singlestrand {true,false}] [--aug-strand {both,forward,backward}] [--aug-min-intron-len AUG_MIN_INTRON_LEN]
                                 [--augustus-weights AUGUSTUS_WEIGHTS [AUGUSTUS_WEIGHTS ...]] [--additional-augustus-options ADDITIONAL_AUGUSTUS_OPTIONS]
                                 [--extrinsic-file-augustus-hints extrinsic_file_augustus_hints] [--augustus-hints-weights AUGUSTUS_HINTS_WEIGHTS [AUGUSTUS_HINTS_WEIGHTS ...]]
                                 [--additional-augustus-hints-options ADDITIONAL_AUGUSTUS_HINTS_OPTIONS] [--geneid-path GENEID_PATH] [--geneid-weights GENEID_WEIGHTS [GENEID_WEIGHTS ...]]
                                 [--geneid-options GENEID_OPTIONS] [--geneid-parameters geneid_parameters] [--geneid-introns-weights GENEID_INTRONS_WEIGHTS [GENEID_INTRONS_WEIGHTS ...]]
                                 [--geneid-introns-options GENEID_INTRONS_OPTIONS] [--gmk-min-contig GMK_MIN_CONTIG] [--gmk-max-contig GMK_MAX_CONTIG] [--gmk-max-gap GMK_MAX_GAP] [--gmk-cores GMK_CORES]
                                 [--additional-genemark-options ADDITIONAL_GENEMARK_OPTIONS] [--genemark-weights GENEMARK_WEIGHTS [GENEMARK_WEIGHTS ...]]
                                 [--additional-genemark-ET-options ADDITIONAL_GENEMARK_ET_OPTIONS] [--genemark-ET-weights GENEMARK_ET_WEIGHTS [GENEMARK_ET_WEIGHTS ...]] [--evm-path EVM_PATH]
                                 [--evm-segmentsize EVM_SEGMENTSIZE] [--evm-overlapsize EVM_OVERLAPSIZE] [--evm-cores EVM_CORES] [--additional-evm-options ADDITIONAL_EVM_OPTIONS]
                                 [--ncRNA-version NCRNA_VERSION] [--Rfam RFAM] [--cmsearch-CPUs CMSEARCH_CPUS] [--illumina-reads illumina_fastqs] [--cdna-reads cDNA_fastqs] [--drna-reads dRNA_fastqs]
                                 [--pb-reads pb_fastqs] [--pb-reads-fasta pb_fastas]

Create a configuration json file for the repeat annotation pipeline.

options:
  -h, --help            show this help message and exit

General Parameters:
  --configFile configFile
                        Configuration file with the pipeline parameters to be created. Default Annotation.config
  --specFile specFile   Cluster specifications JSON fileto be generated. Default Annotation.spec
  --basename base_name  Assembly basename. Default None
  --pipeline-workdir PIPELINE_WORKDIR
                        Base directory for the pipeline run. Default /scratch_isilon/groups/assembly/jgomez/Annotation_AAT_pipeline/
  --annotation-version ANNOTATION_VERSION
                        Version of the annotation process. Default 01
  --project-name project_name project_name
                        Name of the project and version of the annotation space separated, to give the names to the final annotation output.
  --no-redmask          If specified, do not run redmask step.
  --no-pasa             If specified, do not run pasa step.
  --no-transdecoder     If specified, do not run transdecoder step.
  --no-miniprot         If specified, do not run miniprot step.
  --no-augustus         If specified, do not run augustus step.
  --no-augustus-hints   If specified, do not run augustus with hints step.
  --no-geneid           If specified, do not run geneid step.
  --no-geneid-introns   If specified, do not run geneid with introns.
  --no-genemark         If specified, do not run genemark step.
  --no-genemark-ET      If specified, do not run genemark-ET step.
  --no-EVM              If specified, do not run EVM step.
  --no-update           If specified, do not run the annotation update step.
  --no-noncoding        If specified, do not run the non_coding annotation step.
  --no-jbrowse          If specified, do not run the get jbrowse tracks step.
  --keep-intermediate   If specified, do not delete intermediate files.

Inputs:
  --scripts-dir SCRIPTS_DIR
                        Directory with the different scripts for the pipeline. Default /software/assembly/pipelines/Annotation_AAT_fussion/Annotation_AAT/bin/../scripts/
  --genome genome       Path to the genome assembly in fasta format.
  --glen glen           Path to the assembly.genome file.
  --illumina-dir ILLUMINA_DIR
                        Directory where the illumina fastqs are stored. Default None
  --cdna-dir CDNA_DIR   Directory where the cDNA fastqs are stored. Default None
  --drna-dir DRNA_DIR   Directory where the dRNA fastqs are stored. Default None
  --pb-dir PB_DIR       Directory where the pacbio isoseq fastqs are stored. Default None
  --rna-samples-tsv rna_samples_tsv
                        TSV file describing the samples to use with ESPRESSO. Default samples.tsv
  --transcripts transcripts
                        Path to the fasta with transcript evidence.
  --pasa-config pasa_config
                        Path to the pasa configuration file.
  --update-config update_config
                        Path to the pasa update configuration file.
  --proteins proteins   Path to the fasta with protein evidence.

Outputs:
  --genome-masked genome_masked
                        Path to the masked genome assembly in fasta format.
  --repeat-dir REPEAT_ANNOTATION_DIR
                        Directory to the repeat annotation on. Default step01_Repeat_Annotation.V01/
  --Rep-gff Repeat_gff  Path to the Repeat Annotation gff output.
  --rna-outdir RNA_OUTDIR
                        Directory to the RNA processing step on. Default step02_RNAprocessing.V01/
  --TACO-dir TACO_dir   Directory to run the final TACO step. Default step02_RNAprocessing.V01/
  --junctions junctions
                        Path to the final junctions file. Default alljunctions.final.gff3
  --gtf-models RNAmodels
                        Path to the final TACO gtf. Default TACO_assembled.gtf
  --dir-masked-chunks DIR_MASKED_CHUNKS
                        Directory to keep the chunks of the masked genome. Default chunks/masked_reference/
  --annot-dir ANNOTATION_BASEDIR
                        Directory to keep the base annotation steps. Default step03_annotation_pipeline.V01/
  --EVM-dir EVM_DIR     Directory to keep the files for the EVM step. Default step04_EVM.V01
  --miniprot-cds MINIPROT_CDS
                        Output file for the miniprot output in a cds gff3 format. Default step03_annotation_pipeline.V01//protein_and_transcript_mappings/miniprot/proteins_miniprot_cds.gff3
  --miniprot-gene MINIPROT_GENE
                        Output file for the miniprot output in a gene gff3 format. Default step03_annotation_pipeline.V01//protein_and_transcript_mappings/miniprot/proteins_miniprot_gene.gff3
  --augustus-prediction AUGUSTUS_PREDICTION
                        Output file for the augustus predictions. Default step03_annotation_pipeline.V01//gene_predictions/augustus/augustus_gene_prediction.gff3
  --augustus-preEVM AUGUSTUS_PREEVM
                        Output file for the augustus predictions converted for EVM. Default step03_annotation_pipeline.V01//gene_predictions/augustus/augustus_preEVM.gff3
  --geneid-prediction GENEID_PREDICTION
                        Output file for the geneid predictions. Default step03_annotation_pipeline.V01//gene_predictions/geneid_gene_prediction.gff3
  --geneid-preEVM GENEID_PREEVM
                        Output file for the geneid predictions converted for EVM. Default step03_annotation_pipeline.V01//gene_predictions/geneid_preEVM.gff3
  --genemark-prediction GENEMARK_PREDICTION
                        Output file for the genemark predictions. Default step03_annotation_pipeline.V01//gene_predictions/genemark.gtf
  --genemark-preEVM GENEMARK_PREEVM
                        Output file for the genemark predictions converted for EVM. Default step03_annotation_pipeline.V01//gene_predictions/genemark_preEVM.gff3
  --geneid-introns-prediction GENEID_INTRONS_PREDICTION
                        Output file for the geneid with introns predictions. Default step03_annotation_pipeline.V01//gene_predictions/geneid_introns_gene_prediction.gff3
  --geneid-introns-preEVM GENEID_INTRONS_PREEVM
                        Output file for the geneid with introns predictions converted for EVM. Default step03_annotation_pipeline.V01//gene_predictions/geneid_introns_preEVM.gff3
  --genemark-ET-prediction GENEMARK_ET_PREDICTION
                        Output file for the genemark-ET predictions. Default step03_annotation_pipeline.V01//gene_predictions/genemark-ET.gtf
  --genemark-ET-preEVM GENEMARK_ET_PREEVM
                        Output file for the genemark-ET predictions converted for EVM. Default step03_annotation_pipeline.V01//gene_predictions/genemark-ET_preEVM.gff3
  --augustus-hints-prediction AUGUSTUS_HINTS_PREDICTION
                        Output file for the augustus with hints predictions. Default step03_annotation_pipeline.V01//gene_predictions/augustus_hints/augustus_hints_gene_prediction.gff3
  --augustus-hints-preEVM AUGUSTUS_HINTS_PREEVM
                        Output file for the augustus with hints predictions converted for EVM. Default step03_annotation_pipeline.V01//gene_predictions/augustus_hints/augustus_hints_preEVM.gff3
  --evm-out EVM_OUT     File with the final EVM models. Default step04_EVM.V01/evm.best.gff3
  --update-dir UPDATE_DIR
                        Directory to keep the files for annotation update step. Default step05_annotation_update.V01/
  --ncRNA-dir NCRNA_ANNOTATION_DIR
                        Directory to keep the files of the ncRNA annotation step. Default step06_ncRNA_annotation.V01/

Chunks:
  --masked-chunks MASKED_CHUNKS
                        Number of chunks of the masked genome for parallelizing some gene predictors run. Default 23
  --protein-chunks PROTEIN_CHUNKS
                        Number of chunks to split the protein files for running blast and classify the lncRNAs. Default 20

Repeat Annotation:
  --species-rdatabase species_rdatabase
                        Existant database to run a first time Repeat Masker. Default None
  --repeat-library repeat_library
                        fasta file containing a pre-existant library of repeats.
  --rmask-cores rmaskCores
                        Default 8

RedMask:
  --red-wordlen red_wordlen
                        Redmask wordlen parameter. Default 15
  --red-minkmer red_minkmer
                        Redmask minkmer parameter. Default 3
  --add-red-option RED_ADD_OPTS
                        Option given to add extra options to RedMask

BLAST:
  --blastdb blastdb     Blast database to check presence of protein families in RedMask library. Default /scratch_isilon/groups/assembly/data/databases/scratch_blast_db/swissprot
  --blast-eval evalue   Evalue to filter blast hits. Default 1e-06
  --blast-cores blastCores
                        Default 24

Illumina RNA:
  --star-genome-dir star_genome_dir
                        Directory for the star genome index. Default step02_RNAprocessing.V01/star_genome
  --no-pe               If specified, the input is not paired-end.
  --star-dir star_dir   Directory for mapping the illumina reads. Default step02_RNAprocessing.V01/star
  --star-cpu starCores  Number of threads to run star. Default 4
  --indexstar-opts indexstar_additional_options
                        Additional options to run star index with. Default
  --star-opts star_additional_options
                        Additional options to run star with. Default
  --stringtie-illum-opts stringtie_illumina_opts
                        Options to run stringtie in illumina mappings. Default
  --TACO-illum-opts TACO_illumina_opts
                        Options to run TACO in illumina mappings. Default

Trim_Galore:
  --trim-galore-opts trim_galore_opts
                        Optional parameters for the rule trim_galore. Default --max_n 0 --gzip -q 20 --paired --retain_unpaired
  --trim-Illumina-cores Trim_Illumina_cores
                        Number of threads to run the Illumina trimming step. Default 4

cDNA RNA:
  --cdna-mappings cdna_minimap_dir
                        Directory for the cDNA Minimap2 mappings. Default step02_RNAprocessing.V01/cDNA
  --minimap2-cdna-opts minimap2_cDNA_opts
                        Options to run minimap2 in cDNA mappings. Default
  --stringtie-cdna-opts stringtie_cDNA_opts
                        Options to run stringtie in cDNA mappings. Default --conservative -R
  --TACO-cdna-opts TACO_cDNA_opts
                        Options to run TACO in cDNA mappings. Default --isoform-frac 0.01

dRNA RNA:
  --drna-mappings drna_minimap_dir
                        Directory for the dRNA Minimap2 mappings. Default step02_RNAprocessing.V01/dRNA
  --minimap2-drna-opts minimap2_dRNA_opts
                        Options to run minimap2 in dRNA mappings. Default -uf -k14
  --stringtie-drna-opts stringtie_dRNA_opts
                        Options to run stringtie in dRNA mappings. Default
  --TACO-drna-opts TACO_dRNA_opts
                        Options to run TACO in dRNA mappings. Default --isoform-frac 0.01 --filter-min-expr 0.2

Isoseq:
  --pb-mappings pb_minimap_dir
                        Directory for the Pacbio Isoseq Minimap2 mappings. Default isoseq
  --minimap2-pb-opts minimap2_pb_opts
                        Options to run minimap2 in Pacbio Isoseq mappings. Default :hq -uf
  --stringtie-pb-opts stringtie_pb_opts
                        Options to run stringtie in Pacbio Isoseq mappings. Default --conservative -R
  --TACO-pb-opts TACO_pb_opts
                        Options to run TACO in isoseq mappings. Default --isoform-frac 0.01

Model RNA:
  --minimap-cpu minimapCores
                        Number of threads to run Minimap2. Default 8
  --taco-cpu tacoCores  Number of threads to run TACO. Default 4
  --TACO-all-opts TACO_global_opts
                        Options to run TACO when merging all the datasets. Default --isoform-frac 0 --filter-min-expr 0
  --espresso-cpu espressoCores
                        Number of threads to run espresso. Default 4
  --espresso-path espresso_path
                        Path to the ESPRESSO scripts. Default /software/assembly/src/ESPRESSO/espresso_v_1_3_0_beta/src/
  --espresso-outdir espresso_outdir
                        Directory for runnning ESPRESSO. Default ESPRESSO_out/

Pasa parameters:
  --pasa-weights PASA_WEIGHTS [PASA_WEIGHTS ...]
                        Weights given to pasa mappings when running EVM. Specify the weight for each EVM run separated by a space. Example 8 10 8
  --create-database     If specified, create pasa database.
  --aligners ALIGNERS   Program to map the transcripts with.
  --add-pasa-option ADD_OPTION
                        Option given to add extra options to PASA
  --update-rounds UPDATE_ROUNDS
                        Number of rounds to run PASA updates. Default 2

Transdecoder parameters:
  --transdecoder-weights TRANSDECODER_WEIGHTS [TRANSDECODER_WEIGHTS ...]
                        Weights given to pasa transdecodergff3 output file when running EVM. Specify the weight for each EVM run separated by a space. Example 3 2 3

Miniprot parameters:
  --miniprot-path MINIPROT_PATH
                        Path to Miniprot installation. Default /software/assembly/src/miniprot/miniprot/
  --miniprot-cores MINIPROT_CORES
                        Number of threads. Default 8
  --miniprot-weights MINIPROT_WEIGHTS [MINIPROT_WEIGHTS ...]
                        Weights given to miniprot mappings when running EVM. Specify the weight for each EVM run separated by a space. Example 10 8 10
  --additional-miniprot-options ADDITIONAL_MINIPROT_OPTIONS
                        Additional miniprot options to run it, see miniprot help for more information about the possible options.

Augustus parameters:
  --aug-species aug_species
                        Species name to run augustus with its trained parameters. For augustus and augustus with hints steps.
  --aug-conf aug_config_path
                        Path to the augustus config path. Default /software/assembly/conda/augustus3.5.0/config/
  --optimize-aug-cores AUG_OPTIMIZE_THREADS
                        Number of threads to run the parameter optimization during the augustus training step. Default 24
  --aug-alternatives-from-sampling {true,false}
                        Report alternative transcripts generated through probabilistic sampling. Default true
  --aug-alternatives-from-evidence {true,false}
                        Report alternative transcripts when they are suggested by hints. Default true
  --aug-uniqueGeneId {true,false}
                        If true, output gene identifyers like this: seqname.gN. For augustus and augustus with hints. Default true
  --aug-gff3 {ON,OFF,on,off}
                        Output in gff3 format. For augustus and augustus with hints. Default on
  --aug-sample AUG_SAMPLE
                        For augustus and augustus with hints. Default 60
  --aug-noInFrameStop {true,false}
                        Do not report transcripts with in-frame stop codons. Otherwise, intron-spanning stop codons could occur. For augustus and augustus with hints. Default true
  --aug-maxtracks AUG_MAXTRACKS
                        Maximum number of tracks allowed. For augustus and augustus with hints. Default 2
  --aug-singlestrand {true,false}
                        Predict genes independently on each strand, allow overlapping genes on opposite strands. For augustus and augustus with hints. Default false
  --aug-strand {both,forward,backward}
                        For augustus and augustus with hints. Default both
  --aug-min-intron-len AUG_MIN_INTRON_LEN
                        Minimum predicted intron length. For augustus and augustus with hints. Default 30
  --augustus-weights AUGUSTUS_WEIGHTS [AUGUSTUS_WEIGHTS ...]
                        Weights given to augustus predictions when running EVM. Specify the weight for each EVM run separated by a space. Example 2 2 1
  --additional-augustus-options ADDITIONAL_AUGUSTUS_OPTIONS
                        Additional augustus options to run it, see augustus help for more information about the possible options.

Augustus hints parameters:
  --extrinsic-file-augustus-hints extrinsic_file_augustus_hints
                        Path to the Extrinsic file to use when running augustus with hints. Default /software/assembly/conda/augustus3.5.0/config/extrinsic/extrinsic.E.cfg
  --augustus-hints-weights AUGUSTUS_HINTS_WEIGHTS [AUGUSTUS_HINTS_WEIGHTS ...]
                        Weights given to augustus with intron predictions when running EVM. Specify the weight for each EVM run separated by a space. Example 3 3 3
  --additional-augustus-hints-options ADDITIONAL_AUGUSTUS_HINTS_OPTIONS
                        Desired augustus with intron options to run it, see augustus documentation for more information.

Geneid parameters:
  --geneid-path GENEID_PATH
                        Path to the installation of geneid. Default /software/assembly/src/geneid/
  --geneid-weights GENEID_WEIGHTS [GENEID_WEIGHTS ...]
                        Weights given to geneid predictions when running EVM. Specify the weight for each EVM run separated by a space. Example 2 1 2
  --geneid-options GENEID_OPTIONS
                        Desired geneid options to run it, see geneid documentation for more information. Default -3U
  --geneid-parameters geneid_parameters
                        Path to the geneid parameters file. For geneid, geneid with introns and framefixing (part of annotation update) steps.

Geneid Introns parameters:
  --geneid-introns-weights GENEID_INTRONS_WEIGHTS [GENEID_INTRONS_WEIGHTS ...]
                        Weights given to geneid with intron predictions when running EVM. Specify the weight for each EVM run separated by a space. Example 3 3 3
  --geneid-introns-options GENEID_INTRONS_OPTIONS
                        Desired geneid with intron options to run it, see geneid documentation for more information. Default -3U

Genemark parameters:
  --gmk-min-contig GMK_MIN_CONTIG
                        Will ignore contigs shorter then min_contig in training. Default 50000
  --gmk-max-contig GMK_MAX_CONTIG
                        Will split input genomic sequence into contigs shorter than max_contig. Default 5000000
  --gmk-max-gap GMK_MAX_GAP
                        Will split sequence at gaps longer than max_gap. Letters 'n' and 'N' are interpreted as standing within gaps. Default 5000
  --gmk-cores GMK_CORES
                        Number of threads for running genemark. Default 24
  --additional-genemark-options ADDITIONAL_GENEMARK_OPTIONS
                        Additional genemark options to run it, see genemark documentation for more information.
  --genemark-weights GENEMARK_WEIGHTS [GENEMARK_WEIGHTS ...]
                        Weights given to genemark predictions when running EVM. Specify the weight for each EVM run separated by a space. Example 1 1 1

Genemark-ET parameters:
  --additional-genemark-ET-options ADDITIONAL_GENEMARK_ET_OPTIONS
                        Additional genemark-ET options to run it, see genemark documentation for more information.
  --genemark-ET-weights GENEMARK_ET_WEIGHTS [GENEMARK_ET_WEIGHTS ...]
                        Weights given to genemark-ET predictions when running EVM. Specify the weight for each EVM run separated by a space. Example 3 3 3

Evm parameters:
  --evm-path EVM_PATH   Path to the EVM software installation. Default /software/assembly/src/EVM2.1.0/EVidenceModeler-v2.1.0/
  --evm-segmentsize EVM_SEGMENTSIZE
                        Size of the genome partitions for EVM. Default 2000000
  --evm-overlapsize EVM_OVERLAPSIZE
                        Size of the overlap between the different EVM partitions. Default 1000000
  --evm-cores EVM_CORES
                        Number of threads to run EVM. Default 24
  --additional-evm-options ADDITIONAL_EVM_OPTIONS
                        Additional evm options to run it, see evm help for more information about the possible options.

ncRNA Annotation parameters:
  --ncRNA-version NCRNA_VERSION
                        Version for the ncRNA annotation. Default A
  --Rfam RFAM           CM file with the Rfam library. Default /scratch_isilon/groups/assembly/data/databases/RFAM/Rfam.cm
  --cmsearch-CPUs CMSEARCH_CPUS
                        Number of CPUs to run cmsearch Default 32

Wildcards:
  --illumina-reads illumina_fastqs
                        List with basename of the illumina fastqs. Default None
  --cdna-reads cDNA_fastqs
                        List with basename of the cDNA fastqs. Default None
  --drna-reads dRNA_fastqs
                        List with basename of the dRNA fastqs. Default None
  --pb-reads pb_fastqs  List with basename of the Pacbio Isoseq fastqs. Default None
  --pb-reads-fasta pb_fastas
                        List with basename of the Pacbio Isoseq fastas. Default None
```
