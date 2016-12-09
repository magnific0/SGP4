# SGP4
SGP4 C++ code from:

* Vallado, D. A., Crawford, P., Hujsak, R., & Kelso, T. S. (2006). Revisiting Spacetrack Report #3: Rev 2. In AAS/AIAA Astrodynamic Specialist Conference.

The original source code is available at https://celestrak.com/software/vallado-sw.asp.

# Version
There are no version number available, but the latest documented revision is on the September, 15, 2015. File timestamps indicate modifications up to March, 9, 2016. For both dates SGP4.h has been referenced.

This code presents a major revision from previous releases, switching to Visual Studio C++ from Borland C++ and makes use of namespaces to prevent collisions.

# Changes
The aim of the project is to keep the code as close to the original as possible. Only compile errors are fixed, to make it compatible across different platforms and compilers. This means that most of the included test applications will fail at runtime due to hardcoded paths and other issues. In this same spirit a CMakeLists.txt has been provided.
