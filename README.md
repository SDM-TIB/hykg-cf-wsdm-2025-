# HyKG-CF: A Hybrid Approach for Counterfactual Prediction using Domain Knowledge (WSDM 2025)
All code and data are available from https://figshare.com/s/c15551e0f42955329fb1?file=50156028. 

# Instructions of reproducing the experimental results
1. The unit database is stored in `data/KG.csv`. The corresponding code for retrieving the data is in `data/query_data_WSDM.ipynb`.
2. The horn rules (for generating counterfactuals) are generated using `data/data_processing.ipynb` section: "# Rule Mining using Expert Causal Graph".
3. The causal discovery experiments and the corresponding results are presented in `causal/causal_discovery.ipynb`.
4. The counterfactual prediction experiments are done using `causal/counterf_prediction_command.py` with commands: 
```
python counterf_prediction_command.py -rule cg -pca 0.9
python counterf_prediction_command.py -rule cg -pca 0.5
python counterf_prediction_command.py -rule cg -pca 0.1
```
5. The results of counterfactual prediction are presented in `causal/show.ipynb`. 

# LLM prompts used in our experiments:

### LLM prompt for **Baseline2**

\# ROLE \#
Act as an expert on identifying causal relationships between properties within a knowledge graph.

\# CONTEXT \#
You are examining the causal relationships among properties within the knowledge graph.

1. Property: age;
2. Property: episodeType;
3. Property: familyGender;
4. Property: hasBio;
5. Property: hasFamilyCancerType;
6. Property: hasSmokingHabit;
7. Property: locatedIn;
8. Property: relapse;
9. Property: sex;
10. Property: stage;


\# OBJECTIVES \#
1. Analyze all these properties in the # CONTEXT # to identify all possible causal relationships among them.
2. Each identified causal relationship should be supported by evidence from academic studies, referenced using title, DOI or PMCID to ensure confidentiality and verify the reliability of the studies.

\# INSTRUCTIONS \#
1. Examine and understand each property carefully.
2. Identify all possible causal relationships among the properties in the # CONTEXT #; each identified causal relationship must be supported by the title, DOI or PMCID of relevant academic research.
3. Provide, in a code box, the identified causal relationships in the previous step as a Python set of tuples, each with format: ([A], [B]) representing a causal relationship from property [A] to property [B]. 
4. Do not introduce directed loops along the causal relationships.


### LLM prompt for **Baseline3** and **HyKG-CF**

\# ROLE \#
Act as an expert in lung cancer research, focusing on identifying causal relationships between properties within a knowledge graph of non-small cell lung cancer (NSCLC) patients.

\# CONTEXT \#
You are examining the causal relationships among properties of `NLCPatient` class, within a knowledge graph, 
with rich metadata that describe their human-understandable label (`rdfs:label`), human-understandable meaning (`rdfs:comment`), domain (`rdfs:domain`), and range (`rdfs:range`):

1. Property: age
   - `rdfs:label`: "age"
   - `rdfs:comment`: "The age of NSCLC patients, classified as 'Young' (<= 50 years) or 'Old' (> 50 years)."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:integer`;
2. Property: episodeType
   - `rdfs:label`: "treatmentment episode tpye"
   - `rdfs:comment`: "The type of treatment episodes undergone by NSCLC patients, which could include categories such as: 'OralTargetedTherapy', 'Immunotherapy', 'Oral_and_intravenous_chemotherapy', 'Chemotherapy', 'Concomitant_QT-RT', 'QT_intravenous', 'Sequential_QT-RT', 'OralChemotherapy', 'Intravenous_chemotherapy_+_immunotherapy', 'Other', 'Adjuvant_QT-RT', 'HormonalTherapy', 'Neoadjuvant_QT-RT'."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`;
