# oneliners
Useful bash/python/perl one-liners for bioinformatics.


## Basic Awk & Sed

Extract columns/fields 1, 3, and 7 from in.txt:

    awk '{print $1,$3,$7}' in.txt
    
Extract lines 1, 3, and 7 from in.txt:

    cat in.txt | awk 'NR == 1 || NR == 3 || NR == 7'    
    
Print every fifth line:

    awk 'NR % 5 == 0' in.txt
    
Print every fifth line starting at the third line:

    awk 'NR % 5 == 3' in.txt
    
Print everything except the first line
    
    awk 'NR > 1' in.txt
    
Print everything except the last line
    
    sed \$d in.txt
    
Print between (and including) rows 10-20:
    
    awk 'NR>=10&&NR<=20' in.txt
    
Delete blank lines in file:

    sed '/^$/d' in.txt
    
Replace all occurrences of bar with foo:
    
    sed 's/bar/foo/g' in.txt

Compute the mean of column 3:
    
    awk '{x+=$3}END{print x/NR}' in.txt

## FASTA/Q handling

Convert FASTQ to FASTA:
    
    awk 'NR%4==1{print ">"substr($0,2)}NR%4==2{print $0}' in.fq > out.fa

Split multi-FASTA file into individual FASTA files:
    
    awk '/^>/ {tmp=substr($0,2) ".fa"}; {print >> tmp; close(tmp)}' in.fa
    
Extract FASTA sequences from in.fa using IDs stored in ID.txt:
    
    perl -ne 'if(/^>(\S+)/){$p=$i{$1}}$p?print:chomp;$i{$_}=1 if @ARGV' ID.txt in.fa

Linearize multi-FASTA sequences:
    
    awk '/^>/ {printf("%s%s\t",(N>0?"\n":""),$0);N++;next;} {printf("%s",$0);} END {printf("\n");}' in.fa
    
