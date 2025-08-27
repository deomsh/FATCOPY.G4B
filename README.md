## FATCOPY.G4B v0.5.1 (20250827)

<pre><code>Function: front-end for Grubutil 'FAT', function 'copy'
FATCOPY.G4B [--mdbase=sector] DEVICE1/PATH1/FILE1 DEVICE2/PATH2/FILE2 switches
FATCOPY.G4B /? (this text)
Copy files on DEVICE1 to DEVICE2; if omitted DEVICE1/DEVICE2: from/to root
Copy from hidden partitions and to hidden FAT partitions too!
Default 8+3 File Names (except with /lfn[:case], see below)
Arguments space-separated, switches: lower/uppercase free
More convenient => insmod (bd)/fatutils/fatcopy.g4b copy (insmode FAT too!) or rename
--mdbase=sector => set base memory of memdrives, default 0x3000*
*  FAT directory-parsing needs 2048KB, LS directory-parsing needs 128KB
*  Minimum 0x3000, rejected: 0x0000-0x2999, 0x4000-0xD460 and 0x12000-0x12FFF

(source) DEVICE1 = (fd#) & (hd#,#) & (0x#) & (#) with ISO/CD/DVD
(target) DEVICE2 = (fd#) & (hd#,#) & (0x#) & (#) FAT only
/PATH1/ = source directory => without DEVICE1 => on root (PATH1/ too)
/PATH2/ = target directory => without DEVICE2 => on root (PATH2/ too)
if PATH2/ is not specified, target directory is the root directory (/)
FILE1 = copy specified file => on root without DEVICE1 and/or /PATH1/
FILE1 accepts wildcard: '*' AFTER chars in Name and/or Extension (NOT: '?')
Without FILE1 full directory will be copied ('*.*' is NOT needed)
FILE2 = copy/rename FILE1 to FILE2 => on root without DEVICE2 and/or /PATH2/

General switches: /d /s /[-]o /i /p /q /v /t /[-]x:~ /[-]ext:EXT /[-]r /maxfiles:N /maxbyte:n /z /n
/d = add PATH1 to PATH2
/s = copy content of ALL subdirectories too
/[-]o = [never]overwrite files, BTW: /[-] means choice, type /- (!)
/i = incremental copying by comparing CRC32 of files on source and target
/p = pause after each screen (Key Q can be used to exit)
/q = quiet copying: error messages and last file-count message only *
/v = verify with message of status- not with /t (try /v if copying stops)
/t = trial without actual copying (can be used as simple DIR)
/[-]x:~ = only SFN-equivalent of LFN/real shortened LFN will [not] be copied **
/[-]ext:EXT = files with EXTension [not] copied- separator: dot only 3*
/[-]r = Do not ask [not] overwrite read-only files (attributes unset!)- not /t
/maxfiles:N = set max number of files to copy
/maxbyte:n = set max number of bytes to copy- takes k, m, g postfix operator
/z = copy zero byte files (default: ommitted), fake overwriting only 4*
     copied for files, not for directories - from FAT-source only
/n = do not copy Modification Date/-Time from FAT-source (15%-45% faster)
*  Default verbose copying: mkdir and copy and output messages (less with '*.*')
** Character '?' is FORBIDDEN, reserved for internal use!
** Instead of '~' ONE other character can be used too (for name-part only)
3* Examples: /ext:g4b.lll.bat or /-ext:.7z.cab.img.rar.vhd.zip- first dot free
4* date and time never updated with overwriting, to not loose (LFN-)metadata

FAT directory-parser switches: /a:[-]d[-]a[-]r[-]s[-]h /a /lfn:@ *
/a:[-]d[-]a[-]r[-]s[-]h = [not] copy files/directories with attribute(s) **
/a = copy attributes, NT-casebyte, last access date, modification time and date
*  Default directory parser if copying from FAT-source
*  About max 36 000 files AND max 3 000 subdirectories in one directory
** Attributes not copied by FAT, copied files: archive-attribute, or use /a too

LS parser switches: /sfn[:@]|[/lfn[:case] /a] /e /$ /l+f /filenum:[-]n /nocase
/sfn = copy LFN to SFN (LFN is LOST), mandatory for LFN's on non-FAT FS **
/sfn[:]@ = copy LFN to SFN and keep long name inside SFN with @ instead of ~ 3*
/lfn = copy LFN's, library needed: ATTRIBFT.LLL (in same folder as FATCOPY.G4B)
/lfn:case = copy LFN of 8+3 Short File Names too, library needed: ATTRIBFT.LLL
/lfn[:]@ = add LFN of saved Long File Names, library needed: ATTRIBFT.LLL
/e = copy empty subdirectories on NTFS (zero-byte file become subdirectory!)
/$ = copy NTFS $-(meta)files/directories (or showed with /t) 4*
/l+f = copy Ext2/3/4&ReIseR(2)fs directory lost+found (or showed with /t) 4*
/filenum:[-]n = [not]copy files with filenumber only (n = 1-32767) 5*
/nocase = (try to) force case-insensitivity on case-sensitive File Systems
*  About max 10 000 (8+3) files OR max 10 000 subdirectories in one directory
*  Copy from FAT, NTFS, ISO-9660, JOLIET, UDF, EXFAT, Ext2/3/4 & ReIseR(2)fs
*  Autoswitching to /ls for supported source other then FAT or with /ls-switch
*  Copies from ISO-9660/JOLIET & UDF with File Version Number Suffix (;1-32767)
*  Watch with case-sensitive File Systems: PATH1/FILE1 /[-]x:char /[-]ext:EXT
** If exist spaces or '=' in PATH/FILE or switch /noext: use (double-)quotes!
** Double-quote usage: "DEVICE/PATH/FILE"- on ROOT "/PATH1/FILE1" or "FILE1"
** Special case: PATH or FILE containing '='-sign ALWAYS quotes (& "/[-]x:=")
    Or us escaped spaces/ '=': '\ ' or '\=' (try first with switch /t)
** With /lfn wildcard '*' in name/ext-part after maximum 16/6 characters
3* Can be read out and used to restore LFN's in LFN-aware operating system
3* Directories get # instead of @ (file: SFN@num.ext- directory SFN#num.ext)
3* Auto-saved during copying and read-out for existing files (auto with /i)
3* If /[-]x:@ SFN@num.ext not saved but [not]copied- with /sfn:@ '@' reserved
4* (Self-declared) Experts only! $-(meta)files can grow above 4GB, lost+found?
5* Only File Systems supported with File Version Number Suffix

Remarks:
FAT needed, searched in: %~dp0, (bd), ROOT, /, /boot/grub/, /grub/, /g4dll/
File versions: Grubutil FAT 15/02/2015, Grub4Dos 0.4.6a (>=2017 with 'vol')
If TARGET is >= 4GB: use Grubutil FAT >= 2023, April (look in folder 'ext')
FATCOPY.G4B is not not compatible with Grub4Dos 0.4.5b/Grub4Dos 0.4.5c
            is compatible with Grub4Dos for UEFI (other FAT version in 'ext'!)
            but up to latest version (20240901) soon 'out of malloc memory'
UTF-8 characters not supported in first 12 characters of file name
Copying can be stopped by pressing Escape (not on Grub4dos for UEFI)

Example  1: FATCOPY.G4B (hd0,0)/SOMEFILE.EXT (hd0,0)/BACKUP/
Example  2: FATCOPY.G4B (hd0,0)/*.EXT (hd0,0)/BACKUP/EXT/
Example  3: FATCOPY.G4B (hd0,0)/FILE.EX* (hd0,0)/BACKUP/FILE/ /t
Example  4: FATCOPY.G4B (hd0,0)/SOMEFIL*.EX* (hd0,0)/BACKUP/WILDCARD/ /q
Example  5: fatcopy.g4b (fd0)/ (hd0,0)/backup/ /s /maxbyte:1m
Example  6: FATCOPY.G4B (0xe0)/ (hd0,0)/BACKUP/ /sfn
Example  7: FATCOPY.G4B (fd0)/ (hd0,0)/BACKUP/ /s /sfn:@ /v
Example  8: FATCOPY.G4B (hd0,0)/Program\ Files/ (hd0,0)/BACKUP/Program\ Files/ /lfn /s
Example  9: FATCOPY.G4B "(hd0,0)/Program Files/" (hd0,0)/BACKUP/ /lfn /s /d /z
Example 10: FATCOPY.G4B (hd0,0)/PROGRA~1/ (hd0,0)/BACKUP/ /lfn:case /s /d /z /i /a
Example 11: FATCOPY.G4B (fd0)/IO.SYS (hd0,0) /r /a
Example 12: FATCOPY.G4B (hd0,0)/SOMEDI~1/ (hd0,0)/SOMEDI~1/ /s /lfn:@
Example 13: FATCOPY.G4B (hd0,0)/SOMEFILE.EXT (fd0)/NewFile.Ext /lfn:case

Based on copyFF.bat (:cpa & :copyfiles & :sub-dir => originator of call's seems to be Chenall)
Dedicated to Jaclaz for inspiration and help</code></pre>

#### ATTRIBFT.LLL

Concept of 'Loosely Linked Library' is an idea of Wonko the Sane (Jaclaz)  
More information and download: https://github.com/deomsh/ATTRIBFT.LLL  

### HISTORY
Version 0.5.1  
BUGFIX: not compatible with partitions >= ~256GB  

Version 0.5  
NEW: FAT (and ATTRIBFT.LLL if used) not unloaded afterwards if already loaded with insmod  
NEW: copying case from all ls-supported file-systems with switch '/LFN:case' (on truly case-sensitive file-systems first instance copied only)  
BUGFIX: echo 'not enough space on target' if FAT is not found  
BUGFIX: double copy-echo with FILE2 and /O (harmless)  
BUGFIX: copying LFN of FILE1 with switch '/LFN:case' independent of case of FILE1 on command-line  
BUG: no Directory/File-dialog with FILE2 anymore; FIX: xcopy-style dialog removed entirely (target-directory NEEDS '/' at the end: ls-style)  

Version 0.4.2  
NEW: Grub4dos version check (>=20170505)  
NEW: always copy moddate/time unless new switch '/n' is in use  
BUGFIX: total files overwritten -1  
CHANGE: UTF-8 check first 12 characters only  

Version 0.4.1  
NEW: switches '/r' and '/a' compatible with ONE File Allocation Table (number of FAT's = 1)  
BUGFIX: file1 to file2 with suffix on non-ISO9660/UDF = not asking  for next tildenum and not makelfn 
BUGFIX: not copying anymore files with numbered ';'suffix on ISO9660/UDF  

Version 0.4    
NEW: switch /lfn[:]@ to add Long Files Names from previously saved to XXXXXX#n/@n[.EXT]-files to 'tilded (XXXXXX~n[.EXT])' Short File Names (needed: Loosely Linked Library ATTRIBFT.LLL)  
NEW: switch /lfn[:case] to copy Long File Names (needed: Loosely Linked Library ATTRIBFT.LLL)  
NEW: switch /z to copy zerobyte-files (NOT possible if source is NTFS)  
NEW: switch /a to copy all atrributes on source files to target files  
NEW: switch /maxfiles:n to limit maximum of copied bytes  
NEW: switch /maxfiles:N to limit maximum of copied files  
NEW: dialog to overwrite read-only files, do in advance with switch /r  
IMPROVED: switch /sfn:@ has auto-set tilded file number by read-out of already saved Long File Names  
IMPROVED: protection against errors on command-line  
Changes to switch in use to change MDBASE  
Various bugfixes  

Version 0.3  
Earlier published on msfn (download: members only):  
https://msfn.org/board/topic/180597-exploring-setup-p-isdetectbus-and-grub4dos-8%C2%BD-times-win98se-on-usb-drive/?do=findComment&comment=1193210  

Version 0.2  
Earlier published on msfn (download: members only):  
https://msfn.org/board/topic/180597-exploring-setup-p-isdetectbus-and-grub4dos-8%C2%BD-times-win98se-on-usb-drive/?do=findComment&comment=1182924  

Version 0.1  
Earlier published on msfn (download: members only):  
https://msfn.org/board/topic/180597-exploring-setup-p-isdetectbus-and-grub4dos-8%C2%BD-times-win98se-on-usb-drive/?do=findComment&comment=1182924  

### SCREENSHOTS
![TEXTSTAT G4B FATCOPY G4B version 0 5 1 after cleaning](https://github.com/user-attachments/assets/99574291-4ed9-4fbb-91ef-5832d336b81e)

Example of making target directory with copying a file and example use of asterisk wildcard  

![FATCOPY copying file AND copying Sasterisk BAT, to not existing directories I](https://github.com/user-attachments/assets/dc90a32e-8ba8-4f5c-a8ac-01d40b4f06b0)

Example of copying subdirectories too: switch /s combined with switch /maxbyte:n  

![FATCOPY copy subdirectories too, with switch maxbyte](https://github.com/user-attachments/assets/6767efe2-0f92-489c-8fe3-ee9403e71918)

Example of use of switch /sfn to force making Short File Names on target (default: zerobyte files not copied). Source file system: ISO-9660 (Joliet)

![FATCOPY use of switch -sfn to force making Short File Names on target (default zerobyte files not copied)](https://github.com/user-attachments/assets/73626c7f-51d0-4dde-a5e0-d0a90760a595)

Example of use of switch /sfn:@ to force making Short File Names on target AND save Long File Names to files. With switch /z to force copying zerobyte files

![FATCOPY use of switch -sfn@ to force making Short File Names on target AND save Long File Names to file  With switch -z to force copying zerobyte files](https://github.com/user-attachments/assets/f68f0f77-d947-42dd-a917-4595d1fffcea)

Example of use of switch /lfn:@ to add Long File Names earlier saved to files (identical source and target allowed!). Before: saved Long File Names read-out with FATLSDIR.G4B /SFN:@ and afterwards added Long Files Names with FATLSDIR.G4B /lfn

![FATCOPY use of switch -lfn@ to add Long File Names from file to Short File Names, source and target identical  With FATLSDIR before and after](https://github.com/user-attachments/assets/c9958de4-155d-4a9e-ac76-deb834872bad)

Example of use of double-qoutes around spaced source, switch /d to add source-path to target-path, switch /s to copy non-empty sub-directories too and switch /lfn:case to make Long File Names on target, including case of Short File Names. Test-copy first with switch /t. Result showed with LIST.G4B -s

<img width="1028" height="1036" alt="FATCOPY G4B v0 5 Test copy with -d -s -LFN=case and -t first" src="https://github.com/user-attachments/assets/c7cf6b23-a0df-4e43-ae18-6314fe565aa3" />
<img width="740" height="367" alt="LIST G4B -s of results of FATCOPY G4B v0 5 Test copy with -d -s -LFN=case and -t first" src="https://github.com/user-attachments/assets/1cc2f2ab-6e3b-4876-9d19-085ef6bda442" />

Ultimate limits: unsupported chars, directory depth and MAX PATH. showed with switches /lfn:case, /e (copy empty sub-directories too) and /q (quit operation, except error messages and copy statistics)

![FATCOPY limits of unsupporeted chars, directory depth and MAX PATH](https://github.com/user-attachments/assets/164f21c1-225e-4533-9b7a-a24b0d697907)

Example of copying a single file with renaming and switch /LFN:case to make Long File Names on target, including case of Short File Names. Attributes, Dates and Times read-out with ATTRIBFT.G4B

<img width="919" height="867" alt="FATCOPY G4B v0 5 Test copy single file with rename -LFN=case, first -t, later overwrite dialog + ATTRIBFT G4B attriballecho Source and Target" src="https://github.com/user-attachments/assets/6b83ba9f-8134-4a02-8a21-c4164d4d2af6" />


