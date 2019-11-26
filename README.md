# nm-potty-deserts
Here's an interactive dataviz that lets you explore housing without plumbing in New Mexico, on the county level.      
Inspired by Jennifer Peebles' dataviz on Georgia counties for World Toilet Day 2019.      
This uses two ACS tables: B25001, housing units, and B25047, plumbing facilities.        
I'm using the ACS 5-year estimates for 2017.      
Initially I thought to compare two ACS blocks over time but decided comparing estimates with estimates is too dodgy.     
      
         
This begins with .csv files. I previously cleaned the tables in LibreOffice by removing some extraneous columns,      
trimming the county names, renaming ID02 col to fips because that's what it is,      
fixing spelling of Doña Ana County, adding year columns and renaming the files for clarity. Your workflow may vary.
