Isolated COM - Registration-Free COM Sample
===========================================
This is a sample to demonstrate how to call a COM library written in .NET from a C++ client without doing any of the COM related registration steps.

The COM library is a base64 Encoder/Decoder albeit it's named Decoder.

To compile and build the C++ client, you need to provide the COM library's tlb file.
The tlb file can be generated using the following command:

```
tlbexp.exe decoder.dll
```

The trick to get the C++ client to "see" the COM library is to have the correct manifests in place for both client and library.

Generating the manifest for the Decoder library is done using the tool mt.exe shipped with Visual Studio.
```
mt -managedassemblyname:decoder.dll -out:decoder.manifest -nodependency
```

This will generate a manifest that looks something like this:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1"manifestVersion="1.0">
    <assemblyIdentity name="Decoder" version="1.0.0.0" processorArchitecture="msil">
    </assemblyIdentity>
    <clrClass clsid="{6477C617-F645-3313-9F41-CC5112BEDEA5}" progid="Decoder.StringDecoder" threadingModel="Both" name="Decoder.StringDecoder" runtimeVersion="v4.0.30319">
    </clrClass>
    <file name="decoder.dll" hashalg="SHA1"></file>
</assembly>
```

The C++ client's manifest file can be either written by manually, or using Visual Studio's 

project configuration properties > Linker > Manifest File > Additional Manifest Dependencies

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <assemblyIdentity
        type = "win32"
        name = "Win32ConsoleApplication"
        version = "1.0.0.0" />
    <dependency>
        <dependentAssembly>
            <assemblyIdentity
                processorArchitecture="msil" 
                name="Decoder"
                verrsion="1.0.0.0" />
        </dependentAssembly>
    </dependency>
</assembly>
```


Using the mt.exe tool, you can also embed each manifest in its corresponding exe or dll to reduce the number of files deployed.

```
mt -manifest client.exe.manifest -outputresource:client.exe;#1
mt -manifest decoder.manifest -outputresource:decoder.dll;#1
```
