# nex2tbl

nex2tbl is a tool aimed to help with submission of protein-coding DNA sequences to GenBank. Such sequences are commonly submitted through BankIt portal, where a [Feature Table File](https://www.ncbi.nlm.nih.gov/WebSub/html/help/feature-table.html) (\*.tbl file) is prompted if a user uploads multiple records. Preparation of tbl file can be a laborious task, especially if the sequences include multiple introns or start from different codon positions. nex2tbl allows to create a minimum essential tbl with 2 [feature keys](https://www.insdc.org/submitting-standards/feature-table/#7.2) (`gene` and `CDS`) and 5 [qualifiers](http://www.insdc.org/documents/feature_table.html#7.3.1) (`gene`, `product`, `codon_start`, `transl_table`, and `partial` aka `<`/`>`) that are altogether enough for GenBank to correctly translate DNA into amino acids.

## Usage

- Load the scipt 
```R
source("https://raw.githubusercontent.com/Mycology-Microbiology-Center/nex2tbl/main/nex2tbl.R")
```

- Specify input and output file names, as well as user-defined variables.
```R
nex2tbl(
  INPUT_NEX = "TEF1_codes.nex",
  OUTPUT_TBL = "TEF1_codes.tbl",
  GENE = "tef1",
  PRODUCT = "translation elongation factor 1-alpha",
  TRANSL_TABLE = 1,
  CODON_START = 1,
  FULL_GENE = FALSE
  )
```
Resulting tbl file will appear in the same directory.

## Documentation

Input for the tool is alignment of the submitted sequences of one gene in nexus format ([example](/test/exons-introns_CODON_START-1_TEF1_simple.nex)). Intron positions should be specified as column spans in a single charset called `intron`, like this:
```
BEGIN SETS;
charset intron = 202-256 394-451;
END;
```

In addition, a user must specify the following variables:
- `GENE` - gene name, e.g., "rpb1".
- `PRODUCT` - name of the produced protein, e.g., "RNA polymerase II largest subunit".
- `TRANSL_TABLE` - defines the genetic code table used, by default is 1 - universal genetic code table.
- `FULL_GENE` - can be `FALSE` or `TRUE` depending on whether the sequence covers the whole coding region of a protein. Usually it is not the case, and then locations of the first and last  regions (assumed to be incomplete) will be indicated with `<` or `>` before the number. Note: if `TRUE`, GenBank expects `CODON_START` to be 1. 
- `CODON_START` - indicates the offset at which the first complete codon of a coding region can be found in the alignment. It is specified in relation to the first base of the first exon and therefore can only take values 1, 2, or 3. On the example below, the first complete codon is in the seq4 and starts at the column 3 (marked with arrows), therefore `CODON_START` will be 3. Note: to define this variable a user must know the coding frame of alignment.

```
             ↓
codon_pos  23123123123123123123123
seq1       --------ttggcttcgttgttt
seq2       -------gctggcgacgttgttc
seq3       -----------------ttgttc
seq4       gaccgttgcttgcgacgctgttc
column_n   123456789 etc..........
             ↑
```

### Example of output for a single sequence
```
>Features seq4
<1	>2119	gene
			gene	rpb2
<1	74	CDS
128	1087
1144	>2119
			product	RNA polymerase II second largest subunit
			codon_start	3
			transl_table	1
```

## Current limitations

- Intron-only alignment is not supported.

## Credits

- Code: Vladimir Mikryukov
- Idea: Anton Savchenko and Iryna Yatsiuk
