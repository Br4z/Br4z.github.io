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
				"cpp": "echo Executing... && cd $dir && g++ \"$fileName\" -o  main && .\\main.exe && rm main.exe"
			}
		```

		- `echo Executing...`: muestra un mensaje.

		- `cd $dir`: ir al directorio del archivo que se "ejecutó" (se inició la acción).

		- `g++ \"$fileName\" -o  main`: compilar el archivo y nombrar al ejecutable "main".

		- `.\\main.exe` ejecutar el archivo "main".

		- `rm main.exe` borrar el ejecutable.

	- Varios archivos.

		```JSON
		"code-runner.customCommand": "echo Executing... && cd $dir && g++ (Get-ChildItem -recurse *.cpp) -o main  && .\\main.exe && rm \"main.exe\""
		```

		La única diferencia con el de archivos individuales es como listamos los archivos que se van a compilar, optando por una búsqueda recursiva con el comando `(Get-ChildItem -recurse *.cpp)`.

		> Recuerda tener tus proyectos separados por carpetas para que la búsqueda recursiva se haga ahí (la carpeta asociada con el proyecto) y que estas carpetas no tengan espacios en sus nombres (para que no haya errores en la ejecución de los comandos).

- Linux.

	Los comandos en Linux (específicamente en sus shells) son un tanto diferentes pues, las rutas se representan de una forma diferente (la ruta de mi carpeta "Windows" en PowerShell es `C:\Windows` mientras que en **Bash** es `/c/Windows`) y el comando `Get-ChildItem` es exclusivo de PowerShell, por lo que Code Runner no funcionará correctamente si utilizas un entorno de Linux para ejecutar tus programas desde Windows (como WSL).

	- Archivos individuales.

		```BASH
		g++ main.cpp -o main && ./main && rm main
		```

		> En mi caso siempre llamo mis archivos "main.cpp", pero si tu archivo tiene un nombre diferente, debes poner el nombre de este.

	- Varios archivos.

		```BASH
		g++ $(find -name "*.cpp") -o main && ./main && rm main
		```

	Recomiendo asociar un alias a cada de uno de estos dos comandos en la Shell que usas. En mi caso (Bash) tengo los siguientes aliases:

	```BASH
	alias run_c='function _run() { echo Executing... && g++ $1 -o main && shift && ./main "$@" && rm ./main; }; _run'
	alias run_c_o='function _run() { echo Executing... && g++ $(find . -name "*.cpp") -o main && ./main "$@" && rm ./main; }; _run'
	```

	El primer alias permite compilar el archivo que se le pase como primer argumento, además de ejecutar el ejecutable con el resto de argumentos que se le pasen. Por ejemplo, al ejecutar `run_c test.cpp 1 2 3`, estaría compilando el archivo llamado `test.cpp` y ejecutando el ejecutable pasándole los argumentos `1`, `2` y `3`. El segundo alias permite compilar varios archivos (los que estén en el directorio donde ejecutes el comando), luego ejecuta el ejecutable con los argumentos que le pases.

> Si estás trabajando en proyectos grandes [**CMake**](https://cmake.org/) es la opción recomendada.
