# HTSlib Quick start Guide

This is a self-derived HTSlib guide. I came up with these use cases mainly by looking at the sam.h file, and trying to implement something. There may be errors. Currently this guide is only for reading SAM/BAM/CRAM files, no functions regarding the SAM/BAM/CRAM file creation is yet written. Also these include support to CRAM format, and HTSlib can autodetect BAM vs CRAM.

# Disclaimers

* The documentation provided here is unofficial.
* This guide does not necessarily represent best practices:
  - Certified ["It works on my machine"](https://blog.codinghorror.com/the-works-on-my-machine-certification-program/)
* Provided code snippets are likely not optimal, but they do the job.
  - Yes, [it works on my machine](https://blog.codinghorror.com/the-works-on-my-machine-certification-program/)
* HTSlib autodetects SAM vs BAM vs CRAM formats. Although it does not require a reference genome to process SAM and BAM formats, it obviously needs the reference to work on CRAM. All examples given in this documentation work on all three formats (autodetected). The following lines in the [starting page](https://github.com/calkan/docwiki/blob/main/htslib/htslib.md) are optional for SAM/BAM, necessary for CRAM. It does not hurt to have them implemented for SAM/BAM anyway, so I'd recommend keeping them:
```
char *fai_file; /* file name for the reference genome FAI index */
hts_set_fai_filename( bam_file, fai_file); /* pass the reference index to enable reading CRAM files */
```

# The documentation
Start from [here](https://github.com/calkan/docwiki/blob/main/htslib/htslib.md)
