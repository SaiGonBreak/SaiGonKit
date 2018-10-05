
The Jailbreaking process has long been a mysterious process where the iOS system suddenly gets unlocked out of Apple's shackles after running an application for a few seconds. For a very long time, exactly what happened during the runtime of that application was largely unknown and even today as of iOS 11 (12 actually), the end-user (be that casual user, eta folk, reditter or nagger) remains largely oblivious about the processes going on. In this blog post, I am going to try to explain the main elements of a jailbreak as they were implemented and used historically. This post is not all-encompassing and various jailbreak tools for various iOS versions may use different patches and techniques, but they do boil down mostly to what you are about to read.

### Why Jailbreak?
The nomenclature of the process likely comes from the Apple's "Jailed" approach. Applications and users are bound to use only what Apple provides which is a fraction of what the device is capable of. Breaking this Jail of restrictions is the scope of the entire Jailbreak Process.

### For the eta folk, reditter, nagger, et. al.
No, Cydia has nothing to do with Jailbreaking itself. Cydia is a byproduct of the jailbreak "community" and a jailbreak is not considered a jailbreak if it has Cydia, just like a jailbreak that lacks Cydia is still a jailbreak. What differs is the target audience (or user base). 

Cydia is a GUI (Graphical User Interface) application which uses dpkg and apt (amongst others) in the background to install .deb (Debian) Packages. These packages follow a very strict (way too strict if you ask me) format that I will be discussing later. As the astute might have figured out, you don't need Cydia to install packages. Since Cydia relies on apt and dpkg (etc), you can simply use these binaries via SSH or through a mobile terminal application on the device. Cydia is just there to make this process as fool-proof as possible. 

So yes, my iOS 11.3.x/11.2.x Jailbreak, Osiris, released long before Electra was even a thing, was and is a jailbreak even tho I never bundled any GUI installer (Cydia or such) with it. The same thing applies for LiberiOS by Jonathan Levin (iOS 11 to 11.1.2) which was maybe the most stable iOS 11 Jailbreak to date. These jailbreaks are mostly destined for researchers and power users and not the random eta folk (who usually flames at the lack of Cydia).

### So how does it work?
Before being able to open Cydia, Installer 5, Icy Project, or an SSH on the device, the jailbreak has to run.
The stages of a jailbreak differ depending on the iOS version and the device. It used to be less reliant on the device type, but with the advent of KPP (Kernel Patch Protector) on iOS 9.0 and KTRR (allegedly Kernel Text Readonly Region) on iOS 10, that has become a thing more and more. For example, devices pre-iPhone 7 use KPP which is a software protection running in EL3 (ARM Exception LEVEL 3), but the iPhone 7 and newer are using KTRR which is hardware-based. In this case, a jailbreak containing a KPP bypass (like Yalu) would not work on iPhone 7 and newer because KPP itself isn't a thing there. For these devices a KTRR bypass of sorts is required, as siguza has explained in his write-up aptly called <a href="http://siguza.github.io/KTRR/">KTRR</a>. So this way, the jailbreak tool has to know very well with what kind of device it deals.

### Jailbreak History? Look no further than Pangu for iOS 7.x.x

Before anything can happen on the device, the jailbreak payload has to be somehow deployed to the device. This may sound very trivial today because anybody has access to a free Apple Developer Account to sign an IPA file and install it on the device with Cydia Impactor or something akin to this, but it did not use to be this simple. This self-signing with Provisioning Profiles was introduced to the masses by Apple by iOS 9.0 which is not even that far back in the jailbreak history.

Long before that was a thing, codesign was bypassed in very interesting ways by the highly skilled Jailbreak teams which are unfortunately long gone now. If you still have an iPhone 4 just collecting dust around, chances are you are jailbroken with Pangu for iOS 7.1 - 7.1.2. The astute can easily see that since this is talking about iOS 7.1.x, self-signing with provisioning profiles for free and deploying the signed IPAs was not a thing. So what was their trick?

Pangu for iOS 7.1- 7.1.2 has its own Windows and macOS program that does the deployment for you. The application it installs, aptly called "Pangu", is signed with an enterprise certificate which existed at that point and was a powerful thing but it wasn't as easy to obtain on the black market as it is today (hence the advent of all these signing services like Ignition and AppValley).

