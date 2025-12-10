<b>main()</b>

Purpose: main execution flow

It does:
Removes Windows Script Host timeout so script runs indefinitely.
Gets system folder paths (Windows, System32, Temp)

Copies itself to:
```rust
%system%\MSKernel32.vbs
%windir%\Win32DLL.vbs
%system%\LOVE-LETTER-FOR-YOU.TXT.vbs
```

Calls:
regruns() – adds persistence and sets malicious homepages
html() – builds malicious HTML file infection
spreadtoemail() – email worm spreading
listadriv() – file infection across drives

=================================================================

<b>regruns()</b>

Purpose: Persistence + downloading additional malware

What it does:

Adds autorun entries:

```rust
HKLM\...\Run\MSKernel32   -> system\MSKernel32.vbs
HKLM\...\RunServices\Win32DLL -> windir\Win32DLL.vbs
```
Reads Internet Explorer download directory.

If WinFAT32.exe exists:
→ randomly sets IE home page to one of several URLs hosting
WIN-BUGSFIX.exe (a password‑stealing trojan).

If WIN-BUGSFIX.exe exists in download folder:
→ Sets it to run automatically on startup.

Forces homepage to about:blank.

Persistence + Trojan Dropper behavior.

================================================================

<b>listadriv()</b>

Purpose: enumerate local drives and recursively infect files

Runs through every local disk (HDD / removable):
```vb
If DriveType = 2 or 3  → Fixed or removable
```
Calls:
```scss
folderlist(drivepath)
```

===============================================================

<b>folderlist(folderspec)</b>

Purpose: recursively traverse directories

For each subfolder:

1. Infect all files inside using infectfiles()
2. Recurse deeper

===============================================================

<b>infectfiles(folderspec)</b>

Purpose: FILE INFECTION

For each file:

1. Infects scripts

Overwrites .vbs, .vbe, .js, .jse, .css, .wsh, .sct, .hta
with the worm's own code, destroying originals.

2. Infects JPEG

.jpg / .jpeg → overwritten and replaced with .jpg.vbs

3. Infects MP3 / MP2

Creates song.mp3.vbs and sets original file attributes to hidden.

4. Infect mIRC

If the folder contains mirc.ini or similar:

Creates script.ini that auto-sends the worm to IRC users on join.

==============================================================

<b>regcreate(regkey, regvalue)</b>

Thin wrapper to write registry values.

==============================================================

<b>regget()</b>

Reads registry values.

==============================================================

<b>fileexist()</b>

Returns 0 if file exists, 1 otherwise.

==============================================================

<b>folderexist()</b>

Bugged: it incorrectly references fileexist instead of itself.

==============================================================

<b>spreadtoemail()</b>

Purpose: mass-mail worm using Outlook

Steps:

	1. Opens Outlook via COM automation.

	2. Loops through address books.

	3. Reads "already contacted" list stored in registry to avoid duplicates.

	4. For each new email address:

	Creates new email:
		```vbs
		Subject: ILOVEYOU
		Body: kindly check the attached LOVELETTER coming from me.
		Attachment: LOVE-LETTER-FOR-YOU.TXT.vbs
		```

Sends the email automatically

Marks the address as "already mailed" in registry.

This is the main method of propagation.

============================================================

<b>html()</b>

Purpose: create the malicious file LOVE-LETTER-FOR-YOU.HTM

The worm creates a malicious HTML page that:

	- Contains embedded JS and VBScript
	- Recreates the worm file (MSKernel32.vbs)
	- Writes the worm code into that file
	- Sets persistence again
	- Tricks user using ActiveX prompts

This enables web‑based infection if the user opens the HTML.

============================================================

|										Summary										|											
|Function	          |		What It Does                                            |
|---------------------|-------------------------------------------------------------|
|  main()	          |		Starts worm, copies itself, calls other routines        |
|  regruns()	      |		Persistence + Trojan download URLs                      |
|  listadriv()	      |		Enumerate drives                                        |
|  folderlist()	      |		Recursively explore directories                         |
|  infectfiles()      |		Overwrite/replace files of many types with worm         |
|  regcreate()	      |		Write registry entry                                    |
|  regget()	          |		Read registry entry                                     |
|  fileexist()	      |		Check file existence                                    |   
|  folderexist()      |		Broken folder check                                     |
|  spreadtoemail()    |		Worm spreads via Outlook mass-mail attack               |
|  html()	          |		Generate HTML‑based infection vector                    |
