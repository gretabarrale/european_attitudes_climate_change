# European Attitudes Towards Climate Change

Interactive **Power BI** dashboard exploring how people across Europe perceive climate change and how far their beliefs translate into concern and action. Based on **European Social Survey (ESS) Round 8** data (2016–17).

## Project goal

Final assignment of the Data Visualisation and Power BI module of the Master in Data Analytics at Boolean. The goal is building a dashboard for a (fictional) social policy institute that lets users explore European attitudes towards climate change in 2016–17 and highlights the main insights.

Almost everyone agrees the climate is changing, but does that belief turn into real concern and into concrete behaviour? The dashboard explores this gap through three questions:

- **Belief:** do respondents think the climate is changing?
- **Concern:** how worried are they about climate change compared with other energy-related issues?
- **Action:** do they believe their actions make a difference, and how do they behave in their daily energy use?

## Data

- **Source:** [European Social Survey](https://www.europeansocialsurvey.org/), Round 8 (2016–17), module on climate change and energy
- **Sample:** 44,344 respondents from 23 countries taking part in the ESS (22 European countries and Israel)
- **Main variables:** belief in climate change (`clmchng`), worry about climate change and energy issues (`wrclmch`, `wrenexp`, `wrpwrct`…), perceived efficacy of individual, collective and government action (`ownrdcc`, `lklmten`, `gvsrdcc`), energy-saving behaviour (`rdcenr`, `eneffap`, `cflsenr`) and preferred electricity sources (`elgsun`, `elgwind`, `elgcoal`…)

The starting point was a **partially cleaned extract** provided for the assignment, limited to the climate change questions, with country codes already converted into country names and part of the missing-value codes replaced. The data is not included in this repository: the original ESS data can be downloaded for free from the ESS website after registration.

## Tools

- **Power BI Desktop**
- **Power Query** for data cleaning and transformation
- **DAX** for measures and calculated columns

## Process

### 1. Data cleaning (Power Query)

- Removed technical columns not needed for the analysis (`name`, `essround`, `edition`, `proddate`).
- Renamed the original ESS variable codes into readable labels (e.g. `wrclmch` → *Worry About Climate Change*).
- **Checked the remaining missing-value codes against the ESS documentation.** The extract still contained codes such as *Not applicable*, *Refusal*, *Don't know* and *No answer* in several variables: about 1,100 invalid answers in the belief question and about 1,700 in the climate worry question. Since these codes change depending on the scale, I replaced them with `null` column by column: 6–9 on the short scales (1–4 and 1–5) and 77–99 on the 0–10 scales, so that valid answers such as 6, 7 or 8 on a 0–10 scale were kept.
- Filtered out rows without a respondent ID and set the correct data types.
- **Unpivoted** the worry and energy-source variables into a long format (category / value), so each topic can be compared in a single chart.

### 2. Data modelling

- Split the dataset into **thematic tables**: *Climate Beliefs*, *Worry*, *Policy & Efficacy*, *Energy Behavior*, *Energy Source Preference*.
- Built a **star schema** with a `Country` dimension table connected to every thematic table (one-to-many relationships), so a single country slicer filters the whole report.
- Since the ESS respondent ID is only unique within each country, created a combined key (**Respondent ID + Country**) to identify respondents and remove duplicates when building the `Country` table.

### 3. DAX measures

- **Percentage measures** built with `DIVIDE` and `CALCULATE`, counting only valid answers in the denominator. For example, *% worried about climate change* = respondents answering *very* or *extremely worried* out of all valid answers.
- Answers of **7 or more on the 0–10 scales** are counted as "likely" (e.g. *believing own action makes a difference*).
- **Reversed** the electricity-source scale (`6 - AVERAGE(...)`), because in the ESS questionnaire 1 means *a very large amount*: after the transformation, a higher score means a stronger preference.
- **Calculated columns** with `SWITCH` to turn numeric codes into readable labels for the charts.

### 4. Dashboard design

- Two main pages, **Attitudes** and **Actions**, with navigation buttons.
- On the Actions page, **bookmarks** switch between *Expectations* and *Behaviour*.
- A **country slicer** on every page to explore each country individually.

## Dashboard pages

### Attitudes – *Climate Change: Belief vs. Real Concern*

Compares how many respondents believe the climate is changing with how many are actually worried about it. It also shows the full distribution of answers and ranks climate change against other energy-related worries, such as energy costs and power cuts.

### Actions – *From Intention to Action: Energy Behaviours*

Two views, switched with bookmarks:

- **Expectations:** how much respondents believe that their own actions, people's collective efforts and governments can reduce climate change.
- **Behaviour:** how often respondents try to save energy, how confident they are that they could use less, and how likely they are to buy energy-efficient appliances.

The page also shows which energy sources respondents would prefer for producing electricity.


## Key insights

- **Belief does not mean concern:** 93% of respondents believe the climate is changing, but only 28% say they are very or extremely worried about it.
- **Energy costs come first:** among energy-related worries, energy expenses rank slightly higher than climate change itself.
- **Low sense of efficacy:** only 23% believe their own energy reduction would help, 20% trust governments to act and 14% expect people to limit their energy use collectively. People still act: around 7 in 10 respondents often try to reduce their energy use, and most are likely to buy energy-efficient appliances.
- **Renewables are preferred:** solar, wind and hydro are the most preferred sources of electricity, while coal and nuclear are the least.

## Limitations

- The results are **unweighted**: the measures do not apply the ESS survey weights (e.g. `anweight`), so they describe the sample rather than being fully representative of each country's population.
- Cross-country averages give the same importance to each respondent, so countries with larger samples weigh more on the overall figures.

## How to open the project

Download `dashboard.pbix` and open it with [Power BI Desktop](https://www.microsoft.com/power-bi/desktop) (free). If you do not have Power BI, the screenshots above show the main pages of the dashboard.

---

**Author:** Greta Barrale 