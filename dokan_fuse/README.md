# Building and debugging Dokan FUSE

Run the existing Cygwin/MSYS2 build script from the repository root:

```powershell
.\dokan_fuse\build.ps1
```

The script selects CMake's `RelWithDebInfo` configuration by default. With the
supported GCC toolchains this retains optimization and generates debug information
for the FUSE sources. The normal `make install` step retains that information in
`cygdokanfuse2.dll` (Cygwin) and `libdokanfuse2.dll` (MinGW). These builds use DWARF
debug information inside the DLL, rather than a separate MSVC PDB file. Do not
strip the DLL if you need that information.

Choose another CMake configuration explicitly when needed:

```powershell
.\dokan_fuse\build.ps1 -BuildType Debug
.\dokan_fuse\build.ps1 -BuildType Release
```

`Debug`, `Release`, `RelWithDebInfo`, and `MinSizeRel` are accepted. For a direct
CMake build, specify `-DCMAKE_BUILD_TYPE=RelWithDebInfo` when configuring.

Use the debugger and DLL matching your application's toolchain and architecture.
For example, use Cygwin GDB with the Cygwin DLL. The separately built MSVC
`dokanfuse2.dll` uses its matching `dokanfuse2.pdb`, which the MSVC build and
installer already include.

When verifying a new build, inspect source compilation units and line information,
not just the presence of `.debug_info`: GCC runtime objects can contribute debug
sections even when the FUSE sources were compiled without them. For example,
`objdump --dwarf=info` should identify FUSE source files, and GDB should be able to
resolve a breakpoint to a line in `src/fusemain.cpp` (or another FUSE source).
