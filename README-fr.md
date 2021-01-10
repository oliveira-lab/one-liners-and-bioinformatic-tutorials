🌍
[English](README.md) ∙ [Français](README-fr.md) ∙ [Português](README-pt.md)

# One-liners bash/python/perl et tutoriels bioinformatiques utiles

*Remarque: Ce guide fournit un aperçu des quelques commandes UNIX utiles et analyses couramment utilisées dans le domaine bioinformatique. Il s'adresse à un public débutant et ne cesse de croître. Les suggestions pour améliorer cette ressource sont les bienvenues. Envoyez-moi un message si vous souhaitez contribuer. - [Github] (https://github.com/oliveira-lab), [page Web] (http://www.pholiveira.net). Merci!*


- [Unix sur MacOSX](#unix-sur-macosx)
- [Unix sur Windows](#unix-sur-windows)
- [Fichiers de test](#fichiers-test)
- [Awk & Sed basique](#awk-sed-basique)
- [DNA, mots](#dna-mots)
- [Manipulation FASTA/Q](#manipulation-fasta/q)
- [Plus d'informations](#plus-informations)

![fortune | cowsay ](./cowsay.png)



# Unix sur MacOSX

- Mac OS X est livré avec une application nommée Terminal. Vous pouvez le trouver sous Finder → Applications → Utilitaires → Terminal. Si vous prévoyez d'utiliser beaucoup le terminal, faites glisser l'icône du terminal de la fenêtre du Finder sur le Dock. Vous pouvez ensuite lancer Terminal en un seul clic.

- Pour plus de commodité, vous devez ensuite configurer un gestionnaire de paquets pour installer un nouveau logiciel dans le terminal.
  
  - Téléchargez et installez Xcode, l'application de développement logiciel d'Apple.
    
  - Téléchargez et installez les outils de ligne de commande pour Xcode (entrez la commande suivante dans Terminal):
  
		xcode-select —install
  	
  - Téléchargez et installez un gestionnaire de paquets gratuit tel que [Homebrew](https://brew.sh) et/ou [MacPorts](https://www.macports.org).
  

# Unix sur Windows

- Vous pouvez accéder à la puissance du shell Unix sous Microsoft Windows en installant [Cygwin](https://cygwin.com/). La plupart des éléments décrits dans ce document fonctionneront immédiatement.

- Sous Windows 10, vous pouvez utiliser [Windows Subsystem for Linux (WSL)](https://msdn.microsoft.com/commandline/wsl/about), qui fournit un environnement Bash familier avec des utilitaires de ligne de commande Unix.

- Si vous souhaitez principalement utiliser les outils de développement GNU (tels que GCC) sous Windows, considérez [MinGW](http://www.mingw.org/) et [MSYS](http://www.mingw.org/wiki/msys), qui fournit des utilitaires tels que bash, gawk, make et grep. MSYS n'a pas toutes les fonctionnalités par rapport à Cygwin. MinGW est particulièrement utile pour créer des ports Windows natifs des outils Unix.

- Vous pouvez également installer [Homebrew](https://docs.brew.sh/Homexbrew-on-Linux) sur le sous-système Windows pour Linux (WSL).


# Fichiers de test
Pour plus de commodité, je fournis des fichiers de test à toutes les commandes ci-dessous.
Ceux-ci comprennent un fichier FASTA [in.fa](./in.fa), un fichier CSV numérique [in.csv](./in.csv),
un fichier ID avec en-têtes FASTA [ID.txt](./ID.txt), un fichier FASTQ [in.fq](./in.fq), un fichier de motifs FASTA [motifs.fa](./motifs.fa ).
Ouvrez simplement le Terminal (sous MacOSX) ou Cygwin (par exemple) sous Windows et amusez-vous!


# Awk & Sed basique

#### Extraire les colonnes/champs 1, 3 et 7 de in.csv:

	awk '{print $1,$3,$7}' in.csv
    
#### Extraire les lignes 1, 3 et 7 de in.csv:

    cat in.csv | awk 'NR == 1 || NR == 3 || NR == 7'  
    
#### Compter le nombre de lignes uniques dans in.csv:

	cat in.csv | sort | uniq | wc -l
      
#### Imprimez toutes les cinq lignes:

    awk 'NR % 5 == 0' in.csv
    
#### Imprimez toutes les cinq lignes à partir de la troisième ligne:

    awk 'NR % 5 == 3' in.csv
    
#### Imprimez tout sauf la première ligne:
    
    awk 'NR > 1' in.csv
    
#### Imprimez tout sauf la dernière ligne:
    
    sed \$d in.csv
    
#### Imprimer entre (et y compris) les lignes 10-20:
    
    awk 'NR>=10&&NR<=20' in.csv
    
#### Supprimer les lignes vides dans le fichier:

    sed '/^$/d' in.csv
    
#### Supprimer les lignes avec une string (par exemple: 99):

    sed '/99/d' in.csv
    
#### Uniq sur une colonne (champ 3):

    awk '!arr[$3]++' in.csv
    
#### Remplacer toutes les occurrences de 23 par 99:
    
    sed 's/23/99/g' in.csv

#### Calculer la moyenne de la colonne 3:
    
    awk '{x+=$3}END{print x/NR}' in.csv
    
#### Calculer la médiane de la colonne 3:
    
    sort -nk3,3 in.csv | awk 'NF{a[NR]=$3;p++} END {print (p%2==0)?(a[int(p/2)+1]+a[int(p/2)])/2:a[int(p/2)+1]}'
    
#### Transposer le fichier:
    
    awk '{for (i=1; i<=NF; i++)  {a[NR,i] = $i}} NF>p { p = NF } END {for(k=1; k<=p; k++) {z=a[1,k];for(i=2; i<=NR; i++){z=z" "a[i,k];} print z}}' in.csv

#### Comparer deux fichiers en fonction du 1er champ

    awk 'FNR==NR{a[$1]++;next}!a[$1]' in1.csv in2.csv
    
#### Comparer les 1er, 2ème et 3ème champs de in1.csv avec les 1er, 4ème et 5ème champs de in2.csv. Seules les lignes correspondantes dans in2.csv seront imprimées.
    
    awk 'NR==FNR{a[$1,$2,$3]++;next} (a[$1,$4,$5])' in1.csv in2.csv

#### Imprimer une ligne après (A) et deux lignes avant (B) l'expression régulière correspondante (par exemple: 99).

    grep -A1 -B2 '99' in.csv


# DNA, mots

#### Imprimer toutes les combinaisons de séquences d'ADN 4-mers:

    echo {A,C,T,G}{A,C,T,G}{A,C,T,G}{A,C,T,G}
    
#### Générer une liste de 10 entiers aléatoires entre 100 et 200:
    
    awk -v min=100 -v max=200 -v freq=10 'BEGIN{srand(); for(i=0;i<freq;i++) print int(min+rand()*(max-min+1))}'
    
#### Complément inverse de la séquence d'ADN (ex: ATGCA):
    
    echo ATGCA | perl -nle 'print map{$_ =~ tr/ACGT/TGCA/; $_} reverse split("",$_)'
    
    
# Manipulation FASTA/Q

#### Obtener les checksums md5 des fichiers FASTQ:

    ls *.fq | parallel md5sum {} > Checksums.txt

#### Convertir FASTQ en FASTA:
    
    awk 'NR%4==1{print ">"substr($0,2)}NR%4==2{print $0}' in.fq > out.fa

#### Diviser le fichier multi-FASTA en fichiers FASTA individuels:
    
    awk '/^>/ {tmp=substr($0,2) ".fa"}; {print >> tmp; close(tmp)}' in.fa
    
#### Extraire les séquences FASTA de in.fa à l'aide des ID stockés dans ID.txt:
    
    perl -ne 'if(/^>(\S+)/){$p=$i{$1}}$p?print:chomp;$i{$_}=1 if @ARGV' ID.txt in.fa

#### Linéariser les séquences multi-FASTA:
    
    awk '/^>/ {printf("%s%s\t",(N>0?"\n":""),$0);N++;next;} {printf("%s",$0);} END {printf("\n");}' in.fa
    
#### Longueur de séquence de chaque entrée dans un fichier multifasta:

    awk '/^>/ {if (seqlen){print seqlen}; print ;seqlen=0;next;}{seqlen = seqlen + length($0)} END {print seqlen}' in.fa
    
#### Convertir le format de fichier à deux colonnes au format FASTA:
    
    awk '{print ">" $1,"\n" $2;}' in.txt
    
#### Calculer toutes les occurrences et les positions de début et de fin du motif TCTAWA dans un fichier FASTA:   
    
- Une excellente option consiste à installer [SeqKit](https://bioinf.shenwei.me/seqkit/download/) et à l'exécuter en tant que:

		seqkit locate --ignore-case --degenerate --pattern TCTAWA in.fa
	
	Dans le cas ci-dessus, nous demandons à seqkit de localiser toutes les positions du motif dégénéré TCTAWA dans un fichier FASTA (in.fa).	
	Dans le cas où nous aurions plusieurs motifs à tester, nous pouvons simplement les assembler au format FASTA (fichier motifs.fa):
	
		seqkit locate --ignore-case --degenerate -f motifs.fa in.fa

#### Exécutez FASTQC sur vos fichiers FASTQ:

- Installez d'abord FASTQC (par exemple en utilisant brew):

		brew install fastqc

	Ensuite, exécutez simplement (si vous voulez le faire en parallèle):
	
    	find *.fq | parallel -j 12 "fastqc {} --outdir ."
    
	Cela produira un rapport HTML FASTQC et un fichier ZIP contenant tous les fichiers associés.
    
			 
# Plus d'informations

- [awesome-shell](https://github.com/alebcay/awesome-shell): Une liste organisée d'outils et de ressources shell.
- [awesome-osx-command-line](https://github.com/herrbischoff/awesome-osx-command-line): Un guide plus détaillé pour la ligne de commande macOS.
- [Strict mode](http://redsymbol.net/articles/unofficial-bash-strict-mode/) pour écrire de meilleurs scripts shell.
- [shellcheck](https://github.com/koalaman/shellcheck): Un outil d'analyse statique de script shell.
- [Filenames and Pathnames in Shell](http://www.dwheeler.com/essays/filenames-in-shell.html): Les détails tristement complexes sur la façon de gérer correctement les noms de fichiers dans les scripts shell.
- [Data Science at the Command Line](http://datascienceatthecommandline.com/#tools): Plus de commandes et d'outils utiles pour faire de la science des données, tirés du livre du même nom.
