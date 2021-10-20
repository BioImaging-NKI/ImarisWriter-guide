# How to get the PyImarisWriter to work
The [PyImarisWriter package](https://pypi.org/project/PyImarisWriter/) did not work for us. It raised the error: `Could not find module 'bpImarisWriter96.dll' (or one of its dependencies). Try using the full path with constructor syntax.`. Adding the folder to the PATH variable in windows resolved the error but raised a new one: `HDF version mismatch. Headers are 1.10.6, library is 1.12.1`. Replacing the hdf5.dll in the folder with an the [1.10.6 version](https://www.hdfgroup.org/downloads/hdf5/) did not change the error.

# Windows 10 release
We followed the instructions below and packaged the four .dll files in a `.zip` file. If you're lazy and running Windows 10, download the release on this page.

# Build instructions
The build instructions on the ImarisWriter github were a bit too bare metal for us, so we wrote this more detailed version. Skip to result to just download the binaries. 

Steps:
* Install [visual studio 2019](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&rel=16) with "desktop development with c++"
* Install HDF5 pre-build binaries (Create an account to download `hdf5-1.12.1-Std-win10_64-vs16.zip`)
* Clone (or just download and unzip) the following projects
  * https://github.com/imaris/ImarisWriter
  * https://github.com/madler/zlib (zlib\contrib\vstudio\vc14\zlibvc.sln)
  * https://github.com/lz4/lz4/  (lz4\build\VS2017\lz4.sln)
* Create folders:
  * `C:\lz4\bin` (binary will be copied here)
  * `C:\lz4\include` (header files will be copied here)
  * `C:\lz4\lib` (library file will be copied here)
  * `C:\zlib\bin` (binary will be copied here)
  * `c:\zlib\include` (header files will be copied here)
  * `c:\zlib\lib` (library file will be copied here)
  * `C:\ImarisWriter` (binaries and dependencies will be copied here)
* Add a few paths to your PATH varible
  * cmake: `C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin`
  * dumpbin: `C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\bin\Hostx64\x64`
  * ImarisWriter: `C:\ImarisWriter`

At the ImarisWriter github we see this command to run in the command prompt:
```
mkdir release
cd release
cmake -DHDF5_ROOT:PATH="C:\Program Files\HDF_Group\HDF5\1.12.1" -DZLIB_ROOT:PATH="C:\zlib" -DLZ4_ROOT:PATH="C:\lz4" ..
```
First the command prompt should be at the root of the cloned github ImarisWriter. There you can execute the first two, but the third command will not work yet.
There cmake check three paths for the three dependencies of ImarisWriter. All three paths need to contain:
* `\bin` folder with binaries (.dll) 
* `\include` folder with the header files (.h)
* `\lib` folder with the library file (.lib) 

The header files (\*.h) can be found in the projects themselves. Copy them. 11 for zlib in the main folder, 5 for LZ4, in the `\lib` folder.

You must build the binaries for zlib and lz4 yourself by opening the .sln file in the respective github-projects with visual studio (see above). In visual studio you select `release` and `x64`, right click `zlibvc` or `liblz4-dll` and click `build`. The library file (.lib) and binary file (.dll) are found in the build directory. Copy them to their respective folders.
NOTE: Rename `zlibwapi.lib` to `zlib.libto`

Once
the cmake command runs there will be a .sln file in the release directory. You can open it with VisualStudio and build the ImarisWriter. 
The newly created `bpImarisWriter96.dll` you can copy to the ImarisWriter folder (the one previously added to the PATH variable). It has dependancies for a few .dll files. Dependancies you can find with `dumpbin /dependents bpImarisWriter96.dll`. Find and copy the required .dll's to the ImarisWriter folder. You only need to add the three .dll's from lz4, zlib and hdf5. 

## Result
There should now be four .dll files in `C:\ImarisWriter`, and this path should be added to the %PATH% variable.
The four .dll files are also included as a release in this github.
