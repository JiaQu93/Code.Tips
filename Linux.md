# Change permissions
## change all document in this file
chmod /fs/project/PAS1475/guoqi/spatial_data/ -R 777  
chmod -R 777 *  
#Setting 777 permissions (chmod 777) to a file or directory means that it will be readable, writable and executable by all users
<img width="864" alt="820d2596a4e385d02b6ce502bdf43f72" src="https://github.com/user-attachments/assets/e79b8b89-d4c1-49f4-b57b-51914389bc1b">



**Note: do not use blank when name folder**  

# Compress and decompress file 
### compress
```{r}
gzip FileName
```

### decompress file
end with zip
```{r}
unzip FileName.zip
```
end with gz--gzip
```{r}
gzip -d FileName.gz
```
end with tar.gz-tar
```{r}
tar zxvf FileName.tar.gz
```

# HPC(SLURM) sbatch command
### 
```console
scontrol hold jobid 暂停，scontrol release jobid 恢复，scancel jobid 终止
```

# Basic command
### Copy file
```{r}
cd originalpath
cp file targetpath
```

# R  
## start R in terminal
```{r}
module load R/4.1.0-gnu9.1
R
```
## quit R in terminal
```{r}
quit()
```
# Submit R JOB on server
```{linux}
#Create R script by nano ------------------------
nano create_seurat_object.R

library(Seurat)
library(data.table)
# Load gene expression 
DRG_gene_expr <- fread("/fs/ess/PAS2556/Bioinformatics_analysis/Development.DRG/Data/GSE245310_human_DRG_gene_expr.txt")
DRG_gene_expr <- as.data.frame(DRG_gene_expr)
rownames(DRG_gene_expr) <- as.character(DRG_gene_expr[[1]])
DRG_gene_expr$V1 <- NULL
# Load metadata 
DRG_meta <- read.csv("/fs/ess/PAS2556/Bioinformatics_analysis/Development.DRG/Data/DRG_meta_processed.csv", row.names = 1)
# Create Seurat object 
DRG_obj <- CreateSeuratObject(counts = DRG_gene_expr)
# Add metadata
rownames(DRG_meta) <- DRG_meta$cell
DRG_obj <- AddMetaData(DRG_obj, metadata = DRG_meta)
# Save Seurat object
saveRDS(DRG_obj, file = "/fs/ess/PAS2556/Bioinformatics_analysis/Development.DRG/Data/DRG_obj.rds")

#Create sbatch by nano ------------------------
nano create_seurat_object.sh

#!/bin/bash
#SBATCH --job-name=create_seurat
#SBATCH --output=create_seurat.%j.out
#SBATCH --error=create_seurat.%j.err
#SBATCH --account PAS2556
#SBATCH --time=05:00:00
#SBATCH --mem=128G
#SBATCH --cpus-per-task=4
# Load R module (adjust based on your HPC environment)
module purge
module load gcc/12.3.0
module load R/4.4.0 
# Run the R script
Rscript create_seurat_object.R


#Submit sbatch job ------------------------
sbatch xxx.sh
#Supervise
qstat jobname
qstat -u guoqi
https://maveric-informatics.readthedocs.io/en/latest/OSC.html
```
# Transfer file by remote server
```{r}
scp -r guoqi@192.148.247.179:/fs/ess/PCON0022/guoqi/NC-snrna/atac_output/temp ./rawdata/temp
```
# Run sh script
```
/bin/bash .command.sh
```

# Find specific file 
```
specific file in this path
cd path
find ./ -iname projection*
#find specific file in all path
find . -type f -name "*h5Seurat*"
```
# Check object storage
```
du -ah
```

# Show all files including hidden
```
ls -a
```

# OSC

## Create sh command
```{r}
#!/bin/bash
#SBATCH --job-name=directnet_examp
#SBATCH --time=20:50:59
#SBATCH --output="directnet_sh_out"
#SBATCH --account=PCON0022
#SBATCH --mem=150GB
#SBATCH --mail-type=BEGIN,END,FAIL

cd /fs/ess/PCON0022/guoqi/Yang/Stream/Directnet
start=`date +%s`
module load R/4.1.0-gnu9.1
Rscript /fs/ess/PCON0022/guoqi/Yang/Stream/Directnet/Directnet.r
end=`date +%s`
echo Execution time was `expr $end - $start` seconds.
```

# Change file name in batch
```
for file in *_expression*; do     mv -- "$file" "${file//_expression}"; done
for file in sim_*; do     new_name=$(echo "$file" | sed 's/_/-/g');     mv "$file" "$new_name"; done
for file in *.h5ad*; do
    newname="${file/.h5ad/_sim_sim_expression.h5ad}"
    mv -- "$file" "$newname"
done
for file in *.h5Seurat*; do
    newname="${file/.h5Seurat/_sim_sim_expression.h5Seurat}"
    mv -- "$file" "$newname"
done

for file in *_xy*; do     mv -- "$file" "${file//_xy}"; done
for file in sim_*; do     new_name=$(echo "$file" | sed 's/_/-/g');     mv "$file" "$new_name"; done
for file in *.csv*; do
    newname="${file/.csv/_sim_sim_xy.csv}"
    mv -- "$file" "$newname"
done
```
# Submit jobs on VP3

## Submit

```
conda activate virtualenvironment
nohup python ./05_Subcluster_integration_evaluate.py > output_05.txt 2>&1 &
```
## Check jobs

```
jobs
```

## Check id and cancel

```
ps aux | grep 04_Integration_scvi.py
#id is the first number in the first row : 633763
kill id
```

# Download file via terminal

```
wget "paste the copied URL here" -O GSE201586_humanallcelltypes.rds.gz
#URL = https://www.ncbi.nlm.nih.gov/geo/download/?acc=GSE201586&format=file&file=GSE201586%5Fhumanallcelltypes%2Erds%2Egz

#display the file size and confirm that the file is present in your current directory.
ls -lh GSE201586_humanallcelltypes.rds.gz

#uncompress gz terminal
gunzip your_file.rds.gz

```
