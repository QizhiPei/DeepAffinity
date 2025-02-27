# Data and data-curation scripts for ["Explainable Deep Relational Networks for Predicting Compound--Protein Affinities and Contacts" (Karimi, Wu, Wang and Shen, JCIM 2020)](https://pubs.acs.org/doi/abs/10.1021/acs.jcim.0c00866)

Data for explainable prediction of compound-protein affinities: 4446 pairs between 3672 compounds and 1287 proteins, labeled with both inter-molecular affinity (pKd/pKi) and residue-atom contacts/interactions.  

## Table of contents:
* **data.tar.gz**: Please download from https://drive.google.com/file/d/1_ZHPGkE_iqt3B-UskuBI8XsMhQqVgNMW/view?usp=sharing. It contains the supervised learning datasets including:
  * Intra-protein residue-residue contact map matrices (sequence-predicted, used for regularizing joint attentions)
  * Protein-compound residue-atom interaction matrices
  * JNLP scripts for downloading PDB files
  * Split dataset: Includes data for training, test and three generalization datasets (new protein, new compound, and both new), each of which contains 
    * *_acc: solvent accessibility file generated by [SCRATCH](http://scratch.proteomics.ics.uci.edu/)
    * *_inter: interaction matrix file name of corresponding protein-compound pair
    * *_kd: pKd/pKi labels (Kd/Ki are in the unit of M before taking logarithm) 
    * *_seq: protein amin-acid sequences
    * *_smi: compound sequences in canonical SMILE format from PubChem (we used RDkit to convert them to graphs)
    * *_sps: protein sequences in SPS format from [DeepAffinity](https://github.com/Shen-Lab/DeepAffinity) (You don't need it for DeepRelations and most of DeepAffinity+ models)
    * *_uid: Uniprot ID of proteins 
    
* Also included in **data.tar.gz** are those data for 
   * 5 case studies for affinity and contact prediction (merged_data/case_study)
   * 2 case studies for the study of structure-activity relationship (sar.tar.gz)
    
* **scripts for data curation**: Contain scripts for generating intra-protein residue-residue contact matrix from sequence-predicted contact map files and protein-compound residue-atom interaction matrix (intermolecular contacts) from interaction data.  
   * The intra-protein contact maps are used for reguarizing our model during training and not needed during inference.  
   * The inter-molecular contacts are used, together with affinity values, as labels in training.  

## Usage of data curation scripts
* **Intra-Protein Residue-Residue Contact Map Matrix**:
  1. Calculate contact map score from [RaptorX](http://raptorx.uchicago.edu/ContactMap/). They will need the protein sequence in FASTA format(as the example shown in [final_seq.txt](./final_seq.txt)) as input to calculate the contact map score. You may select the "check here if only contact/distance matrices are needed" checkbox on the server to make it faster. After finished, you will get a folder containing multiple files. We only need the contactmap file which contains the score of contacts on each position like [seq1.txt](./seq1.txt).
  2. Please put all generated contact map files into one folder and name it as "contact_file"
  3. Prepare the file containing those sequences and their corresponding Uniprot ID like [uid_seq](./uid_seq). The UID should be the same as the ones used in your dataset.
  4. Put "final_seq.txt", "uid_seq" and "contact_file" under the same folder with the script and run by
  ```
  python convert_contact_matrix.py
  ```
  In the end, you should get a folder named "contact_matrix", which is the contact matrix you need.  

* **Protein-Compound Residue-Atom Interaction Matrix**:
  1. Download interaction data from [PDBsum](http://www.ebi.ac.uk/thornton-srv/databases/cgi-bin/pdbsum/GetPage.pl?pdbcode=index.html). 
   * You may download it automatically by our script. In this case, you need to prepare two files. The "uid_het_cid_useq_smi_ki" contains Uniprot ID of proteins, its corresponding HET ID and Pubchem ID of compounds and protein sequence from Uniprot. Each line has one pair in order of "Uniprot_ID,HET,Pubchem_ID,Uniprot_seq" and use "Tab" as delimiter. The "pid_uid" contains PDB ID and Uniprot ID and use "Tab" as delimiter. After this, you can download raw interaction file by:
    ```
    python get_interaction_data.py
    ```
   * If there are few examples and you would like to download them manually, you can directly download them from PDBsum. One [example](http://www.ebi.ac.uk/thornton-srv/databases/cgi-bin/pdbsum/GetLigInt.pl?pdb=1kfv&ligtype=01&ligno=01) of raw interaction file.
  2. Download PDB files and save them in folder named "pdb". You may download them by creating "pdb" folder and run two .jnlp scripts in it. Since the PDB data here is the same as the one we used for our supervised learning dataset, you only need download them once and copy/move them here.
  3. Align indices between the Uniprot sequence and interaction data. Since we used whole protein sequences from Uniprot and there are only partial structure data from PDB, we need to adjust the indices of interaction data to align with our sequences. If you are using sequences from PDB files, you may skip this step. Run the script by"
   ```
   python protein_contact_shift.py
   ```
  You are expected to see a new folder "interaction_shifted"
  4. Convert raw interaction data into matrix. Simply run script by:
   ```
   python file2matrix.py
   ```
  You are expected to see the result in folder "interaction_matrix"
  
  
## Citation:
If you find the data or/and the scripts useful, please consider citing our work as follows 

```
@article{karimi2020explainable,
  title={Explainable Deep Relational Networks for Predicting Compound--Protein Affinities and Contacts},
  author={Karimi, Mostafa and Wu, Di and Wang, Zhangyang and Shen, Yang},
  journal={Journal of Chemical Information and Modeling},
  volume={61},
  number={1},
  pages={46--66},
  year={2020},
  publisher={ACS Publications}
}
```
