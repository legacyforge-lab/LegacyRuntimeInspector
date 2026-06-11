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

Without that license information, the OCX may be present and correctly registered, but VB6 will still refuse to load the control in design mode.

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

## Practical Diagnostic Procedure

When VB6 reports:

```text
license not found
```

and the OCX is already present and correctly registered, check the design-time license registry entries.

On a known working development machine:

1. Open `REGEDIT`.
2. Navigate to:

```text
HKEY_CLASSES_ROOT\Licenses
```

3. Look for license entries related to the affected ActiveX controls.
4. Export the relevant license keys to a `.reg` file.

On the target machine:

1. Open `REGEDIT`.
2. Navigate to:

```text
HKEY_CLASSES_ROOT\Licenses
```

3. Verify whether the same license entries exist.
4. If the user owns a valid license for the component, import the missing `.reg` file.
5. Restart Visual Basic 6.
6. Reopen the project.

In many cases, the OCX itself is not the problem.

The missing part is the design-time license metadata stored in the Windows Registry.

## Important Licensing Note

Commercial ActiveX controls may require a valid development license.

Registry license entries should only be exported and imported when the user legitimately owns the corresponding software license.

This procedure should not be used to bypass third-party licensing restrictions.

## Why REGSVR32 Is Not Enough

`REGSVR32` registers the COM component.

It does not necessarily install:

- design-time license keys;
- vendor-specific registry metadata;
- development environment integration;
- supporting files required by the original installer.

This is why copying and registering an OCX can be enough for runtime execution but still fail when opening forms in the VB6 IDE.

## Resolution

The safest resolution is to install the original licensed component package whenever available.

If a known working development machine exists, the relevant license registry entries can be compared and documented.

After restoring the missing license entries, VB6 can usually load the affected controls correctly.

## Lessons Learned

`License not found` does not mean the same thing as `OCX not registered`.

These are different failure modes:

- OCX missing
- OCX present but not registered
- OCX registered but missing design-time license
- OCX registered but incompatible with the project reference
- OCX registered but dependent on vendor-specific registry metadata

Each one requires a different fix.

## Recommendation

A diagnostic tool should distinguish between:

- Missing OCX file
- Missing COM registration
- Missing registered path
- Type library version mismatch
- Possible design-time license issue
- Missing vendor-specific metadata

This distinction is especially important when maintaining or migrating legacy VB6 applications that depend on commercial ActiveX controls.

## Future Legacy Runtime Inspector Feature

A future version of Legacy Runtime Inspector could add a dedicated check for VB6 design-time issues by comparing:

- detected ActiveX controls;
- COM registration;
- registered file path;
- type library version;
- possible license entries under `HKEY_CLASSES_ROOT\Licenses`;
- project references stored in `.vbp` files.

This would help explain cases where a control appears installed but still cannot be loaded by the VB6 IDE.
