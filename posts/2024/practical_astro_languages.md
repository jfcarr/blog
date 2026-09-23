---
title: "Practical Astronomy Algorithms in Various Languages"
date: "2024-04-03"
author: "Jim Carr"
categories: [Astronomy]
---

For several years, I've worked off-and-on to implement astronomical algorithms from the Practical Astronomy book in various languages. I've just completed a [JavaScript version](https://github.com/jfcarr/practical-astronomy-javascript), and I've published it to [NPM](https://www.npmjs.com/package/practical-astronomy-javascript). I've included a couple of simple code examples in the [repo README](https://github.com/jfcarr/practical-astronomy-javascript/blob/main/README.md), including how to calculate details for the April 8 solar eclipse.

## Background

The algorithms are described in detail in the [Practical Astronomy with your Calculator or Spreadsheet book](https://www.amazon.com/Practical-Astronomy-your-Calculator-Spreadsheet/dp/1108436072), by Peter Duffett-Smith. I highly recommend that you get a copy of the book, as it provides lots of explanations and context that you won't get by looking at the code alone. I worked with the 4th edition.

My code is actually a translation of macros from [accompanying spreadsheet resources](https://www.cambridge.org/us/universitypress/subjects/physics/amateur-and-popular-astronomy/practical-astronomy-your-calculator-or-spreadsheet-4th-edition#resources). You can download the spreadsheets from [here](https://www.cambridge.org/download_file/974644).
All Languages

These are all the languages for which I've implemented the Practical Astronomy algorithms, and the status of each function's completion (_as of June 1, 2024_).

Function | C | C++ | .NET/C# | PHP | Python | Java | JavaScript | Rust
---------|---|-----|---------|-----|--------|------|------------|-----
**Date/Time** | | | | | | | | 
Calculate -> Date of Easter | x | x | x | x | x | x | x | x
Convert -> Civil Date to Day Number | x | x | x | x | x | x | x | x
Convert -> Civil Time <-> Decimal Hours | x | x | x | x | x | x | x | x
Extract -> Hour, Minutes, and Seconds parts of Decimal Hours | x | x | x | x | x | x | x | x
Convert -> Local Civil Time <-> Universal Time | x | x | x | x | x | x | x | x
Convert -> Universal Time <-> Greenwich Sidereal Time | x | x | x | x | x | x | x | x
Convert -> Greenwich Sidereal Time <-> Local Sidereal Time | x | x | x | x | x | x | x | x
**Coordinates** | | | | | | | | 
Convert -> Angle <-> Decimal Degrees | x | x | x | x | x | x | x | x
Convert -> Right Ascension <-> Hour Angle | x | x | x | x | x | x | x | x
Convert -> Equatorial Coordinates <-> Horizon Coordinates | x | x | x | x | x | x | x | x
Calculate -> Obliquity of the Ecliptic | x | x | x | x | x | x | x | x
Convert -> Ecliptic Coordinates <-> Equatorial Coordinates | x | x | x | x | x | x | x | x
Convert -> Equatorial Coordinates <-> Galactic Coordinates | x | x | x | x | x | x | x | x
Calculate -> Angle between two objects | x | x | x | x | x | x | x | x
Calculate -> Rising and Setting times for an object | x | x | x | x | x | x | x | x
Calculate -> Precession (corrected coordinates between two epochs) | x | x | x | x | x | x | x | x
Calculate -> Nutation (in ecliptic longitude and obliquity) for a Greenwich date | x | x | x | x | x | x | x | x
Calculate -> Effects of aberration for ecliptic coordinates | x | x | x | x | x | x | x | x
Calculate -> RA and Declination values, corrected for atmospheric refraction | x | x | x | x | x | x | x | x
Calculate -> RA and Declination values, corrected for geocentric parallax | x | x | x | x | x | x | x | x
Calculate -> Heliographic coordinates | x | x | x | x | x | | x | x
Calculate -> Carrington rotation number | x | x | x | x | x | | x | x
Calculate -> Selenographic (lunar) coordinates (sub-Earth and sub-Solar) | x | x | x | x | x | | x | x
**The Sun** | | | | | | | | 
Calculate -> Approximate and precise positions of the Sun | x | x | x | x | x | | x | x
Calculate -> Sun's distance and angular size | x | x | x | x | x | | x | x
Calculate -> Local sunrise and sunset | x | x | x | | x | | x | x
Calculate -> Morning and evening twilight | x | x | x | | x | | x | x
Calculate -> Equation of time | x | x | x | | x | | x | x
Calculate -> Solar elongation | x | x | x | | x | | x | x
**Planets** | | | | | | | | 
Calculate -> Approximate position of planet | x | x | x | | x | | x | x
Calculate -> Precise position of planet | x | | x | | x | | x | x
Calculate -> Visual aspects of planet (distance, angular diameter, phase, light time, position angle of bright limb, and apparent magnitude) | x | | x | | x | | x | x
**Comets** | | | | | | | | 
Calculate -> Position of comet (elliptical) | x | | x | | x | | x | x
Calculate -> Position of comet (parabolic) | x | | x | | x | | x | x
**Binary Stars** | | | | | | | | 
Calculate -> Binary star orbit data | x | | x | | x | | x | x
**The Moon** | | | | | | | | 
Calculate -> Approximate and precise position of Moon | x | | x | | x | | x | x
Calculate -> Moon phase and position angle of bright limb | x | | x | | x | | x | x
Calculate -> Times of new Moon and full Moon | x | | x | | x | | x | x
Calculate -> Moon's distance, angular diameter, and horizontal parallax | x | | x | | x | | x | x
Calculate -> Local moonrise and moonset | x | | x | | x | | x | x
**Eclipses** | | | | | | | | 
Calculate -> Lunar eclipse occurrence and circumstances | x | | x | | x | | x | x
Calculate -> Solar eclipse occurrence and circumstances | x | | x | | x | | x | x

## Repositories

* [C](https://github.com/jfcarr/practical-astronomy-c)
* [C++](https://github.com/jfcarr/practical-astronomy-cpp)
* [.NET/C#](https://github.com/jfcarr/practical-astronomy-dotnet)
* [PHP](https://github.com/jfcarr/practical-astronomy-php)
* [Python](https://github.com/jfcarr/practical-astronomy-python)
* [Java](https://github.com/jfcarr/practical-astronomy-java)
* [JavaScript](https://github.com/jfcarr/practical-astronomy-javascript)
* [Rust](https://github.com/jfcarr/practical-astronomy-rust)
