# London Mobile Coverage Analysis

A Power BI analysis of predicted 4G and 5G coverage across **all 32 London boroughs and the City of London**, comparing three reference snapshots between January 2025 and January 2026.

This project explores how mobile coverage differs between local authorities, how coverage changes over time, and how the picture changes when a higher 5G signal threshold is applied. It is the initial analytical prototype for a broader telecommunications and geospatial application planned as part of a **SoftITO Data Analytics graduation project**.

[View the report PDF](reports/london_mobile_coverage_report.pdf) · [View the dashboard image](images/dashboard.png)

![London mobile coverage dashboard showing coverage indicators, borough comparisons and historical trends](images/dashboard.png)

## Project Background

Mobile coverage is an important part of everyday connectivity, but a single citywide percentage can conceal substantial differences between areas. A high overall figure also does not necessarily mean that every network offers equally strong coverage in every neighbourhood.

This project uses public Ofcom coverage statistics to examine those differences across London. The first stage focuses on transparent data preparation, appropriate aggregation and an interactive reporting structure. London provides an open-data case study for developing an approach that could later be adapted to Istanbul with authorised network data.

The long-term ambition extends beyond a standalone Power BI dashboard. Future work is intended to combine telecommunications analysis, geographic visualisation and software development in an application that helps users investigate local connectivity conditions.

## Questions Addressed

- How does predicted 5G coverage differ across London's 33 local authorities?
- How much geographic coverage is available from all four operators at the same locations?
- How does the result change between Ofcom's High Confidence and Very High Confidence 5G categories?
- Which boroughs recorded the largest coverage improvements between January 2025 and January 2026?
- How does the borough-level picture compare with the weighted London-wide result?
- Which areas could merit further investigation when additional network-performance evidence becomes available?

The dashboard supports descriptive comparison. It does not identify the cause of a coverage difference or establish where a new base station should be built.

## Data Coverage and Structure

| Item | Scope |
| --- | --- |
| Geographic coverage | 32 London boroughs and the City of London |
| Number of local authorities | 33 |
| Reference dates | 1 January 2025, 1 July 2025, 1 January 2026 |
| Prepared analytical grain | One local authority per reference date |
| Prepared records | 99 borough–date records |
| Source | Ofcom Connected Nations public data releases |
| Measurement basis | Operator-predicted coverage |
| Main technologies | 4G and 5G |
| Main reporting basis | Coverage available from all four operators |

Each of the three snapshots contains all 33 local authorities. These dates are **three observations of coverage conditions**, not a monthly time series and not annual averages.

The underlying sources are the local and unitary authority coverage files from the Spring 2025, Annual 2025 and Spring 2026 releases. Source publication dates differ from the reference dates of the data.

Borough records are matched using the official local authority code, `borough_code`, rather than relying on spelling or capitalisation in borough names.

## Coverage Indicators

| Indicator | Meaning | Denominator |
| --- | --- | --- |
| 5G High Confidence Coverage | Predicted outdoor 5G coverage from all four operators using Ofcom's High Confidence threshold | Geographic pixels |
| 5G Very High Confidence Coverage | Predicted outdoor 5G coverage from all four operators using the higher Very High Confidence threshold | Geographic pixels |
| 4G Indoor Coverage | Predicted indoor 4G coverage from all four operators | Premises |
| 5G Coverage Change | Difference in High Confidence coverage between January 2025 and January 2026 | Percentage points |

Ofcom's outdoor 5G thresholds are **−110 dBm SS-RSRP** for High Confidence and **−100 dBm SS-RSRP** for Very High Confidence. The higher threshold produces a more demanding coverage criterion. These are coverage-classification categories, not statistical confidence intervals and not measured average signal levels.

“All four operators” means that coverage is predicted to be available from every one of the four networks at the relevant locations. It does not mean an average of four separate operator percentages. The open borough-level files used here do not support named individual-operator rankings.

The 4G premises-based result and 5G geographic result use different denominators and thresholds. They should not be interpreted as directly equivalent measures of user experience.

## Dashboard Components

