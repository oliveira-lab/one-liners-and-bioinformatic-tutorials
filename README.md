# oneliners
Useful bash/python one-liners for bioinformatics.


## Basic Awk & Sed

Extract fields 1, 3, and 7 from in.txt:

    awk '{print $1,$3,$7}' in.txt
