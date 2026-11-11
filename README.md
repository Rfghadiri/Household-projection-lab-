# Household projection lab

An interactive, single-file web app that compares five ways of projecting the number of households, runs them side by side on the same population and base year, and extends the result to neighborhood-scale housing assessment.

It is built for planners and demographers who want to see how method choice changes a household projection, and to try their own census data without writing code.

> **Important:** the models are stylised teaching models, not forecasts. Every method is calibrated to the same base year so you can compare how each one responds to the same drivers. For real work, use your own census or register data and validate the results.

## Features

- **Five projection approaches** on one screen, each with a chart, headline figures and notes on how it works, the data it needs, its strengths and its limits.
- **Compare all** tab that overlays the five central projections and tabulates end-year households, change, persons per household, data demand and links to demographic events.
- **Census data input**: enter population and households for two or more census years (for example 2011 and 2016). The base year, base values, population growth and headship trend are fitted from them, and your observed values appear on every chart.
- **Neighborhoods tab (محلات)**: enter or paste data per neighborhood and get a ranked housing assessment: current balance between dwellings and households, projected households, and additional dwellings needed by the target year.
- **Word report export**: builds a `.docx` with the scenario, results, charts, method descriptions, sources and the neighborhood table.
- Interactive charts with hover values, light and dark themes, keyboard-accessible controls and a responsive layout.

## Quick start

There is no build step and no package to install.

