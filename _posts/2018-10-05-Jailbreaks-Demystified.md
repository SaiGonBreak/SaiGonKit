
The Jailbreaking process has long been a misterious process where the iOS system suddenly gets unlocked out of Apple's shackles after running an application for a few seconds. For a very long time, exactly what happened during the runtime of that application was largely unknown and even today as of iOS 11 (12 actually), the end-user (be that casual user, eta folk, reditter or nagger) remains largely oblivious about the processes going on. In this blog post I am going to try to explain the main elements of a jailbreak as they were implemented and used historically. This post is not all-encompassing and various jailbreak tools for various iOS versions may use different patches and techniques, but they do boil down mostly to what you are about to read.

### Why Jailbreak?
The nomenclature of the process likely comes from the Apple's "Jailed" approach. Applications and users are bound to use only what Apple provides which is a fraction of what the device is capable of. Breaking this Jail of restrictions is the scope of the entire Jailbreak Process.

### For the eta folk, reditter, nagger, et. al.
No, Cydia has nothing to do with Jailbreaking itself. Cydia is a byproduct of the jailbreak "community" and a jailbreak is not considered a jailbreak if it has Cydia, just like a jailbreak that lacks Cydia is still a jailbreak. What differs is the target audience (or user base). 

Cydia is a GUI (Graphical User Interface) application which uses dpkg and apt (amongst others) in the background to install .deb (Debian) Packages. These packages follow a very strict (way too strict if you ask me) format that I will be discussing later. As the astute might have figured out, you don't need Cydia to install packages. Since Cydia relies on apt and dpkg (etc), you can simply use these binaries via SSH or through a mobile terminal application on the device. Cydia is just there to make this process as fool-proof as possible. 

So yes, my iOS 11.3.x/11.2.x Jailbreak, Osiris, released long before Electra was even a thing, was and is a jailbreak even tho I never bundled any GUI installer (Cydia or such) with it. The same thing applies for LiberiOS by Jonathan Levin (iOS 11 to 11.1.2) which was maybe the most stable iOS 11 Jailbreak to date. These jailbreaks are mostly destined for researchers and power users and not the random eta folk (who usually flames at the lack of Cydia).

### So how does it work?
Before being able to open Cydia, Installer 5, Icy Project, or a SSH on the device, the jailbreak has to run.
The stages of a jailbreak differ depending on the iOS version and the device. It used to be less reliant on the device type, but with the advent of KPP (Kernel Patch Protector) on iOS 9.0 and KTRR (allegedly Kernel Text Readonly Region) on iOS 10, that has become a thing more and more. For example, devices pre-iPhone 7 use KPP which is a software protection running in EL3 (ARM Exception LEVEL 3), but the iPhone 7 and newer are using KTRR which is hardware-based. In this case a jailbreak containing a KPP bypass (like Yalu) would not work on iPhone 7 and newer because KPP itself isn't a thing there. For these devices a KTRR bypass of sorts is required, as siguza has explained in his write-up aptly called <a href="http://siguza.github.io/KTRR/">KTRR</a>. So this way, the jailbreak tool has to know very well with what kind of device it deals.

### Jailbreak History? Look no further than Pangu for iOS 7.x.x

Before anything can happen on the device, the jailbreak payload has to be somehow deployed to the device. This may sound very trivial today because anybody has access to a free Apple Developer Account to sign an IPA file and install it ont he device with Cydia Impactor or something akin to this, but it did not use to be this simple. This self-signing with Provisioning Profiles was introduced to the masses by Apple by iOS 9.0 which is not even that far back in the jailbreak history.

Long before that was a thing, codesign was bypassed in very interesting ways by the highly skilled Jailbreak teams which are unfortunately long gone now. If you still have an iPhone 4 just collecting dust around, chances are you are jailbroken with Pangu for iOS 7.1 - 7.1.2. The astute can easily see that since this is talking about iOS 7.1.x, self-signing with provisioning profiles for free and deploying the signed IPAs was not a thing. So what was their trick?

Pangu for iOS 7.1- 7.1.2 has its own Windows and macOS program that does the deployment for you. The application it installs, aptly called "Pangu", is signed with an enterprise certificate which existed at that point and was a powerful thing but it wasn't as easy to obtain on the black marget as it is today (hence the advent of all these signing services like Ignition and AppValley).

