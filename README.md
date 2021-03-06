*Mono on i Project October 13, 2018*

# 10/13/18 Binary Preview Package Notes and Instructions 

The Mono on i Project began porting Mono to PASE on IBM i on January 19, 2018. 
There are still numerous bugs to fix, and the port is hardly production-ready 
as of yet. However, progress has been made at a very rapid pace, and we 
would like others to try it out and report problems. We encourage users 
to report issues to the Mono on i Project by opening an issue at our 
[GitHub issue tracker](https://github.com/MonoOni/binarydist/issues).

## Changelog

### Changes in the 10/13/18 release

* Updated to latest changeset.

### Changes in the 7/6/18 release

* Updated to latest changeset.

* Issues with `System.Decimal` have been fixed.

* Issues with `System.TimeZoneInfo` have been fixed, by including the standard [IANA time zone info files](https://www.iana.org/time-zones) with the distribution. The tools to manage the database are also included inside a distribution.

### Changes in the 6/26/18 release

* Updated to latest changeset. Upstream has done significant work, including progress towards Roslyn on PowerPC, `Span<T>` intrinsics, and further integration of the CoreFX platform abstraction layer. CoreFX PAL work will involve heavy work for AIX and PASE integration, and the beginnings of such work have been done.

* Spurious messages when spawning new threads on AIX have been reduced.

* System.Diagnostics.Process should function better on AIX.

### Changes in the 4/28/18 release

* Updated to latest changeset. Note upstream is doing refactoring work on tail calls, so we have applied an experimental patch to fix PPC support for such things.

### Changes in the 4/15/18 release

* Updated to latest changeset. This version includes a fixed and upstreamed version of the exceptions issue fixes.

### Changes in the 4/10/18 release

* Updated to latest changeset.

### Changes in the 3/27/18 release

* Updated to latest changeset.

### Changes in the 3/13/18 release

* Updated to latest changeset.

* BoringTLS is now available, with TLSv1.2 support. Certificates will need to be installed to trust remote sites.

* An experimental patch to resolve exception crashes in static constructors has been integrated. Thanks to Bernhard for trying to fix this!

### Changes in the 2/27/18 release

* Updated to latest changeset.

* Async callbacks have been enabled for the runtime.

* Explicit null checks are always enabled due to null pages..

### Changes in the 2/17/18 release

* Spurious errors with asynchronous threaded I/O have been fixed.

### Changes in the 2/14/18 release

* libintl is properly packaged.

* In this build, special features allowing the JIT to use new CPU instructions
of POWER7 processors have been disabled due to reports of issues when enabled.

### Changes in the 2/13/18 release

* PowerPC processor features are more accurately detected.

* `System.Environment.OSVersion` now properly reports the operating system version.

* Fix a big with getting array sizes.

* Compatibility issues with i 7.1 have been resolved to the point where the
runtime can successfully run programs.

### Changes in the 2/7/18 release

Attempting to run Mono on 7.1 resulted in error messages relating to mkdtemp.
We discovered that Mono's built in glib has an implementation of mkdtemp,
so we modified the code to use that instead.

## Required system 

Either AIX 6.1 TL9 or i 7.1. The JIT compiler will automatically adjust 
parameters to optimize for the current CPU. Testing is primarily done on AIX,
then on i 7.2, then i 7.1.

It is extremely critical to ensure that the system QCCSID value is 
properly set. Check your QCCSID value with `DSPSYSVAL QCCSID`. If it is 
65535, please set it to the proper value and sign off and on before 
installing Mono. 

It is strongly recommended that the following IBM LPPs and PTFs are 
installed: 

* LPP 5733-SC1 OpenSSH/OpenSSL 

* LPP 5733-OPS Open Source Solutions Option 7 (Tools) 

* Open Source Solutions PTF Group SF99123 (7.1), SF99223 (7.2), 
or SF99725 (7.3) Level 5 (or later) 

## Included contents 

* Mono (pre-release version 5.13; built from master branch) 

* mcs (non-Roslyn C# compiler; supports C# 7) 

* xbuild (non-Microsoft solution/project make tool) 

* vbnc (non-Roslyn Visual Basic.NET compiler; supports VB 2005,
partial VB 2010 support) 

* xsp (stand-alone ASP.NET server) 

* FastCGI Server 

* NUnit (unit testing library and toolchain) 

## Known issues 

Please see the [issues page](https://github.com/MonoOni/binarydist/issues) for a list of known issues and to file further ones.

<!-- Removed swathes better served by the issues page -->

* FastCGI and mod_mono servers are shipped, but untested. XSP can be 
used as a development server in the meantime. Be aware that many ASP.net 
controls refer to System.Drawing, which means that all but the smallest 
ASP.net pages will likely run into the aforementioned problems with 
System.Drawing. 

* Debug messages such as below will be printed during normal operation. 
This is expected, often due to semantic differences between other Unices 
and AIX/PASE. The team is working to resolve these issues. 

```
mono_thread_internal_set_priority: pthread_setschedparam failed, error: 
"Operation not permitted." (1) 
```

* Due to differences in how AIX/PASE handle shared libraries, many 
applications may crash attempting to load native libraries. As such, you 
may have to alter the application's DLL mappings, either for the 
assembly in its `.config` file, or the global `/opt/mono/etc/config` file. 
Consult <http://www.mono-project.com/docs/advanced/pinvoke/dllmap/> for 
how DLL mappings work on Mono. Issues with the global DLL mapping are 
due to packaging toolchain defiencies and will be fixed in a later 
release. 

* Portions of the standard library may not fully function due to missing 
support for AIX/PASE. Users are encouraged to report these incidents to 
the team. 

* Calling between ILE and PASE code is currently not possible. The team 
expects to eventually solve this problem, but has not crossed that 
bridge as of this time. 

* We have not attempted to connect to DB2 as of yet. The IBM DB2i .NET 
provider included within iACS calls into a Win32 DLL, which 
unfortunately means that we cannot use it on the IBM i. The team is 
looking into alternative solutions currently. 

## Installation instructions (on IBM i)

* The latest release is available from
 <https://github.com/MonoOni/binarydist/releases>

* Create an empty save file with which to receive the save file that you 
downloaded to your PC. To do so, open up a 5250 "green screen" session 
and run the command `CRTSAVF SAVF(QGPL/MONO101318)` on your system. 

* Transfer the save file using a command line FTP client in binary mode 
to `QGPL/MONO101318`. 

* Restore the Mono on i binaries by running 
`RST DEV('/QSYS.LIB/QGPL.LIB/MONO101318.FILE') OBJ('/QOpenSys/opt/mono')`
on your system. 

* After unpacking the save file, it is important to make sure that a 
symbolic link to /QOpenSys/opt is present at /opt. If you have used the 
YiPS package scripts to install AIX RPMs on PASE, this symbolic link has 
already been created for you. To check if it exists, run the command 
`WRKLNK '/opt'`. If you get "Object not found.", create it by running 
`ADDLNK OBJ('/QOpenSys/opt') NEWLNK('/opt')` on your system. 

## Compile and run your first Mono program 

* A sample "Hello, World!" program is included in 
/opt/mono/samples/hello.cs 

* To compile it, you can either open a SSH 
session into a PASE shell, or, less optimally, `CALL QP2TERM`. Once you 
are in the PASE shell, run the following commands to make the Mono 
commands available to your shell: 

```
$ PATH=/opt/mono/bin:$PATH
$ export PATH
```

* Copy the hello.cs file from /opt/mono/samples

```
$ cp /opt/mono/samples/hello.cs hello.cs
```

* Now, invoke the Mono C# compiler to compile hello.cs: 

```
$ mcs hello.cs 
```

* A file named hello.exe will be created in your current directory. This 
is a Mono/.NET "managed code assembly" containing the compiled bytecode, 
much like a Java .JAR file, or a program object. You can even FTP this 
file to your PC and then run it on Windows. Try it! 

* Now, to run the program: 

```
$ mono hello.exe 

Hello from Mono for the IBM i! 
```

* Similarly, you can compile a .NET program on Visual Studio for 
Windows, copy it to your IBM i system, and run it using the "mono" 
command. Be aware though, that this may unearth numerous undiscovered 
bugs in the Mono for IBM i port. 

## TLS certificates

Out of the box, the runtime's TLS layer won't trust anything due to lacking
certificates for it. You can install certificates into Mono using [the same
tools](http://www.mono-project.com/docs/faq/security/#secure-socket-layer-ssl--transport-layer-security-tls) as Mono uses on other systems.
