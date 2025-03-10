# SOC-DGL
SOC-DGL is an innovative model designed for drug-target interaction prediction, 
leveraging a dual-encoder graph neural network to enhance the representation of homogeneous information. 
The model employs a graph convolutional encoder to extract deep relational features between drugs and targets,
and integrating a homogeneous information aggregation encoder to uncover indirect associations among drugs or targets. 



---

# Datasets Overview

## KIBA Dataset
- **Statistics**:
  - **Drugs**: 1,720  
  - **Proteins**: 220  
  - **Validated DTIs**: 22,154
- **Files**:
  - `drugs.xlsx`: Drug IDs and corresponding SMILES representations.  
  - `targets.xlsx`: UniProt IDs and FASTA sequences of proteins.  
  - `dti_list.xlsx`: Mapping list of drug-target interactions.  
  - `dti_mat.xlsx`: Matrix representation of drug-target interactions.  

## Davis Dataset
- **Statistics**:
  - **Drugs**: 68  
  - **Proteins**: 379  
  - **Validated DTIs**: 7,320
- **Files**:
  - `drugs.xlsx`: Drug IDs and SMILES representations.  
  - `targets.xlsx`: UniProt IDs and FASTA sequences of proteins.  
  - `dti_list.xlsx`: Mapping list of drug-target interactions.  
  - `dti_mat.xlsx`: Matrix representation of drug-target interactions.

## BindingDB Dataset
- **Statistics**:
  - **Drugs**: 3,400  
  - **Proteins**: 886  
  - **Validated DTIs**: 9,166
- **Files**:
  - `drugs.xlsx`: Drug IDs and SMILES representations.  
  - `targets.xlsx`: UniProt IDs and FASTA sequences of proteins.  
  - `dti_list.xlsx`: Mapping list of drug-target interactions.  
  - `dti_mat.xlsx`: Matrix representation of drug-target interactions.  

## DrugBank Dataset
- **Statistics**:
  - **Drugs**: 1,821  
  - **Proteins**: 1,447  
  - **Validated DTIs**: 6,869
- **Files**:
  - `drugs.xlsx`: Drug IDs and SMILES representations.  
  - `targets.xlsx`: UniProt IDs and FASTA sequences of proteins.  
  - `dti_list.xlsx`: Mapping list of drug-target interactions.  
  - `dti_mat.xlsx`: Matrix representation of drug-target interactions.

---

# Prepare conda enviroment
+ conda create -n your_env_name python=3.9

dependencies:
+ biotite == 0.33.0
+ Bottleneck == 1.3.7
+ certifi == 2023.7.22
+ conda-pack == 0.7.1
+ contourpy == 1.1.1
+ MarkupSafe == 2.1.5
+ matplotlib == 3.4.3
+ numpy == 1.22.4
+ pandas == 1.5.3
+ Pillow == 10.1.0
+ pip == 23.3
+ torch-scatter == 2.1.0+pt113cu116
+ torch-sparse == 0.6.16+pt113cu116
+ torchaudio == 0.13.0+cu116
+ torchvision == 0.14.0+cu116
+ tornado == 6.2



---

# Running for SOC-DGL
### 1. Generating drug fingerprints
*(Example: KIBA dataset)*

Run the following commands to generate drug fingerprints:
```
python MACC_fingerprint.py -drug_smile_path ./KIBA/drugs.xlsx -feature_save_path ./KIBA/drug_features/MACC_features.xlsx

python Mogan_fingerprint.py -drug_smile_path ./KIBA/drugs.xlsx -feature_save_path ./KIBA/drug_features/Mogan_features.xlsx

python Topological_fingerprint.py -drug_smile_path ./KIBA/drugs.xlsx -feature_save_path ./KIBA/drug_features/Topological_features.xlsx
```
then you will get:

---> drug_features/MACC_features.xlsx

---> drug_features/Mogan_features.xlsx

---> drug_features/Topological_features.xlsx


### 2. Generating protein features
retrieve all the fasta sequences according to the target uniprot id, and put all the fasta seqs in fasta_file directory. 

Running: 
```
python /generate_protein_features/process_fasta.py
```
---> fasta_seqs.txt

Running: 
```
python /iFeature-master/iFeature.py --file ../fasta_seqs.txt --type AAC --out ../protein_features/AAC_features.txt

python /iFeature-master/iFeature.py --file ../fasta_seqs.txt --type CTDC --out ../protein_features/CTDC_features.txt

python /iFeature-master/iFeature.py --file ../fasta_seqs.txt --type CTDT --out ../protein_features/CTDT_features.txt

python /iFeature-master/iFeature.py --file ../fasta_seqs.txt --type CTDD --out ../protein_features/CTDD_features.txt 

python /iFeature-master/iFeature.py --file ../fasta_seqs.txt --type Moran --out ../protein_features/Moran_Correlation_features.txt

python /generate_protein_features/CTD_concate.py --ctdc_path --ctdt_path --ctdd_path --out ../CTD_features.txt 

python /iFeature-master/iFeature.py --file ../fasta_seqs.txt --type PAAC --out ../protein_features/PAAC_features.txt
```
then you will get:

   ---> protein_features/AAC_features.txt

   ---> protein_features/CTD_features.txt

   ---> protein_features/CTDC_features.txt

   ---> protein_features/CTDD_features.txt

   ---> protein_features/CTDT_features.txt

   ---> protein_features/Moran_Correlation_features.txt

   ---> protein_features/PAAC_features.txt



### 3. Generating drug affinity matrix and target affinity matrix 
Running: 
```
python generate_drug_affinity_mat.py
```
---> drug_affinity_mat.txt 
```
python generate_target_affinity_mat.py
```
---> target_affinity_mat.txt 

