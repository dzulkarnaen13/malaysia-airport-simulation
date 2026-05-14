# Phase 2 — Data Modelling

## Overview

This phase transforms the six cleaned datasets into a structured data model inside Power BI Desktop. The goal is to establish correct relationships between tables so that DAX measures and dashboard visuals can query data accurately across all dimensions.

## Approach

A **Star Schema** design was applied — fact tables at the centre, dimension tables surrounding them. This is the industry-standard approach for analytical data models because it simplifies DAX calculations and improves query performance.

## Tables in the Model

| Table | Type | Rows | Description |
|---|---|---|---|
| fact_flights | Fact | 50,821 | Flight records — OTP, delay, load factor |
| fact_complaints | Fact | 7,000 | Passenger complaint records |
| dim_airports | Dimension | 15 | Airport details and region |
| dim_airlines | Dimension | 6 | Airline details and type |
| dim_date | Dimension | 730 | Date calendar 2023–2024 |
| dim_routes | Dimension | 20 | Route details and distance |

## Relationships Built

7 relationships were established across the model:

| From (Fact) | Key | To (Dimension) | Cardinality |
|---|---|---|---|
| fact_flights | DateKey | dim_date | Many to One |
| fact_flights | AirlineCode | dim_airlines | Many to One |
| fact_flights | OriginCode | dim_airports | Many to One |
| fact_flights | RouteID | dim_routes | Many to One |
| fact_complaints | DateKey | dim_date | Many to One |
| fact_complaints | AirlineCode | dim_airlines | Many to One |
| fact_complaints | AirportCode | dim_airports | Many to One |

All relationships use single-direction filtering (Dimension → Fact).

## Key Decisions

**Why Star Schema?**
Keeps dimension tables independent from each other. DAX filter context flows cleanly from dimension to fact — no ambiguity.

**Why OriginCode for fact_flights → dim_airports?**
Analysis is conducted from the departure airport perspective, consistent with how airport-level performance is typically measured.

**Why hide foreign key columns in fact tables?**
Prevents report builders from accidentally using raw ID columns in visuals instead of the descriptive columns from dimension tables.

**Why disable Auto Date/Time?**
Power BI auto-generates hidden date tables for every date column. Since we have our own dim_date, disabling this avoids duplicate hierarchies and conflicting DAX behaviour.

## Validation

A Matrix visual was built to verify that relationships function correctly:

| Airline | Sum of DelayMinutes |
|---|---|
| AirAsia | 379,510 |
| AirAsia X | 81,873 |
| Batik Air Malaysia | 119,231 |
| Firefly | 79,578 |
| Malaysia Airlines | 133,379 |
| MASwings | 128,274 |
| **Total** | **921,845** |

All 6 airlines returned data correctly.

## Files in This Folder

| File | Description |
|---|---|
| Phase2_Modelling_Guide.txt | Step-by-step modelling guide |
| model_view.png | Star schema diagram from Power BI Model View |
| matrix_test.png | Validation test — AirlineName vs Sum of DelayMinutes |
