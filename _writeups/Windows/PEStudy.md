[**< back**](/readme.md)

![PEStudyBanner](/_storage/_img/PEStudy/PEStudyBanner.jpg)
# PE File Format Study 

## So What is this writeup for exactly?!

I want do discuss `PE File` format thoroughly in this writeup with C/C++ code examples, nevertheless I've been working as a Malware Analyst for years for now, I have never really dug into `PE file` format so religiosly... So here am I doing it right now and right here, hope this study will be useful for others too.

## Basic Review of PE file format

<img src="/_storage/_img/PEStudy/PEStructureOutline.jpg" height="263">  <img src="/_storage/_img/PEStudy/PEBearSections.jpg" width="183">


The `PE` (Portable Executable) file structure is a standardized format used by Windows, it is based on `COFF` (Common Object File Format, used for storing compiled code in a structured manner, `coff` can be used to store individual functions or symbols, fragments of programs, libraries or entire executables), DLLs, device drivers and other binary files. It is providing a structure for organizing such files and making them linkable and executable on Windows.

#### DOS Header
Any PE file begins with a DOS Header, which is also called MS-DOS header. A 64-byte-long structure, which is not essential for functioning on modern Windows systems, for now it's only purpose is backwards compatiblity for old DOS systems. In case of trying to launch PE file on a DOS system it will just output an error code `This program cannot be run in DOS mode` and exit with error.

`winnt.h` header provides a structure for `dos header`, _IMAGE_DOS_HEADER

```
typedef struct _IMAGE_DOS_HEADER {
    WORD e_magic;         // Magic number ("MZ" in ASCII)
    WORD e_cblp;          // Bytes on the last page of file
    WORD e_cp;            // Pages in file
    WORD e_crlc;          // Relocations
    WORD e_cparhdr;       // Size of the header in paragraphs
    WORD e_minalloc;      // Minimum extra paragraphs needed
    WORD e_maxalloc;      // Maximum extra paragraphs needed
    WORD e_ss;            // Initial (relative) SS value
    WORD e_sp;            // Initial SP value
    WORD e_csum;          // Checksum
    WORD e_ip;            // Initial IP value
    WORD e_cs;            // Initial (relative) CS value
    WORD e_lfarlc;        // File address of relocation table
    WORD e_ovno;          // Overlay number
    WORD e_res[4];        // Reserved words (unused)
    WORD e_oemid;         // OEM identifier (for e_oeminfo)
    WORD e_oeminfo;       // OEM information
    WORD e_res2[10];      // Reserved words (unused)
    LONG e_lfanew;        // File address of the PE header
} IMAGE_DOS_HEADER, *PIMAGE_DOS_HEADER;

```


#### DOS Stub
`DOS Stub` is an executable itself which basically just checks if the binary is running on a DOS system, and if so prints an error and exits with an error code `This program cannot be run in DOS mode`. 
We can check this by running any PE executable in DOS emulator.

We can dump a stub section from any executable using PEBear (Extracting first 64 bytes).
<img src="/_storage/_img/PEStudy/DumpStub.jpg">

Then mount the folder on DOS emulator and try to run it.

<img src="/_storage/_img/PEStudy/StubExecutionInDOS.png">

For a bit deeper dive we can open up the `stub` executable in IDA and disassemble it in `16-bit mode`.
<img src="/_storage/_img/PEStudy/StubOpenInIda.png">
<img src="/_storage/_img/PEStudy/Stub16bitInIda.png">

The disassembled code looks as follows
<img src="/_storage/_img/PEStudy/StubDisassemblyInIda.png">

If you take a look at DOS 8086 interrupts, it's pretty easy to analyze the disassembled code, it aligns data segment and code segment, prints the `This program cannot be run in DOS mode` and returns an error, that's the whole philosophy behind the DOS Stub code.

**Rich Header**

After the DOS stub code and before `NT Header`, there's an another chunk of bytes called `Rich Header`, 


#### PE Header/NT Header
#### Section Table
#### Sections

