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
    
Print everything except the first line:
    
    awk 'NR > 1' in.txt
    
Print everything except the last line:
    
    sed \$d in.txt
    
Print between (and including) rows 10-20:
    
    awk 'NR>=10&&NR<=20' in.txt
    
Delete blank lines in file:

    sed '/^$/d' in.txt
    
Delete lines with string (e.g.: foo):

    sed '/foo/d' in.txt
    
Uniq on one column (field 3):

    awk '!arr[$3]++' in.txt
    
Replace all occurrences of bar with foo:
    
    sed 's/bar/foo/g' in.txt

Compute the mean of column 3:
    
    awk '{x+=$3}END{print x/NR}' in.txt
    
Compute the median of column 3:
    
    sort -nk3,3 in.txt | awk 'NF{a[NR]=$3;p++} END {print (p%2==0)?(a[int(p/2)+1]+a[int(p/2)])/2:a[int(p/2)+1]}'
    
Transpose file:
    
    awk '{for (i=1; i<=NF; i++)  {a[NR,i] = $i}} NF>p { p = NF } END {for(k=1; k<=p; k++) {z=a[1,k];for(i=2; i<=NR; i++){z=z" "a[i,k];} print z}}' in.txt


## DNA, words

Print all possible 4-mer DNA sequence combinations:

    echo {A,C,T,G}{A,C,T,G}{A,C,T,G}{A,C,T,G}
    
Generate a list of 10 random integers between 100 and 200:
    
    awk -v min=100 -v max=200 -v freq=10 'BEGIN{srand(); for(i=0;i<freq;i++) print int(min+rand()*(max-min+1))}'
    
Reverse complement DNA sequence (e.g.: ATGCA):
    
    echo ATGCA | perl -nle 'print map{$_ =~ tr/ACGT/TGCA/; $_} reverse split("",$_)'


## FASTA/Q handling

Obtain md5 checksums of fastq files:

    ls *fastq.gz | parallel md5sum {} > Checksums.txt

Convert FASTQ to FASTA:
    
    awk 'NR%4==1{print ">"substr($0,2)}NR%4==2{print $0}' in.fq > out.fa

Split multi-FASTA file into individual FASTA files:
    
    awk '/^>/ {tmp=substr($0,2) ".fa"}; {print >> tmp; close(tmp)}' in.fa
    
Extract FASTA sequences from in.fa using IDs stored in ID.txt:
    
    perl -ne 'if(/^>(\S+)/){$p=$i{$1}}$p?print:chomp;$i{$_}=1 if @ARGV' ID.txt in.fa

Linearize multi-FASTA sequences:
    
    awk '/^>/ {printf("%s%s\t",(N>0?"\n":""),$0);N++;next;} {printf("%s",$0);} END {printf("\n");}' in.fa
    
Sequence length of every entry in a multifasta file:

    awk '/^>/ {if (seqlen){print seqlen}; print ;seqlen=0;next;}{seqlen = seqlen + length($0)} END {print seqlen}' in.fa

    
