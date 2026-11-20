# Strategic Patient Risk Stratification & Readmission Predictive Modeling
**Vitality Health Network (VHN) — Health Informatics Consulting Project**  
*Module: ITS 2122 - Python for Data Science & AI (Semester 3)*

---

## 📌 Executive Overview
Vitality Health Network (VHN) faces significant financial penalties under the Hospital Readmissions Reduction Program (HRRP) due to an elevated 30-day readmission rate among diabetic patients. 

This project analyzes over 100,000 clinical encounters from 130 US hospitals (1999–2008) to uncover why readmissions occur and identify which patients are at high risk using Python, web scraping, exploratory data analysis, and clinical risk scoring.

---

## 📂 Repository Structure

```text
├── data/
│   ├── raw/
│   │   ├── diabetic_data.csv          # Original 10-year clinical dataset
│   │   └── IDs_mapping.csv            # Relational mapping for IDs
│   └── processed/
│       ├── 01_cleaned.csv             # Phase 1: Cleaned dataset (deceased removed, types fixed)
│       ├── 02_enriched.csv            # Phase 2: Enriched with scraped ICD-9 disease names
│       ├── 04_with_vci.csv            # Phase 4: Final dataset with VCI scores & risk levels
│       └── icd9_top20.csv             # Backup table of scraped top 20 ICD-9 descriptions
├── notebooks/
│   └── VHN_main.ipynb                 # Master Jupyter notebook containing all 4 phases
├── .gitignore
└── README.md
```

---

## 🔄 Project Workflow (Phased Execution)

### Phase 1: Data Ingestion & Clinical Sanitation
- **Standardized Null Values**: Treated `?` as `NaN` while preserving clinically valid `'None'` test outcomes (`max_glu_serum`, `A1Cresult`).
- **Data Quality Audit**: Identified that `weight` is missing in 96.9% of records; dropped the column and documented as a data quality limitation.
- **Handled Deceased Patients**: Consulted `IDs_mapping.csv` to dynamically identify discharge codes for "Expired" (11, 19, 20, 21), safely removing 1,652 deceased encounters.
- **Data Deduplication & Typing**: Confirmed 0 exact duplicates and cast identifier columns to `category`.

### Phase 2: Data Enrichment via Web Scraping
- **Target Selection**: Identified the top 20 most frequent `diag_1` ICD-9 codes, accounting for **50.8%** of all patient encounters.
- **Code Standardization**: Standardized codes with leading zero padding (`zfill(3)`).
- **Web Scraper**: Built a scraper using `requests` and `BeautifulSoup` targeting `icd9.chrisendres.com` with robust error handling and a 1-second delay (`time.sleep(1)`) for ethical scraping.
- **Enrichment**: Mapped disease descriptions to create `Primary_Diagnosis_Desc`, labeling unmapped codes as `"Other"`.

### Phase 3: Exploratory Data Analysis (EDA)
- **Readmission Imbalance**: Identified that 30-day readmission (`<30`) occurs in only **11.3%** of cases, showing the necessity of evaluating group rates rather than raw counts.
- **Demographic Insights**: Found that **67%** of patients are 60 years or older. Evaluated readmission rates across race and gender.
- **Medication Regimens**:
  - Patients on **Insulin only** have the highest 30-day readmission rate (**13.2%**), followed by Insulin + oral (**11.2%**), Oral only (**10.2%**), and No medication (**9.8%**).
  - Patients with medication dosage adjustments during stay (`change = 'Ch'`) showed higher readmission rates (**12.0%** vs **10.8%**), indicating clinical instability.
- **Operational Metrics**:
  - Found a weak correlation between length of stay and number of lab procedures ($r = 0.32$).
  - Identified correlation pairs to monitor multicollinearity (`num_medications` & `time_in_hospital`: $r = 0.46$).
  - Discharge destination showed significant risk disparity: **Skilled Nursing Facility (14.7%)** vs **Home (9.3%)**.

### Phase 4: Feature Engineering — Vitality Complexity Index (VCI)
Implemented an algorithm adapted from the validated clinical **LACE index** ($L + A + C + E$):
- **L (Length of Stay)**: $<1$ day (0 pts), $1\text{–}4$ days (1 pt), $5\text{–}13$ days (4 pts), $\ge 14$ days (7 pts).
- **A (Acuity)**: Emergency / Trauma Center admission (3 pts), others (0 pts).
- **C (Comorbidity Proxy)**: $<4$ diagnoses (0 pts), $4\text{–}7$ (3 pts), $\ge 8$ (5 pts).
- **E (Emergency History)**: 0 visits (0 pts), $1\text{–}4$ (3 pts), $>4$ (5 pts).

#### Risk Stratification & Model Validation
- **Low Risk** ($\text{VCI} < 7$): **8.8%** 30-day readmission rate
- **Medium Risk** ($\text{VCI } 7\text{–}10$): **11.4%** 30-day readmission rate
- **High Risk** ($\text{VCI} > 10$): **14.7%** 30-day readmission rate

*The VCI successfully stratifies risk: High-risk patients are ~1.7 times more likely to be readmitted within 30 days compared to low-risk patients.*

---

## 🚀 How to Run

1. **Clone / Download the Repository**
2. **Install Required Libraries**:
   ```bash
   pip install pandas numpy matplotlib seaborn requests beautifulsoup4
   ```
3. **Execute the Analysis**:
   - Open Jupyter Notebook or VS Code:
     ```bash
     jupyter notebook notebooks/VHN_main.ipynb
     ```
   - Run all cells sequentially from top to bottom.

---

## 👥 Contributors (Group 3)

This project was developed with dedication and teamwork for the **ITS-2122: Python for Data Science & AI** course:

| # | Contributor | GitHub | Profile Link |
| :---: | :--- | :---: | :--- |
| 1 | **Shaluka Ranhinda** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github)](https://github.com/ShalukaRanhinda) | [@ShalukaRanhinda](https://github.com/ShalukaRanhinda) |
| 2 | **Induni Palliyaguru** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github)](https://github.com/InduniPalliyaguru) | [@InduniPalliyaguru](https://github.com/InduniPalliyaguru) |
| 3 | **Hashini Emalsha** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github)](https://github.com/hashii2002) | [@hashii2002](https://github.com/hashii2002) |
| 4 | **Chathunga Bimsara** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github)](https://github.com/chathunga2007) | [@chathunga2007](https://github.com/chathunga2007) |
| 5 | **Pabodya Nethmi** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github)](https://github.com/pabodyanethmi) | [@pabodyanethmi](https://github.com/pabodyanethmi) |
| 6 | **Isuru Anupama** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github)](https://github.com/ANu-771) | [@ANu-771](https://github.com/ANu-771) |