Their certificate was, however, expired. The Pangu program on the computer instructed the user to set the date and the time of the device way back to a date in 2014 (June 2, 2014, to be more specific).

The dummy application is deployed by the Pangu program and its main purpose is to drop that sweet certificate. The IPA itself is actually part of the Pangu Windows / macOS binary itself. That can easily be spotted by using any disassembler (I use Jtool and IDA).
Jtool by Jonathan Levin has a dope feature which can produce HTML output(!) which is very useful for when I build write-ups for my blog. 

Here's how the Pangu binary on macOS looks like. See the extra segments?

```bash
  LC 00: LC_SEGMENT_64             Mem: 0x000000000-0x100000000    __PAGEZERO
  LC 01: LC_SEGMENT_64             Mem: 0x100000000-0x101e71000    __TEXT
    Mem: 0x100002370-0x1000292cf        __TEXT.__text    (Normal)
    Mem: 0x1000292d0-0x10002982e        __TEXT.__stubs    (Symbol Stubs)
    Mem: 0x100029830-0x10002a132        __TEXT.__stub_helper    (Normal)
    Mem: 0x10002a140-0x10002a690        __TEXT.__const    
    Mem: 0x10002a690-0x10002b914        __TEXT.__objc_methname    (C-String Literals)
    Mem: 0x10002b914-0x10002b9d5        __TEXT.__objc_classname    (C-String Literals)
    Mem: 0x10002b9d5-0x10002beb6        __TEXT.__objc_methtype    (C-String Literals)
    Mem: 0x10002bec0-0x10002e8d5        __TEXT.__cstring    (C-String Literals)
    Mem: 0x10002e8d6-0x10002e92e        __TEXT.__ustring    
    Mem: 0x10002e92e-0x10003dc04        __TEXT.__objc_cons1    
    Mem: 0x10003dc04-0x10029ed87        __TEXT.__objc_cons2    ; Yeee, see this!
    Mem: 0x10029ed87-0x1002b71a9        __TEXT.__objc_cons3    
    Mem: 0x1002b71a9-0x100f11a36        __TEXT.__objc_cons4    
    Mem: 0x100f11a36-0x10160e0ca        __TEXT.__objc_cons5    
    Mem: 0x10160e0ca-0x101dd6e3f        __TEXT.__objc_cons6    
    Mem: 0x101dd6e3f-0x101dd7152        __TEXT.__objc_cons7    
    Mem: 0x101dd7152-0x101dd7a17        __TEXT.__objc_cons8    
    Mem: 0x101dd7a17-0x101e45a6e        __TEXT.__objc_cons9    
    Mem: 0x101e45a6e-0x101e57e74        __TEXT.__objc_cons10    
    Mem: 0x101e57e74-0x101e69288        __TEXT.__objc_cons11    
    Mem: 0x101e69288-0x101e699e0        __TEXT.__unwind_info    
    Mem: 0x101e699e0-0x101e71000        __TEXT.__eh_frame    
  LC 02: LC_SEGMENT_64            Mem: 0x101e71000-0x101e75000    __DATA
    Mem: 0x101e71000-0x101e71028        __DATA.__program_vars    
    Mem: 0x101e71028-0x101e710b8        __DATA.__got    (Non-Lazy Symbol Ptrs)
    Mem: 0x101e710b8-0x101e710c8        __DATA.__nl_symbol_ptr    (Non-Lazy Symbol Ptrs)
    Mem: 0x101e710c8-0x101e717f0        __DATA.__la_symbol_ptr    (Lazy Symbol Ptrs)
    Mem: 0x101e717f0-0x101e717f8        __DATA.__mod_init_func    (Module Init Function Ptrs)
    Mem: 0x101e717f8-0x101e71800        __DATA.__mod_term_func    (Module Termination Function Ptrs)
    Mem: 0x101e71800-0x101e71b40        __DATA.__const    
    Mem: 0x101e71b40-0x101e71b60        __DATA.__objc_classlist    (Normal)
    Mem: 0x101e71b60-0x101e71b68        __DATA.__objc_nlclslist    (Normal)
    Mem: 0x101e71b68-0x101e71b78        __DATA.__objc_catlist    (Normal)
    Mem: 0x101e71b78-0x101e71ba0        __DATA.__objc_protolist    
    Mem: 0x101e71ba0-0x101e71ba8        __DATA.__objc_imageinfo    
    Mem: 0x101e71ba8-0x101e72f90        __DATA.__objc_const    
    Mem: 0x101e72f90-0x101e73590        __DATA.__objc_selrefs    (Literal Pointers)
    Mem: 0x101e73590-0x101e735a0        __DATA.__objc_protorefs    
    Mem: 0x101e735a0-0x101e736f8        __DATA.__objc_classrefs    (Normal)
    Mem: 0x101e736f8-0x101e73718        __DATA.__objc_superrefs    (Normal)
    Mem: 0x101e73718-0x101e738a8        __DATA.__objc_data    
    Mem: 0x101e738a8-0x101e73930        __DATA.__objc_ivar    
    Mem: 0x101e73930-0x101e74390        __DATA.__cfstring    
    Mem: 0x101e74390-0x101e746b8        __DATA.__data    
    Mem: 0x101e746c0-0x101e74b60        __DATA.__bss    (Zero Fill)
    Mem: 0x101e74b60-0x101e74b90        __DATA.__common    (Zero Fill)
LC 03: LC_SEGMENT_64          Mem: 0x101e75000-0x101eba000    __ui0
LC 04: LC_SEGMENT_64          Mem: 0x101eba000-0x101ebf000    __LINKEDIT
LC 05: LC_DYLD_INFO          
LC 06: LC_SYMTAB             
    Symbol table is at offset 0x1ebbc48 (32226376), 293 entries
    String table is at offset 0x1ebd610 (32232976), 4776 bytes
....
```

