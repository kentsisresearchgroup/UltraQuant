# Change Log
All notable changes to MaxQuant will be documented in this file.

## [1.6.2.3] - 2018-06-11
### Fixed
- MaxQuant-247 MQ is crashing if more than one parameter group is specified

## [1.6.2.2] - 2018-06-10
### Added
- TMT support for timsTOF

### Fixed
- MaxQuant-246 some searches crash after main search (error reading search results)
- MaxQuant-243 MaxQuant 1.6.2.1 creates invalid XML in mqpar file (spaces in encoding specification) resolved

 ## [1.6.2.1] - 2018-06-01
 ### Added
 - A new commandline interface for AndromedaCmd.exe

 ### Fixed
  - "mqpar.xml conversion error" is resolved (MaxQuant-242)
  - Bruker TIMS files are loadable

## [1.6.2.0] - 2018-05-25
### Added
- Fasta file input parameter: parse rules and taxonomy can now be specified
  in the parameter tabs
- Bruker TIMS Tof PASEF is supported  
- Additional parameters for command line call

### Fixed
 - "Could not find file tmpPeptideFiles" is resolved (MaxQuant-192)

 ## [1.6.1.0] - 2017-12-08
 ### Added

 ### Fixed
  - MaxQuant running smoothly on Ubuntu Linux
  - MaxQuant-222 Fragmentation type always identified as CID

 ## [1.6.0.16] - 2017-08-29
 ### Fixed
  - Added missing button icons/tool tips to Andromeda configuration parse rule testing GUI [[MaxQuant-197](https://maxquant.myjetbrains.com/youtrack/issue/MaxQuant-197)].
  - MaxQuant-191 Export of MS/MS spectrum to pdf in MaxQuant 1.6.0.1 viewer
  - Equal results between mono and .NET framework

## [1.6.0.13] - 2017-08-11
### Added
  - Raw data access API is in open source code on GitHub
  - MzXml raw data reader is in open source code on GitHub

### Fixed
  - 3D spectrum viewer is working again.
  - MS3 based TMT quantification is working again.
  - Special characters as german umlauts can be used in file paths for Bruker.

## [1.5.8.0] - 2017-03-13
### Added
 - Winforms based GUI.

## [1.5.7.4] - 2017-01-27
### Fixed
 - Group-specific parameters are loaded properly from the mqpar.xml file.
 - Command line version works again.

## [1.5.7.0] - 2017-01-12
### Fixed
 - Support for NeuCode labeling added.

## [1.5.6.5] - 2016-11-22
### Fixed
 - Removed possibility to load zipped fasta files since it is not working.
 - Specifying fractions does not lead to a crash any more upon clicking starrt.

## [1.5.6.0] - 2016-10-31
### Added
 - This version corresponds to the feature set described in the the publication Tyanova et al. (2016) Nature Protocols 11, 2301-19.
