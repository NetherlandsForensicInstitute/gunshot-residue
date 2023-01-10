# Gunshot residue comparisons

This repository contains a dataset from the Netherlands Forensic Institute for gunshot residue comparisons. It contains data from cases as well as samples created for research purposes. All information that could be identifying of a case has been removed and replaced by a hash. The relationships between the tables (and their rows) remain intact. 

This dataset can be used to reproduce the analysis in [T. Matzen et al. "Objectifying evidence evaluation for gunshot residue comparisons using machine learning on criminal case data." Forensic science international 335 (2022): 111293](https://www.sciencedirect.com/science/article/abs/pii/S0379073822001232). Please cite this paper if you are using the dataset. 

If you have any questions about the dataset, please get in touch with Kim de Bie at the Netherlands Forensic Institute via k.de.bie@nfi.nl.  

The dataset contains four tables. These are stored as csv files in the folder `data`. The table `particle` has been split into 14 separate files (because of file size restrictions), where the integer in the file name indicates the maximum stub id in the file.
  
Below, each table and the relevant columns are described. 
 

## stub

Traces were collected using stubs, which is an aluminum holder covered with a conductive adhesive layer. The stub table contains all the stubs that were taken for different projects, cases and sampling locations.  

| column  | description   |
| :------------ | :------------ |
| id | id of the stub (unique for each row)  |
| sample_name  | a description of the sample, such as the sampling location (hashed)   |
| project  | the name of the case or research project (hashed)  |
| type_project  | Whether the stub originates from a case (‘zaak’) or from a research project (‘R&D’)  |
| ammo_type  | ammo type (hashed), not always specified   |
| sample_type  | type of sample, not always specified    |
| sample_source  | source of sample, not always specified   |

## particle 

This table contains the elemental composition of the measured particles for all the stubs. Only relevant elements are recorded (see paper).

| column | description |
| :------------ | :------------ |
|stub_id |id of the stub (usually there are multiple particles, so multiple rows, for each stub) |
| particle_id | id of the particle (unique for each row) |
| relevance_class | particles are assigned into predefined particle classes based on the elemental composition of the particle (see paper) |
| ac to zr | percentage per element of the total particle |
| merged_relevance_class | the relevance classes were grouped to reduce the number of distinct classes. The rules for merging the relevance classes are described below.|

Rules for merging the relevance classes (as defined by NFI gunshot residue experts):

| merged\_relevance | relevance\_class         |
| :----------------- | :------------------------ |
| Ba                | Ba + BaSi +BaSr          |
| BaAl              | BaAl + BaAlS             |
| BaCaSi            | BaCaSi + BaCaSiS         |
| BaSb              | BaSb + BaSbSn            |
| CuZn              | CuZn                     |
| Pb                | Pb                       |
| PbBa              | PbBa + PbBaSn            |
| PbBaSb            | PbBaSb+PbBaSbSn+PbBaSbSr |
| PbSb              | PbSb + PbSbSn            |
| Sb                | Sb + SbSn                |
| Sr                | Sr                       |
| ZnTi              | ZnTi                     |
| Hg                | Hg                       |
| TiZnGd            | TiZnGd                   |
| GaCuSn            | GaCuSn                   |

## source

The source table is used to create same and different shot/ammo type pairs, the type of group\_by is used to decide which stubs we consider same and different source. For example, in the shots\_by\_zaak grouping, each stub belonging to the same case has the same source\_id (grouping), and so are considered same source samples; for the hulzen\_by\_ammo\_type grouping all stubs from the same ammo_type belong to the same source. Stubs can be linked with their source through the stub\_source table and the group\_by field can be used to decide which samples are considered same and different source. A source can also have a cluster, which is the case or research project.  
 
| column    | description                                                                                                            |
| :--------- | :---------------------------------------------------------------------------------------------------------------------- |
| id        | id of the source (unique for each row)                                                                                 |
| name      | name of the source, hashed                                                                                             |
| cluster   | name of the cluster, hashed                                                                                            |
| group\_by | three possible groupings are used (so this column can take one of these three values). The levels are described below. |


| group\_by                 | description                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hulzen\_by\_ammo\_type    | Only cartridge data is used. Cartridges are grouped by ammo\_type. Both case and research samples are used for this grouping. Each cluster is either the case name or the name of the research project (from the \`name\` column).                                                                                                                                         |
| shots\_by\_zaak           | The data is grouped by case name (from the \`name\` column); the cluster is not used.                                                                                                                                                                                                                                                                                      |
| shots\_by\_sample\_source | Group by the source of the data, per case. The full cases are the clusters. This means we regard all samples from the victim in a case as being the same shot, and make no statements about whether other samples in that case are the same as the victim samples (or whether they are from the suspect). \[This was the grouping that was used in [Matzen et al. (2022)](https://www.sciencedirect.com/science/article/abs/pii/S0379073822001232) .\] |

## stub_source 

This table can be used to link the stubs to their source. 

| column     | description                                                     |
| :---------- | :--------------------------------------------------------------- |
| id         | id of the stub\_id/source\_id combination (unique for each row) |
| stub\_id   | id of the stub                                                  |
| source\_id | id of the source                                                |
