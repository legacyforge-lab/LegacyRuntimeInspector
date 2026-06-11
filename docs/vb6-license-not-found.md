# Understanding "License Not Found" Errors in VB6 ActiveX Controls

## The Symptom

After resolving missing OCX registration issues, Visual Basic 6 may still display errors such as:

```text
Cannot load control SSPanel1; license not found.
Cannot load control PnlHeader; license not found.
Cannot load control PnlFooter; license not found.
```

Many developers assume the OCX is still missing or not registered.

In reality, this is a different problem.

## Runtime vs Design-Time

VB6 ActiveX controls can have two separate requirements.

### Runtime

Runtime registration is required to execute an application.

This is usually handled with:

```bat
regsvr32 control.ocx
```

If the application only needs to run, this may be enough.

### Design-Time

Design-time support is required to open forms inside the VB6 IDE.

Some commercial ActiveX controls require additional license information stored in the Windows Registry.

Without that license information, the OCX may be registered, but VB6 will still refuse to load the control in design mode.

## Real Case

A legacy VB6 project used Sheridan 3D controls:

```text
THREED32.OCX
```

The OCX file was copied from a working machine and registered successfully.

The compiled application could run.

However, the VB6 IDE still displayed:

```text
license not found
```

and refused to load controls such as `SSPanel`.

## Root Cause

The design-time license keys stored under:

```text
HKEY_CLASSES_ROOT\Licenses
```

were missing.

Copying these files was not sufficient:

```text
THREED32.OCX
THREED32.OCA
```

The missing component was the registry license information required by the VB6 IDE.

## Resolution

The correct resolution is to install the original licensed component package whenever available.

If a known working development machine exists, the relevant license registry entries may be compared and documented.

Important: commercial ActiveX controls may be licensed software. License information should only be restored when the user owns the appropriate license.

## Lessons Learned

`License not found` does not mean the same thing as `OCX not registered`.

These are different failure modes:

- OCX missing
- OCX present but not registered
- OCX registered but missing design-time license
- OCX registered but incompatible with the project reference

Each one requires a different fix.

## Recommendation

A diagnostic tool should distinguish between:

- Missing OCX file
- Missing COM registration
- Missing registered path
- Type library version mismatch
- Possible design-time license issue

This distinction is especially important when maintaining or migrating legacy VB6 applications that depend on commercial ActiveX controls.
