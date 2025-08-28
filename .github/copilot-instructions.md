# FortuneTeller32 - Korean Fortune Telling Application

FortuneTeller32 is a .NET Framework 4.7.2 Windows Forms desktop application written in C# that provides Korean fortune telling (사주) services. The application reads fortune data from CSV files and maintains user history.

**Always reference these instructions first and fallback to search or bash commands only when you encounter unexpected information that does not match the info here.**

## Quick Reference

| Command | Purpose | Timeout |
|---------|---------|---------|
| `sudo apt-get update && sudo apt-get install -y mono-complete` | Install Mono | 600s |
| `rm -rf bin/ obj/ && xbuild FortuneTeller32.sln` | Clean build | 30s |
| `ls -la bin/Debug/FortuneTeller32.exe` | Verify build output | N/A |
| `du -h bin/Debug/FortuneTeller32.exe` | Check file size (~450KB) | N/A |

**CRITICAL:** Cannot run in headless environments - Windows Forms requires GUI display.

## Working Effectively

### Build Environment Setup
On Linux environments:
- Install Mono: `sudo apt-get update && sudo apt-get install -y mono-complete`
- Verify installation: `mono --version` and `which xbuild`

### Build Commands
- Clean build (Debug): `rm -rf bin/ obj/ && xbuild FortuneTeller32.sln /p:Configuration=Debug` -- NEVER CANCEL: Takes ~2 seconds. Set timeout to 30+ seconds.
- Clean build (Release): `rm -rf bin/ obj/ && xbuild FortuneTeller32.sln /p:Configuration=Release` -- NEVER CANCEL: Takes ~2 seconds. Set timeout to 30+ seconds.
- Incremental build: `xbuild FortuneTeller32.sln` -- NEVER CANCEL: Takes ~1 second. Set timeout to 30+ seconds.

### Critical Build Notes
- Build uses xbuild (Mono) on Linux - NOT dotnet CLI
- Builds are extremely fast (~2 seconds) but still require proper timeout settings
- Warning about "TargetFrameworkVersion 'v4.7.2' not supported" is EXPECTED and harmless
- Warning about "Don't know how to handle GlobalSection ExtensibilityGlobals" is EXPECTED and harmless
- Build output: `bin/Debug/FortuneTeller32.exe` or `bin/Release/FortuneTeller32.exe`

## Runtime Limitations - CRITICAL

**THIS APPLICATION CANNOT BE RUN IN HEADLESS LINUX ENVIRONMENTS:**
- Windows Forms application requires GUI/X11 display
- Running on Linux without display fails with: "Could not open display (X-Server required)"
- Application is designed for Windows with .NET Framework 4.7.2
- On Linux with Mono: Can build successfully, but cannot execute functionality testing
- **NO functional testing possible in CI/CD environments** - focus on build validation only

## Required Data Files

The application depends on CSV data files that must exist in the working directory:

### results.csv (Required)
Contains fortune telling data in format: `saju|message`
Example content:
```
갑자|오늘은 좋은 일이 일어날 것입니다.
을축|새로운 시작에 대한 기회가 찾아올 것입니다.
병인|인내심을 가지고 기다리면 좋은 결과가 있을 것입니다.
```

### history.csv (Auto-created)
User history file created automatically at runtime in format: `birthday birthhour|saju|message`

## Validation

### Build Validation - ALWAYS RUN
1. Clean build: `rm -rf bin/ obj/ && xbuild FortuneTeller32.sln`
2. Verify executable exists: `ls -la bin/Debug/FortuneTeller32.exe`
3. Check file size is reasonable: `du -h bin/Debug/FortuneTeller32.exe` (should be ~450KB)

### Code Quality Validation
- **NO linting tools configured** - rely on compiler warnings only
- **NO unit tests exist** - this is a simple GUI application without test infrastructure
- **NO automated testing possible** due to GUI requirements

### Manual Validation on Windows (when available)
1. Ensure `results.csv` exists with proper Korean text fortune data
2. Launch application and test basic fortune telling flow:
   - Enter birthday (YYYYMMDD format)
   - Enter birth hour (1-24)
   - Click result button
   - Verify fortune displays correctly
   - Check that `history.csv` file is created/updated

## Navigation and Key Components

### Solution Structure
```
FortuneTeller32.sln          # Visual Studio solution file
FortuneTeller32.csproj       # Project file (.NET Framework 4.7.2)
App.config                   # Application configuration
```

### Main Source Files
- `Program.cs` - Application entry point, starts Windows Forms
- `Form1.cs` - Main fortune telling interface and logic
- `Form1.Designer.cs` - Auto-generated UI layout (DO NOT EDIT manually)
- `FormAbout.cs` - About dialog
- `FormHistory.cs` - History viewing functionality and dialog

### Key Methods to Understand
- `Form1.LoadResults()` - Loads fortune data from results.csv
- `Form1.GetFortune()` - Random fortune selection logic
- `Form1.SaveHistory()` - Saves user history to history.csv
- `Form1.btnResult_Click()` - Main fortune telling button handler

### Resource Files
- `Form1.resx`, `FormAbout.resx`, `FormHistory.resx` - UI resources (auto-generated)
- `Properties/Resources.resx` - Application resources
- `Properties/AssemblyInfo.cs` - Assembly metadata

## Common Development Tasks

### Adding New Fortune Data
Edit `results.csv` directly using format: `saju|message` (Korean text)

### Debugging File Issues
Common issues involve missing CSV files:
- FileNotFoundException - `results.csv` missing
- UnauthorizedAccessException - permission issues with `history.csv`
- Check Korean text encoding (UTF-8)

### Code Changes
When modifying forms:
- Edit `.cs` files for logic
- Use Visual Studio Form Designer for UI changes (generates `.Designer.cs` files)
- **DO NOT manually edit** `.Designer.cs` files

## Troubleshooting

### Build Failures
- Ensure Mono is properly installed: `sudo apt-get install mono-complete`
- Framework version warnings are normal and can be ignored
- Clean build if resources fail to compile: `rm -rf bin/ obj/`

### Runtime Issues (Windows only)
- Verify `results.csv` exists and has proper format
- Check Korean text encoding (UTF-8 BOM or UTF-8)
- Ensure write permissions for `history.csv` creation
- UI language is Korean - ensure proper font support

### File Format Issues
- CSV files must use pipe `|` separator (not comma)
- Korean text requires proper UTF-8 encoding
- Each line in `results.csv`: `saju|message` format
- History format: `YYYYMMDD HH|saju|message`

## Important Notes and Edge Cases

### Build System Considerations
- The application will build successfully even if `results.csv` is missing (runtime dependency, not build-time)
- xbuild is deprecated but still works - msbuild is not available in this Mono version
- Clean builds are fast (~2 seconds) but incremental builds are even faster (~0.2 seconds)
- Build output size is consistent: ~450KB for executable

### Development Workflow Best Practices
- ALWAYS verify `results.csv` exists and has valid Korean text before testing changes
- Build first to catch compilation errors: `xbuild FortuneTeller32.sln`
- Check file sizes after build: `ls -la bin/Debug/FortuneTeller32.exe`
- Korean text display issues are common - ensure proper UTF-8 encoding
- Focus on logic changes rather than UI testing (GUI cannot be tested in Linux environments)

### Common Gotchas
- Do NOT attempt to run the application in CI/CD - it will always fail
- Do NOT expect unit tests - this is a simple desktop application
- Do NOT manually edit `.Designer.cs` files - they are auto-generated
- CSV format is strict: must use pipe separator, not commas
- File paths are relative to working directory - CSV files must be in same directory as executable