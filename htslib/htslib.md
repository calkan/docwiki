# Prerequisites

HTSlib requires:

 * libbz2 (development libraries; http://www.bzip.org/)
	* Ubuntu example:
	sudo apt-get install libbz2-dev

 * liblzma (development libraries; https://tukaani.org/xz/)
	* Ubuntu example:
	sudo apt-get install liblzma-dev


# Local set up

1. Fetch the latest version of HTSlib from: https://github.com/samtools/htslib
2. Place htslib somewhere that is accessible by your repo

# Git submodule set up

Under your working directory:

`git submodule add https://github.com/samtools/htslib`

# Makefile

You can use TARDIS Makefile as a guide: https://github.com/BilkentCompGen/tardis/blob/master/Makefile

You will need to add HTSlib object code to LDFLAGS together with its own dependencies:

`LDFLAGS = htslib/libhts.a -lz -lpthread -llzma -lbz2`

Obviously make sure that LDFLAGS is in your compile command:

`$(CC) $(OBJECTS) -o $@ $(LDFLAGS)`

Also make sure that HTSlib is compiled before your own code:

```
libs:
        make -C htslib
```

# Header files

You will need to include the following:

```
#include <htslib/sam.h>
#include <htslib/hts.h>
#include <zlib.h>
#include <htslib/faidx.h>
```

# Compulsory data structures & variables:

You will need the following:

```
htsFile* bam_file; /* file pointer to the SAM/BAM/CRAM file */
hts_idx_t* bam_file_index; /* pointer to the BAM/CRAM index */
hts_itr_t *iter; /* iterator to read and parse the lines in SAM/BAM/CRAM file*/
bam_hdr_t* bam_header; /* pointer to the header information of the BAM/CRAM file */
bam1_core_t bam_alignment_core; /*  Structure for core alignment information */
bam1_t* bam_alignment; /* Structure for one alignment */
```

# The following variables are assumed to be initialized in the rest of these examples

```
char *path; /* input SAM/BAM/CRAM file name */
char *fai_file; /* file name for the reference genome FAI index as generated using, 
                   e.g., samtools faidx ref.fa */
```

# Initialization

```
bam_file = hts_open( path, "r"); /* open SAM/BAM/CRAM file. htslib automatically detects the format of the file. Returns NULL if there is an error. */
bam_header = sam_hdr_read(bam_file); /* Read in BAM header information */ 
hts_set_fai_filename( bam_file, fai_file); /* pass the reference index to enable reading CRAM files */
```

# Information to be read from the header

1. [Reading the sample name using HTSlib](https://github.com/calkan/docwiki/blob/main/htslib/readsamplename.md)
2. [Loading library information using HTSlib](https://github.com/calkan/docwiki/blob/main/htslib/loadlibraryinfo.md)

# Reading alignment lines

Initialize reading pointer:

```
    bam_alignment = bam_init1();
```

Reading one line from the SAM/BAM/CRAM:

```
    int return_value = sam_read1( bam_file, bam_header, bam_alignment);
    /* return >= 0 on successfully reading a new record, -1 on end of stream, < -1 on error */
```

Reading the entire file (or some of it depending on your loop):

```
    while (return_value != -1)
         return_value = sam_read1( bam_file, bam_header, bam_alignment);
```

[Parsing alignment information using HTSlib](https://github.com/calkan/docwiki/blob/main/htslib/parsealignment.md)

[Searching & iterating through mapping intervals](https://github.com/calkan/docwiki/blob/main/htslib/searchiterate.md)