Their certificate was, however, expired. The Pangu program on the computer instructed the user to set the date and the time of the device way back tyo a date in 2014 (June 2 2014 to be more specific).

The dummy application is deployed by the Pangu program and its main purpose is to drop that sweet certificate. The IPA itself is actually part of the Pangu Windows / macOS binary itself. That can easily be spotted by using any disassembler (I use Jtool and IDA).
Jtool by Jonathan Levin has a dope feature which can produce HTML output(!) which is very useful for when I build write-ups for my blog. 

Here's how the Pangu binary on macOS looks like. See the extra segments?

```bash
  LC 00: LC_SEGMENT_64             Mem: 0x000000000-0x100000000	__PAGEZERO
  LC 01: LC_SEGMENT_64             Mem: 0x100000000-0x101e71000	__TEXT
	Mem: 0x100002370-0x1000292cf		__TEXT.__text	(Normal)
	Mem: 0x1000292d0-0x10002982e		__TEXT.__stubs	(Symbol Stubs)
	Mem: 0x100029830-0x10002a132		__TEXT.__stub_helper	(Normal)
	Mem: 0x10002a140-0x10002a690		__TEXT.__const	
	Mem: 0x10002a690-0x10002b914		__TEXT.__objc_methname	(C-String Literals)
	Mem: 0x10002b914-0x10002b9d5		__TEXT.__objc_classname	(C-String Literals)
	Mem: 0x10002b9d5-0x10002beb6		__TEXT.__objc_methtype	(C-String Literals)
	Mem: 0x10002bec0-0x10002e8d5		__TEXT.__cstring	(C-String Literals)
	Mem: 0x10002e8d6-0x10002e92e		__TEXT.__ustring	
	Mem: 0x10002e92e-0x10003dc04		__TEXT.__objc_cons1	
	Mem: 0x10003dc04-0x10029ed87		__TEXT.__objc_cons2	; Yeee, see this!
	Mem: 0x10029ed87-0x1002b71a9		__TEXT.__objc_cons3	
	Mem: 0x1002b71a9-0x100f11a36		__TEXT.__objc_cons4	
	Mem: 0x100f11a36-0x10160e0ca		__TEXT.__objc_cons5	
	Mem: 0x10160e0ca-0x101dd6e3f		__TEXT.__objc_cons6	
	Mem: 0x101dd6e3f-0x101dd7152		__TEXT.__objc_cons7	
	Mem: 0x101dd7152-0x101dd7a17		__TEXT.__objc_cons8	
	Mem: 0x101dd7a17-0x101e45a6e		__TEXT.__objc_cons9	
	Mem: 0x101e45a6e-0x101e57e74		__TEXT.__objc_cons10	
	Mem: 0x101e57e74-0x101e69288		__TEXT.__objc_cons11	
	Mem: 0x101e69288-0x101e699e0		__TEXT.__unwind_info	
	Mem: 0x101e699e0-0x101e71000		__TEXT.__eh_frame	
  LC 02: LC_SEGMENT_64            Mem: 0x101e71000-0x101e75000	__DATA
	Mem: 0x101e71000-0x101e71028		__DATA.__program_vars	
	Mem: 0x101e71028-0x101e710b8		__DATA.__got	(Non-Lazy Symbol Ptrs)
	Mem: 0x101e710b8-0x101e710c8		__DATA.__nl_symbol_ptr	(Non-Lazy Symbol Ptrs)
	Mem: 0x101e710c8-0x101e717f0		__DATA.__la_symbol_ptr	(Lazy Symbol Ptrs)
	Mem: 0x101e717f0-0x101e717f8		__DATA.__mod_init_func	(Module Init Function Ptrs)
	Mem: 0x101e717f8-0x101e71800		__DATA.__mod_term_func	(Module Termination Function Ptrs)
	Mem: 0x101e71800-0x101e71b40		__DATA.__const	
	Mem: 0x101e71b40-0x101e71b60		__DATA.__objc_classlist	(Normal)
	Mem: 0x101e71b60-0x101e71b68		__DATA.__objc_nlclslist	(Normal)
	Mem: 0x101e71b68-0x101e71b78		__DATA.__objc_catlist	(Normal)
	Mem: 0x101e71b78-0x101e71ba0		__DATA.__objc_protolist	
	Mem: 0x101e71ba0-0x101e71ba8		__DATA.__objc_imageinfo	
	Mem: 0x101e71ba8-0x101e72f90		__DATA.__objc_const	
	Mem: 0x101e72f90-0x101e73590		__DATA.__objc_selrefs	(Literal Pointers)
	Mem: 0x101e73590-0x101e735a0		__DATA.__objc_protorefs	
	Mem: 0x101e735a0-0x101e736f8		__DATA.__objc_classrefs	(Normal)
	Mem: 0x101e736f8-0x101e73718		__DATA.__objc_superrefs	(Normal)
	Mem: 0x101e73718-0x101e738a8		__DATA.__objc_data	
	Mem: 0x101e738a8-0x101e73930		__DATA.__objc_ivar	
	Mem: 0x101e73930-0x101e74390		__DATA.__cfstring	
	Mem: 0x101e74390-0x101e746b8		__DATA.__data	
	Mem: 0x101e746c0-0x101e74b60		__DATA.__bss	(Zero Fill)
	Mem: 0x101e74b60-0x101e74b90		__DATA.__common	(Zero Fill)
LC 03: LC_SEGMENT_64          Mem: 0x101e75000-0x101eba000	__ui0
LC 04: LC_SEGMENT_64          Mem: 0x101eba000-0x101ebf000	__LINKEDIT
LC 05: LC_DYLD_INFO          
LC 06: LC_SYMTAB             
	Symbol table is at offset 0x1ebbc48 (32226376), 293 entries
	String table is at offset 0x1ebd610 (32232976), 4776 bytes
....
```