1. Download `household-projection-lab.html`.
2. Open it in a current browser (Chrome, Edge, Firefox or Safari). An internet connection is needed for web fonts and the JSZip library (see [Dependencies](#dependencies)).
3. Move the sliders in **Scenario drivers**, switch between the tabs, and open **Enter census years** to add your own data.

### Host it on GitHub Pages

1. Rename `household-projection-lab.html` to `index.html` and commit it to the repository.
2. In the repository, open **Settings > Pages**, choose the branch and the root folder, and save.
3. The app is served at `https://<your-user>.github.io/<your-repo>/`.

## The five approaches

| Tab | Family | What the model does here | Sources |
| --- | --- | --- | --- |
| Headship rates | Static | Population by age group multiplied by age-specific headship rates, held constant or extrapolated | Perreault & George (1989); Mason & Racelis (1992) |
| Size and age headship | Static with demographic drivers | Household size mix tilts with union dissolution, ageing and leaving-home behaviour; households equal persons divided by mean size | Jiang & O'Neill (2004, 2007) |
| Macrosimulation | Dynamic, multistate | Rates move groups of people between four living arrangements each year | van Imhoff & Keilman (1991); Keilman & Brunborg (1995); Zeng et al. (2006) |
| Microsimulation | Dynamic, individual level | Simulated people face yearly random life events; 14 runs of 700 people show the spread | Rogers et al. (2014); Siripanicha & Rashidi (2020) |
| Probabilistic forecast | Uncertainty bands | 300 runs sample growth, headship trend, ageing and yearly shocks; the fan shows percentiles | Alho & Keilman (2010); Scherbov & Ediev (IR-08-002); Christiansen & Keilman (2013) |

Classification of models into static and dynamic, micro and macro follows Gill & Keilman (1990).

## Using the app

### Scenario drivers

| Driver | Default | Used by |
| --- | --- | --- |
| Population growth | 0.6% a year | All methods |
| Population ageing | 0.5 (0 to 1) | All methods |
| Headship trend | +0.10% a year | Headship rates, Probabilistic |
| Leaving parental home | 4% a year | Size and age headship, Macro, Micro |
| Union formation | 7% a year | Macro, Micro |
| Union dissolution | 1.8% a year | Size and age headship, Macro, Micro |
| Uncertainty | x1.0 | Probabilistic |
| Base population | 1,000 thousand | All methods |

Drivers that the selected method does not use are dimmed. The default base year is 2026 and the default horizon is 30 years.

### Census data

Open **Enter census years** and fill one row per census year:

| Year | Population (thousands) | Households (thousands) |
| --- | --- | --- |
| 2011 | 1,000 | 330 |
| 2016 | 1,060 | 365 |

The example numbers are illustrative. Then choose the end year (5 to 50 years after the latest census) and click **Apply to projection**. The app then:

- starts the projection from your latest year, using its population and households as the base for all five methods;
- fits population growth and the headship trend (households per person) as a log-linear trend across every year you enter, so more years give a steadier trend;
- draws your observed households as hollow dots on the charts.

The fitted headship trend also reflects ageing. Lower the **Population ageing** driver if you do not want ageing counted twice. Only totals are used; age-specific headship rates per census year are not supported.

### Neighborhoods tab

Enter one row per neighborhood, either in the table or by pasting rows copied from Excel or a CSV file (tab, semicolon or comma separated, one neighborhood per line):

```text
name, population, households, dwellings, dilapidated %, growth %
```

- Population, households and dwellings must be positive numbers. Dilapidated share and growth are optional. An empty growth cell uses the city population growth driver.
- Persian and Arabic-Indic digits are accepted. If your numbers contain thousands separators, paste tab-separated rows.
- **Load example data** fills ten made-up neighborhoods so you can see the output.

Settings:

- **Headship change from**: which of the five methods supplies the change in households per person.
- **Target vacancy %**: share of dwellings that should stand vacant in a healthy market (default 5%).
- **Also replace dilapidated units**: adds the dilapidated share of each neighborhood's stock to the need.

Calculation, for each neighborhood:

```text
multiplier       = (city households in target year / city households in base year)
                   / (city population in target year / city population in base year)
households_T     = households_now x (1 + neighborhood growth)^years x multiplier
dwellings_needed = households_T x (1 + target vacancy) [+ dilapidated dwellings]
additional       = max(0, dwellings_needed - dwellings_now)
```

Status today compares current vacancy, (dwellings - households) / dwellings, with the target: shortage below zero, tight below the target, high vacancy above three times the target, balanced otherwise.

### Word report

Click **Export Word report**, choose the title, author and sections, then save. The report is an A4 `.docx` with Word headings and embedded charts, always drawn in a light palette.

## How the models work

All five methods are scaled so that base-year households match the base value (your latest census year, or about 44 households per 100 people when no data are entered). The differences you see come from what each method lets the drivers change.

- **Headship rates**: six age groups with base shares 16, 17, 15, 14, 13 and 25% and base headship rates 0.06, 0.36, 0.50, 0.54, 0.56 and 0.58. The ageing driver moves up to ten percentage points of population from the four younger groups to the two oldest by the end of the horizon. Rates are capped at 0.85.
- **Size and age headship**: four household size classes (1, 2, 3 and 4 or more people, with 4.8 as the mean for the last class) and a base mix of 22, 29, 20 and 29%. A driver that combines dissolution, ageing and leaving-home rates tilts the mix toward smaller households over the horizon.
- **Macrosimulation**: four states (with parents, alone, couple, other). The starting distribution is the stationary distribution of the baseline rates. Mortality rises with the ageing driver. Households are alone + couple / 2 + other / 3.
- **Microsimulation**: the same four states and transition rules, applied to individual agents with random draws. The button **Run again with new random draws** changes the seed.
- **Probabilistic forecast**: each run samples growth, headship trend and ageing around the chosen values, plus a yearly shock. The chart shows the median with 50% and 80% bands. The **Uncertainty** driver scales all the spreads.

## Dependencies

The app is one HTML file with inline CSS and JavaScript. It loads two things from the web:

- Google Fonts: Literata and Hanken Grotesk, with system font fallbacks.
- [JSZip](https://stuk.github.io/jszip/) 3.10.1 from cdnjs, used to assemble the `.docx` file.

Without a connection the app still runs with fallback fonts, but Word export will not work.

## Known limitations

- **Word export outside Claude:** the export button appears only when the host page provides a file-saving capability (`window.claude.use("downloads")`, as in a published Claude artifact). In a plain GitHub Pages deployment it stays hidden. To enable it, make two changes in the script:
  1. Show the button on load by setting `$("exp").hidden = false`.
  2. In the click handler for `#go`, replace the `DL.save({...})` call with a standard download:

     ```js
     const a = document.createElement("a");
     a.href = URL.createObjectURL(blob);
     a.download = "household-projection-report.docx";
     a.click();
     URL.revokeObjectURL(a.href);
     ```
- **Stylised parameters:** base age shares, headship rates, size mix and transition rates are illustrative. Replace them with values for your study area.
- **Census input** takes totals only, not age tables.
- **Neighborhood assessment** applies one city-level headship factor to every neighborhood. It has no migration between neighborhoods, land supply, prices or housing types, and no map.
- **Storage:** neighborhood data is kept in your browser's local storage under the key `hpl-nb`. Clear it from the **Clear all** button or your browser settings.
- There is no automated test suite yet.

## Project structure

```text
household-projection-lab.html   the whole app: styles, models, charts, export
README.md                       this file
```

Inside the HTML file the script is organised in this order: state and constants, the five models, chart drawing, controls and tabs, report export, census input, neighborhood assessment.

To add a sixth method, add its key to the `ALL` array, add a text entry to the `A` object, compute its yearly household series in `compute()`, and map it in `series()` and `endV()`.

## Sources

- Perreault, J. & George, M. V. (1989). *Statistical Journal of the UN Economic Commission for Europe*, 6(4).
- Mason, A. & Racelis, R. (1992). A comparison of four methods for projecting households. *International Journal of Forecasting*, 8(4), 509-527. Please verify the author names against the original.
- Jiang, L. & O'Neill, B. C. (2004). Toward a new model for probabilistic household forecasts. *International Statistical Review*, 72(1), 51-64.
- Jiang, L. & O'Neill, B. C. (2007). IIASA Interim Report IR-07-017.
- van Imhoff, E. & Keilman, N. (1991). *LIPRO 2.0: an application of a dynamic demographic projection model to household structure in the Netherlands*. Swets & Zeitlinger.
- Keilman, N. & Brunborg, H. (1995). Statistics Norway Reports 95/21.
- Zeng, Y., Land, K. C., Wang, Z. & Gu, D. (2006). *Population Research and Policy Review*, 25.
- Rogers, A. et al. (2014). A geospatial dynamic microsimulation model for household population projections. *International Journal of Microsimulation*, 7(2), 119-146.
- Siripanicha, N. & Rashidi, T. H. (2020). arXiv:2006.09474.
- Alho, J. & Keilman, N. (2010). On future household structure. *Journal of the Royal Statistical Society A*, 173(1), 117-143.
- Scherbov, S. & Ediev, D. IIASA Interim Report IR-08-002.
- Christiansen, S. & Keilman, N. (2013). *Demographic Research*, 28(43).
- Gill, L. & Keilman, N. (1990), on the classification of household projection models.

Entries above were assembled from abstracts and reference lists found online. Check titles, page numbers and authors against the originals before citing them.

## License

No license has been chosen yet. Add a `LICENSE` file before publishing, for example MIT if you want others to reuse the code freely.
