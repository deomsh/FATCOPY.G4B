## FATCOPY.G4B v0.4 (20241005)

<pre><code></code>
Function: front-end for Grubutil 'FAT', function 'copy'
FATCOPY.G4B [--mdbase=sector] DEVICE1/PATH1/FILE1 DEVICE2/PATH2/FILE2 switches
FATCOPY.G4B /? (this text)
Copy files on DEVICE1 to DEVICE2; if omitted DEVICE1/DEVICE2: from/to root
Copy from hidden partitions and to hidden FAT partitions too!
Default 8+3 File Names (except with /lfn[:case], see below)
Arguments space-separated, switches: lower/uppercase free
More convenient => insmod (bd)/fatutils/fatcopy.g4b copy (or rename file)

--mdbase=sector => set base memory of memdrives, default 0x3000*
* FAT directory-parsing needs 2048KB, LS directory-parsing needs 128KB
* Minimum 0x3000, rejected: 0x0000-0x2999, 0x4000-0xD460 and 0x12000-0x12FFF
(source) DEVICE1 = (fd#) & (hd#,#) & (0x#) & (#) with ISO/CD/DVD
(target) DEVICE2 = (fd#) & (hd#,#) & (0x#) & (#) FAT only
/PATH1/ = source directory => without DEVICE1 => on root (PATH1/ too)
/PATH2/ = target directory => without DEVICE2 => on root (PATH2/ too)
if PATH2/ is not specified, target directory is the root directory (/)
FILE1 = copy specified file => on root without DEVICE1 and/or /PATH1/
FILE1 accepts wildcard: '*' AFTER chars in Name and/or Extension (NOT: '?')
Without FILE1 full directory will be copied ('*.*' is NOT needed)
FILE2 = copy/rename FILE1 to FILE2 => on root without DEVICE2 and/or /PATH2/

General switches: /d /s /[-]o /i /p /q /v /t /[-]x:~ /[-]ext:EXT /[-]r /maxfiles:N /maxbyte:n /z /a
/d = add PATH1 to PATH2
/s = copy content of ALL subdirectories too
/[-]o = [never]overwrite files, BTW: /[-] means choice, type /- (!)
/i = incremental copying by comparing CRC32 of files on source and target
/p = pause after each screen
/q = quiet copying: error messages & last file-count message only *
/v = verify with message of status- not with /t (try /v if copying stops)
/t = trial without actual copying (can be used as simple DIR)
/[-]x:~ = only SFN-equivalent of LFN/real shortened LFN will [not] be copied **
/[-]ext:EXT = files with EXTension [not] copied- separator: dot only 3*
/[-]r = Do not ask [not] overwrite read-only files (attributes unset!)- not /t
/maxfiles:N = set max number of files to copy
/maxbyte:n = set max number of bytes to copy- takes k, m, g postfix operator
/z = copy zero byte files (default: ommitted), fake overwriting only 4*
     copied for files, not for directories - from FAT-source only
*  Default verbose copying: mkdir & copy & output messages (less with '*.*')
** Character '?' is FORBIDDEN, reserved for internal use!
** Instead of '~' ONE other character can be used too (for name-part only)
3* Examples: /ext:g4b.lll.bat or /-ext:.7z.cab.img.rar.vhd.zip- first dot free
4* date and time never updated with overwriting, to not loose (LFN-)metadata

FAT directory-parser switches: /a:[-]d[-]a[-]r[-]s[-]h /a  /lfn:@ *
/a:[-]d[-]a[-]r[-]s[-]h = [not] copy files/directories with attribute(s) **
/a = copy attributes, NT-casebyte, last access date, modification time and date
* Default directory parser if copying from FAT-source
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
5* Only File Systems supported with File Version Numbser Suffix && echo

Remarks:
FAT needed, searched in: %^~dp0, (bd), ROOT, /, /boot/grub/, /grub/, /g4dll/
File versions: Grubutil FAT 15/02/2015, Grub4Dos 0.4.6a (>=2017 with 'vol')
If TARGET is >= 4GB: use Grubutil FAT >= 2023, April (look in folder 'ext')
FATCOPY.G4B is not not compatible with Grub4Dos 0.4.5b/Grub4Dos 0.4.5c
            is compatible with Grub4Dos for UEFI (other FAT version in 'ext'!)
            but up to latest version (20240901) soon 'out of malloc memory'
UTF-8 characters not supported in first 12 characters of file-name

Example: FATCOPY.G4B (hd0,0)/SOMEFILE.EXT (hd0,0)/BACKUP
Example: FATCOPY.G4B (hd0,0)/*.EXT (hd0,0)/BACKUP/EXT/
Example: FATCOPY.G4B (hd0,0)/FILE.EX* (hd0,0)/BACKUP/FILE/ /t
Example: FATCOPY.G4B (hd0,0)/SOMEFIL*.EX* (hd0,0)/BACKUP/WILDCARD/ /q
Example: fatcopy.g4b (fd0)/ (hd1,0)/backup/ /s /maxbyte:64m
Example: FATCOPY.G4B (fd0)/ (hd1,0)/BACKUP/ /sfn
Example: FATCOPY.G4B (fd0)/ (hd1,0)/BACKUP/ /s /sfn:@ /v
Example: FATCOPY.G4B (hd0,0)/Program\ Files/ (hd1,0)/Program\ Files/ /lfn /s /t
Example: FATCOPY.G4B (hd0,0)/Program\ Files/ (hd1,0)/BACKUP/Program\ Files/ /lfn /s /t
Example: FATCOPY.G4B "(hd0,0)/Program Files/" (hd1,0)/BACKUP/ /lfn /s /d /z
Example: FATCOPY.G4B (hd0,0)/PROGRA~1/ (hd1,0)/BACKUP/ /lfn:case /s /d /z /i /a
Example: FATCOPY.G4B (fd0)/IO.SYS (hd0,0) /r /a
Example: FATCOPY.G4B (hd1,0)/SOMEDI~1/ (fd1)/SOMEDI~1/ /s /lfn:@

Based on copyFF.bat (:cpa & :copyfiles & :sub-dir => originator of call's seems to be Chenall)
Dedicated to Jaclaz for inspiration and help</pre>

#### ATTRIBFT.LLL

Concept of 'Loosely Linked Library' is courtesy of Wonko the Sane (Jaclaz)  
More information and download: https://github.com/deomsh/ATTRIBFT.LLL  

### HISTORY

Version 0.4    

Version 0.3  
Earlier published on msfn (members only): https://msfn.org/board/topic/180597-exploring-setup-p-isdetectbus-and-grub4dos-8%C2%BD-times-win98se-on-usb-drive/?do=findComment&comment=1193210  

Version 0.2  
Earlier published on msfn (members only): https://msfn.org/board/topic/180597-exploring-setup-p-isdetectbus-and-grub4dos-8%C2%BD-times-win98se-on-usb-drive/?do=findComment&comment=1182924  

Version 0.1  
Earlier published on msfn (members only): https://msfn.org/board/topic/180597-exploring-setup-p-isdetectbus-and-grub4dos-8%C2%BD-times-win98se-on-usb-drive/?do=findComment&comment=1182924  

### SCREENSHOTS