Do you see the __TEXT.__objc_cons2 section? 

If you do 0x10029ed87 - 0x10003dc04 = 2494851 bytes (decimal) => 2.494851 Megabytes
That is hell of a big section. No wonder. It is the embedded IPA file. objc_cons1, objc_cons2 and objc_cons3 are all embedded parts of the jailbreak payload (the untether, plists, libraries etc).

In fact, let's not talk about it, let's see it! 

Jtool is a very powerful tool. It has the ability to extract whole sections from a binary. The command is jtool -e (extract) /path.
If we do that to the Pangu binary we will get a new file called "pangu.__TEXT.__objc_cons2" which so happens to be identified by the file(1) as being a "gzip compressed data, from Unix", so a `tar tvf` should be able to list the contents quite fine.
It can and it does.

```bash
Saigon:~ geosn0w$ /Users/geosn0w/Desktop/ToolChain/jtool/jtool -e __TEXT.__objc_cons2 /Users/geosn0w/Desktop/pangu.app/Contents/MacOS/pangu 
Requested section found at Offset 252932
Extracting __TEXT.__objc_cons2 at 252932, 2494851 (261183) bytes into pangu.__TEXT.__objc_cons2
Saigon:~ geosn0w$ file /Users/geosn0w/pangu.__TEXT.__objc_cons2
/Users/geosn0w/pangu.__TEXT.__objc_cons2: gzip compressed data, from Unix
Saigon:~ geosn0w$ tar tvf /Users/geosn0w/pangu.__TEXT.__objc_cons2 
drwxrwxrwx  0 0      0           0 Jun 27  2014 Payload/
drwxrwxrwx  0 0      0           0 Jun 27  2014 Payload/ipa1.app/
drwxrwxrwx  0 0      0           0 Jun 27  2014 Payload/ipa1.app/_CodeSignature/
-rwxrwxrwx  0 0      0        3638 Jun 27  2014 Payload/ipa1.app/_CodeSignature/CodeResources
-rwxrwxrwx  0 0      0       15112 Jun 27  2014 Payload/ipa1.app/AppIcon60x60@2x.png
-rwxrwxrwx  0 0      0       20753 Jun 27  2014 Payload/ipa1.app/AppIcon76x76@2x~ipad.png
-rwxrwxrwx  0 0      0        8017 Jun 27  2014 Payload/ipa1.app/AppIcon76x76~ipad.png
-rwxrwxrwx  0 0      0       75320 Jun 27  2014 Payload/ipa1.app/Assets.car
-rwxrwxrwx  0 0      0        7399 Jun 27  2014 Payload/ipa1.app/embedded.mobileprovision
drwxrwxrwx  0 0      0           0 Jun 27  2014 Payload/ipa1.app/en.lproj/
-rwxrwxrwx  0 0      0          74 Jun 27  2014 Payload/ipa1.app/en.lproj/InfoPlist.strings
-rwxrwxrwx  0 0      0        1955 Jun 27  2014 Payload/ipa1.app/Info.plist
-rwxrwxrwx  0 0      0      312208 Jun 27  2014 Payload/ipa1.app/ipa1
-rwxrwxrwx  0 0      0         968 Jun 27  2014 Payload/ipa1.app/ipa1-Info.plist
-rwxrwxrwx  0 0      0      235794 Jun 27  2014 Payload/ipa1.app/LaunchImage-700-568h@2x.png
-rwxrwxrwx  0 0      0      785321 Jun 27  2014 Payload/ipa1.app/LaunchImage-700-Landscape@2x~ipad.png
-rwxrwxrwx  0 0      0      261481 Jun 27  2014 Payload/ipa1.app/LaunchImage-700-Landscape~ipad.png
-rwxrwxrwx  0 0      0      660541 Jun 27  2014 Payload/ipa1.app/LaunchImage-700-Portrait@2x~ipad.png
-rwxrwxrwx  0 0      0      244644 Jun 27  2014 Payload/ipa1.app/LaunchImage-700-Portrait~ipad.png
-rwxrwxrwx  0 0      0      216627 Jun 27  2014 Payload/ipa1.app/LaunchImage-700@2x.png
-rwxrwxrwx  0 0      0           8 Jun 27  2014 Payload/ipa1.app/PkgInfo
-rwxrwxrwx  0 0      0         150 Jun 27  2014 Payload/ipa1.app/ResourceRules.plist
drwxrwxrwx  0 0      0           0 Jun 27  2014 Payload/ipa1.app/zh-Hans.lproj/
-rwxrwxrwx  0 0      0          73 Jun 27  2014 Payload/ipa1.app/zh-Hans.lproj/InfoPlist.strings
Saigon:~ geosn0w$ 

```
Doing a `tar xvf` will extract the contents to a "Payload" folder. 
So the IPA file that gets deployed on the phone is actually ipa1. As you can see, there is a file called `embedded.mobileprovision` which contains the enterprise certificate. If we Right-click on it and select "Get Info", Finder is capable to show us some information about the embedded certificate. As you can see, it belongs to "Hefei Bo Fang", whatever that is.

