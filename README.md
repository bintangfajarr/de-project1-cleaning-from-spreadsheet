# Example of cleaning data with dummy data
**Using Google Sheets + Python (Pandas)**  

This project demonstrates an end-to-end data-cleaning workflow for a sales transaction dataset containing multiple formatting issues, inconsistencies, and typos.  
The cleaning process is divided into **two major stages**:

1. **Initial Cleaning using Google Sheets (ARRAYFORMULA-based transformation)**
2. **Advanced Cleaning using Python (Pandas) in Google Colab / Jupyter Notebook**

The objective of this project is to produce a **fully clean, consistent, and analysis-ready dataset** 

---

## 1. Raw Dataset Overview  

The raw dataset includes several common data quality issues:

- Inconsistent date formats (`2024/01/03`, `03-01-2024`, `Jan 04 2024`)
- Non-standard quantity values (`1`, `two`, `1 2OO`, `-1`, `-`)
- Incorrect price formats due to typos (`1OOO` → `1000`, `2OO` → `200`)
- Inconsistent capitalization in customer names
- Product names with mixed casing  
- Incorrect or negative total calculations

The raw dataset is stored in Google Sheets under **sales_raw**.
https://docs.google.com/spreadsheets/d/1hB1Nt0u90r9I-ww8eavSRyUKNBG_dhhRPYvuUCfsIKM/edit?usp=sharing

---

#  2. Cleaning in Google Sheets  

Cleaning in Google Sheets focuses on simple but essential normalization using formulas.  
This step ensures the dataset is semi-clean and visually validated before Python processing.

---

## **2.1 Standardizing Date Format**

Convert all date formats into `yyyy-mm-dd`.

```gs
=ARRAYFORMULA(
  IF(ROW(raw_data!B:B)=1,
     "date",
     IF(LEN(raw_data!B:B)=0,
        "",
        IFERROR(
          TEXT(DATEVALUE(REGEXREPLACE(raw_data!B:B,"[.\-]","/")),"yyyy-mm-dd"),
          TEXT(DATEVALUE(raw_data!B:B),"yyyy-mm-dd")
        )
     )
  )
)
```
---
## **2.2 Normalized Customer and Product Name**
```gs
=ARRAYFORMULA(IF(ROW(raw_data!C:C)=1,"customer_name",IF(raw_data!C:C="", "", PROPER(TRIM(raw_data!C:C)))))
```
```gs
=ARRAYFORMULA(IF(ROW(raw_data!D:D)=1,"product",IF(raw_data!D:D="", "", PROPER(TRIM(raw_data!D:D)))))
```
---
## **2.3 Cleaning Quantity**
In this dataset, i convert negative value to positive value
i can add the if statement if needed

```gs
=ARRAYFORMULA(
  IF(ROW(raw_data!E:E)=1,"quantity",
    IF(LEN(raw_data!E:E)=0,"",
      IFERROR(
        ABS(
          VALUE(
            IF(LOWER(TRIM(raw_data!E:E))="one","1",
            IF(LOWER(TRIM(raw_data!E:E))="two","2",
            IF(LOWER(TRIM(raw_data!E:E))="three","3",
            IF(LOWER(TRIM(raw_data!E:E))="four","4",
            IF(LOWER(TRIM(raw_data!E:E))="five","5",
              REGEXREPLACE(TRIM(raw_data!E:E),"[^0-9\-]","")
            )))))
          )
        )
      ,"")
    )
  )
)
```
---
## **2.4 Cleaning Price**
```gs
=ARRAYFORMULA(IF(ROW(raw_data!F:F)=1,"price",
 IF(raw_data!F:F="","",
  VALUE(REGEXREPLACE(TRIM(raw_data!F:F),"[Oo]","0"))
  )))

```
---
## **2.5 Recalculate Total**
```gs
=ARRAYFORMULA(
  IF(ROW(cleaned_data!A:A)=1,"total",
    IF(LEN(cleaned_data!A:A)=0,"",
      IFERROR(VALUE(cleaned_data!E:E) * VALUE(cleaned_data!F:F), "")
    )
  )
)
```
#  3. Cleaning in Python
You can refer to the Jupyter notebook that you uploaded all the Python steps, explanations, and outputs are already visible there. The notebook reflects the full process clearly, so you can review or rerun any part of it as needed.
