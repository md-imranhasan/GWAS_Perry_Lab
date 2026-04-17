awk '
BEGIN { OFS="\t" }

# Scrub invisible Windows carriage returns from every line
{ gsub(/\r/, ""); }

# 1. Save target SNPs
NR==FNR { snp[$1]=1; order[++n]=$1; next }

# 2. Extract data if the SNP matches
{
    # Meta file (RSID is col 1)
    if (FILENAME == ARGV[2] && $1 in snp) { 
        m_p[$1]=$10; m_ea[$1]=$2; m_nea[$1]=$3 
    }
    # Harmonized files (RSID is col 3)
    else if ($3 in snp) {
        if (FILENAME == ARGV[3]) { mvp_p[$3]=$9; mvp_ea[$3]=$4; mvp_nea[$3]=$5 }
        if (FILENAME == ARGV[4]) { ukb_p[$3]=$9; ukb_ea[$3]=$4; ukb_nea[$3]=$5 }
        if (FILENAME == ARGV[5]) { fin_p[$3]=$9; fin_ea[$3]=$4; fin_nea[$3]=$5 }
        if (FILENAME == ARGV[6]) { kea_p[$3]=$9; kea_ea[$3]=$4; kea_nea[$3]=$5 }
    }
}

# 3. Print the final combined table
END {
    # Header
    print "SNP", "Meta_Status","Meta_P","Meta_EA","Meta_NEA", "MVP_Status","MVP_P","MVP_EA","MVP_NEA", "UKB_Status","UKB_P","UKB_EA","UKB_NEA", "FIN_Status","FIN_P","FIN_EA","FIN_NEA", "KEA_Status","KEA_P","KEA_EA","KEA_NEA"
    
    # Rows
    for(i=1; i<=n; i++) {
        s = order[i]
        print s, \
            (s in m_p?"Present":"Missing"), (s in m_p?m_p[s]:"NA"), (s in m_ea?m_ea[s]:"NA"), (s in m_nea?m_nea[s]:"NA"), \
            (s in mvp_p?"Present":"Missing"), (s in mvp_p?mvp_p[s]:"NA"), (s in mvp_ea?mvp_ea[s]:"NA"), (s in mvp_nea?mvp_nea[s]:"NA"), \
            (s in ukb_p?"Present":"Missing"), (s in ukb_p?ukb_p[s]:"NA"), (s in ukb_ea?ukb_ea[s]:"NA"), (s in ukb_nea?ukb_nea[s]:"NA"), \
            (s in fin_p?"Present":"Missing"), (s in fin_p?fin_p[s]:"NA"), (s in fin_ea?fin_ea[s]:"NA"), (s in fin_nea?fin_nea[s]:"NA"), \
            (s in kea_p?"Present":"Missing"), (s in kea_p?kea_p[s]:"NA"), (s in kea_ea?kea_ea[s]:"NA"), (s in kea_nea?kea_nea[s]:"NA")
    }
}' \
target_snps.txt \
/scratch/negishi/hasan128/data/manhatton/SBP_ALL_META_1_with_chr_pos.tbl \
/scratch/negishi/hasan128/data/harmonized/SBP/MVP_afr_maf_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/SBP/UKB_Afr_maf_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/SBP/Finngen_maf_harmonized.tsv \
/scratch/negishi/hasan128/data/harmonized/SBP/ukb_keaton_maf_harmonized.tsv \
> SBP_meta_4studies_status.tsv
