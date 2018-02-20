# UEFTW

```
 _____ _____ _____ _____ _____
|| U ||| E ||| F ||| T ||| W ||  Some of UEFI Toys by me. Taken from my early
||___|||___|||___|||___|||___||  forked of Clover and 'others' below.
|/___\|/___\|/___\|/___\|/___\|  No sources available yet, just binary (EAT that!).
```

> This project is intended for general use and no warranty is implied for suitability to any given task. I hold no responsibility for your setup or any damage done while using / installing / modifying current sources / supplied binaries. **YOU HAVE BEEN WARNED**. While my old forked of Clover sources still available for FREE to uses [here](https://github.com/cecekpawon/CloverPkg).

- [How-to](#how-to).
- [Download](#download).

# Shell Applications

> Which meant must be run from / within Shell.

## ShellOpt

> Port of GNUEFI _Finnbarr P. Murphy_ ShellOpt ([>>>](/fpmurphy/UEFI-Utilities)) to EDK2, to set / delete various Shell options.

```
Usage: ShellOpt -s|--set [-nomap] [-nostartup | -startup] [-noversion]
                         [-noconsolein] [-noconsoleout] [-nointerrupt]
                         [-_exit] [-nonest] [-delay[:n]]
       ShellOpt -c|--clear
       ShellOpt -r|--restore
       ShellOpt -h|--help
```

## ShellExpand

> To eliminate known Shell bugs `edit` command by translating TABS to SPACES with custom size.


```
* Usage: ShellExpand -t[:n] infile outfile
         ShellExpand infile outfile
         ShellExpand inoutfile
         ShellExpand -h
* Default / min tab size (-t): 2
```

# UEFI Drivers

## DBounce

| Label | Value |
| --- | --- |
|`<driver-guid>` | F97ABEC7-BC15-4954-B4A7-83FC6323D27C |
|`<driver-name>` | DBounce |

> An UEFI driver to load all required drivers first before finally calling a chainloader.
Originally introduced by _Christoph Pfisterer_ (rEFIts author). The original source can be found [here](https://sourceforge.net/p/refit/code/HEAD/tree/trunk/refit/dbounce/).
Later I port this module to work with EDK2 with following changes (compared to original):

**Launch default shell by hotkey from known absolute path by hotkey as (original) `FALLBACKSHELL` replacement.**

Default path would be `\Shellx64.efi` (to follow ASUS rule).

**In sequence drivers list option, instead of just scan and load specified drivers directory.**

DBounce will let you choose whether to:
- Scan and load specified drivers directory: `DriversPath`.
- Or to load in sequence drivers list option: `DriversList`.

**Suppress driver verbose log.**

If this option are set to `TRUE`, it will mute driver verbose screen print while loading.

**Hotkeys and Apple NVRAM boot-args support for options.**

Following hotkeys are available to use while booting:

|Key|Function|
| --- | --- |
| x | Disable this driver. |
| c | Clear previously stored nvram config. |
| s | Launch shell app (if exists) as chainloader replacement. |

**Multiple places config plist to load** ([>>>](#how-to)):

**Sample of config**:

```xml
<dict>
  <key>DriversPath</key>
  <string>\EFI\DDrivers</string>
  <key>LoaderPath</key>
  <string>\EFI\Loader.efi</string>
  <key>DriversList</key>
  <array>
    <string>\EFI\DDrivers\Driver-1.efi</string>
    <string>\EFI\DDrivers\Driver-2.efi</string>
  </array>
  <key>NoVerbose</key>
  <true/>
  <key>Preferences</key>
  <dict>
    <key>Debug</key>
    <true/>
    <key>Off</key>
    <true/>
    <key>SaveLogToFile</key>
    <true/>
  </dict>
</dict>
```

| Key | Description |
| --- | --- |
| DriversPath | Scan and load drivers from specified directory. **Other than uefi driver type will be rejected**. |
| LoaderPath | Chainloader path. **Other than uefi application type will be rejected**. |
| DriversList | Sequenced drivers list to load. **With given drivers list, the specified drivers directory will simply ignored**. |
| NoVerbose | Suppress driver verbose log. |
| (*) | Plus some of generic options ([>>>](#how-to)). |

**Indicator**

Stoled from _CodeRush_ [CrScreenshotDxe](https://github.com/LongSoft/CrScreenshotDxe), will appear as a small square on top-left corner of your screen while booting:

| Color | Description |
| --- | --- |
| White | DBounce are initialised. |
| Green | DBounce are enable. |
| Red | DBounce are disable. |
| Orange | Some of hotkey were executed. |

**Credits goes to:**

_Christoph Pfisterer_ + _CodeRush_ for codes, and _modbin_ for pointing to this useful driver.

## KernextPatcher

| Label | Value |
| --- | --- |
| `<driver-guid>` | 99665243-5AED-4D57-92AF-8C785FBC7558 |
| `<driver-name>` | KernextPatcher |

> KernextPatcher _(stand for Kernel & Kext Patcher)_ is an **Darwin** kernel & extensions patcher UEFI driver based on Clover Memfix by _dmazar_. This driver will try to hook ExitBootServices event and do patching booter and kernelcache including kernel it self and kexts.

**Multiple places config plist to load** ([>>>](#how-to)):

**Sample of config**

```xml
<dict>
  <key>BlockKextCaches</key>
  <array>
    <string>com.apple.driver.AppleHDA</string>
  </array>
  <key>KextsToPatch</key>
  <array>
    <dict>
      <key>Comment</key>
      <string>ALC892 - 1</string>
      <key>Find</key>
      <data>
      ixnUEQ==
      </data>
      <key>Name</key>
      <string>com.apple.driver.AppleHDA</string>
      <key>Replace</key>
      <data>
      kgjsEA==
      </data>
      <key>MatchOS</key>
      <string>10.9-10.12</string>
    </dict>
  </array>
  <key>KernelToPatch</key>
  <array>
    <dict>
      <key>Comment</key>
      <string>startupExt</string>
      <key>Disabled</key>
      <true/>
      <key>Find</key>
      <data>
      voQAAQAxwOibEdH/
      </data>
      <key>MatchOS</key>
      <string>10.11,10.12</string>
      <key>Replace</key>
      <data>
      kJCQkEiJ3+gbBQAA
      </data>
    </dict>
  </array>
  <key>BooterToPatch</key>
  <array>
    <dict>
      <key>Comment</key>
      <string>UnlockSlideSupportForSafeModeAndCheckSlide</string>
      <key>Count</key>
      <integer>1</integer>
      <key>Disabled</key>
      <false/>
      <key>Find</key>
      <data>
      AUAAAMwBQAAA
      </data>
      <key>MatchOS</key>
      <string>10.12</string>
      <key>Replace</key>
      <data>
      /////8z/////
      </data>
      <key>Wildcard</key>
      <string>0xCC</string>
    </dict>
  </array>
  <key>WholePrelinked</key>
  <false/>
  <key>Preferences</key>
  <dict>
    <key>Debug</key>
    <false/>
    <key>SaveLogToFile</key>
    <true/>
    <key>Off</key>
    <false/>
  </dict>
</dict>
```

| Key | Description |
| --- | --- |
| BlockKextCaches | Array of kexts identifier to block. |
| KextsToPatch | Array of kexts to patch. |
| KernelToPatch | Array of kernel to patch. |
| BooterToPatch | Array of booter to patch. |
| WholePrelinked | Patch the whole prelinked / just kernel. |
| (*) | Plus some of generic options ([>>>](#how-to)). |

**Credits goes to:**

_dmazar_ and _Clover devs_.

## AcpiPatcher

| Label | Value |
| --- | --- |
| `<driver-guid>` | AB6CE992-8D17-4C3A-A414-0FEAA3904504 |
| `<driver-name>` | AcpiPatcher |

> AcpiPatcher is an **Darwin** ACPI patcher UEFI driver. Yes, its a MEGA stripped version compare to original one. At least, we can now get rid from some of complexity to load custom ACPI tables with some fixes. This driver try to hook ExitBootServices event and do patching ACPI as below.

**Multiple places config plist to load** ([>>>](#how-to)):

**Sample of config**

```xml
<dict>
  <key>DropTables</key>
  <array>
    <dict>
      <key>Signature</key>
      <string>SSDT</string>
      <key>TableId</key>
      <string>Cpu0Ist</string>
    </dict>
  </array>
  <key>FixHeader</key>
  <true/>
  <key>GenerateCPUStates</key>
  <true/>
  <key>PatchOemTableOnly</key>
  <true/>
  <key>Patches</key>
  <array>
    <dict>
      <key>Comment</key>
      <string>COPR [to] MATH</string>
      <key>Disabled</key>
      <false/>
      <key>Find</key>
      <data>
      Q09QUg==
      </data>
      <key>Replace</key>
      <data>
      TUFUSA==
      </data>
    </dict>
  </array>
  <key>Preferences</key>
  <dict>
    <key>Debug</key>
    <true/>
    <key>Off</key>
    <false/>
    <key>SaveLogToFile</key>
    <true/>
  </dict>
</dict>
```

| Key | Description |
| --- | --- |
| DropTables | Drop tables by Signature / TableId. |
| FixHeader | Fix known ACPI header bugs in newest macOS. |
| GenerateCPUStates | Generate CPU states. |
| PatchOemTableOnly | Only patch OEM tables. |
| Patches | Array of ACPI binary to patch. |
| (*) | Plus some of generic options ([>>>](#how-to)). |

**Credits goes to:**

_mackerintel_, _Mozodojo_, _Slice_,  _SunnyKi_, _al3xtjames_, and _Clover devs_.

# How-to

**Driver generic options:**

| Key | Description |
| --- | --- |
| Preferences -> Debug | With additional log messages. (*1) |
| Preferences -> Off | Disable this driver. (*2) |
| Preferences -> SaveLogToFile | Save log to file ('`<driver-name>`Log.txt' at the same path as '`<driver-name>`.efi' / ESP as a fallback). (*3) |

**Apple NVRAM boot-args generic options:**

| Key | Description |
| --- | --- |
| -`<driver-name>`Dbg | refer to (*1) above. |
| -`<driver-name>`Off | refer to (*2) above. |
| -`<driver-name>`Log | refer to (*3) above. |

**Driver will start to look for user config with following sequences:**

| # | Key | Description |
| --- | --- | --- |
| 1 | nvram | Default is set by driver (after successfully reading user config). But user may manually to do so. Its locate in 'AppleBootGuid (`7C436110-AB2A-4BBB-A880-FE41995C9F82`)' with '`<driver-guid>`:`<driver-name>`' key.
| 2  | disk | Driver will read '`<driver-name>`.plist' as user config from the same path as `<driver-name>`.efi.
|  3 | rom | Mainly as template / fallback, when driver cannot found valid user config in nvram / disk.

**Register this driver, from shell:**

```
bcfg driver add N <driver-name>.efi <driver-name>
```

**Extract binary:**

Please use [UEFITool](https://github.com/LongSoft/UEFITool) to extract the driver.

| Section | Description |
| --- | --- |
| PE32 image | Contain efi binary. |
| Raw | Contain default config plist. |

# Download

Precompiled binaries always can be found [here](Releases).
