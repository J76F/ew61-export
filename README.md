# Aanpassingen
SQL output toegevoegd; maakt nog geen tabel aan; basis staat weergegeven in bestand songs_tabel.sql.

# EasyWorship 6.1 Exporter

* Author: James Inglis <hello@jamesinglis.no>
* URL: https://github.com/jamesinglis/ew61-export
* License: MIT (i.e. do whatever you want with it, but no warranty!)


## Overview

This PHP-based solution accesses the EasyWorship 6.1 database and exports all of the songs as plain text files suitable for importing into another lyric projection system (tested with ProPresenter 6.1).

Previous versions of EasyWorship (including 6.0) used a different database storage engine. EasyWorship 6.1 now stores its databases in SQLite3 format, which meant that all other export utilities no longer worked (see below for Other Export Methods that work with previous versions).
 
This solution was conceived when faced with an EasyWorship 6.1 library of 800+ songs which no-one wanted to retype! It caused me to look closely at the database format and come to the conclusion that the best solution was to extract the data from the tables and clean up the contents programmatically.


## Other Export Methods

Previous versions of EasyWorship have had different 3rd party solutions that have sprung up.

For further information about these, the following links may be useful:

* EasySearch - http://www.headwest-productions.co.uk/Software.htm
* OpenLP's song importer - https://manual.openlp.org/songs.html
* Video Psalm - http://myvideopsalm.weebly.com/how-to-import-songs-and-bibles-from-other-sources.html
* https://forums.openlp.org/discussion/831/import-easyworship-songs


## Getting Started

To use this solution, you'll need to be comfortable with running PHP scripts. It doesn't have a graphical user interface, however you can run this from the command line or from a web browser.

