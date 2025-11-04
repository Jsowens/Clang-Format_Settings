# Installation
1. Download clang-format.txt to the root of your project.
2. Install clang-format
   - Debian based linux: ```sudo apt update && sudo apt install clang-format```
3. Configure your editor to use the installed clang-format app and to reference the format file
   - VSCode
     1. Install the ```C/C++ Extension Pack by Microsoft``` extension.
     2. Go to File -> Preferences -> Settings
     3. Type ```clang``` in the search bar
     4. Set ```C_Cpp: Clang_format_path``` to ```/usr/bin/clang-format``` or wherever you installed clang-format
     5. Set ```C_Cpp: Clang_format_style``` to ```file:${workspaceFolder}/clang-format.txt```
     6. Type ```format``` in the search bar
     7. Check ```Editor: Format On Save```
