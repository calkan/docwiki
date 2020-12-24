# Searching for an interval

Assuming the following:

```
    hts_itr_t *iter; /* iterator */
    int chrom_id; /* ID of the chromosome as defined in the BAM/CRAM header. First chromosome ID is 0.*/
    int start; /* starting coordinate: 0-based */
    int end; /* ending coordinate: 0-based */
```


Create the iterator:

```
    iter = sam_itr_queryi( bam_file_index, chrom_id, start, end);
    /* returns NULL if it fails */
```

Alternative iterator creation:

```
    const char *region; /* text-based query */
    iter = sam_itr_querys( bam_file_index, bam_header, region);
```

Here, region can be used as follows:

| Region | Meaning |
| ----------- | ----------- |
| chr5 | All alignments in chr5 |
| chr5:134877 | All alignments starting at chr5:134877 to the end of chr5 |
| chr5:-45445157 | All alignments starting at chr5:0 and ending at chr5:45445157 |
| chr5:134877-45445157 | All alignments starting at chr5:134877 and ending at chr5:45445157 |

# Iterate

```
    while( sam_itr_next( bam_file, iter, bam_alignment) > 0)
	{
		bam_alignment_core = bam_alignment->core;
                /* do other things */
        }
```

# Cleanup

Destroy the iterator:

```
    sam_itr_destroy( iter);
```