### 4. Check whether the above steps are correct 
*(Example: KIBA dataset)*

- **`KIBA`**
  - **drug_features:**
    - MACC_features.xlsx
    - Mogan_features.xlsx
    - Topological_features.xlsx
  - **protein_features:**
    - AAC_features.txt
    - CTD_features.txt
    - CTDC_features.txt
    - CTDD_features.txt
    - CTDT_features.txt
    - Moran_Correlation_features.txt
    - PAAC_features.txt
  - **drugs.xlsx**
  - **dti_list.xlsx**
  - **dti_mat.xlsx**
  - **targets.xlsx**


### 5. Setting Hyperparameters, training, and testing the Model
Running: 

balanced_situatuion on KIBA
```
python main.py --root_path "E:/SOC-DGL/Data
                  --dataset KIBA
                  --device cuda:0
                  --n_splits 10 
                  --drug_sim_file drug_affinity_mat.txt
                  --target_sim_file target_affinity_mat.txt
                  --dti_mat dti_mat.xlsx
                  --hgcn_dim 3000
                  --hidden_dim 1500
                  --K 200
                  --alpha 0.1
                  --w 0.8
                  --dropout 0.1
                  --epoch_num 1500
                  --lr 0.00003
                  --topk 1 
``` 
imbalanced_situatuion on KIBA 
```
python main.py --root_path "E:/SOC-DGL/Data
                  --dataset KIBA
                  --device cuda:0
                  --n_splits 10 
                  --drug_sim_file drug_affinity_mat.txt
                  --target_sim_file target_affinity_mat.txt
                  --dti_mat dti_mat.xlsx
                  --hgcn_dim 3000
                  --hidden_dim 1300
                  --K 200
                  --alpha 0.35
                  --w 0.2
                  --pos_weight 0.6
                  --dropout 0.1
                  --epoch_num 1500
                  --lr 0.00005
                  --topk 10  
```

balanced_situatuion on davis 
```
python main.py --root_path "E:/SOC-DGL/Data
                  --dataset davis
                  --device cuda:0
                  --n_splits 10 
                  --drug_sim_file drug_affinity_mat.txt
                  --target_sim_file target_affinity_mat.txt
                  --dti_mat dti_mat.xlsx
                  --hgcn_dim 1000
                  --hidden_dim 200
                  --K 200
                  --alpha 0.4
                  --w 0.2
                  --dropout 0.2
                  --epoch_num 3000
                  --lr 0.0003
                  --topk 1 
```

imbalanced_situatuion on davis 
```
python main.py --root_path "E:/SOC-DGL/Data
                  --dataset davis
                  --device cuda:0
                  --n_splits 10 
                  --drug_sim_file drug_affinity_mat.txt
                  --target_sim_file target_affinity_mat.txt
                  --dti_mat dti_mat.xlsx
                  --hgcn_dim 1000
                  --hidden_dim 3500
                  --K 200
                  --alpha 0.4
                  --w 0.2
                  --pos_weight 0.5
                  --dropout 0.01
                  --epoch_num 2100
                  --lr 0.0002
                  --topk 10 
```
balanced_situatuion on BindingDB
```
python main.py --root_path "E:/SOC-DGL/Data
                  --dataset BindingDB
                  --device cuda:0
                  --n_splits 10 
                  --drug_sim_file drug_affinity_mat.txt
                  --target_sim_file target_affinity_mat.txt
                  --dti_mat dti_mat.xlsx
                  --hgcn_dim 2000
                  --hidden_dim 100
                  --K 5
                  --alpha 0.15
                  --w 0.5
                  --dropout 0.00001
                  --epoch_num 900
                  --lr 0.0000045
                  --topk 1 
```
imbalanced_situatuion on BindingDB 
```
python main.py --root_path "E:/SOC-DGL/Data
                  --dataset BindingDB
                  --device cuda:0
                  --n_splits 10 
                  --drug_sim_file drug_affinity_mat.txt
                  --target_sim_file target_affinity_mat.txt
                  --dti_mat dti_mat.xlsx
                  --hgcn_dim 1200
                  --hidden_dim 100
                  --K 5
                  --alpha 0.4
                  --w 0.8
                  --pos_weight 0.2
                  --dropout 0.001
                  --epoch_num 750
                  --lr 0.000006
                  --topk 10 
```
balanced_situatuion on DrugBank 
```
python main.py --root_path "E:/SOC-DGL/Data
                  --dataset DrugBank
                  --device cuda:0
                  --n_splits 10 
                  --drug_sim_file drug_affinity_mat.txt
                  --target_sim_file target_affinity_mat.txt
                  --dti_mat dti_mat.xlsx
                  --hgcn_dim 3000
                  --hidden_dim 50
                  --K 50
                  --alpha 0.4
                  --w 0.9
                  --dropout 0.5
                  --epoch_num 900
                  --lr 0.0001
                  --topk 1 
```
  
imbalanced_situatuion on DrugBank  
```
python main.py --root_path "E:/SOC-DGL/Data
                  --dataset DrugBank
                  --device cuda:0
                  --n_splits 10 
                  --drug_sim_file drug_affinity_mat.txt
                  --target_sim_file target_affinity_mat.txt
                  --dti_mat dti_mat.xlsx
                  --hgcn_dim 3000
                  --hidden_dim 50
                  --K 50
                  --alpha 0.6
                  --w 0.9
                  --pos_weight 0.1
                  --dropout 0.01
                  --epoch_num 1200
                  --lr 0.00002
                  --topk 10 
```
   
