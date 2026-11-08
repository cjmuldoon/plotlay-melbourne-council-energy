# Opening the Power BI report

`MelbourneCouncilEnergy.pbip` is a Power BI Project: the report (`MelbourneCouncilEnergy.Report`, PBIR format) and the semantic model (`MelbourneCouncilEnergy.SemanticModel`, TMDL) saved as text, so the repo can show every change. Power BI Desktop opens it like a `.pbix`.

## What you need

- Power BI Desktop for Windows, a recent version (2026). If Desktop is older than about mid 2025, turn on **File, Options and settings, Options, Preview features, Power BI Project (.pbip) save option** and **Store reports using enhanced metadata format (PBIR)**, then restart Desktop.
- The repo folder on the Windows machine. In Parallels the Mac home folder is shared, usually as `\\Mac\Home`.

## Open it

1. In Windows, open File Explorer and go to the repo's `powerbi` folder.
2. Double-click `MelbourneCouncilEnergy.pbip`, or in Desktop: **File, Open report, Browse reports**, file type *Power BI project files (\*.pbip)*.
3. The visuals are empty the first time. Select **Refresh** on the Home ribbon.
   - By default the data is read from the repo's public copy on GitHub (`DataFolder` is `https://raw.githubusercontent.com/cjmuldoon/plotlay-melbourne-council-energy/main/data/`). If Desktop asks how to connect, choose **Anonymous** and **Connect**.
   - To read the files on disk instead: **Home, Transform data, Edit parameters**, set `DataFolder` to the full path of the repo's `data` folder **ending with a backslash**, then **OK** and **Apply changes**.
   - If Desktop warns about privacy levels, choose **Ignore privacy levels** for this file (all sources are public open data) or set them to Public.
4. Check the numbers (below).

If the report was updated while open, close Power BI and reopen the .pbip rather than using Apply external changes. (After Apply external changes one export drew the page at about 57% inside a much larger canvas; a full close and reopen put it right.)

## Check the numbers after Refresh

After **Refresh**, with the Measure dropdown on **Electricity** and the Month dropdown empty (as the report opens):

| Overview card | Should read |
|---|---|
| Usage of the mapped assets, measure picked | 9,698,626 kWh (Power BI may show 9.70M) |
| Assets with no location in the register | 18 |

Pick **Gas** and the first card reads 19,558,151 MJ; pick **Water** and it reads 832,872 kL (the README's figures for the placed assets). With Water and the month **Jan 2014** it reads 140,960 kL. The bar chart's top three for electricity: Administration - Council House 1 2,177,677, Lease - Melbourne Town Hall 1,065,910, Administration - Council House 2 857,692.

On **Per square metre** the water bar for Park - Parliament reads 3,710 mm and the top electricity bar, Recreation Centre - North Melbourne, 282.7 kWh per m². Drill through from Administration - Council House 1: electricity 2,177,677 kWh for the year.

## One measure at a time

Electricity (kWh), gas (MJ) and water (kL) are rows of one `Usage` table with a `measure` column. The `Amount` measure always counts one measure: the one picked on the Measure slicer (a dropdown that takes one at a time and opens on Electricity), or electricity if the slicer is ever cleared, so the three are never added together. With no month picked it is the whole year. `Electricity kWh`, `Gas MJ` and `Water kL` are fixed to their measure for the per-area pages.

## The map

- **Shape Map** (Overview page). The custom map is saved inside the report (`MelbourneCouncilEnergy.Report/StaticResources/RegisteredResources/council_assets.topojson`, a copy of `data/geometry/council_assets.topojson` that keeps only the asset name), so the assets draw on open, coloured by the measure picked. It matches `Assets[asset_name]`. The Shape Map has no base map of its own, so the saved file also carries the context: the City of Melbourne boundary as a double line, suburbs (ABS) in a pale ground tone, the Yarra, Victoria Harbour and Port Phillip showing as the map's pale blue background (cut out using ABS water mesh blocks and Vicmap Hydro), and a coloured ring round each asset too small to see at this scale. The plain file from `data/` has none of that. The Shape Map draws areas only: the five boulevards (lines) and four points are in the GeoJSON for Azure Maps, not in the Shape Map. If the map shows the United States instead (an older Desktop can ignore a saved map): select the map, **Format visual** (the paintbrush), **Visual**, **Map settings**, **Map type: Custom map**, **Add map type** (or **Browse**), and choose `..\data\geometry\council_assets.topojson`. Under **View map keys** the council's asset names should be matched. If the Shape Map is not in the Visualizations pane at all, turn it on in **File, Options and settings, Options, Preview features, Shape map visual** and restart Desktop.
- Optional, **Azure Maps**: add a reference layer from `..\data\geometry\council_assets.geojson` (Format visual, Visual, Reference layer, Data source: File), join property `key`, map style **Grayscale (light)** or **Blank**. Do not publish satellite basemap screenshots.

If the Shape Map comes up empty after opening, drag the fields back: Location `Assets[asset_name]`, Color saturation `[Amount]`.

## The month play axis

The scatter chart on **Month by month** has `Months[month]` on its built-in Play axis. Press the play button under the chart to step July 2013 to June 2014: the parks climb the Water axis from November and fall back by May.

## Save a .pbix too

After the refresh: **File, Save as**, type **Power BI file (\*.pbix)**, save as `powerbi\MelbourneCouncilEnergy.pbix`. It includes the data.

## Publish (only if you choose to)

1. **Home, Publish**, sign in, pick a workspace.
2. In the service, the semantic model's **Settings, Data source credentials**: the GitHub address uses **Anonymous**. Scheduled refresh then works with no gateway.
3. **Publish to web** makes a public embed link; anyone with the link can see the report. Only if you intend it to be public.

## Shot list for the website and the video

Capture at 1920 × 1080 (Windows display scaling 100%), Plotlay theme on. Snipping Tool (Win+Shift+S) for stills, Win+Alt+R or Clipchamp for clips. Save to `powerbi/Screenshots/`.

| # | Shot | How | Length |
|---|---|---|---|
| 1 | Overview, water, the whole year | Measure dropdown on Water, Month dropdown empty | still |
| 2 | The measure switch | Pick Water, Electricity, Gas in the Measure dropdown, about 1 s each: the map, card and bars change | 4 s clip |
| 3 | Overview, January 2014 | Water, Month dropdown on Jan 2014 | still |
| 4 | The play axis | Month by month page: press play on the scatter chart and let it run July to June | 10 to 12 s clip |
| 5 | Per square metre | Both bar charts; hover Parliament Reserve to show its tooltip | still |
| 6 | Drill-through | Right-click Administration - Council House 1 in the bar chart, **Drill through, Asset** | still, then a 3 s clip of the click |
| 7 | Model view | The four tables (Assets, Usage, Months, Utilities) and their relationships | still |
| 8 | Power Query | **Transform data**: the `DataFolder` parameter and the Usage steps | still |

Keep the footer with the attribution visible in every shot.

## What's in the folder

- `MelbourneCouncilEnergy.pbip`: open this.
- `MelbourneCouncilEnergy.SemanticModel/definition/`: `tables/*.tmdl` (columns, measures, Power Query), `relationships.tmdl`, `expressions.tmdl` (the `DataFolder` parameter and a small `GetFile` function).
- `MelbourneCouncilEnergy.Report/definition/`: one folder per page and visual (PBIR), `report.json` (the Plotlay theme registered).
- Pages: Overview, Month by month, Per square metre, and Asset (drill-through target, by asset name).
