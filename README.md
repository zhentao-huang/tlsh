TLSH
=======================================

TLSH is a fuzzy matching library. Given a binary object, it generates a hash 
value. The hash values can be used for similarity comparison. Similary 
objects have similar hash values. Similar hash values signal similar objects.

The output hash is 35 bytes long. The first 3 bytes are used to capture the 
global similarity. The last 32 bytes are used to capture the local similarity.

Here are the design choices.
* To improve comparison accuracy, TLSH tracks counting bucket height 
  distribution in quartiles. Bigger quartile difference results in higher 
  difference score.
* Use specially 6 trigrams to give equal representation of the bytes in the 5 
  byte sliding window which produces improved results.
* Pearson hash is used to distribute the trigram counts to the counting buckets.
* The global similarity score distances objects with significant size 
  difference. Global similarity can be disabled. It also distances objects with 
  different quartile distributions.
* TLSH can be compiled to generate 70 or 134 characters hash strings. The longer
  version is more accurate.

TLSH similarity is expressed as a difference score.
* A score of 0 means the objects are almost identical.
* For the 70 characters hash, a score of 200 or higher means the objects are 
  very different.
  For the 134 characters hash, a score of 400 or higher means the objects are
  very different.

Get Started
=======================================

Obtain TLSH

  wget https://github.com/trendmicro/tlsh/tarball/3.1.1 -O tlsh.tar.gz
  tar xfvz tlsh.tar.gz
  cd trendmicro-tlsh-xxxxxxx (x... is the last git commit ID)
 OR
  git clone git://github.com/trendmicro/tlsh.git
  cd tlsh
  git checkout 3.1.1  

***************************************** NOTE *****************************************
       TLSH depends upon cmake to create the Makefile and then make the project,
	   so the build will fail if cmake is not installed.
****************************************************************************************

A static library will be created under the "lib" directory. Look under the "test" 
directory for example code.

Edit CMakeLists.txt to build TLSH with different options.

  TLSH_BUCKETS - determines using 128 or 256 buckets, more is better
  TLSH_CHECKSUM_1B - determines checksum length, longer means less collision

To build on Linux, Cygwin:

  execute: make.sh 

To build on Visual Studio on Windows

  mkdir build
  cd build
  "c:\program files (x86)\cmake 2.8\bin\cmake" ..
  Open TLSH.sln in Visual Studio
  Build Solution
  Note: tlsh_unittest is not build because it has not been updated for Windows

Python extension

  Prerequisite:
    Install python 2.7 or above. For Windows, install 32 bits version.
    If you have Visual Studio 2010 installed, execute
      SET VS90COMNTOOLS=%VS100COMNTOOLS%
    or with Visual Studio 2012 installed
      SET VS90COMNTOOLS=%VS110COMNTOOLS%

  cd py_ext
  python setup.py build
  python setup.py install (sudo, run as root or administrator)

  import tlsh
  tlsh.hash(data)
  tlsh.diff(h1, h2)
  tlsh.diffxlen(h1, h2)

Create source tarball

  make package_source
  
Changes
=======================================

3.0.0 - Implemented TLSH.
      - Updated to build with CMake.
3.0.1 - Enabled C++ optimization. Runs 4x faster.
3.0.2 - Supports Windows and Visual Studio.
3.0.3 - Added python extension library. TLSH is callable in Python.
      - Stop generating hash if the input is less than 512 bytes.
      - Cleaned up.
3.0.4 - Length difference consideration can be disabled in this version. See
        totalDiff in tlsh.h.
      - TLSH can be compiled to generate the 70 or 134 character hashes. The 
        longer version is more accurate.
3.1.0 - The checksum can be changed from 1 byte to 3 bytes. The collison rate 
        is lower using 3 bytes.
      - If the incoming data has few features. The algorithm will not generate 
        hash value. At least half the buckets must be non-zero.
      - Null or invalid hash strings comparison will return -EINVAL(-22).
      - Python extension library will read CMakeLists.txt to pick the compile
        options.
      - The default build will use half the buckets and 1 byte checksum.
      - New executable tlsh_version reports number of buckets, checksum length.
3.1.1 - Add make.sh and clean.sh scripts for building/cleaning the project
      - Modifications to tlsh_unittest.cpp to write errors to stderr (not stdout)
        and to continue processing in some error cases. Also handle a listfile 
        (-l parameter) which contains both TLSH and filename.
      - Updated expected output files based on changes to tlsh_unittest.cpp

