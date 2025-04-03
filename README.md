# OpenGL Project Setup Guide (Windows, C++ / VS Code)

This guide provides a comprehensive, step-by-step walkthrough for setting up a modern OpenGL project on Windows using C++ with Visual Studio Code. We will use MinGW-w64 as our compiler, CMake as our build system, GLFW for window creation and input, and GLAD for loading OpenGL functions. The guide also covers creating the necessary folder structure, configuring environment variables, and building your project.

---

## Table of Contents

1. [Install Visual Studio Code](#1-install-visual-studio-code)
2. [Configure VS Code & Create Project Structure](#2-configure-vs-code--create-project-structure)
3. [Download & Install MinGW-w64 (64-bit)](#3-download--install-mingw-w64-64-bit)
4. [Install CMake](#4-install-cmake)
5. [Download & Prepare GLFW](#5-download--prepare-glfw)
6. [Generate & Prepare GLAD Files](#6-generate--prepare-glad-files)
7. [Create CMakeLists.txt](#7-create-cmakeliststxt)
8. [Create Build Batch File](#8-create-build-batch-file)
9. [Create main.cpp](#9-create-maincpp)
10. [Build & Run the Project](#10-build--run-the-project)
11. [Final Directory Structure](#11-final-directory-structure)

---

## 1. Install Visual Studio Code

- **Download**: Visit [Visual Studio Code Download](https://code.visualstudio.com/download) and download the Windows installer.
- **Install**: Run the installer and follow the prompts (default settings are typically fine).
- **Launch**: Open VS Code once installation is complete.

---

## 2. Configure VS Code & Create Project Structure

- **Open VS Code**.
- **Install Extensions**:  
   - Press `Ctrl+Shift+X` to open Extensions.  
   - Search for **"C/C++"** by Microsoft and install it.  
   - Optionally, install the **"C++ Extension Pack"** for additional features (linting, debugging, etc.).
- **Create a New Project Folder**:  
   - Create a folder named, for example, `OpenGLProject`.
- **Inside the Project Folder, Create the Following Subfolders**:
   - `include/` – for header files.
   - `src/` – for source code files.
   - `lib/` – for library files.

Your folder structure should now look like:

```
OpenGLProject
├── include
├── lib
└── src
```


---

## 3. Download & Install MinGW-w64 (64-bit)

- **Download MinGW-w64**:  
   - Use this link: [winlibs-x86_64-posix-seh-gcc-14.2.0-llvm-19.1.7-mingw-w64ucrt-12.0.0-r3.7z](https://github.com/brechtsanders/winlibs_mingw/releases/download/14.2.0posix-12.0.0-ucrt-r3/winlibs-x86_64-posix-seh-gcc-14.2.0-llvm-19.1.7-mingw-w64ucrt-12.0.0-r3.7z)
- **Extract the Archive**:  
   - Use 7-Zip or another extraction tool to extract the contents.
   - Move the extracted folder (usually named similar to `mingw64`) to a location of your choice. For example, copy it to `C:\mingw64\`.
- **Add MinGW-w64 to Your PATH**:
   - Open the Start menu, search for “Edit the system environment variables,” and open it.
   - Click on **Environment Variables**.
   - Under **System variables**, find and select **Path**, then click **Edit**.
   - Click **New** and add: `C:\mingw64\bin`
   - Click **OK** to save and close.
- **Verify Installation**:
   - Open a new Command Prompt or PowerShell window and type:
     ```
     g++ -v
     ```
     or
     ```
     g++ --version
     ```
   - The output should show `x86_64-w64-mingw32`, confirming a 64-bit installation.

---

## 4. Install CMake

- **Download CMake**:  
   - Visit [CMake Download](https://cmake.org/download/) and download the Windows installer.
- **Install CMake**:
   - Run the installer and **check the option “Add CMake to system PATH”**.
- **Verify CMake**:
   - Open a new terminal and run:
     ```
     cmake --version
     ```
   - A version string should be displayed.

---

## 5. Download & Prepare GLFW

- **Extract the GLFW Package**  
   - Download the GLFW package (e.g., `glfw-3.4.bin.WIN64.zip`) from the [GLFW Release](https://github.com/glfw/glfw/releases/download/3.4/glfw-3.4.bin.WIN64.zip).
   - Extract the contents of the package.

- **Copy the Library File**  
   - Inside the extracted package, open the folder named (for example) **`lib-mingw-w64`**.
   - Copy the file **`libglfw3.a`** from that folder into your project’s **`lib/`** folder.
     - *(Note: If using dynamic linking, you would also copy `glfw3.dll` to your project root, but this guide uses the static library.)*

- **Copy the Include Folder**  
   - In the extracted package, open the **`include`** folder.
   - Copy the entire **`GLFW`** folder (which contains the header files such as `glfw3.h`) into your project’s **`include/`** folder.

---

## 6. Generate & Prepare GLAD Files

- **Generate GLAD**:  
   - Visit [GLAD Generator](https://glad.dav1d.de/).
   - **Language**: Select `C/C++`.
   - **API**: Choose `gl` and select your desired OpenGL version (e.g., 3.3, 4.5, or 4.6).
        - To check your current OpenGL version on your system, you can install **OpenGL Extensions Viewer**:
        - Download from [CNET](https://download.cnet.com/download/opengl-extensions-viewer/3000-18487_4-34442.html).
        - Install and run the tool; it will list the highest supported OpenGL version for your system.
  - Install and run the tool; it will list the highest supported OpenGL version for your system.
  - Use that version number when generating your GLAD files.
   - **Profile**: Choose `Core`.
   - **Extensions**: Add all extensions you might need (or leave as default).
   - **Loader**: Ensure “Generate loader” is checked.
   - Click **Generate** and then download the resulting zip file.
- **Extract GLAD**:
   - Unzip the downloaded file.
- **Copy Headers**:
   - From the extracted folder, locate the `include/` directory.  
   - You will find **two** folders:
     - `glad/` – containing **glad.h**
     - `KHR/` – containing **khrplatform.h**
   - Copy both the `glad/` and `KHR/` folders into your project’s `include/` folder.
- **Copy GLAD Source**:
   - Locate the `glad.c` file in the extracted source (typically in a `src/` folder).
   - Copy `glad.c` into your project’s `src/` folder.

After this step, your `include/` folder should have this structure:

```
OpenGLProject
├── include
│   ├── glad
│   │   └── glad.h
│   ├── GLFW
│   │   ├── glfw.h
│   │   └── glfw3native.h
│   └── KHR
│       └── khrplatform.h
└── src
    └── glad.c
```

## 7. Create CMakeLists.txt

- In the root of your project (`OpenGLProject/`), create a file named **CMakeLists.txt**
- Open this [CMakeLists.txt file on GitHub](https://github.com/yourusername/yourrepo/blob/main/CMakeLists.txt) and copy its contents into your local `CMakeLists.txt`.
- Adjust any paths if necessary to match your project’s folder structure.

## 8. Create main.cpp

please use this [main.cpp](https://github.com/etsubdink-m/Computer-Graphics-Lab/blob/main/main.cpp). Follow these steps:

- In the `src/` folder of your project (`OpenGLProject/src/`), create a file named `main.cpp`.
- Open the [main.cpp file on GitHub](https://github.com/etsubdink-m/Computer-Graphics-Lab/blob/main/main.cpp) and copy its contents into your local `main.cpp`.

## 9. Create build_cmake.bat (Optional)

please use the [build_cmake.bat](https://github.com/etsubdink-m/Computer-Graphics-Lab/blob/main/build_cmake.bat). Follow these steps:

- In the root of your project (`OpenGLProject/`), create a file named `build_cmake.bat`.
- Open the [build_cmake.bat file on GitHub](https://github.com/etsubdink-m/Computer-Graphics-Lab/blob/main/build_cmake.bat) and copy its contents into your local `build_cmake.bat`.
- Save the file and use it to build your project by double-clicking it or running it from the command line.


## 10. Build & Run the Project

- **Locate the Batch File**  
   - In the root of your project folder (`OpenGLProject/`), you should have a file named `build_cmake.bat`.

- **Execute the Batch File**  
   - **Double-click** `build_cmake.bat` from Windows Explorer, or open a Command Prompt, navigate to your project root, and run:
     ```
     .\build_cmake.bat
     ```

- **What the Batch File Does**  
   - **Creates a Build Directory**:  
     If a `build/` folder does not exist, the script creates one to keep generated files separate from your source files.
   - **Generates Build Files with CMake**:  
     It runs a CMake command (using the "MinGW Makefiles" generator) to process your `CMakeLists.txt` and generate the appropriate Makefiles.
   - **Builds the Project**:  
     The script then calls CMake to build the project. This step compiles your source files (`main.cpp` and `glad.c`) and links them with the GLFW library.
   - **Runs the Executable**:  
     If the build is successful, the batch file automatically runs `Main.exe`.

- **Expected Outcome**  
   - After the build process completes, you should see a new window titled **"Learn OpenGL"**.
   - ![photo_2025-04-03_01-24-27](https://github.com/user-attachments/assets/cab35b85-e9da-45c5-86f5-e4d0787671b6)
     
## 11. Final Directory Structure

After setting everything up, your project folder should look similar to this:

```
OpenGLProject
├── build_cmake.bat
├── CMakeLists.txt
├── include
│   ├── glad
│   │   └── glad.h
│   ├── GLFW
│   │   ├── glfw.h
│   │   └── glfw3native.h
│   └── KHR
│       └── khrplatform.h
├── lib
│   └── libglfw3.a
└── src
    ├── main.cpp
    └── glad.c
```
