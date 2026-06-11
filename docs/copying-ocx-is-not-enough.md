# Why Copying an OCX from a Working Machine Is Not Always Enough

## The Common Migration Strategy

When moving a VB6 application to another computer, developers often follow a simple process:

1. Copy the OCX from a working machine.
2. Place it in `System32`, `SysWOW64` or the application folder.
3. Register it using `REGSVR32`.
4. Reopen the application or VB6 project.

Sometimes this works.

Sometimes it does not.

## What Is Missing?

Many ActiveX controls depend on more than the OCX file itself.

Possible dependencies include:

- COM registry entries
- Type library registration
- Design-time license keys
- Additional DLL dependencies
- Vendor-specific installation entries
- Binary compatibility information
- Correct 32-bit or 64-bit registration

For VB6 applications, this is even more important because the IDE is a 32-bit application and expects 32-bit ActiveX components.

## Example

A legacy VB6 project using:

```text
MSCOMCTL.OCX
THREED32.OCX
```

was migrated to a clean virtual machine.

Both files were copied and registered.

The result was:

```text
MSCOMCTL.OCX could not be loaded
```

followed by:

```text
license not found
```

for multiple controls.

The issue was not simple file availability.

The issue was missing or incompatible metadata stored elsewhere in the operating system.

## Why It Happens

An OCX file is only one part of the ActiveX environment.

VB6 may also depend on:

- the exact version referenced by the `.vbp` file;
- the registered type library version;
- the original development license;
- registry keys created by the vendor installer;
- additional files required by the control.

This explains why copying a file from a working machine can still fail.

## Best Practice

Instead of copying individual files blindly:

- Use the original vendor installer whenever available.
- Preserve or document exact OCX versions.
- Check the `.vbp` `Object=` references.
- Register 32-bit controls using the 32-bit `REGSVR32`.
- Verify design-time licenses for commercial controls.
- Compare a working development machine with the target machine.
- Keep a technical inventory of all legacy dependencies.

For 32-bit VB6 components on 64-bit Windows, the correct registration command usually uses:

```bat
C:\Windows\SysWOW64\regsvr32.exe C:\Windows\SysWOW64\CONTROL.OCX
```

## Future Tooling

A dependency scanner should identify:

- ActiveX controls
- COM registration status
- Registered paths
- Missing dependencies
- Type library version mismatches
- Possible design-time license issues
- Differences between project references and installed components

This is one of the motivations behind Legacy Runtime Inspector.

Legacy VB6 systems often fail not because the main executable is broken, but because the runtime environment around it has not been reconstructed correctly.
