## easyqc config:

alloc -A pdrineas --partition=cpu --qos=normal --nodes=1 --ntasks=1 --cpus-per-task=4 --mem=64G --time=04:00:00

``` bash
module --force purge
module load biocontainers
module load gcc
module load r
module load rstudio
module load cairo
module load libpng
module load freetype
module load fontconfig
R

.libPaths(c("~/Rlibs_4.2.2", .libPaths()))
library(EasyQC)
EasyQC("/scratch/negishi/hasan128/data/harmon_marry_data/finngen_eur_hg19_easyqc.ecf")
```

``` bash
Final easy qc code:

cat > /scratch/negishi/hasan128/data/harmon_marry_data/finngen_test_minimal.ecf <<'EOF'
DEFINE --acolIn chromosome;base_pair_location;effect_allele;other_allele;beta;standard_error;effect_allele_frequency;p_value;rsid --acolInClasses character;integer;character;character;numeric;numeric;numeric;numeric;character --acolNewName CHR;POS;A1;A2;BETA;SE;EAF;P;RSID --strMissing . --strSeparator TAB --pathOut /scratch/negishi/hasan128/data/harmon_marry_data/easyqc_out

EASYIN --fileIn /depot/ppaschou/data/NEW_CKM/qc/BMI/meta_ready_hg19/Finngen_EUR_hg19_final.tsv --fileInShortName Finngen_EUR_hg19_final

START EASYQC

GETCOLS --acolOut RSID;CHR;POS;A1;A2;BETA;SE;EAF;P

WRITE --strMode txt --strPrefix TEST_MINIMAL. --strSuffix .out --strSep TAB --strMissing .

STOP EASYQC
EOF







cat > /scratch/negishi/hasan128/data/harmon_marry_data/finngen_step2.ecf <<'EOF'
DEFINE --acolIn chromosome;base_pair_location;effect_allele;other_allele;beta;standard_error;effect_allele_frequency;p_value;rsid --acolInClasses character;integer;character;character;numeric;numeric;numeric;numeric;character --acolNewName CHR;POS;A1;A2;BETA;SE;EAF;P;RSID --strMissing . --strSeparator TAB --pathOut /scratch/negishi/hasan128/data/harmon_marry_data/easyqc_out

EASYIN --fileIn /depot/ppaschou/data/NEW_CKM/qc/BMI/meta_ready_hg19/Finngen_EUR_hg19_final.tsv --fileInShortName Finngen_EUR_hg19_final

START EASYQC

ADDCOL --rcdAddCol paste(CHR,POS,sep=':') --colOut MarkerName

FILTER --rcdFilter !is.na(CHR)&!is.na(POS)&!is.na(A1)&!is.na(A2)&!is.na(BETA)&!is.na(SE)&!is.na(EAF)&!is.na(P)&!is.na(RSID)&RSID!='.'&SE>0&P>0&P<=1&EAF>=0&EAF<=1 --strFilterName numSNP_basicQC

FILTER --rcdFilter nchar(A1)==1&nchar(A2)==1&A1%in%c('A','C','G','T')&A2%in%c('A','C','G','T') --strFilterName numSNP_simpleSNP

CLEANDUPLICATES --colInMarker RSID --strMode keepfirst

GETCOLS --acolOut RSID;CHR;POS;A1;A2;BETA;SE;EAF;P;MarkerName

WRITE --strMode txt --strPrefix STEP2. --strSuffix .out --strSep TAB --strMissing .

STOP EASYQC
EOF

``` 















## Full-Final Code





``` bash
cat > /scratch/negishi/hasan128/data/harmon_marry_data/finngen_eur_hg19_easyqc.ecf <<'EOF'
DEFINE --acolIn chromosome;base_pair_location;effect_allele;other_allele;beta;standard_error;effect_allele_frequency;p_value;rsid --acolInClasses character;integer;character;character;numeric;numeric;numeric;numeric;character --acolNewName CHR;POS;A1;A2;BETA;SE;EAF;P;RSID --strMissing . --strSeparator TAB --pathOut /scratch/negishi/hasan128/data/harmon_marry_data/easyqc_out

EASYIN --fileIn /depot/ppaschou/data/NEW_CKM/qc/BMI/meta_ready_hg19/Finngen_EUR_hg19_final.tsv --fileInShortName Finngen_EUR_hg19_final

START EASYQC

ADDCOL --rcdAddCol paste(CHR,POS,sep=':') --colOut MarkerName

FILTER --rcdFilter !is.na(CHR)&!is.na(POS)&!is.na(A1)&!is.na(A2)&!is.na(BETA)&!is.na(SE)&!is.na(EAF)&!is.na(P)&!is.na(RSID)&RSID!='.'&SE>0&P>0&P<=1&EAF>=0&EAF<=1 --strFilterName numSNP_basicQC

FILTER --rcdFilter nchar(A1)==1&nchar(A2)==1&A1%in%c('A','C','G','T')&A2%in%c('A','C','G','T') --strFilterName numSNP_simpleSNP

CLEANDUPLICATES --colInMarker RSID --strMode keepfirst

GETCOLS --acolOut RSID;CHR;POS;A1;A2;BETA;SE;EAF;P;MarkerName

MERGE --colInMarker RSID --fileRef /scratch/negishi/hasan128/data/harmon_marry_data/dbsnp37_lookup_chr_header.tsv --strSeparator TAB --strMissing . --acolIn CHRREF;POSREF;RSID;RefA1;RefA2 --acolInClasses character;integer;character;character;character --acolNewName CHRREF;POSREF;RSID;RefA1;RefA2 --colRefMarker RSID --strRefSuffix .ref --blnInAll 1 --blnRefAll 0 --blnWriteNotInRef 1 --blnWriteNotInIn 0 --strTag REF

FILTER --rcdFilter !is.na(RefA1.ref)&!is.na(RefA2.ref)&nchar(RefA1.ref)==1&nchar(RefA2.ref)==1&RefA1.ref%in%c('A','C','G','T')&RefA2.ref%in%c('A','C','G','T')&!grepl(',',RefA2.ref) --strFilterName numSNP_singleAltRef

AFCHECK --colInMarker RSID --colInA1 A1 --colInA2 A2 --colInFreq EAF --acolInBeta BETA --fileRef /scratch/negishi/hasan128/data/harmon_marry_data/dbsnp37_lookup_chr_header.tsv.gz --strSeparator TAB --strMissing . --acolIn CHRREF;POSREF;RSID;RefA1;RefA2 --acolInClasses character;integer;character;character;character --acolNewName CHRREF;POSREF;RSID;RefA1;RefA2 --colRefMarker RSID --colRefA1 RefA1 --colRefA2 RefA2 --blnMetalUseStrand 0 --strTag AFCHECK

FILTER --rcdFilter !((A1=='A'&A2=='T')|(A1=='T'&A2=='A')|(A1=='C'&A2=='G')|(A1=='G'&A2=='C')) --strFilterName numSNP_nonPalindromic

GETCOLS --acolOut RSID;CHR;POS;A1;A2;EAF;BETA;SE;P;CHRREF.ref;POSREF.ref;RefA1.ref;RefA2.ref

WRITE --strMode txt --strPrefix HARMONIZED. --strSuffix .hg19 --strSep TAB --strMissing .

STOP EASYQC
EOF



``` 
