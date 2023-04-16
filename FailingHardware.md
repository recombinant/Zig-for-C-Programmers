# Failing Hardware

The client had a definite requirement to modernize their in-house software. The software would only run on their aging Windows XP computers. They had the source code on a single 3.5" floppy disk.

This warranted some examination of the situation before considering any work…

- the executables were 16 bit. These would not run natively on modern 64 bit Windows.
- the 3.5" floppy contained a single zip file
  - the zip file contained only .cpp and .hpp source dated the early 1990s.
  - the source was formatted with MS-DOS CrLf line endings plus a single Ctrl-Z at the end of each file (as expected for this era).
  - the source appeared to target the contemporaneous Borland C++ compiler.
  - there were some very obvious bugs.
- the customer used [Pandas](https://pandas.pydata.org/) and would be amenable to Python being installed if not already available.
- the customer could not name any editor that they may have been using.
- the customer did not know about source control.

After some discussion, the customer had some requirements…

- the customer did not want their software rewritten in the latest fancy software with a nice logo, slick website and proselytizing proponents.
- the customer did not want to install any bloatware.
- the customer did not want any bugs fixed unless they crashed the system or caused any obvious problems.
- speed and confidentiality.

The job would be done with the following…

- [Zig](https://ziglang.org/) for the compiler toolchain
- [Fossil SCM](https://fossil-scm.org/) for source control
- [Microsoft Visual Studio Code](https://code.visualstudio.com/) as the editor - C/C++ and Zig plugins.
- The customer would provide a [PFY](https://en.wiktionary.org/wiki/PFY) to control access to the source code.

The job proceeded.

- write a Python script to
    - prove that the source was ASCII
    - remove the trailing Ctrl-Z and trailing whitespace from the files
    - remove the trailing whitespace from individual lines
    - ensure that there was one blank line at the end of each file
    - save the files with Lf line endings (if the file had changed)

The .cpp files did not contain the text "new", "delete" or "class" and there were no C++ headers. An educated guess suggested that these were C files that had been given a .cpp extension. This was common practice when the Microsoft, Borland, Wacom and Zortech C++ compilers were first appearing. Changing to C extensions was approved as it would limit any future developer's potential for "getting clever".

- write another Python script
    - change the file extensions .cpp ⇒ .c and .hpp ⇒ .h
    - tweak #include statements with regex to reflect the header file name change
    - eliminate the redundant "near" keyword (used for some memory models on 16 bit Windows).

The next step was to compile files. Simplified…

- move all .h files to a freshly minted "include" folder
- find everything without a main() and move it to a freshly minted "lib" folder
- write one batch file to `zig cc` the "lib" folder contents into a static library and separate `zig cc` lines for each executable using the static library
- by trial and error determine the files that did not belong in the "lib" folder (usually because of name collisions)
- refactor any Borland specific code to use the C Standard Library - just a few time-related refactorings, everything else compiled as vanilla C

The next step was to run the executables…

- Only one crash. No debugger, found by bisecting the code with puts() statements. Classic buffer overflow. Easily fixed.
```C
char am_pm[2];
strcpy(am_pm, "am");
```
This was the only crash. Someone had not understood C strings and sentinels. More than just this once.

The code was good enough, the executables ran, and the job was complete.

