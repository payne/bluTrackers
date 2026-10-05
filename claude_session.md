# Claude session: Blu Network Fall 2026 web app

Date: 2026-10-05
Project folder: `/home/mpayne/git/bluTrackers`

## Request

> Create a responsive web app using the data in the docx file. This relates to sensors like https://celltracktech.com/pages/digital-radio-products

The source file was `blu.docx`. It is the operator's report on the 2.4 GHz Blu Series Motus tracking system for late summer 2026.

## Results

| Item | Location |
|---|---|
| Web app (one self-contained file) | `index.html` |
| Published artifact (private until shared) | https://claude.ai/artifact/DTcJpoPpXzs8xgy5iGoKx1 |
| This session log | `claude_session.md` |

The local `index.html` opens directly in a browser. It needs an internet connection for Google Fonts and d3 (`cdnjs`, d3 7.9.0).

## Source report (text extracted from blu.docx)

> Report of activities fro the 2.4 GHz Blu series Motus tracking system late summer 2026.
>
> I am only reporting off site detection. If the animal had a transmitter fitted at that location it will not be in this report. The Monarch tags are not reported to Motus website. The data has to be downed loaded from each receiver location and cross referenced form Project Monarch.
>
> **Niobrara State Park**
> 09/19/26 Monarch from Ponca State park.
>
> **Ponca State Park**
> 09/13/26 Eastern Red Bat From Ponca will be posted on Motus
> 09/13/26 Silver Haired Bat From Ponca will be posted on Motus
>
> Note many Monarch were tagged at Ponca which created a large amount of data. I have to manually sort though the data to find is any outside visitors monarch were detected. I have not found any but that does not mean I missed some.
>
> **Hitchcock Nature Center Iowa**
> 9/12/26 Monarch from Winnipeg Manitoba
> 9/21/26 Monarch from Bemidji MN
> 9/18/26 Monarch from Guelph Toronto Ontario
>
> **Zoo Omaha**
> 9/12/26 Monarch from Winnipeg Manitoba
> 9/21/26 Monarch from Bemidji MN
> 9/28/26 Monarch from Midewin Nation grassland Il.
> 9/28/26 Monarch from Oswego New York
> 10/02/26 Monarch from Winnipeg Manitoba
>
> **Offutt AFB Omaha**
> 09/06/26 Two Monarch from Zoo Omaha
> 09/08/26 Monarch from Zoo Omaha
> 09/27/26 Monarch from Madison WI
> 09/20/26 Monarch from North of Madison WI
>
> **Glacier Creek Preserve Omaha**
> 09/17/26 Monarch from Sioux Fall SD
> 09/20/26 Monarch From Madison WI
> 09/27/26 Monarch form Madison WI
>
> **Nebraska City**
> 09/08/26 Monarch from Zoo Omaha
> 09/12/26 Monarch from Glacier Creek DR Bragg
> 09/27/26 Monarch from Toronto Ontario
> 09/27/26 Monarch from Sudbury Manitoulin Island Ontario
>
> **Peru State College**
> 09/08/26 Monarch From Zoo Omaha
> 09/12/26 Monarch From Glacier Creek Dr Bragg
> 09/12/26 Monarch From Winnipeg Manitoba
> 09/19/26 Monarch From Belleplaine MN
> 09/21/26 Monarch From Parry Sound Ontario CA
>
> **Indian Cave State Park**
> 10/02/26 Monarch from Parry Sound Ontario Ca note this is different Monarch then the one detected in Peru

## Steps taken

1. Extracted the text from `blu.docx` (unzipped `word/document.xml` and removed the XML tags).
2. Downloaded Natural Earth 1:50m data for state/province boundaries (`ne_50m_admin_1_states_provinces_lakes`) and lakes (`ne_50m_lakes`) from GitHub. A Python script then:
   - kept only US and Canadian polygons inside lon −108 to −70, lat 35 to 53
   - rounded coordinates to 2 decimals and dropped duplicate points
   - wrote about 205 KB of GeoJSON
3. Wrote `index.html` and embedded that GeoJSON inline (replacing a `__GEO__` placeholder). Map tile servers and runtime fetches are blocked inside the artifact viewer, so the basemap has to ship with the page.
4. Rendered it once in headless Chromium and fixed what the screenshot showed:
   - receivers were clipped at the bottom edge of the map
   - the heading was too narrow
   - a lake label overlapped other labels
5. Published it as a claude.ai artifact.

