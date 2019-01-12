# oneliners
Useful bash/python one-liners for bioinformatics.


## Basic Awk & Sed

Extract columns/fields 1, 3, and 7 from in.txt:

    awk '{print $1,$3,$7}' in.txt
    
Extract lines 1, 3, and 7 from in.txt:

    cat in.txt | awk 'NR==1 || NR==3 || NR==7'    
    
