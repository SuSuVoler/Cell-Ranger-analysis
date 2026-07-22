# login with urblauna account
UNIL password and the 2FA code
```
ssh zsu@u-ssh.dcsr.unil.ch
```

# Structure de dossiers in urblauna

Télécharger directement dans /data/ 
I don't have acess to internet with urblauna, solution: télécharger in curnagl, then copy to urblauna

login with curnagl account, UNIL password
```
ssh zsu@curnagl.dcsr.unil.ch
cd /work/PRTNR/CHUV/MED/cpich/5g-fr2/
wget https://cf.10xgenomics.com/supp/cell-exp/refdata-gex-GRCh38-2020-A.tar.gz
tar -xzvf refdata-gex-GRCh38-2020-A.tar.gz
```

transfer the reference to urblauna
```
cp -r /work/PRTNR/CHUV/MED/cpich/5g-fr2/refdata-gex-GRCh38-2020-A \
  /data/PRTNR/CHUV/MED/cpich/5g-fr2
```
use filezilla to transfer probe_Sets, fastqs...
the final structure should look like this:
```
/data/PRTNR/CHUV/MED/cpich/5g-fr2/CellRanger/
├── references/
│   └── refdata-gex-GRCh38-2020-A/    ← génome de référence
├── probe_sets/
│   └── Chromium_Human_Probe_Set.csv   ← sondes Flex
├── fastqs/
│   └── Flex_run1/
│       ├── Sample1_S1_L001_R1_001.fastq.gz
│       └── Sample1_S1_L001_R2_001.fastq.gz
├── config.csv                         ← ton fichier de config
└── output/
    └── Flex_run1/                     ← résultats CellRanger
```
