# USPTO Patent Intelligence
### Bibliographic Data, Assignments & Inventor Networks — by The Data Developers

[![Snowflake Marketplace](https://img.shields.io/badge/Available%20on-Snowflake%20Marketplace-29B5E8?style=flat&logo=snowflake)](https://app.snowflake.com/marketplace)
[![Data Source](https://img.shields.io/badge/Source-USPTO%20PatentsView%20%26%20Google%20Patents-blue)](https://patentsview.org)
[![License](https://img.shields.io/badge/License-Commercial-green)](https://www.thedatadevelopers.com)
[![Coverage](https://img.shields.io/badge/Coverage-6M%2B%20US%20Patents-orange)](https://patentsview.org)

---

## Overview

The **USPTO Patent Intelligence** dataset by **The Data Developers** delivers the complete US patent universe — structured, cross-linked, and query-ready — directly in your Snowflake environment.

Sourced from USPTO PatentsView and Google Patents Public Data, this listing covers 6 million+ US patent grants with full bibliographic records, assignee ownership networks, inventor relationships, CPC and IPC classifications, citation graphs, patent assignment transaction history since 1970, and government interest statements.

14 fully structured, cross-linked tables give IP attorneys, pharma BD teams, investment analysts, and corporate patent counsel the intelligence to map competitive landscapes, track ownership transfers, analyze technology trends, and conduct patent due diligence — entirely in SQL.

---

## Scale & Coverage

| Metric | Value |
|--------|-------|
| Total US Patent Grants | 6M+ |
| Patent-Assignee Relationships | 5.6M+ |
| Patent-Inventor Relationships | 14.9M+ |
| CPC Classification Records | 32.8M+ |
| IPC Classification Records | 11.5M+ |
| US Patent Citations | 89M+ |
| Foreign Patent Citations | 22M+ |
| Assignment Transactions | Since 1970 |
| Tables | 14 |

---

## Database Structure

**Database:** `TDD_USPTO`
**Schema:** `PATENTS`

All tables are cross-linked via `patent_id` and `publication_number` keys enabling multi-dimensional patent intelligence queries.

---

## Table Reference

### PATENT_PUBLICATIONS
**Core Patent Bibliographic Data**

The primary patent table sourced from Google Patents Public Data. Contains full bibliographic records for all US patent grants.

| Column | Description |
|--------|-------------|
| `publication_number` | Unique patent publication number (e.g. US-9876543-B2) — primary join key |
| `country_code` | Country of publication (US for all records) |
| `filing_date` | Application filing date (YYYYMMDD) |
| `grant_date` | Patent grant date (YYYYMMDD) |
| `priority_date` | Earliest priority date |
| `title_localized` | Patent title array (language + text) |
| `abstract_localized` | Patent abstract array (language + text) |
| `assignee_harmonized` | Harmonized assignee name and country array |
| `inventor_harmonized` | Harmonized inventor name and country array |
| `ipc` | IPC classification codes array |
| `cpc` | CPC classification codes array |
| `claims_localized` | Patent claims text array |
| `citation` | Citation records array (patent and non-patent) |
| `family_id` | Patent family identifier for grouping related applications |

---

### PATENT_ASSIGNMENTS
**Patent Ownership Transfer History**

Sourced from USPTO OCE Patent Assignment Dataset. Contains all recorded patent assignment transactions since 1970.

| Column | Description |
|--------|-------------|
| `rf_id` | Reel/frame identifier — unique assignment record ID |
| `file_id` | Assignment file identifier |
| `cname` | Assignee name — entity receiving ownership |
| `caddress_1` | Assignee address line 1 |
| `caddress_2` | Assignee address line 2 |
| `caddress_3` | Assignee address line 3 |
| `caddress_4` | Assignee address line 4 |
| `reel_no` | Reel number of recorded assignment |
| `frame_no` | Frame number of recorded assignment |
| `convey_text` | Conveyance type (ASSIGNMENT, LICENSE, MERGER, etc.) |
| `record_dt` | Date assignment was recorded at USPTO |
| `last_update_dt` | Last update date |
| `page_count` | Number of pages in assignment document |
| `purge_in` | Purge indicator |

---

### PATENT_ASSIGNMENT_MATCH
**Assignment to Publication Number Bridge**

Links assignment records to patent publication numbers.

| Column | Description |
|--------|-------------|
| `publication_number` | Patent publication number — join to PATENT_PUBLICATIONS |
| `grant_doc_num` | Grant document number |
| `pgpub_doc_num` | Pre-grant publication document number |

---

### PATENTSVIEW_PATENT
**PatentsView Core Patent Table**

| Column | Description |
|--------|-------------|
| `id` | PatentsView patent ID — primary join key for all PatentsView tables |
| `type` | Patent type (utility, design, plant, reissue) |
| `number` | Patent number (e.g. 9876543) |
| `country` | Country code |
| `patent_date` | Grant date (DATE type) |
| `abstract` | Patent abstract text |
| `title` | Patent title |
| `kind` | Kind code (B1, B2, etc.) |
| `num_claims` | Number of claims |
| `filename` | Source XML filename |
| `withdrawn` | Withdrawn patent indicator |

---

### PATENTSVIEW_ASSIGNEE
**Harmonized Assignee Entities**

| Column | Description |
|--------|-------------|
| `id` | Assignee entity ID — join to PATENTSVIEW_PATENT_ASSIGNEE |
| `type` | Entity type (0=unassigned, 2=US company, 3=foreign company, 4=US individual, 5=foreign individual, 6=US government, 7=foreign government, 8=country, 9=state) |
| `name_first` | First name (individual assignees) |
| `name_last` | Last name / organization name |
| `organization` | Organization name (company assignees) |

---

### PATENTSVIEW_PATENT_ASSIGNEE
**Patent to Assignee Mapping**

| Column | Description |
|--------|-------------|
| `patent_id` | Patent ID — join to PATENTSVIEW_PATENT.id |
| `assignee_id` | Assignee ID — join to PATENTSVIEW_ASSIGNEE.id |
| `location_id` | Location ID — join to PATENTSVIEW_LOCATION.id |
| `sequence` | Assignee sequence number for multi-assignee patents |

---

### PATENTSVIEW_INVENTOR
**Harmonized Inventor Entities**

| Column | Description |
|--------|-------------|
| `id` | Inventor entity ID — join to PATENTSVIEW_PATENT_INVENTOR |
| `name_first` | Inventor first name |
| `name_last` | Inventor last name |

---

### PATENTSVIEW_PATENT_INVENTOR
**Patent to Inventor Mapping**

| Column | Description |
|--------|-------------|
| `patent_id` | Patent ID — join to PATENTSVIEW_PATENT.id |
| `inventor_id` | Inventor ID — join to PATENTSVIEW_INVENTOR.id |
| `location_id` | Location ID — join to PATENTSVIEW_LOCATION.id |
| `sequence` | Inventor sequence number |

---

### PATENTSVIEW_CPC_CURRENT
**CPC Classification Codes**

Current Cooperative Patent Classification codes for each patent.

| Column | Description |
|--------|-------------|
| `uuid` | Unique record identifier |
| `patent_id` | Patent ID — join to PATENTSVIEW_PATENT.id |
| `section_id` | CPC section (A-H, Y) |
| `subsection_id` | CPC subsection (e.g. A61 = Medical/Pharma) |
| `group_id` | CPC group |
| `subgroup_id` | CPC subgroup (most granular classification) |
| `category` | Classification category (inventive/additional) |
| `sequence` | Classification sequence number |

**Key CPC Sections for Life Sciences:**

| Section | Coverage |
|---------|----------|
| `A61K` | Preparations for medical, dental, or toilet purposes |
| `A61P` | Specific therapeutic activity of chemical compounds |
| `A61B` | Diagnosis, surgery, identification |
| `C07` | Organic chemistry |
| `C12` | Biochemistry, microbiology, genetic engineering |

---

### PATENTSVIEW_IPCR
**IPC Classification Codes**

International Patent Classification codes for each patent.

| Column | Description |
|--------|-------------|
| `uuid` | Unique record identifier |
| `patent_id` | Patent ID — join to PATENTSVIEW_PATENT.id |
| `classification_level` | Classification level |
| `section` | IPC section |
| `ipc_class` | IPC class |
| `subclass` | IPC subclass |
| `main_group` | IPC main group |
| `subgroup` | IPC subgroup |
| `symbol_position` | Symbol position (first/later) |
| `classification_value` | Classification value (inventive/additional) |
| `classification_status` | Status |
| `classification_data_source` | Data source |
| `action_date` | Action date |
| `ipc_version_indicator` | IPC version |
| `sequence` | Sequence number |

---

### PATENTSVIEW_USPATENTCITATION
**US Patent Citation Network**

| Column | Description |
|--------|-------------|
| `uuid` | Unique record identifier |
| `patent_id` | Citing patent ID — join to PATENTSVIEW_PATENT.id |
| `citation_id` | Cited patent ID |
| `date` | Citation date |
| `name` | Cited patent name |
| `kind` | Kind code of cited patent |
| `country` | Country of cited patent |
| `category` | Citation category |
| `sequence` | Citation sequence number |

---

### PATENTSVIEW_FOREIGNCITATION
**Foreign Patent Citation Records**

| Column | Description |
|--------|-------------|
| `uuid` | Unique record identifier |
| `patent_id` | Citing US patent ID |
| `date` | Citation date |
| `number` | Foreign patent number |
| `country` | Country of cited foreign patent |
| `category` | Citation category |
| `sequence` | Citation sequence number |

---

### PATENTSVIEW_LOCATION
**Inventor and Assignee Locations**

| Column | Description |
|--------|-------------|
| `id` | Location ID — join from patent_assignee and patent_inventor |
| `city` | City name |
| `state` | US state code |
| `country` | Country code |
| `latitude` | Latitude coordinate |
| `longitude` | Longitude coordinate |
| `county` | County name |
| `state_fips` | State FIPS code |
| `county_fips` | County FIPS code |

---

### PATENTSVIEW_GOVERNMENT_INTEREST
**Government-Funded Patents**

Patents containing government interest statements — indicating federal R&D funding.

| Column | Description |
|--------|-------------|
| `patent_id` | Patent ID — join to PATENTSVIEW_PATENT.id |
| `gi_statement` | Full government interest statement text |

---

### PATENTSVIEW_MATCH
**PatentsView to Google Patents Bridge**

Links PatentsView patent IDs to Google Patents publication numbers.

| Column | Description |
|--------|-------------|
| `patent_id` | PatentsView patent ID |
| `publication_number` | Google Patents publication number |

---

## Data Model

```
PATENT_PUBLICATIONS (Google Patents — bibliographic)
  publication_number ──────────────────────── PATENT_ASSIGNMENT_MATCH
                                                      │
                                              PATENT_ASSIGNMENTS
                                              (ownership history)

PATENTSVIEW_PATENT (core patent record)
  id ──┬──► PATENTSVIEW_PATENT_ASSIGNEE ──► PATENTSVIEW_ASSIGNEE
       │         └──► PATENTSVIEW_LOCATION  (organization name)
       │
       ├──► PATENTSVIEW_PATENT_INVENTOR ──► PATENTSVIEW_INVENTOR
       │         └──► PATENTSVIEW_LOCATION  (name_first, name_last)
       │
       ├──► PATENTSVIEW_CPC_CURRENT         (section, subsection, group)
       ├──► PATENTSVIEW_IPCR                (section, class, subclass)
       ├──► PATENTSVIEW_USPATENTCITATION    (citation_id)
       ├──► PATENTSVIEW_FOREIGNCITATION     (country, number)
       └──► PATENTSVIEW_GOVERNMENT_INTEREST (gi_statement)

PATENTSVIEW_MATCH
  patent_id ──────────────────────────────── publication_number
  (bridges PatentsView ↔ Google Patents)
```

---

## Key Concepts

**CPC Section A61 — Pharmaceutical & Medical Patents**
Filter `PATENTSVIEW_CPC_CURRENT.subsection_id = 'A61'` to isolate all pharmaceutical, medical device, and healthcare patents. This is the primary filter for life sciences patent analysis.

**Assignee Harmonization**
The `PATENT_PUBLICATIONS.assignee_harmonized` field contains Google's harmonized assignee names — normalizing variations like "Pfizer Inc", "PFIZER INC.", and "Pfizer Incorporated" to a consistent entity. Use this for portfolio analysis. For raw filing names use `PATENTSVIEW_RAWASSIGNEE`.

**Patent Assignment History**
`PATENT_ASSIGNMENTS.convey_text` identifies the type of ownership transfer. Key values include ASSIGNMENT (outright ownership transfer), LICENSE (licensing agreement), MERGER (corporate merger), CHANGE OF NAME, and SECURITY AGREEMENT. Filter to ASSIGNMENT for true ownership transfer analysis.

**Government Interest Patents**
`PATENTSVIEW_GOVERNMENT_INTEREST` identifies patents arising from federally funded research. These patents are subject to Bayh-Dole Act provisions — critical intelligence for technology transfer and licensing analysis.

**Citation Analysis**
`PATENTSVIEW_USPATENTCITATION` enables forward and backward citation analysis. High citation counts indicate foundational patents. Join `patent_id` for backward citations (what this patent cites) and `citation_id` for forward citations (what cites this patent).

**PatentsView vs Google Patents**
This dataset includes both PatentsView (normalized, disambiguated entity data) and Google Patents Public Data (broader bibliographic coverage, harmonized assignees). Use `PATENTSVIEW_MATCH` to bridge between the two. For portfolio and landscape analysis, Google Patents provides broader coverage. For inventor and assignee networks, PatentsView provides cleaner disambiguation.

---

## Use Cases

**IP Due Diligence & Portfolio Analysis**
Map the complete patent portfolio of any company, track ownership transfers, analyze filing velocity trends, and assess portfolio breadth by technology classification.

**Pharmaceutical Patent Cliff Analysis**
Identify drug patents expiring by therapeutic area, map which companies hold expiring patents, and monitor generic entry timing using grant dates and classification filters.

**Technology Landscape Mapping**
Understand who owns what technology in any sector using CPC classification hierarchies, track technology concentration and emerging players, and identify white spaces in the patent landscape.

**Competitive Intelligence**
Monitor competitor patent filing activity, identify inventors moving between organizations, track technology acquisitions via assignment history, and detect emerging patent thickets.

**Government-Funded Innovation Tracking**
Identify patents arising from federally funded research across NIH, DOD, DOE, and other agencies using government interest statements — essential for technology transfer and licensing analysis.

**Patent Litigation Support**
Build prior art citation networks, identify foundational patents in a technology space, and map the citation landscape for validity and infringement analysis.



---

## About The Data Developers

The Data Developers is a biomedical data intelligence company providing structured, query-ready life sciences datasets on Snowflake Marketplace and Google Cloud Marketplace. We specialize in cross-linked intelligence across clinical trials, drug safety, regulatory data, patent intelligence, genomics, and real-world evidence.

**Website:** [thedatadevelopers.com](https://www.thedatadevelopers.com)
**Contact:** support.marketplace@thedatadevelopers.com

---