3. Property: familyGender
   - `rdfs:label`: "family gender"
   - `rdfs:comment`: "The gender of NSCLC patients' cancered family antecedents, either 'Women' or 'Men'."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`;
4. Property: hasBio
   - `rdfs:label`: "biomarker test result"
   - `rdfs:comment`: "The biomarker test results of NSCLC patients, including ALK or EGFR; 'other biomarker' includes MET, HER2, FGFR1, KRAS, RET, PDL1, HER2Mut, ROS1, BRAF."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`;
5. Property: hasFamilyCancerType
   - `rdfs:label`: "family cancer type"
   - `rdfs:comment`: "The type of cancer in the family of NSCLC patients, which can be 'Major' representing cancer types in ('Lung', 'Colorectal', 'Head and neck', 'Uterus/cervical', 'Esophagogastric', 'Prostate'), 'Breast' cancer or 'Minor' which represents other cancer types."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`;
6. Property: hasSmokingHabit
   - `rdfs:label`: "smoking habits"
   - `rdfs:comment`: "The smoking habits of NSCLC patients, classified as 'Non-Smoker' or 'Smoker'."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`;
7. Property: locatedIn
   - `rdfs:label`: "hospital location"
   - `rdfs:comment`: "The geographic location or region in Spain where the hospital (where the NSCLC patient recieves diagnosis) is located."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`;
8. Property: relapse
   - `rdfs:label`: "relapse or progression"
   - `rdfs:comment`: "Indicates whether the NSCLC patient has experienced a relapse or progression of the lung cancer after initial treatment, classified as either 'Relapse' or 'Progression'."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`;
9. Property: sex
   - `rdfs:label`: "gender"
   - `rdfs:comment`: "The gender of NSCLC patients, either 'male' or 'female'."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`;
10. Property: stage
   - `rdfs:label`: "cancer stage"
   - `rdfs:comment`: "The diagnosed stage of lung cancer of the patient, classified according to the TNM staging system (e.g., Stage I, Stage II, Stage III, Stage IV) which indicates the severity and spread of the cancer."
   - `rdfs:domain`: `NLCPatient`
   - `rdfs:range`: `xsd:string`.

Here are some associations between properties recognized in the knowledge graph:
`associations = [('hasBio', 'hasFamilyCancerType'), ('locatedIn', 'relapse'), ('hasFamilyCancerType', 'hasSmokingHabit'), ('hasBio', 'locatedIn'), ('locatedIn', 'hasSmokingHabit'), ('hasBio', 'stage'), ('sex', 'relapse'), ('relapse', 'stage'), ('sex', 'hasSmokingHabit'), ('age', 'relapse'), ('age', 'hasSmokingHabit'), ('hasBio', 'familyGender'), ('familyGender', 'relapse'), ('hasBio', 'relapse'), ('hasBio', 'hasSmokingHabit'), ('age', 'hasBio'), ('familyGender', 'hasSmokingHabit'), ('hasBio', 'episodeType'), ('hasBio', 'sex'), ('relapse', 'hasSmokingHabit'), ('hasSmokingHabit', 'stage'), ('episodeType', 'relapse'), ('episodeType', 'hasSmokingHabit'), ('hasFamilyCancerType', 'relapse')]`,
where each tuple denote the association between two properties of `NLCPatient` class.

\# OBJECTIVES \#
1. Analyze all these properties in the # CONTEXT # to identify all possible causal relationships among them.
2. Each identified causal relationship should be supported by evidence from academic studies, referenced using title, DOI or PMCID to ensure confidentiality and verify the reliability of the studies.

\# INSTRUCTIONS \#
1. Examine and understand the metadata of each property carefully. 
2. For each tuple ([A], [B]) in the `association` list, if there exists causation between properties [A] and [B]? if exists, what is the causal direction.
3. Identify all possible causal relationships within the `association` list where the properties is described in the # CONTEXT #; each identified causal relationship must be supported by the title, DOI or PMCID of relevant academic research.
4. Provide, in a code box, the identified causal relationships in the previous step as a Python set of tuples, each with format: ([A], [B]) representing a causal relationship from property [A] to property [B].
5. Do not introduce directed loops along the causal relationships.	

 

