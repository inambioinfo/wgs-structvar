# Whole Genome Sequencing Structural Variation Pipeline

## Quick start

```bash
# Install nextflow:
curl -fsSL get.nextflow.io | bash
mv ./nextflow ~/bin

# Set work dir to no-backup, put this in your .bashrc
export NXF_WORK=$SNIC_NOBACKUP/work

# Pull worfklow from this repo, run manta, fermikit and variant effect predictor:
nextflow run NBISweden/wgs-structvar --project <uppmax_project_id> --bam <bamfile.bam> --steps manta,normalize,vep

# Monitor log file
tail -f .nextflow.log
```

Your summary files will be in the `results` subdirectory.


## General information

This is a pipeline for running the two structural variation callers fermikit
and manta on UPPMAX.
You can choose to run either of the two structural variation callers or both
(and generate summary files).

### Masking

The pipeline will use the following mask files to remove known artifacts:

* From [cc2qe/speedseq](https://github.com/cc2qe/speedseq): https://github.com/cc2qe/speedseq/raw/master/annotations/ceph18.b37.lumpy.exclude.2014-01-15.bed
* From [lh3/varcmp](https://github.com/lh3/varcmp): https://github.com/lh3/varcmp/raw/master/scripts/LCR-hs37d5.bed.gz


## Detailed usage

### Command line options

```
Run a local copy of the wgs-structvar WF:
    nextflow main.nf --bam <bamfile> [more options]
OR run from github:
    nextflow nbisweden/wgs-structvar --bam <bamfile> [more options]

Options:
  Required
    --bam           Input bamfile
       OR
    --runfile       Input runfile for multiple bamfiles in the same run.
                    Whitespace separated, first column is bam file,
                    second column is output directory and an optional third column
                    with a run id to more easily keep track of the run (otherwise
                    it\'s autogenerated).
    --project       Uppmax project to log cluster time to
       OR
    -profile local  Run locally, only for very small datasets
  Optional
    --help          Show this message and exit
    --fastq         Input fastqfile (default is bam but with fq as fileending)
                    Used by fermikit, will be created from the bam file if
                    missing.
    --steps         Specify what steps to run, comma separated: (default: manta, vep)
                Callers: manta, fermikit
                Annotation: vep, snpeff
                Extra: normalize (with vt)
    --outdir        Directory where resultfiles are stored (default: results)
    --prefix        Prefix for result filenames (default: no prefix)
```

The log file `.nextflow.log` will be produced when running and can be monitored
by e.g. `tail -f .nextflow.log`

### SLURM usage

There are three ways to run the pipeline:
```bash
# Local run on node where one is logged in
nextflow main.nf -profile local <options>

# Called from login node, nextflow will queue SLURM jobs itself
nextflow main.nf <options>

# When running inside a SLURM job nextflow will run on the local node
srun nextflow main.nf <options>
```

### Customization

Nextflow can pull from github (master branch) so if you specify this repo it will run
what is currently in it. However if you want to customize the parameters more you will
want to clone the repo and edit the `nextflow.config` file in it.
It's probably only the `params` scope of the config file that is of interest
to customize.

The first part has the default values for the command line parameters, see the
usage message for information on them.

The next section has the reference assembly to use, both as fasta and assembly
name.

You may want to use different versions of the modules used by this workflow, and
the `modules` section contains all of them and their
versions. Customize the modules here, NOT in the `main.nf` file. 

Finally the `runtime` section has the different runtimes for the different
parts of the workflow. `fermikit` has it's own timespec since that is a very
long running program, otherwise the workflow differentiates between `callers`
and other supporting `simple` single-core jobs.


## Support

If you need help with this module, please call 1-800-NBISFIXIT-PLACEHOLDER

## External links

* [NextFlow website](http://www.nextflow.io)
* [NextFlow gitter chat](https://gitter.im/nextflow-io/nextflow)

[![Stories in Ready](https://badge.waffle.io/NBISweden/wgs-structvar.png?label=ready&title=Ready)](https://waffle.io/NBISweden/wgs-structvar)