Do you see the <code class="high">__TEXT.__objc_cons2 section</code>?

If you do 0x10029ed87 - 0x10003dc04 = 2494851 bytes (decimal) => 2.494851 Megabyte.
That is hell of a big section. No wonder, It is the embedded IPA file. objc_cons1, objc_cons2 and objc_cons3 are all embedded parts of the jailbreak payload (the untether, plists, libraries etc).

In fact, let's not talk about it, let's see it! 

Jtool is a very powerful tool. It has the ability to extract whole sections from a binary. The command is jtool -e (extract) /path.
If we do that to the Pangu binary we will get a new file called "pangu.__TEXT.__objc_cons2" which so happens to be identified by the file(1) as being a "gzip compressed data, from Unix", so a <code class="high">tar tvf</code> should be able to list the contents quite fine.
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
Doing a <code class="high">tar xvf</code> will extract the contents to a "Payload" folder. 
So the IPA file that gets deployed on the phone is actually ipa1. As you can see, there is a file called <code class="high">embedded.mobileprovision</code> which contains the enterprise certificate. If we Right-click on it and select "Get Info", Finder is capable to show us some information about the embedded certificate. As you can see, it belongs to "Hefei Bo Fang", whatever that is.

<p align="center">
  <img src="https://user-images.githubusercontent.com/15067741/46533169-a763df00-c871-11e8-884b-fa51df3b0f6e.png"/>
</p>

### Getting there...

As you can see, Pangu, like many other jailbreaks relied on developer certificate to bypass the CodeSign, but getting the IPA deployed to the device is not as easy as you may think. Nowadays we quickly fire Cydia Impactor, drag and drop the IPA, sign in and there we go. This wasn't the case until iOS 9.0, so Pangu had to do what other jailbreak teams before them did - use Apple against itself.

iTunes can easily communicate with the device and up until iTunes 12.x, iTunes was capable to handle iOS applications too. It was since stripped off this functionality but that hinted to the fact that one or more frameworks (or DLLs for the Windows folk) have to be able to create a connection to the device and perform application-related tasks. Of course, we are talking about AppleMobileDevice.(framework / dll). Shipped with iTunes and its driver packages, this framework was largely used in the jailbreaks before and it is still used by all these "Backup iOS / Photos / Contacts / Whatever" programs on Windows to communicate reliably with the device. The APIs are, of course, private but they were reversed to shit and beyond by multiple researchers. They were also recreated in the <code class="high">libimobiledevice</code> project.

As you can see, with that in place, Pangu could finally talk to the device and drop the payload at the right moment.
The rest follows an almost formulaic set of canonical patches I am going to discuss below.

Pangu was my desired example here because it is fairly new so it applies most of the following patches (compared to redsn0w etc) and also because it is one of the jailbreaks I have used myself a lot on an iPhone 4 I still happen to have.

