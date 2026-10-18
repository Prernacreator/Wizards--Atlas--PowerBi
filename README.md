# The Wizard's Atlas: A Guide to Destiny in Fabricland

An interactive 4-page Power BI report that helps a prospective student compare 10 fictional colleges on **cost and return, career outcomes, academics and student life**.

> Built with Power BI, Power Query and DAX. Dataset: "The College Quest in Fabricland" (Microsoft Fabric learning challenge).

---

## 1. Problem

Choosing a college means balancing money, career prospects, course quality and campus life. These facts usually sit in separate places. This report puts them in one place and answers four questions:

| Page | Name | Question it answers |
|---|---|---|
| 1 | The Treasury | Is the tuition worth the return? Which colleges are "Hidden Gems"? |
| 2 | The Guild Hall | Which colleges lead to the best jobs, salaries and alumni satisfaction? |
| 3 | The Grand Library | Which majors have the best graduation rates, faculty and internship access? |
| 4 | The Common Room | How good is student life: housing, support, clubs and happiness? |

## 2. Data

- **Source file:** `The College Quest in Fabricland Dataset.xlsx` (3 sheets)
- **College_Profiles:** 10 rows, 21 columns (tuition, placement rate, salary, housing, happiness, clubs, events, scholarships and more)
- **Programs_Majors:** 850 rows, 17 columns (program, major category, enrollment, graduation rate, salary, faculty rating, internships)
- **Data Dictionary:** describes every column
- **Major categories:** Alchemy (250 programs), Engineering (250), Leadership (200), Healing (150)

**Data checks:** I checked both tables for missing values and duplicate rows and found none.

## 3. Data model

- Two tables joined by `CollegeID` (**one college to many programs**): `College_Profiles[CollegeID]` to `Programs_Majors[CollegeID]`.
- This lets one filter (for example a kingdom or college) change both college-level and program-level visuals.

## 4. Data preparation (Power Query)

- Promoted the first row to headers on every sheet.
- Set the correct data types (text, whole number, decimal, date, true/false) for each column.
- No rows needed removing because the data was already clean.

## 5. Key DAX measures

The model has about 59 measures. These are the main ones.

| Measure | What it does |
|---|---|
| **ROI Score** | Expected starting salary (program average x placement rate) divided by tuition. Shows how many dollars of salary each dollar of tuition is expected to return. |
| **Career Success Score** | Weighted score: 40% placement rate, 40% salary (in thousands), 20% alumni satisfaction. |
| **Program Quality Score** | Weighted score: 35% graduation rate, 35% faculty rating, 30% internship availability. |
| **Life Quality Score** | Weighted score of happiness, housing, facilities and student support. |
| **Perfect College Score** | Overall score: 25% each of ROI, career, program quality and life quality. |
| **Is Hidden Gem** | Returns 1 if a college has below-median tuition and above-median happiness. |
| **Internship Percentage** | Share of programs that offer internships, using `DIVIDE` to avoid divide-by-zero errors. |
| **Rank ROI / Rank Career Success** | Ranks colleges with `RANKX`. |

Example (ROI Score):

```dax
ROI Score =
VAR AvgSalary     = AVERAGE(Programs_Majors[AvgStartingSalary])
VAR PlacementRate = AVERAGE(College_Profiles[AvgPlacementRate])
VAR Tuition       = AVERAGE(College_Profiles[TuitionFee])
RETURN
    IF(Tuition > 0,
       ((AvgSalary * (PlacementRate / 100)) / Tuition) * 100,
       BLANK())
```

Other techniques used: `CALCULATE` with filters, `SWITCH(TRUE())` for labels and conditional colours, `SELECTEDVALUE` and `HASONEVALUE` for dynamic titles, and variables (`VAR`) for readable formulas.

## 6. Key insights

All numbers below come from the dataset (10 colleges, so patterns are indicative only).

1. **ROI is fairly flat across colleges.** ROI ranges from **8.86 to 11.51** (average about 10.2). Ironforge Institute of Engineering gives the best return (11.51) and Silverleaf University of Healing the lowest (8.86).
2. **Higher cost goes with better outcomes.** Tuition has a strong positive relationship with placement rate (correlation about 0.94) and happiness (about 0.81). Expensive colleges deliver more, but not proportionally more per dollar.
3. **Ironforge is the only "Hidden Gem" by my definition.** It is the only college with tuition below the median ($7,350) and happiness above the median (4.45).
4. **Majors differ a lot.** Leadership has the highest average starting salary (about $89,982), the highest graduation rate (92.2%) and the best faculty rating (4.5). Engineering has the largest enrollment (157,904 students) but the lowest faculty rating (3.6) and a lower average salary (about $75,895).
5. **Clubs and events do not clearly raise happiness here.** The correlation of happiness with clubs is about -0.21 and with events about -0.10. With only 10 colleges, I treat this as "no clear link" rather than a conclusion.

## 7. Recommendations

- A student who wants the **best return per dollar** should look at Ironforge, Crimson Peak and Emberfall.
- A student who wants the **best experience and outcomes** and can afford it should look at Starfall and Silverleaf.
- Engineering applicants should compare faculty ratings before choosing a college, since this major scores lowest.

## 8. Limitations

- The data is **fictional** and covers only **10 colleges**, so results show the method, not real-world facts.
- Every program has internships available (`InternshipAvailable` is TRUE in all 850 rows), so internship access does not differentiate colleges.
- Two salary fields exist: the college-level `AvgStartingSalary` ($45K to $72K) and the program-level one (used in the dashboard, about $82K on average). The report uses the **program-level** salary.

## 9. How I used AI

I used **Claude (Anthropic)** as my AI assistant across this project, as a guide while learning and building. I made the decisions and built the report in Power BI myself.

| Task | AI tool | What AI did | What I did |
|---|---|---|---|
| Understanding what students need | Claude | Helped me list the questions students ask when choosing a college (cost, jobs, courses, campus life) | Turned them into the 4 report pages |
| Data preparation | Claude | Explained how to check and clean data in Power Query | Loaded the data, set data types and checked for missing values and duplicates |
| DAX measures | Claude | Explained formulas and gave draft versions | Built the measures in Power BI and tested the results |
| Report layout and visuals | Claude | Suggested chart types and page structure | Chose the final design and built each page |
| Storytelling text ("The Oracle Says" boxes) | Claude | Helped with wording | Checked the claims against the data |
| This README | Claude | Helped organise the structure | Verified the numbers and added my own findings |

## 10. What I learned

- How to prepare and check data before analysis (data types, missing values, duplicates).
- How to write DAX measures, including `CALCULATE`, `VAR`, `SWITCH`, `DIVIDE` and `RANKX`, and how to turn a business idea (like "return on investment") into a formula.
- How to understand what students need when choosing a college, and how to turn that into questions the data can answer.
- How to build a dashboard that tells a story, so that a student can compare colleges and decide what suits them best.
- How to work with an AI assistant while still checking the results myself.


## 11. Files in this repository

- `The_Wizard_atlas.pbit` - Power BI template (open in Power BI Desktop; you will be asked to point to the Excel file)
- `The College Quest in Fabricland Dataset.xlsx` - source data
- `screenshots/` - report page images

## 12. Author

**Prerna Keshari** - aspiring Data Analyst | Power BI, SQL, Excel, Python basics
LinkedIn: [www.linkedin.com/in/prerna-keshari-515706357]
