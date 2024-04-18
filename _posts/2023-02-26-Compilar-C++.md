---
layout: single
title:  C++ - Compilación de archivos
excerpt: "Guía básica para compilar archivos C++ en Windows y Linux."
date: 2023-02-26
classes: wide
header:
    teaser: /assets/images/post thumbnails/4_Compilacion-C++.png
    teaser_home_page: true
    icon: /assets/images/icons/Linux_Windows.png
categories:
    - Windows
    - Linux
tags:
    - guía
---

## Compilador

Tanto para Windows como para Linux vamos a usar los compiladores que provee GCC (GNU Compiler Collection), la diferencia está en el "proveedor" de donde lo obtengamos.

- Windows.

    Vamos a usar la versión que trae el kit de desarrollo [**w64devkit**](https://github.com/skeeto/w64devkit/releases) (a través de MinGW-w64).

    > Descargamos el ZIP que se llame de la forma `w64devkit-<version>.zip`.

- Linux.

    La mayoría de las distribuciones ya traen este paquete, pero si no es tu caso busca el paquete e instálalo (por lo general se encuentra como "gcc").

## Implementación (Windows)

Una vez descargado y descomprimido el **w64devkit**, agregaremos los binarios al `PATH` de Windows:

> Es importante no mover los archivos una vez que hayas realizado el siguiente paso (tampoco los elimines)

1. Buscamos "Editar las variables de entorno del sistema".

2. En el path agregamos la ruta `bin` del **w64devkit**, lo que se terminara viendo como `...\w64devkit\bin`.

> Lo podemos agregar tanto a las variables de un usuario o del sistema en general, pero recomiendo la segunda opción.

> Todo esto también puede realizarse con el comando `setx /m PATH "...\w64devkit\bin;%PATH%"`.

## Corriendo programas

Vamos a hacer uso de la extensión **Code Runner**, con la siguiente configuración (en los `settings.json` de tu **VSC**):

> Si no estás usando **VSC**, a continuación explicaré el comando para que puedas adaptarlo a tus necesidades (también estará su análogo en Linux).

- Windows.

    - Archivos individuales.

        ```JSON
        "code-runner.executorMap": {
                "cpp": "echo Executing... && cd $dir && g++ \"$fileName\" -o main && .\\main.exe && rm .\\main.exe"
            }
        ```

        - `echo Executing...`: muestra un mensaje.

        - `cd $dir`: ir al directorio del archivo que se "ejecutó" (se inició la acción).

        - `g++ \"$fileName\" -o main`: compilar el archivo y nombrar al ejecutable "main".

        - `.\\main.exe` ejecutar el archivo "main".

        - `rm .\\main.exe` borrar el ejecutable.

    - Varios archivos.

        ```JSON
        "code-runner.customCommand": "echo Executing... && cd $dir && g++ (Get-ChildItem -recurse *.cpp) -o main && .\\main.exe && rm .\\main.exe"
        ```

        La única diferencia con el de archivos individuales es como listamos los archivos que se van a compilar, optando por una búsqueda recursiva con el comando `(Get-ChildItem -recurse *.cpp)`.

        > Recuerda tener tus proyectos separados por carpetas para que la búsqueda recursiva se haga ahí (la carpeta asociada con el proyecto) y que estas carpetas no tengan espacios en sus nombres (para que no haya errores en la ejecución de los comandos).

- Linux.

    Los comandos en Linux (específicamente en sus shells) son un tanto diferentes pues, las rutas se representan de una forma diferente (la ruta de mi carpeta "Windows" en PowerShell es `C:\Windows` mientras que en **Bash** es `/c/Windows`) y el comando `Get-ChildItem` es exclusivo de PowerShell, por lo que Code Runner no funcionará correctamente si utilizas un entorno de Linux para ejecutar tus programas desde Windows (como WSL).

    - Archivos individuales.

        ```BASH
        g++ main.cpp -o main && ./main && rm ./main
        ```

        > En mi caso siempre llamo mis archivos "main.cpp", pero si tu archivo tiene un nombre diferente, debes poner el nombre de este.

    - Varios archivos.

        ```BASH
        g++ $(find -name "*.cpp") -o main && ./main && rm ./main
        ```

    En mi caso uso la siguiente función escrita en Bash para compilar y ejecutar mis programas:

    ```BASH
    function run_c {
        echo "Compiling..."

        if [[ $1 == 0 ]]; then
            if [[ -f "$2" ]]; then
                g++ "$2" -o main

                shift 2
            else
                echo "Error: source file not found"
                return 1
            fi
        else
            source_files=$(find . -name "*.cpp")

            if [[ -z "$source_files" ]]; then
                echo "Error: no .cpp files found"
                return 1
            fi

            g++ $source_files -o main

            shift 1
        fi

        echo "Executing..."

        if [ -f "./main" ]; then
            ./main "$@"
            rm ./main
        else
            return 1
        fi
    }
    ```

    1. Modo `0` (`run_c 0 <cpp file> <execution arguments>`).

        - Compila y ejecuta el archivo C++ especificado (`<cpp file>`).

        - Los argumentos adicionales proporcionados se pasan directamente al programa.

    2. Modo `1` (`run_c 1 <execution arguments>`).

        > Realmente este es el modo que se ejecuta cuando el primer argumento no es `"0"`.

        - Realiza la búsqueda automática y compilación de todos los archivos `.cpp` en el directorio actual.

        - Los argumentos adicionales se consideran como argumentos para el programa resultante.

    El código análogo para PowerShell podría ser el siguiente:

    ```PowerShell
    function run_c {
        Write-Host "Compiling..."

        if ($args[0] -eq 0) {
            if (Test-Path $args[1]) {
                & "g++" -Wall -Wextra -Wpedantic -Werror $args[1] -o main
                $args = $args[2..($args.Length - 1)]
            } else {
                Write-Error "Error: source file not found"
                return 1
            }
        } else {
            $sourceFiles = Get-ChildItem -Filter "*.cpp" -Recurse

            if ($sourceFiles.Count -eq 0) {
                Write-Error "Error: no .cpp files found"
                return 1
            }
            & "g++" -Wall -Wextra -Wpedantic -Werror $sourceFiles.FullName -o main
            $execution_args = $args[1..($args.Length - 1)]
        }

        if (Test-Path "./main") {
            Write-Host "Executing..."

            & .\main.exe $execution_args
            Remove-Item .\main.exe
        } else {
            return 1
        }
    }
    ```

> Si estás trabajando en proyectos grandes [**CMake**](https://cmake.org) es la opción recomendada.