* Ensure that PHP has PDO SQLite support and MBString support (see https://github.com/jamesinglis/ew61-export/issues/1 for details - thanks jonathantjm!)
* Clone this repository to a location on your computer that PHP has write access to.
* Locate the EasyWorship database files in the EasyWorship data directory
    * This is commonly in C:\Users\Public\Softouch\EasyWorship\Default\Databases\Data\ but may vary
    * On my installation, the installer for EasyWorship 6.1 had created a new subfolder for the 6.1 profile at C:\Users\Public\Softouch\EasyWorship\Default\6.1\
* Copy the following files from your EasyWorship data directory to [this repository root]/databases/:
    * Songs.db
    * SongWords.db
* Review the custom formatting options in config.php and set the values to true or false
* The default settings will export your songs in plain text files. If you want to use the experimental ProPresenter6 export feature, change `$file_export_type = 'plain_text';` to `$file_export_type = 'propresenter6';` in config.php.
* From the command line, change directory to the repository root and run:
    * php ./process.php
* Alternatively, if the repository in a directory served by a web server, you may run process.php in a web browser.
* The progress of the conversion is displayed on the screen
* The exported files can be found in [this repository root]/output/

Note: this script does not write anything to the EasyWorship database files, however accidents can happen. To safeguard your existing data, make sure that you run this script on copies of your database files. I take no responsibility for any data loss that may occur directly or indirectly from using this script! 


## Features

* Exports all songs and lyrics from EasyWorship 6.1 database files into a date-labeled sub-directory in /output directory
* Attempts to strip all formatting from song words to make for a clean import into target system
* Outputs to text files by default with experimental ProPresenter6 export (specify $file_export_type in config.php)
* Attempts to handle inconsistencies with text encoding
* Reflow song sections over 2 lines long (can be changed in config file)
* Custom formatting functions (needs to be enabled in config file)
    * 'capitalize_names' - Capitalize some property names (defined in $words_to_capitalize variable)
    * 'remove_end_punctuation' - Remove line-ending punctuation
    * 'fix_mid_line_punctuation' - Fixes mid-line punctuation - replaces '.' with a line break, and makes sure ',;\?!' is always followed by a space
    * 'straighten_curly_quotes' - Straighten curly quotes
    * 'remove_x2' - Remove 'x2' type references and empty parentheses
    * 'start_with_capital' - Begin all lines with capital letter
    * 'standardize_song_sections' - Standardize the names of the song sections to fit ProPresenter's default 'Groups'
    * 'standardize_title_format' - Standardize the formatting of the name of the songs
    * 'prevent_overwrites' - Prevent overwriting files - adds a '(1)' style suffix
    * 'add_metadata_to_export_files' - Adds the metadata block to the top of the export files
    * 'condense_slide_breaks' - Condense all slide breaks
    * 'reflow_large_blocks' - Reflow large blocks (target line length defined in $reflow_max_lines)
    * 'output_subdirectory' - Output files in a timestamp labelled sub-directory
    * 'aggressive_text_encoding' - Aggressively convert songs to ISO-8859-1 character set - this will most likely break songs with non-Latin characters!
* ProPresenter-specific settings
    * 'prop6_add_blank_intro' - Adds a blank "Intro" slide to ProPresenter files (useful for adding cues and arrangements)
	* 'prop6_add_blank_end' - Adds a blank "End" slide to ProPresenter files (useful for adding cues and arrangements)
    * 'prop6_add_hotkeys' - Assign hot keys to slide groups

### NEW in v0.2 - Export direct to ProPresenter6 .pro6 fileformat

* Creates a directory of .pro6 song files
* Creates ProPresenter slide groups from EasyWorship song sections
* Adds hot keys to ProPresenter slide groups following [Luke McElroy's 2014 recommendation](https://www.worshiphousemedia.com/church-media-blog/software/simple-effective-propresenter-hot-key-system/)
    * Title - T
    * Intro - I
    * Verse 1 - A
    * Verse 2 - S
    * Verse 3 - D
    * Verse 4 - F
    * Verse 5 - G
    * Verse 6 - H
    * Verse 7 - J
    * Verse 8 - K
    * Verse 9 - L
    * Pre-Chorus - X
    * Chorus 1 - C
    * Chorus 2 - V
    * Chorus 3 - 
    * Bridge 1 - B
    * Bridge 2 - M
    * Tag - N
    * End - Z
    
### NEW in v0.3 - Better text export

* I didn't realize until now that the data is stored in EasyWorship in standard RTF format! My manual functions to strip this formatting didn't work for everyone. Implementing a more robust RTF to text formatting solution.
* ProPresenter hot keys are now managed in the config file - easier to override!

## To Do in Future
(if there's a demand for it)

* Clean up the code so it's cleaner and more extensible
    * Not the best coding - this started as a quick and dirty solution!
* Export to other file formats suitable for other projection
* Include all song metadata
    * My test library didn't have any song metadata so it wasn't a priority!


## Questions & Answers

* Why is this written in PHP? Surely [insert language/framework here] would be a better choice!
    * You may be right, however this script was developed to meet an immediate need for myself and PHP is the language in which I am most proficient!
* What version of PHP do I need to run this?
    * The solution was written for PHP 5.6. I have run conversions with it in December 2021 with PHP 8.0.12 (with minor change made in v0.3.2). 
* Why am I getting strange characters in my export files?
    * It's most likely a text-encoding issue. This script was developed for a non-English EasyWorship library so unicode characters should be handled correctly, however I can't make any promises!
    * For what it's worth, my CLI version of PHP 5.6 garbles the "Å" character but the web server version of the same PHP installation has no problems encoding this character.
* Why am I getting random references to fonts at the top of my song exports?
    * This is a remnant of the RTF formatting - I'm manually stripping out references to Arial and Tahoma, but if you used a different font in EasyWorship, that font name may show up. If this happens, add the name of your font to the regex on line 130 of functions.php. 
    
## Version history

### 0.3.2 (2021-12-07)
* Tweak for PHP8 compatibility
* Adds reference to PHP library dependencies answered in jonathantjm's issue
* Update config and readme documentation

### 0.3.1 (2018-10-21)
* Adds missing ProPresenter slide text template
* Update config and readme documentation

### 0.3 (2018-04-11)

* Adds real RTF processing to the song data from EasyWorship!
* Move ProPresenter hot keys to config file. 

### 0.2 (2018-03-02)

* Numerous bug fixes from real-world use
* Adds experimental ProPresenter6 export feature

### 0.1 (2016-09-03)

* Initial version