### Coverage Indicators

Three cards display the selected reference date's London-wide coverage indicators. Their calculations use geographic or premises weights as appropriate, rather than treating every borough as equally large.

### Borough Coverage Comparison

A horizontal bar chart ranks boroughs by High Confidence 5G coverage. This makes local differences visible and allows areas with lower predicted all-four-operator coverage to be examined.

### Coverage Trends

A two-series line chart compares High Confidence and Very High Confidence 5G coverage across all three reference dates. Its interaction with the reference-date slicer is disabled so that selecting a date for the cards does not reduce the trend chart to one observation.

The lines connect the three published snapshots. They do not establish coverage levels for the unobserved months between those dates.

### Borough Improvement

A second horizontal bar chart compares January 2025 with January 2026. Changes are expressed in **percentage points**, not relative percentage growth. For example, an increase from 70% to 90% is a 20-percentage-point increase.

### Filters and Source Notes

The report includes reference-date and borough selection controls, together with a visible source, geographic scope and interpretation note. The repository's PDF is a static export of the report; its displayed filter controls are not interactive.

## Findings from the Prepared Data

The weighted geographic indicators developed for this analysis are approximately:

| Reference snapshot | 5G High Confidence, all four operators | 5G Very High Confidence, all four operators |
| --- | ---: | ---: |
| January 2025 | 88.2% | 49.9% |
| July 2025 | 89.1% | 51.9% |
| January 2026 | 97.9% | 81.1% |

The difference between the two 5G indicators shows why the choice of signal threshold matters. Broad predicted availability can coexist with substantially lower coverage at a higher signal threshold.

The exported PDF has **January 2025 selected**. Its cards show 88.2% High Confidence 5G geographic coverage, 49.9% Very High Confidence 5G geographic coverage and 99.0% indoor 4G premises coverage.

In the exported borough-improvement chart, High Confidence coverage increased by approximately **35.9 percentage points in Havering**, **32.6 in Kingston upon Thames**, and **18.0 in Bromley** between January 2025 and January 2026.

These are descriptive changes in published predictions. They do not isolate the impact of network investment: modelling inputs, reference populations, geographic denominators and other source revisions may also affect comparisons.

## Methodology

### Preparation and Validation

1. Select London local authority records using their official geographic identifiers.
2. Verify that each reference snapshot contains all 33 authorities.
3. Standardise borough names while retaining the official codes as the join keys.
4. Create a borough dimension and a coverage fact table at borough–snapshot level.
5. Set text, date, integer and decimal types in Power Query.
6. Preserve the distinction between premises-based and geographic coverage.
7. Create DAX measures for weighted coverage and fixed-period changes.
8. Check headline values and slicer interactions against the prepared data.

Percentage fields in the prepared source use a 0–100 scale. Measures divide by 100 before applying Power BI's percentage display format. Missing source values should remain missing rather than being assumed to represent zero coverage.

### Data Model

```mermaid
flowchart LR
    B["London_Boroughs_33: 33 authorities"] -->|"borough_code: one to many"| C["Coverage: 99 borough-date records"]
```

The relationship uses a single filter direction from the borough dimension to the fact table. Reference-date filtering uses the date column in `Coverage`.

### Weighted Aggregation

For a selected reference date, the geographic indicator is calculated as:

**Sum of (borough geographic pixel count × borough coverage percentage) ÷ total geographic pixel count.**

Premises-based indicators use the same approach with premises counts. Geographic source cells are 100 m × 100 m pixels.

Because the published borough percentages are rounded, the resulting London aggregates are approximate. An unweighted mean of borough percentages would answer a different question and is not used for the headline London-wide indicators.

### Example DAX Measure

```dax
5G High Confidence Coverage =
IF(
    HASONEVALUE(Coverage[snapshot_date]),
    DIVIDE(
        SUMX(
            Coverage,
            Coverage[geographic_pixel_count] *
            Coverage[coverage_5g_high_geographic_all4_pct]
        ),
        SUM(Coverage[geographic_pixel_count]) * 100
    )
)
```

