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
| b4c5ab9 | Mesh components have no index buffers, so every triangle sends three vertices: `MeshDesc` gains an optional `indices` buffer (one uint16 or uint32 stream), drawn by index in local vertex space and replaceable at run time with `go.set(mesh, "indices", res)` and `resource.set_buffer`; Bob builds the field. A feature, not a fix; the stock editor does not know the field. Formatted with 1.14.0's clang-format, so its alignment differs from cfcb712 | M1.039 (a task, not a bug) | Not yet offered; the pull request will come from `feature/mesh-index-buffers` (cfcb712, on upstream `dev`) |
| e91f8ab | Test builds that build bob-light (gamesys, for example) hang on Windows: the `installBobLight` custom command left a Gradle daemon holding Ninja's output pipe open. It now runs Gradle with `--no-daemon`. Build tooling only; the engine and Bob are unchanged | BUG.089 | Not yet offered; the pull request will come from `fix/bob-light-gradle-no-daemon` (9f33024, on upstream `dev`) |
| 1ec1022 | `build.py` itself ran Bob's Gradle builds (`build_bob_light`, `build_bob`, `build_bob_plugins`) without `--no-daemon`, so `update-build.ps1` left a Gradle daemon holding about 700 MB after the build exited. They now pass `--no-daemon` too. Build tooling only | BUG.114 | Not yet offered; it joins e91f8ab's pull request on `fix/bob-light-gradle-no-daemon` (2910da3) |
| 1b169f6 | `install_ext` stopped when Windows tar could not unlink a file another process (probably a virus scanner) briefly held: `build.py` now retries each tar extraction up to three times, two seconds apart, on Windows. Build tooling only | BUG.115 | Not yet offered; the pull request will come from `fix/win32-tar-extract-retry` (745b07d, on upstream `dev`) |

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