Fixes made during the build:
- Stats line: removed a placeholder caption.
- Scale bar: simplified the "nice distance" calculation.
- Replay animation: the line-drawing effect now sets `stroke-dasharray` as an inline style. As an SVG attribute it lost to the CSS dashed style used for Blu → Blu lines.

## App features

- **Header:** eyebrow (2.4 GHz Blu Series · Motus receiver network · Late summer 2026), headline, short intro, and four figures:
  - 29 detections
  - 9 of 9 receivers with hits
  - 12 distant tagging sites (MB · ON · MN · WI · IL · SD · NY)
  - longest track 1,001 mi (Oswego → Zoo Omaha)
- **Map:** d3 conic conformal projection with parallels 40°/48°, centred on 88°W.
  - Curved flight lines from tagging site to receiver: orange for long-distance monarchs, dashed teal for moves between Blu sites, purple for bats.
  - Hollow circles mark tagging sites. Teal squares mark Blu receivers.
  - Drag to pan and pinch or scroll to zoom. Marker and label sizes stay constant as you zoom.
  - "Full range" and "Receivers" zoom buttons. Station names appear once you zoom past about 3.2×.
  - Live km scale bar.
- **Species filter:** All / Monarchs / Bats.
- **Replay season:** steps through Sep 6 to Oct 2 and draws each day's lines as they appear. It skips the animation when the viewer prefers reduced motion.
- **Detections-per-day chart:** stacked monarch and bat bars. Tap a day to filter to it.
- **Detection log:** groups by station, date or distance. The grouping choice is remembered in localStorage. Each entry shows:
  - date
  - species tag (Monarch / Blu → Blu / bat species)
  - tagging site
  - distance in miles
  - notes

  Clicking a station heading, a map marker, a line or a tagging site filters the log, and each filter shows as a removable tag.
- **Where they came from:** tagging sites ranked by detection count, with the distance to the farthest receiver that heard them. Click a site to filter.
- **Field notes:** caveats taken from the report.
- **Footer:** link to CTT digital radio products and a note on data sources.
- **Responsive and themed:** two columns at 980 px and wider, one column on phones. Light and dark themes are built from CSS tokens.

### Design

| Role | Light | Dark |
|---|---|---|
| Background | `#EDF1EF` | `#0E1412` |
| Text | `#16201D` | `#E2E9E5` |
| Monarch | `#C9561A` | `#F0883A` |
| Bat | `#6553A8` | `#A898E6` |
| Receiver / Blu → Blu | `#0D6A62` | `#3EC2B1` |
| Water | `#C3DAE4` | `#0C2530` |

Typefaces:
- Archivo, condensed width, for headings
- Source Sans 3 for body text
- JetBrains Mono for dates, labels and numbers

## Data as modelled

### Blu receivers

| id | Name | State | Lat | Lon |
|---|---|---|---|---|
| niobrara | Niobrara State Park | NE | 42.752 | −98.046 |
| ponca | Ponca State Park | NE | 42.601 | −96.715 |
| hitchcock | Hitchcock Nature Center | IA | 41.416 | −95.858 |
| glacier | Glacier Creek Preserve | NE | 41.390 | −96.111 |
| zoo | Zoo Omaha | NE | 41.226 | −95.928 |
| offutt | Offutt AFB | NE | 41.118 | −95.912 |
| nebcity | Nebraska City | NE | 40.677 | −95.859 |
| peru | Peru State College | NE | 40.477 | −95.731 |
| indiancave | Indian Cave State Park | NE | 40.263 | −95.563 |

### Tagging sites (approximate centres)

| id | Name | Lat | Lon |
|---|---|---|---|
| winnipeg | Winnipeg, MB | 49.895 | −97.138 |
| bemidji | Bemidji, MN | 47.473 | −94.880 |
| belleplaine | Belle Plaine, MN | 44.623 | −93.769 |
| siouxfalls | Sioux Falls, SD | 43.546 | −96.731 |
| madison | Madison, WI | 43.073 | −89.401 |
| nmadison | North of Madison, WI | 43.320 | −89.420 |
| midewin | Midewin Tallgrass Prairie, IL | 41.360 | −88.080 |
| guelph | Guelph, ON | 43.545 | −80.248 |
| toronto | Toronto, ON | 43.653 | −79.383 |
| parrysound | Parry Sound, ON | 45.347 | −80.035 |
| sudbury | Sudbury / Manitoulin Is., ON | 45.900 | −81.700 |
| oswego | Oswego, NY | 43.455 | −76.511 |
| ponca, zoo, glacier | Blu receiver sites (use the receiver coordinates) | | |

