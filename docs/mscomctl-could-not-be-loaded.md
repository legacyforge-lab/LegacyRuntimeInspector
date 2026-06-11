# Why VB6 Reports "MSCOMCTL.OCX Could Not Be Loaded" Even After Registration

## Overview

A common assumption is that when Visual Basic 6 reports:

> MSCOMCTL.OCX could not be loaded

the OCX is simply missing or not registered.

In practice, the root cause can be more subtle.

During the migration of a legacy industrial VB6 application to a clean Windows 7 virtual machine, the project failed to load even though `MSCOMCTL.OCX` had been copied and registered successfully using `REGSVR32`.

## Investigation

The original control was copied from a known working machine.

The following steps were performed:

1. Unregister the existing `MSCOMCTL.OCX`
2. Remove the file
3. Copy the original OCX from the production machine
4. Register it using `REGSVR32`
5. Reopen the VB6 project

The project still reported:

> MSCOMCTL.OCX could not be loaded

At first sight, the OCX appeared to be present and registered correctly.

## VBP Reference Check

The VB6 project file contained an object reference similar to:

```text
Object={831FDD16-0C5C-11D2-A9FC-0000F8754DA1}#2.1#0; MSCOMCTL.OCX
```

This means that the project was not only looking for a file named `MSCOMCTL.OCX`.

It was also expecting a specific ActiveX type library version.

## Root Cause

The issue was not simple COM registration.

VB6 requires the ActiveX control version expected by the project.

Even when two files share the same filename:

```text
MSCOMCTL.OCX
```

they may expose different type library versions or binary compatibility information.

A runtime-compatible version is not always sufficient for design-time loading inside the VB6 IDE.

## Lessons Learned

When diagnosing VB6 ActiveX loading failures:

- Do not assume registration is the only problem.
- Verify the exact OCX file version.
- Check the `Object=` entries stored in the `.vbp` file.
- Compare the type library version required by the project.
- Compare the OCX against a known working development machine.
- Use the original development OCX whenever possible.

## Recommendation

A diagnostic tool should report:

- File existence
- COM registration status
- Registered path
- File version
- Type library version
- Required version from the VB6 project file
- Possible design-time compatibility warnings

This would significantly reduce troubleshooting time during legacy VB6 migrations.