The single-date condition prevents a headline card from pooling multiple snapshots into one unclear coverage percentage. In the trend chart, each date on the axis supplies the date context for the measure.

## Technology Used

- **Power BI Desktop:** report development and interactive visualisation.
- **Power Query:** data preparation and type handling.
- **DAX:** weighted indicators and percentage-point change calculations.
- **CSV:** prepared public-data inputs for the analytical model.

## Repository Contents

```text
README.md
images/dashboard.png
reports/london_mobile_coverage_report.pdf
```

This repository currently provides the report PDF, dashboard preview and project documentation. The editable `.pbix` file and prepared CSV inputs are not included in this version. No private operator data or personal subscriber information is included.

## Viewing the Project

1. Open the [dashboard PDF](reports/london_mobile_coverage_report.pdf) to inspect the exported report.
2. Use the [full dashboard image](images/dashboard.png) for a quick visual overview.
3. Read the indicator definitions and limitations above before interpreting the figures.
4. Use the linked Ofcom releases to consult the original data and methodology.

Interactive filtering requires the original Power BI Desktop report, which has not yet been added to this repository. There is no hosted interactive application in this version.

## Limitations

- Predicted coverage is not the same as measured user speed, latency, reliability or customer satisfaction.
- Coverage of all 33 authorities does not mean every address or street has been drive-tested.
- The data does not measure actual 5G-to-4G fallback, handovers or dropped connections.
- Named operator comparisons cannot be derived from operator-count categories.
- An area without all-four-operator coverage is not necessarily an area without any coverage.
- Borough aggregates cannot locate a specific street-level problem or determine the engineering cause.
- Three snapshots do not establish month-by-month changes.
- A low predicted coverage value alone is insufficient evidence for a base-station deployment recommendation.

## Planned Development

The next stages are intended to extend the work beyond dashboard reporting:

1. **Istanbul case study:** adapt the geographic model to Istanbul using data that is authorised for the project.
2. **Network-performance analysis:** incorporate anonymised, aggregated signal quality, throughput, latency or connection-success indicators where available.
3. **Geospatial investigation:** examine suitable spatial units and map local variation with explicit data-availability information.
4. **Software application:** develop an application for exploring geographic and temporal network indicators.
5. **Evaluation:** validate findings against additional evidence and seek domain feedback before proposing engineering interventions.

These are development goals, not completed capabilities. The final scope will depend on data availability, permissions, spatial resolution and methodological validation. The current report does not use Turkcell data and does not imply Turkcell endorsement or an established data-sharing agreement.

## Author

**İrem Buğday** — SoftITO Data Analytics participant, with a previous internship in Turkcell Network Technologies and a particular interest in telecommunications, network analytics and software development.

## Sources and Attribution

- [Ofcom Connected Nations Update: Spring 2025](https://www.ofcom.org.uk/phones-and-broadband/coverage-and-speeds/connected-nations-update-spring-2025) — January 2025 reference snapshot.
- [Ofcom Connected Nations 2025: Data Downloads](https://www.ofcom.org.uk/phones-and-broadband/coverage-and-speeds/connected-nations-20252/data-downloads-2025?language=en) — July 2025 reference snapshot.
- [Ofcom Connected Nations Update: Spring 2026](https://www.ofcom.org.uk/phones-and-broadband/coverage-and-speeds/connected-nations-update-spring-2026) — January 2026 reference snapshot.
- [Ofcom 2025 Mobile Coverage Data Dictionary](https://www.ofcom.org.uk/siteassets/resources/documents/research-and-data/multi-sector/infrastructure-research/connected-nations-2025/about-this-data---mobile-coverage.pdf?v=407846).
- [Ofcom Spring 2026 Mobile Coverage Data Dictionary](https://www.ofcom.org.uk/siteassets/resources/documents/research-and-data/multi-sector/infrastructure-research/connected-nations-spring-2026/about-this-data---mobile-coverage-2026.pdf?v=417687).

Contains public sector information licensed under the [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/). This attribution applies to the public-sector source data and does not establish a separate licence for all original repository content.
