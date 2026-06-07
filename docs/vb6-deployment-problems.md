# Why a VB6 Application Works on One PC but Not Another

Many organizations still rely on Visual Basic 6 applications developed years or even decades ago.

A common situation is:

"The application works perfectly on the old PC but fails on the new one."

## The Executable Is Only Part of the System

A VB6 executable often depends on:

* OCX controls
* COM components
* DLL libraries
* Runtime files
* Registry entries

Copying only the EXE file is rarely sufficient.

## Common Failure Symptoms

Typical errors include:

* Component not correctly registered
* ActiveX component can't create object
* Class not registered
* Missing DLL
* Unexpected application crash at startup

## Hidden COM Dependencies

Many dependencies are not obvious.

A control may be referenced through a COM GUID rather than a file name.

Without inspecting these references, troubleshooting becomes difficult.

## Legacy Deployment Challenges

Modern Windows systems differ significantly from Windows XP environments:

* Different runtimes
* Different security settings
* Different registry contents
* Missing legacy components

As a result, applications that ran for years may suddenly fail after migration.

## Recommended Process

1. Analyze the executable.
2. Identify dependencies.
3. Verify registration status.
4. Install missing components.
5. Test again.

This approach is significantly more reliable than manually copying files between machines.
