# Notes of Compilation Using GCC

Basic form:

```bash
g++ solver.C -o solver
```

Use `-D` to define **Macro** (or Variable maybe ?):

```bash
g++ -std=c++11 solver.C \
    -DWM_DP -DWM_LABEL_SIZE_32 \
    -I$FOAM_SRC/OpenFOAM/lnInclude \
    -L$WM_PROJECT_DIR/lib/$WM_OPTIONS/libOpenFOAM.so \
    -o solver
```

* WM_DP: double precision floats
* WM_LABLE_SIZE=32: 32 bit int


## Compile Dynamic Link Library

**1. Two Step**

* Preprocessing + Compilation + Assembly

```bash
g++ -std=c++11 -Wall -O3 -DGHGH -I. -iquote. -fPIC -c myLib.C -o myLib.o
```

* Linking (link to libtest.so)

```bash
g++ -std=c++11 -Wall -O3 -DGHGH -I. -iquote. -fPIC -shared myLib.o -L. -ltest -o libmyLib.so
```

**Notes:**

`-Wall`: show warning message of code itself

`-O3`: make long compilation time bust fast executable

`-iquote`: the directory behind is only searched for ".H"

`-fPIC`: relative directory

`-g`: debugging

**2. One Step**

```bash
g++ -std=c++11 -Wall -O3 -DGHGH -I. -iquote. -fPIC -shared myLib.C -L. -ltest -o libmyLib.so
```


## Compile Execution

**1. Two Step**

* Preprocessing + Compilation + Assembly

```bash
g++ -std=c++11 -Wall -O3 -DGHGH -I. -iquote. -fPIC -c myApp.C -o myApp.o
```

* Linking (link to libtest.so)

```bash
g++ -std=c++11 -Wall -O3 -DGHGH -I. -iquote. -fPIC myApp.o -L. -ltest -Wl,-rpath='.' -o myApp
```

**Notes:**

`-Wl,-rpath='.'`: used to set library link path for executable. Without this option the following command must used to set LD_LIBRARY_PATH

```bash
LD_LIBRARY_PATH=. ./myApp
```

`pthread`: if using multi-thread

**2. One Step**

```bash
g++ -std=c++11 -Wall -O3 -DGHGH -I. -iquote. -fPIC myApp.C -L. -ltest -o -Wl,-rpath='.' -o myApp
```


## Example of Using `wmake`

Dynamic link library (.so)

```bash
    ln: ./lnInclude
Making dependency list for source file testLinkingLib.C
g++ -std=c++11 -m64 -pthread -DOPENFOAM=2106 -DWM_DP -DWM_LABEL_SIZE=32 -Wall -Wextra -Wold-style-cast -Wnon-virtual-dtor -Wno-unused-parameter -Wno-invalid-offsetof -Wno-attributes -Wno-unknown-pragmas  -O3  -DNoRepository -ftemplate-depth-100  -iquote. -IlnInclude -I/home/alex/OpenFOAM/OpenFOAM-v2106/src/OpenFOAM/lnInclude -I/home/alex/OpenFOAM/OpenFOAM-v2106/src/OSspecific/POSIX/lnInclude   -fPIC -c testLinkingLib.C -o Make/linux64GccDPInt32Opt/testLinkingLib.o
g++ -std=c++11 -m64 -pthread -DOPENFOAM=2106 -DWM_DP -DWM_LABEL_SIZE=32 -Wall -Wextra -Wold-style-cast -Wnon-virtual-dtor -Wno-unused-parameter -Wno-invalid-offsetof -Wno-attributes -Wno-unknown-pragmas  -O3  -DNoRepository -ftemplate-depth-100  -iquote. -IlnInclude -I/home/alex/OpenFOAM/OpenFOAM-v2106/src/OpenFOAM/lnInclude -I/home/alex/OpenFOAM/OpenFOAM-v2106/src/OSspecific/POSIX/lnInclude   -fPIC -shared -Xlinker --add-needed -Xlinker --no-as-needed  Make/linux64GccDPInt32Opt/testLinkingLib.o -L/home/alex/OpenFOAM/OpenFOAM-v2106/platforms/linux64GccDPInt32Opt/lib \
      -o /home/alex/OpenFOAM/alex-v2106/platforms/linux64GccDPInt32Opt/lib/libtestLinkingLib.so
```

Execution

```bash
Making dependency list for source file testLinkingApp.C
g++ -std=c++11 -m64 -pthread -DOPENFOAM=2106 -DWM_DP -DWM_LABEL_SIZE=32 -Wall -Wextra -Wold-style-cast -Wnon-virtual-dtor -Wno-unused-parameter -Wno-invalid-offsetof -Wno-attributes -Wno-unknown-pragmas  -O3  -DNoRepository -ftemplate-depth-100 -I/home/alex/OpenFOAM/alex-v2106/src/testLinkingLib/lnInclude -iquote. -IlnInclude -I/home/alex/OpenFOAM/OpenFOAM-v2106/src/OpenFOAM/lnInclude -I/home/alex/OpenFOAM/OpenFOAM-v2106/src/OSspecific/POSIX/lnInclude   -fPIC -c testLinkingApp.C -o Make/linux64GccDPInt32Opt/testLinkingApp.o
g++ -std=c++11 -m64 -pthread -DOPENFOAM=2106 -DWM_DP -DWM_LABEL_SIZE=32 -Wall -Wextra -Wold-style-cast -Wnon-virtual-dtor -Wno-unused-parameter -Wno-invalid-offsetof -Wno-attributes -Wno-unknown-pragmas  -O3  -DNoRepository -ftemplate-depth-100 -I/home/alex/OpenFOAM/alex-v2106/src/testLinkingLib/lnInclude -iquote. -IlnInclude -I/home/alex/OpenFOAM/OpenFOAM-v2106/src/OpenFOAM/lnInclude -I/home/alex/OpenFOAM/OpenFOAM-v2106/src/OSspecific/POSIX/lnInclude   -fPIC -Xlinker --add-needed -Xlinker --no-as-needed  Make/linux64GccDPInt32Opt/testLinkingApp.o -L/home/alex/OpenFOAM/OpenFOAM-v2106/platforms/linux64GccDPInt32Opt/lib \
    -L/home/alex/OpenFOAM/alex-v2106/platforms/linux64GccDPInt32Opt/lib -ltestLinkingLib -lOpenFOAM -ldl  \
     -lm -o /home/alex/OpenFOAM/alex-v2106/platforms/linux64GccDPInt32Opt/bin/testLinkingApp
```


# Using MingGW (Minimalist GNU for Windows) on Windows

* Dynamic Link Library: .dll
* Execution: .exe

Other steps are similar with discussion on Linux.

For example, as for one step compilation:

```bash
g++ -std=c++11 -Wall -O3 -DGHGH -I. -iquote. -fPIC -shared myLib.C -L. -ltest -o libmyLib.dll
g++ -std=c++11 -Wall -O3 -DGHGH -I. -iquote. -fPIC myApp.C -L. -ltest -o -o myApp.exe
```
