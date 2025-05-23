#!/bin/bash

#SBATCH -J plink
#SBATCH --mem-per-cpu 4G
#SBATCH --cpus-per-task 1
#SBATCH --partition day
#SBATCH -t 10:00
#SBATCH --mail-type ALL
#SBATCH --mail-user db2533@yale.edu

#This script was used to filter the raw MAP/PED files. The input VCF file should first be converted to BED/BIM/FAM format using PLINK. After conversion, the FAM file must be edited to assign a family ID to each sample.
#plink --vcf input.vcf --aec --keep-allele-order --make-bed --const-fid --out output
#After editing the fam file, convert to map/ped
#plink --bfile output --aec --keep-alelle-order --recode --out output

module load PLINK/1.9b_6.21-x86_64

# Verificar que se haya proporcionado un argumento
if [ $# -ne 1 ]; then
    echo "Uso: $0 <archivo_de_entrada>"
    exit 1
fi

# Guardar el nombre del archivo de entrada proporcionado como argumento
input_file="$1"

# Paso 1: Filtrar por frecuencia alélica mínima (MAF)
plink --file "$input_file" --aec --keep-allele-order --maf 0.01 --recode --out "${input_file}_maf001"

# Paso 2: Filtrar por genotipificación mínima (GENO)
plink --file "${input_file}_maf001" --aec --keep-allele-order --geno 0.2 --recode --out "${input_file}_maf001_geno80"

# Paso 3: Filtrar por calidad mínima de datos (MIND)
plink --file "${input_file}_maf001_geno80" --aec --keep-allele-order --mind 0.05 --recode --out "${input_file}_maf001_geno80_mind005"

# Paso 4: Calcular marcadores independientes
plink --file "${input_file}_maf001_geno80_mind005" --aec --keep-allele-order --indep-pairwise 50 10 0.3 --out "${input_file}_maf001_geno80_mind005.ldi"

# Paso 5: Extraer marcadores independientes y crear un nuevo conjunto de datos
plink --file "${input_file}_maf001_geno80_mind005" --aec --keep-allele-order --extract "${input_file}_maf001_geno80_mind005.ldi.prune.in" --recode --out "${input_file}_maf001_geno80_mind005_LD"
