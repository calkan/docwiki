First, get the number of libraries in the SAM/BAM/CRAM file

```
int get_library_count( char* header_text)
{
	int number_of_libraries = 0;

	/* Delimit the BAM header text with newlines */
	char *tmp_header = (char *) malloc(sizeof(char) * (strlen(header_text) + 1) );
	strncpy( tmp_header, header_text, (strlen(header_text) + 1) );
        char* p = strtok( tmp_header, "\n");

	while( p != NULL)
	{
		/* If the current token (which is a line) has "RG" as the second and third characters,
		 we have found a new library */
		if( p[1] == 'R' && p[2] == 'G')
		{
			number_of_libraries = number_of_libraries + 1;
		}
		p = strtok( NULL, "\n");
	}

	free( tmp_header);
	return number_of_libraries;
}
```

Use this function as:

` int no_of_libs = get_library_count( bam_header->text);`


Now load the library names:

```
char ** get_library_names( char* header_text, int no_of_libs)
{
	char line_buffer[1024];
	char library_name_buffer[1024];
	int i;
	char *saveptr_p, *saveptr_q;
        char **lib_names;

        lib_names = (char **) malloc(sizeof(char *)  * no_of_libs);

	/* Delimit the BAM header text with newlines */
	i = 0;

	char *tmp_header = (char *) malloc(sizeof(char) * (strlen(header_text) + 1) );
	strncpy( tmp_header, header_text, (strlen(header_text) + 1) );

	char* p = strtok_r( tmp_header, "\n", &saveptr_p);

	while( p != NULL)
	{
		/* If the current token (which is a line) has "RG" as the second and third characters,
			we copy the current line to a new char* and tokenize it to get the id/name */
		if( p[1] == 'R' && p[2] == 'G')
		{
			/* Copy the current line to the line buffer */
			strncpy( line_buffer, p, strlen( p));
			line_buffer[strlen( p)] = '\0';

			/* Tokenize the line buffer by tabs */
			char* q = strtok_r( line_buffer, "\t", &saveptr_q);
			while( q != NULL)
			{
				if( q[0] == 'I' && q[1] == 'D')
				{
					/* Get the Library Name */
					strncpy( library_name_buffer, q + 3, strlen( q) - 3);

					/* Add the NULL terminator */
					library_name_buffer[strlen( q) - 3] = '\0';

					/* Exit loop */
					break;
				}

				q = strtok_r( NULL, "\t", &saveptr_q);
			}
			        
                        lib_names[i] = (char *) malloc(sizeof(char)  * (strlen(library_name_buffer) + 1));

                        strcpy( lib_names[i], library_name_buffer);
			i = i + 1;
		}
		p = strtok_r( NULL, "\n", &saveptr_p);
	}
	free( tmp_header);
        return lib_names;
}
```

Use this function as:

`char **library_names = get_library_names( bam_header->text, no_of_libs);`
