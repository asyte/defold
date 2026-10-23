# TrailPark patches

This branch is Defold 1.14.0 (tag `1.14.0`, d295ed4) plus the patches TrailPark carries.
`git log 1.14.0..trailpark/1.14.0` lists exactly what changed. TrailPark's
`tasks/M1/CHORE.001.txt` and `docs/defold_mirror.md` explain the branch layout, the build
and how to update to a new release.

## Patches

Each patch is one commit. Give its commit, what it fixes, the TrailPark bug it belongs
to (BUG.###), and its upstream pull request or "not offered, because ...".

| Commit | Fixes | TrailPark bug | Upstream |
| ------ | ----- | ------------- | -------- |
| 4fb637c | A startup crash on the job thread when the OpenGL async texture check fails: `ValidateAsyncJobProcessing()` now deletes its test texture directly, so no delete job is left to lock the mutexes `AsyncInitialize()` deletes | BUG.001 | Issue and pull request not yet filed; the pull request will come from `fix/async-validation-texture-race` |

## Build machine

The x86_64-win32 engine and Bob were first built from this branch on 2026-10-19, with:

- Visual Studio Community 2026 18.11 (MSVC 14.51), Windows SDK 10.0.26100
- Clang 22.1.3 and Ninja 1.13.2, both from Visual Studio
- CMake 4.4.4 (1.14.0 needs 4.0 or later, which README_SETUP.md does not say)
- Temurin JDK 25.0.4
- Python 3.12.4, plus the packages `build.py install_ext` installs
- Git Bash as the build shell

TrailPark's `tools/defold/update-build.ps1` runs install_ext, build_engine and build_bob,
and copies dmengine.exe, dmengine.pdb and bob.jar out of the build tree.