<p align="center">
  <img src="https://user-images.githubusercontent.com/15067741/46533169-a763df00-c871-11e8-884b-fa51df3b0f6e.png"/>
</p>

### Getting there...

As you can see, Pangu, like many other jailbreaks relied on developer certificate to bypass the CodeSign, but getting the IPA deployed to the device is not as easy as you may think. Nowadays we quickly fire Cydia Impactor, drag and drop the IPA, sign in and there we go. This wasn't the case until iOS 9.0, so Pangu had to do what other jailbreak teams before them did - use Apple against itself.

iTunes can easily communicate with the device and up until iTunes 12.x, iTunes was capable to handle iOS applications too. It was since stripped off this functionality but that hinted to the fact that one or more frameworks (or DLLs for the Windows folk) has to be able to create a connection to the device and perform application related tasks. Of course, we are talking about AppleMobileDevice.(framework / dll). Shipped with iTunes and its driver packages, this framework was largely used in the jailbreaks before and it is still used by all these "Backup iOS / Photos / Contacts / Whatever" programs on Windows to communicate reliably with the device. The APIs are, of course, private but they were reversed to shit and beyond by multiple researchers. They were also recreated in the `libimobiledevice` project.

As you can see, with that in place, Pangu could finally talk to the device and drop the payload at the right moment.
The rest follows an almost formulaic set of canonical patches I am going to discuss below.

### Canonical Patches

Pangu was my desired example here because it is fairly new so it applies most of the following patches (compared to redsn0w etc) and also because it is one of the jailbreaks I have used myself a lot on an iPhone 4 I still happen to have.

I have created the following diagram which should (in theory) show the flow of most jailbreaks. Of course, the implementation and techniques would be different from iOS version to iOS version and some jailbreaks may do the actions in a totally different order.

<p align="center">
  <img src="https://user-images.githubusercontent.com/15067741/46536163-aa63cd00-c87b-11e8-88c3-f0c5c37bb494.png"/>
</p>

