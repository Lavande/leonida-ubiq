# Windows 11 Build Troubleshooting & Source Verification

This guide covers common Windows 10/11 toolchain errors when following [first_run_ui.md](first_run_ui.md) or running `Build.bat` / `make_game.py`, plus a brief source-verification checklist for newcomers.

## 1. x64 Developer PowerShell & CMake/Ninja Configuration

The runtime build (`cmake -S runtime -B $build -G Ninja`) expects an **x64** MSVC environment.

- **Symptom**: `CMAKE_CXX_COMPILER not found`, `ninja: not found`, or `x86` architecture mismatch.
- **Fix**: Launch **x64 Native Tools Command Prompt for VS 2022** (or import `VsDevCmd.bat -arch=x64` in PowerShell) with the *Desktop development with C++* workload installed, and confirm `cmake --version` and `ninja --version` resolve.

## 2. Vulkan SDK (`spirv-val`) & AMDGPU `llvm-mc` Not on `PATH`

- **Symptom**: CMake fails with `LEONIDA_REQUIRE_VULKAN=ON` or shader stages fail looking for `spirv-val` / `llvm-mc`.
- **Fix**:
  1. Install the LunarG Vulkan SDK and ensure `%VULKAN_SDK%\Bin` is on `PATH`.
  2. Install an LLVM build with the `AMDGPU` target enabled and verify `llvm-mc --version` lists `amdgpu`.
  3. Restart your shell after modifying environment variables.

## 3. Missing `tools/rpf9dump` Submodule

- **Symptom**: `tools/rpf9dump` is empty or fails to build.
- **Fix**: Run `git submodule update --init` from the repository root (as noted in [CONTRIBUTING.md](../CONTRIBUTING.md)).

## 4. Python 3.12+ & Isolated Mode (`python -I`)

- Standard-library tools and tests run under `python -I`.
- If `python` resolves to the Microsoft Store stub, disable the App Execution Aliases for `python.exe` / `python3.exe` in Windows Settings and install Python 3.12+ from python.org with **Add python.exe to PATH** enabled.
- Pytest-based tests require the packages in `tests/requirements.txt` and run with `python -m pytest tests/test_<name>.py`.

## 5. Disk Space & Writable Scratch / Backing Directories

- Keep at least **~350 GB free SSD space** for the shader workspace (`$work`), build artifacts, and local port output (`$port`), placed **outside** both the repository checkout and your game package directory.
- If the default volume is low on space when launching `gta6rt.exe --first-run-ui`, redirect guest output and sparse physical-memory backing with `--overlay DIR` and `LEONIDA_XMEM_BACKING_DIR`.

## 6. Safe Source Verification

Legitimate Project Leonida repositories ship **source code only** with zero pre-compiled executables, keys, or game files.

- Reject any third-party site offering a pre-compiled `.exe` installer or password-protected archive (e.g. password `leonida`) claiming to be a playable PC build.
- Inspect `Build.bat` and `.gitmodules` before executing scripts from unfamiliar mirrors.

## Community References

- Step-by-step Windows setup & troubleshooting guide: [gtaleonida.net/leonida-build-tutorial](https://gtaleonida.net/leonida-build-tutorial)
