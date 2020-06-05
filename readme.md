![](https://github.com/sumatrapdfreader/sumatrapdf/workflows/Build/badge.svg)

## SumatraPDF Reader WITH Json Loading Ability

### Original SumatraPDF Information

SumatraPDF is a multi-format (PDF, EPUB, MOBI, FB2, CHM, XPS, DjVu) reader
for Windows under (A)GPLv3 license, with some code under BSD license (see
AUTHORS).

More information:
* [main website](http://www.sumatrapdfreader.org) with downloads and documentation
* [manual](https://www.sumatrapdfreader.org/manual.html)
* [all other docs](https://www.sumatrapdfreader.org/docs/SumatraPDF-documentation-fed36a5624d443fe9f7be0e410ecd715.html)

To compile you need Visual Studio 2019. [Free Community edition](https://www.visualstudio.com/vs/community/) works.

I tend to update to the latest release of Visual Studio. Lately C++ evolves quickly
and Visual Studio constantly adds latest capabilities. If things don't compile,
first make sure you're using the latest version of Visual Studio.

Open `vs2019/SumatraPDF.sln` when using Visual Studio 2019

You need at least version 16.4 of Visual Studio 2019.

Notes on targets:
* `x32_sp` target is for building for Windows XP and requires v141_xp toolset, which is an optional component of Visual Studio setup
* `x32_asan` target is for enabling address sanitizer, only works in 32-bit Release build and requires installing an optional "C++ AddressSanitizers" component (see https://devblogs.microsoft.com/cppblog/addresssanitizer-asan-for-windows-with-msvc/ for more information)

### Asan notes

Flags: https://github.com/google/sanitizers/wiki/SanitizerCommonFlags
Can be set with env variable:
* `ASAN_OPTIONS=allocator_may_return_null=1:verbosity=1:check_malloc_usable_size=false:suppressions="C:\Users\kjk\src\sumatrapdf\asan.supp"`

In Visual Studio, this is in  `Debugging`, `Environment` section.

Supressing issues: https://clang.llvm.org/docs/AddressSanitizer.html#issue-suppression
Note: I couldn't get supressing to work.

## Changes to this fork
This is a rather simple fork to v3.3 of SumatraPDF that allows Sumatra to use ROBOCOPY to get PDF's from a network location and store them locally and then open them up for viewing. Originally this was all handeled by a simple bat script but issues occured when users "pinned" the exe to the toolbar preventing the batscript from running.

A workaround for this was found using the information found here
http://rolfsnotepad.blogspot.com/2016/06/unpin-edge-and-pin-internet-explorer.html
but figured Sumatra is open source and I could probably handel all of this internally inside the exe.

This is done by reading JSON files (see file structure below) using rapidJSON libary (found here https://github.com/Tencent/rapidjson/ )

The largest changes to SumatraPDF are found in Flags.cpp and Flags.h mostly adding extra variables for me to store json information into. Some go unused but for now everything seems to work. The Icon was also changed because I could....

### File Structure Local
* **Root Folder**
  - fileLocationSettings.json
  - README.txt (explains the file structure
  * **PDF Folder**
    - contains the PDFs referenced in pdfinfo.json
    - These are robocopied from the remotepath directory if remote is enabled
  * **Viewers** (This folder contains a subfolder for every seperate instance that you want)
    * **SumatraInstance**
      - IconCredit.txt (credits the artist from the thenounproject.com that the icons are based off)
      - mode.json
      - runtimeIcon.ico (this is optional)
      - SumatraPDF.exe
     
### Json Files Local
These are setup for my desired use but they should be easy enough to understand

<mode.json>
```
  {
    "mode": string, //Subviewer Type
    "icon": string //Path to Icon
  }
```
<fileLocationSettings.json>
```
  {
    "office": string, //this sets the user type
    "remotePath": string, // Path to the remote server
    "localPath": string, // Path to install directory, not needed
    "useRemote": boolean // true robocopies the PDFs to the local folder and updates the pdfinfo.json
  }
  ```
  <pdfinfo.json>
  ```
  {
    "Offices": array of strings, //this isn't currently used
    "Codes": array of strings, //this isn't currently used
    "PDFLocations": 
    [
      {
        "Location": string, //path to PDF file
        "offices": array of strings, //users to load the pdf file for
        "type": string // Subviewer type
      }
    ]
   }
    ```
  
