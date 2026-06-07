# Data Visualizations

Data journalism experiments in analysis and storytelling with data.

**Live site:** [researchanddave.github.io/data-visualizations](https://researchanddave.github.io/data-visualizations/)

## Experiments

### [The Ground Beneath Their Feet](https://researchanddave.github.io/data-visualizations/football-injuries/)

How field surface type shapes injury risk in professional football. An analysis of 4,004 distinct injury events across 6 leagues — Premier League, La Liga, MLS, Bundesliga, Serie A, and Ligue 1.

Examines whether turf, grass, hybrid, or retractable fields correlate with higher injury rates, normalized by games played and team count per surface type.

**Data source:** [API-Football](https://www.api-football.com/) (api-sports.io)
**Surface data:** [DBpedia](https://dbpedia.org/) + manual stadium research
**Season:** 2025-26

#### Key features

- Single-scroll NYT-style narrative with Tufte data visualization principles
- Per-section accent color highlighting the highest value in each chart
- Injuries per game rate — normalized for surface exposure
- Injury spell collapsing — repeated fixture reports collapsed into single events
- WCAG AA accessible (4.5:1+ contrast, color never sole indicator)
- Vanilla HTML/JS/CSS — no frameworks, no dependencies

## Tech Stack

- **Pipeline:** Python 3 + requests — pulls from API-Football, enriches with surface overrides, collapses injury spells
- **Visualization:** Single `index.html` — vanilla HTML/JS/CSS, no build tools
- **Hosting:** GitHub Pages

## Running the Pipeline

```bash
# Install dependencies
pip install requests

# Pull data (requires API-Football API key)
python3 football-injury-pipeline.py --api-key YOUR_KEY

# Pull specific leagues
python3 football-injury-pipeline.py --api-key YOUR_KEY --leagues premier laliga mls

# Limit fixture queries for testing
python3 football-injury-pipeline.py --api-key YOUR_KEY --max-fixtures 10
```

The pipeline source is in the [football-injuries](https://github.com/davezen1/football-injuries) repo.

## License

MIT