I mostly gave it as an example so that you can see the difference between getting to bypass CodeSign back then vs getting to bypass CodeSign now. 

### Canonical Patches


I have created the following diagram which should (in theory) show the flow of most jailbreaks. Of course, the implementation and techniques would be different from iOS version to iOS version and some jailbreaks may do the actions in a totally different order.

<p align="center">
  <img src="https://user-images.githubusercontent.com/15067741/46536163-aa63cd00-c87b-11e8-88c3-f0c5c37bb494.png"/>
</p>

So, as you can see from the diagram, the most important step is getting on the device. You cannot do much from outside the device. The entry vector can be different from jailbreak to jailbreak. Nowadays, most jailbreaks including my Osiris, Coolstar & Co's Electra and Jonathan's LiberiOS use the IPA applications signed with a temporary certificate and deployed with either Xcode or Cydia Impactor (or a signing service) to the device. From there, the application is executed by the user and the exploit is triggered.

Other methods include but are not limited to WebKit exploits, mail exploits, etc. 
The WebKit ones are more common. TotallyNotSpyware is a good example of an iOS 10.x to 10.3.3 64-Bit Jailbreak, and if we talk legacy, JailbreakMe series is probably the best example. These WebKit-based jailbreaks are usually deployed by accessing a website in Safari on the device. The website is crafted so that it exploits a webkit vulnerability (WebKit is at Safari's core), and thus gaining arbitrary code execution.

In the rest of this write-up, I will assume an IPA based Jailbreak like Osiris, LiberiOS or Electra. Also, this write-up assumes we already have a raw kernel exploit that gives us TFP0, and the KPPless approach.

After the application has been successfully installed and can run, CodeSign is no longer a problem, at least for the initial stage. We still cannot run unsigned or fake-signed binaries, but at least we can run ourselves (the exploit application) without being killed by AMFI. However, the problem is that we are still limited by the SandBox. The SandBox keeps us from accessing anything outside our container, so no R/W permissions for us. All we can see is our own data, nothing more. That has to change. We have to bestow ourselves the might of Shai Hulud!

The SandBox is a kernel extension (KEXT) which ensures that you do not access more than you're supposed to access. By default, everything in /var/mobile/Containers is sandboxed. Apple's own default applications are also sandboxed. When you install an application via Xcode, the App Store or via Cydia Impactor, you are automatically placing the application in /var/mobile/Containers/Bundle/Application/<UUID of the APP>. There is no other way to install an app, so our jailbreak application will be sandboxed by default, no matter what.

So how do apps have access to the services required in order for them to run?! How can Deezer connect to my Bluetooth headset? How can YouTube decode frames? How can Twitter send me notifications? It's simple, through APIs. These APIs allow your containerized app to communicate in a controlled manner with Core Services (bluetoothd, wifid, mediaserverd, etc) which are also sandboxed, and these Core Services talk with the kexts / kernel through IOKit. So no, you don't directly talk to the kernel.
The following diagram should help you see how this looks like.

<p align="center">
  <img src="https://user-images.githubusercontent.com/15067741/46547236-4fd96980-c899-11e8-8d48-1947418eb777.png"/>
</p>

Of course, as an Application on iOS, not only you cannot see the File System and the User Data, but you are also largely oblivious of the existence of any other applications. Yes, through some APIs you can pass files / data to another application if that other application is registered as one that accepts to handle such input, but even then, you as an application don't know anything about the existence of the other app and it is through the series of APIs provided by iOS that you pass your .PDF file, for example, to be opened in whatever application. 

Some applications also provide uri schemes for you to communicate with them. Let's say you are in Chrome on iOS and you find a phone number to a company you wanna call. If you press it, you get asked if you really wanna call, and then you go straight to the Call app from iOS and the number is already being dialed. How? 

Simple. the Phone application has registered an uri scheme that looks like this: <code class="high">tel://XXXXXXXXXXXX</code> so if you add tel://5552220001 to an HTML page and click it in Safari, the iOS knows who to open to handle that. Same goes for Facebook, Whatsapp, 

To use the URI scheme from your app, you just have to call the right UIApplication method. That is 

```swift
UIApplication.shared.open(url, options: [:], completionHandler: nil)
```

So does that mean you bypassed SandBox because you were able to pass data to another app and open it? Not even close. All you did was through a very well controlled set of APIs. You don't know that Phone app exists. iOS knows.
Here's how SandBoxing feels for an application.

<p align="center">
  <img src="https://user-images.githubusercontent.com/15067741/46547949-9760f500-c89b-11e8-966a-fef2dbfb28d9.png"/>
</p>

SandBox escaping can be done in multiple ways.

Osiris Jailbreak uses QiLin's built-in SandBox escape which is called "ShaiHulud", a Dune reference.

QiLin (and therefore LiberiOS and Osiris Jailbreak) escape the SandBox by assuming Kernel's credentials. Not only that, but since now we have the Kernel's credentials, we have access to whatever we want including syscalls like execve(), fork(), and posix_spawn()! Jonathan Levin has explained very well how QiLin proceeds in escaping the sandbox and assuming the kernel creds <a href="http://newosxbook.com/QiLin/qilin.pdf"> in this write-up</a>, part of the <code class="high"><a href="https://www.amazon.com/MacOS-iOS-Internals-III-Insecurity/dp/0991055535/ref=as_sl_pc_qf_sp_asin_til?tag=newosxbookcom-20&linkCode=w00&linkId=0b61c945365c9c37cd3cf88f10a5f629&creativeASIN=0991055535">*OS Internals Volume III</a></code>

Of course, QiLin saves our application's credentials and restores them before exiting, that is to prevent creating panics due to the various locks that govern the kernel creds. 

Electra for iOS 11.2.x -> iOS 11.3.1 also uses the same kernel credential method for sandbox bypass and other privs.

### Remounting the File System

So, we exploited the kernel and got kernel memory R/W, we exploited a bug or we bestowed ourselves the kernel credentials so we also exited the SandBox, now we wanna drop our payload (which can contain Cydia, binaries, config files, dummy files for checking whether the jailbreak was installed, etc). To be able to do that, we need to have Write permissions over the file system. By default, the iOS ROOT FS is mounted as Read Only, so we will need to remount that, hence the name of the patch: Root FS Remount.

This is the component that was missing back in July 2018 when Electra for iOS 11.3.1 was in development, and most of the eta folks went haywire.

So, how do we do that?

On QiLin (and therefore on LiberiOS and Osiris Jailbreak), the remounting up to iOS 11.2.6 works this way:
As I said, by default the ROOT FS is mounted as read-only. Not only that, but the SandBox also has a hook that prevents you from remounting it as Read / Write. The hook is enforced through a MACF calls in mount_begin_update() and mount_common(). All the hook does it to check for the presence of the <code class="high">MNT_ROOTFS</code> flag in the mount flags. If it exists, the operation fails. So what QiLin does? Simple. It turns off the <code class="high">MNT_ROOTFS</code> flag. 

The following listing is the <code class="high">remountRootFS</code> in the QiLin Toolkit and was made publicly available by Jonathan Levin on newosxbook.com and in the Volume III of *OS Internals.

```c
int remountRootFS (void)
{
   ...
   uint64_t rootVnodeAddr = findKernelSymbol("_rootvnode");
   uint64_t *actualVnodeAddr;
   struct vnode *rootvnode = 0;
   char *v_mount;
   status("Attempting to remount rootFS...\n");
   readKernelMemory(rootVnodeAddr, sizeof(void *), &actualVnodeAddr);
   readKernelMemory(*actualVnodeAddr, sizeof(struct vnode), &rootvnode);
   readKernelMemory(rootvnode->v_mount, 0x100, &v_mount);
   // Disable MNT_ROOTFS momentarily, remounts , and then flips the flag back
   uint32_t mountFlags = (*(uint32_t * )(v_mount + 0x70)) & ~(MNT_ROOTFS | MNT_RDONLY);
   writeKernelMemory(((char *)rootvnode->v_mount) + 0x70 ,sizeof(mountFlags), &mountFlags);
   char *opts = strdup("/dev/disk0s1s1");
   // Not enough to just change the MNT_RDONLY flag - we have to call
   // mount(2) again, to refresh the kernel code paths for mounting..
   int rc = mount("apfs", "/", MNT_UPDATE, (void *)&opts);
   printf("RC: %d (flags: 0x%x) %s \n", rc, mountFlags, strerror(errno));
   mountFlags |= MNT_ROOTFS;
   writeKernelMemory(((char *)rootvnode->v_mount) + 0x70 ,sizeof(mountFlags), &mountFlags);
   // Quick test:
   int fd = open ("/test.txt", O_TRUNC| O_CREAT);
   if (fd < 0) { error ("Failed to remount /"); }
   else {
     status("Mounted / as read write :-)\n");
     unlink("/test.txt"); // clean up
   }
 return 0;
```
Jonathan Levin's code is pretty straightforward. Flip the MNT_ROOTFS flag, call <code class="high">mount(2)</code> to refresh the kernel code paths for mounting, restore the flag and test. Done. You're R/W. 

On older jailbreaks patches to <code class="high">LightweightVolumeManager::_mapForIO</code> were done. 

### Electra's remount

iOS 11.3 took it a step further by involving APFS Snapshots. APFS has been used for quite a long time in iOS at the moment when Apple started using the snapshots, but when they did it broke the tried and true remount we had for iOS 11.2.x and even older. To fix this, a new bug needed to be found. the problem is that iOS would revert to a snapshot which is mounted read-only, so everything we install in terms of tweaks, binaries, etc is gone.

At this point two things can be done: Change the whole jailbreaking and go ROOTless, or find a way around the snapshots.
It is thanks to @Pwn20wnd and @umanghere that a proper remount has been created. Umang has found a bug that pwn20wnd has exploited in Electra.

Pwn20wnd's bypass for this snapshot problem is also a very straightforward one.
Here is the function from the source code of Electra for iOS 11.3.1:

```c
int remountRootAsRW(uint64_t slide, uint64_t kern_proc, uint64_t our_proc, int snapshot_success){
    if (/* iOS 11.2.6 or lower don't need snapshot */ kCFCoreFoundationVersionNumber <= 1451.51 || /* we're already remounted properly */ snapshot_success == 0){
        return remountRootAsRW_old(slide, kern_proc, our_proc);
    }
    
    if (!getOffsets(slide)){
        return -1;
    }
    
    uint64_t kernucred = rk64(kern_proc+offsetof_p_ucred);
    uint64_t ourucred = rk64(our_proc+offsetof_p_ucred);
     
    uint64_t vfs_context = get_vfs_context();
    
    char *dev_path = "/dev/disk0s1s1";
    uint64_t devVnode = getVnodeAtPath(vfs_context, dev_path);
    uint64_t specInfo = rk64(devVnode + offsetof_v_specinfo);
    
    wk32(specInfo + offsetof_si_flags, 0); //clear dev vnode's v_specflags
    
    if (file_exists(ROOTFSMNT))
        rmdir(ROOTFSMNT);
    
    mkdir(ROOTFSMNT, 0755);
    chown(ROOTFSMNT, 0, 0);
    
    printf("Temporarily setting kern ucred\n");
    
    wk64(our_proc+offsetof_p_ucred, kernucred);
    
    int rv = -1;
    
    if (mountDevAsRWAtPath(dev_path, ROOTFSMNT) != ERR_SUCCESS) {
        printf("Error mounting root at %s\n", ROOTFSMNT);
        
        goto out;
    }
    
    /* APFS snapshot mitigation bypass bug by CoolStar, exploitation by Pwn20wnd */
    /* Disables the new APFS snapshot mitigations introduced in iOS 11.3 */
    
    printf("Disabling the APFS snapshot mitigations\n");
    
    const char *systemSnapshot = find_system_snapshot(ROOTFSMNT);
    const char *newSystemSnapshot = "orig-fs";
    
    if (!systemSnapshot) {
        goto out;
    }
    
    int rvrename = do_rename(ROOTFSMNT, systemSnapshot, newSystemSnapshot);
    
    if (rvrename) {
        goto out;
    }
    
    rv = 0;

    unmount(ROOTFSMNT, 0);
    rmdir(ROOTFSMNT);
    
out:
    printf("Restoring our ucred\n");
    
    wk64(our_proc+offsetof_p_ucred, ourucred);
    
    //cleanup
    vnode_put(devVnode);
    
    if (!rv) {
        printf("Disabled the APFS snapshot mitigations\n");
        
        printf("Restarting\n");
        restarting();
        sleep(2);
        do_restart();
    } else {
        printf("Failed to disable the APFS snapshot mitigations\n");
    }
    
    return -1;
}
```

It may look complicated. That's because it is. It took Pwn20wnd a lot of work to achieve this bypass, but once the bug was known, the problem started to fall apart. See, the bug is very simple: iOS will revert to an APFS System snapshot after every reboot if there is one. Here's the catch - if there is one. If there isn't, instead of boot looping or erroring out in a destructive way, iOS casually continues booting just like it did on iOS 11.2.6 where there was no snapshot. 

So the patch? Find and rename the snapshot to something else.

The implementation? A bit more complicated.
If you analyze the code you can see that the name of the said snapshot is dynamic (or at least contains a dynamic part) so it cannot be hardcoded. The dynamic part happens to be the <code class="high">boot-manifest-hash</code>. Before doing anything, Pwn20wnd seems to be bestowing himself the kernel credentials.

You can find it yourself by running:

```bash
ioreg -p IODeviceTree -l | grep boot-manifest-hash
```
In the Electra, all the logic for finding the boot-manifest-hash is located in <code class="high">apfs_util.c</code> as you can see here:

```c
char *copyBootHash(void) {
    unsigned char buf[1024];
    uint32_t length = 1024;
    io_registry_entry_t chosen = IORegistryEntryFromPath(kIOMasterPortDefault, "IODeviceTree:/chosen");
    
    if (!MACH_PORT_VALID(chosen)) {
        printf("Unable to get IODeviceTree:/chosen port\n");
        return NULL;
    }
    
    kern_return_t ret = IORegistryEntryGetProperty(chosen, "boot-manifest-hash", (void*)buf, &length);
    
    IOObjectRelease(chosen);
    
    if (ret != ERR_SUCCESS) {
        printf("Unable to read boot-manifest-hash\n");
        return NULL;
    }
    
    // Make a hex string out of the hash
    char manifestHash[length*2+1];
    bzero(manifestHash, sizeof(manifestHash));
    
    int i;
    for (i=0; i<length; i++) {
        sprintf(manifestHash+i*2, "%02X", buf[i]);
    }
    
    printf("Hash: %s\n", manifestHash);
    return strdup(manifestHash);
}
```

This function is called from inside another function called <code class="high">find_system_snapshot</code> which handles the logic for finding the snapshot itself. The function appends the retrieved manifestHash to the hard-coded part which is <code class="high">com.apple.os.update-</code> resulting in the real name of the current snapshot. It then returns the snapshot name to the caller, but not before printing it out loud :P

```c
const char *find_system_snapshot(const char *rootfsmnt) {
    char *bootHash = copyBootHash();
    char *system_snapshot = malloc(sizeof(char *) + (21 + strlen(bootHash)));
    bzero(system_snapshot, sizeof(char *) + (21 + strlen(bootHash)));
    
    if (!bootHash) {
        return NULL;
    }
    sprintf(system_snapshot, "com.apple.os.update-%s", bootHash);
    printf("System snapshot: %s\n", system_snapshot);
    return system_snapshot;
}
```
With kernel's credentials in place, and with the proper name of the snapshot, Pwn20wnd returns back into rootfs_remount.c for the last part of this magnificent exploit - renaming the Snapshot. He renames it to "orig-fs", then he checks if the renaming succeeded. Then he restores his own credentials and drops kernel's. Finally, he reboots the device. That's why the first time you use Electra for iOS 11.3-11.3.1 your device will have a mandatory reboot no matter which exploit you use. 

Now that the snapshot has been renamed, iOS has no snapshot to mount so it simply doesn't. Problem solved. 10/10 - IGN.

### Special thanks
<ul>
<li>Jonathan Levin for his <a href="http://newosxbook.com/index.php">books</a>, tools and impressive patience with me and my odd questions - YOU ROCK!</li>
<li>@Pwn20Wnd for pushing me to learn more (and more) and being supportive </li>
<li>ETA Folks / reditters / naggers for making me laugh from time to time </li>
<li>IBSparkes for answering many of my questions during all these months</li>
</ul>

### License for the diagrams
The diagrams are all built by myself in a tedious process, however, I license them as MIT. Use them as you please as long as you credit me.

### Errare humanum est!
If you find anything wrong in the article feel free to trash-talk me on reddit or better yet, to tell me on Twitter :P
