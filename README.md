# Makefiles
Philipp Schilk 

# c_generic
A generic C makefile.
Features:   
    - Multiple source/header locations   
    - Automatic source and header discovery    
    - Automatic compilation-dependency management (using compatible compiler)  
    - Out-of-source build    

# stm32
An outline for an STM32 makefile.
The compilation settings *will* have to be adjusted depending 
on the specific part used.

Features:
    - Multiple source/header locations
    - Optional automatic source file discovery
    - Automatic compilation-dependency management (using compatible compiler)  
    - Out-of-source build   
    - Separate release/debug builds  
    
The easiest workflow for setting up a project is the following:
    - Create a *makefile* project using CubeMX
    - Adapt this makefile to reflect the project-specific settings from CubeMX's makefile, including:
        - Needed Source/ASM files
        - Include directories
        - MCU specific compilation flags
        - Optimization flags
        - Pre-processor defines 
        - Linker script name 