### Detections (28 records, 29 animals)

| Date | Receiver | Species | From | Count | Note |
|---|---|---|---|---|---|
| 2026-09-19 | Niobrara State Park | Monarch | Ponca State Park | 1 | |
| 2026-09-13 | Ponca State Park | Eastern red bat | Ponca State Park | 1 | Will be posted on Motus |
| 2026-09-13 | Ponca State Park | Silver-haired bat | Ponca State Park | 1 | Will be posted on Motus |
| 2026-09-12 | Hitchcock Nature Center | Monarch | Winnipeg, MB | 1 | |
| 2026-09-21 | Hitchcock Nature Center | Monarch | Bemidji, MN | 1 | |
| 2026-09-18 | Hitchcock Nature Center | Monarch | Guelph, ON | 1 | Reported as "Guelph Toronto Ontario" |
| 2026-09-12 | Zoo Omaha | Monarch | Winnipeg, MB | 1 | |
| 2026-09-21 | Zoo Omaha | Monarch | Bemidji, MN | 1 | |
| 2026-09-28 | Zoo Omaha | Monarch | Midewin, IL | 1 | Midewin National Tallgrass Prairie |
| 2026-09-28 | Zoo Omaha | Monarch | Oswego, NY | 1 | |
| 2026-10-02 | Zoo Omaha | Monarch | Winnipeg, MB | 1 | |
| 2026-09-06 | Offutt AFB | Monarch | Zoo Omaha | 2 | Two monarchs |
| 2026-09-08 | Offutt AFB | Monarch | Zoo Omaha | 1 | |
| 2026-09-20 | Offutt AFB | Monarch | North of Madison, WI | 1 | |
| 2026-09-27 | Offutt AFB | Monarch | Madison, WI | 1 | |
| 2026-09-17 | Glacier Creek Preserve | Monarch | Sioux Falls, SD | 1 | |
| 2026-09-20 | Glacier Creek Preserve | Monarch | Madison, WI | 1 | |
| 2026-09-27 | Glacier Creek Preserve | Monarch | Madison, WI | 1 | |
| 2026-09-08 | Nebraska City | Monarch | Zoo Omaha | 1 | |
| 2026-09-12 | Nebraska City | Monarch | Glacier Creek (Dr. Bragg) | 1 | |
| 2026-09-27 | Nebraska City | Monarch | Toronto, ON | 1 | |
| 2026-09-27 | Nebraska City | Monarch | Sudbury / Manitoulin Is., ON | 1 | Reported as "Sudbury Manitoulin Island" |
| 2026-09-08 | Peru State College | Monarch | Zoo Omaha | 1 | |
| 2026-09-12 | Peru State College | Monarch | Glacier Creek (Dr. Bragg) | 1 | |
| 2026-09-12 | Peru State College | Monarch | Winnipeg, MB | 1 | |
| 2026-09-19 | Peru State College | Monarch | Belle Plaine, MN | 1 | |
| 2026-09-21 | Peru State College | Monarch | Parry Sound, ON | 1 | |
| 2026-10-02 | Indian Cave State Park | Monarch | Parry Sound, ON | 1 | Different monarch from the one at Peru |

Distances are great-circle (haversine, Earth radius 6,371 km), shown in miles.

## Approximations and readings to check

- Tagging sites are placed at town or preserve centres, so distances are estimates. "North of Madison, WI" is a rough guess.
- "Guelph Toronto Ontario" is mapped to Guelph.
- "Sudbury Manitoulin Island" is placed between Sudbury and Manitoulin Island.
- "Midewin Nation grassland" is read as Midewin National Tallgrass Prairie.
- The two Ponca bats are recorded as "from Ponca State Park," as written in the report. That conflicts slightly with the report covering only off-site detections, so their tagging location is worth confirming.
- Receiver coordinates are my estimates, not surveyed receiver positions.
- The same monarch can be heard by more than one receiver, so detection counts are not counts of individual animals.

## How to update

- **Add or change detections:** edit the `RAW` array in the script in `index.html`. Each record is `[date, stationId, "m"|"b", originId, count, note, label]`.
- **Add a tagging site:** add an entry to `ORIGINS` with `name`, `short`, `lat` and `lon`. Optionally add a label offset in `OLBL`.
- **Add a receiver:** add it to `STATIONS` and give it a label offset in `SLBL`.
- **Change the season:** update the season range (`DAY0` / `DAYN`) if detections fall outside Sep 6 to Oct 2, and adjust the chart's date labels to match.
- **Republish:** republish the same file to keep the artifact URL above.
