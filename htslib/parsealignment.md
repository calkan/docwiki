These operations work on bam_alignment data structure that is loaded using the sam_read1() function as shown in [HTSlib quick start guide](https://github.com/calkan/docwiki/blob/main/htslib/htslib.md).

First, you need the extract the alignment core:

```
    bam_alignment_core = bam_alignment->core;
```

The alignment core has most of the necessary information:

```
    typedef struct {
        int32_t tid; /* chromosome ID, defined by bam_hdr_t. Starts with 0. */
        int32_t pos; /* 0-based leftmost coordinate */
        uint16_t bin; /* bin calculated by bam_reg2bin() */
        uint8_t qual; /* mapping quality */
        uint8_t l_qname; /* length of the query name */
        uint16_t flag; /* bitwise flag */
        uint8_t unused1; /* unused field*/ 
        uint8_t l_extranul; /* length of extra NULs between qname & cigar (for alignment) */ 
        uint32_t n_cigar; /* number of CIGAR operations */
        int32_t l_qseq; /* length of the query sequence (read) */
        int32_t mtid; /* chromosome ID of next read in template, defined by bam_hdr_t */
        int32_t mpos; /* 0-based leftmost coordinate of next read in template */ 
        int32_t isize; /* insert size of the read pair */
    } bam1_core_t;
```


E.g., fetching insert size / fragment size / pair span:

```
   bam_alignment_core.isize  /* this is a simple int data type */
```

The following values are predefined in sam.h:
```
/* the read is paired in sequencing, no matter whether it is mapped in a pair */
#define BAM_FPAIRED        1
/* the read is mapped in a proper pair */
#define BAM_FPROPER_PAIR   2
/* the read itself is unmapped; conflictive with BAM_FPROPER_PAIR */
#define BAM_FUNMAP         4
/* the mate is unmapped */
#define BAM_FMUNMAP        8
/* the read is mapped to the reverse strand */
#define BAM_FREVERSE      16
/* the mate is mapped to the reverse strand */
#define BAM_FMREVERSE     32
/* this is read1 */
#define BAM_FREAD1        64
/* this is read2 */
#define BAM_FREAD2       128
/* not primary alignment */
#define BAM_FSECONDARY   256
/* QC failure */
#define BAM_FQCFAIL      512
/* optical or PCR duplicate */
#define BAM_FDUP        1024
/* supplementary alignment */
#define BAM_FSUPPLEMENTARY 2048
```

For example, to check if this is read1:

```
     if (bam_alignment_core.flag & BAM_FREAD1 != 0)
        printf("this is read1 in the pair.\n");
```

Some macros:

1. Checking mapping strand
* Is the read reversed?:
```
   int is_reverse = bam_is_rev( bam_alignment); 
   /* boolean true if this read is mapped to the reverse strand */
```
* Is the mate of the read reversed?
```
   int is_mate_reverse = bam_is_mrev( bam_alignment); 
   /* boolean true if the mate of this read is mapped to the reverse strand */
```

2. Fetching auxiliary information:

Function `bam_aux_get` returns a uint8_t * (~ char*). This applies to any auxiliary information tags, examples follow:

* Read group
```
    bam_aux_get( bam_alignment, "RG"));  /* returns a char * */
```
* MD field
```
    bam_aux_get( bam_alignment, "MD"));  /* returns a char * */
```

3. Fetching read name
```
    bam_get_qname( bam_alignment); /* returns a char * */
```

4. Fetching the read sequence
```
    bam_get_seq( bam_alignment); /* returns a char * */
```

5. Fetching the read quality
```
    bam_get_qual( bam_alignment); /* returns a char * */
```

6. Fetching and parsing CIGAR

The CIGAR field resides in an array of size bam_alignment_core.n_cigar

```
/* Pull out the cigar field from the alignment */
const uint32_t *cigar = bam_get_cigar(bam_alignment);
/* Number of cigar operations */
int n_cigar = bam_alignment_core.n_cigar;
```

CIGAR operation check:
```
      char bam_cigar_opchr(cigar[i])
```
CIGAR operation length:
```
      int bam_cigar_oplen(cigar[i])
```


Example:

```
for (i=0; i < n_cigar; i++)
{
   	if (bam_cigar_opchr(cigar[i]) == 'H')
		printf ("hard clipped, number of bases: %d\n", bam_cigar_oplen(cigar[i]));
	else if (bam_cigar_opchr(cigar[i]) == 'D')
		printf ("deletion, number of bases: %d\n", bam_cigar_oplen(cigar[i]));
	else if (bam_cigar_opchr(cigar[i]) == 'I')
		printf ("insertion, number of bases: %d\n", bam_cigar_oplen(cigar[i]));
	else if (bam_cigar_opchr(cigar[i]) == 'M')
		printf ("match/mismatch, number of bases: %d\n", bam_cigar_oplen(cigar[i]));
	else if (bam_cigar_opchr(cigar[i]) == 'S')
		printf ("soft clipped, number of bases: %d\n", bam_cigar_oplen(cigar[i]));    
}    
```
