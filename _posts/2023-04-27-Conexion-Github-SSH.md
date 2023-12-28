---
layout: single
title:  GitHub - Login por medio de SSH
excerpt: "Aprende a conectarte por medio de SSH a tu cuenta de GitHub para administrar tus repositorios."
date: 2023-04-27
classes: wide
header:
    teaser: /assets/images/post thumbnails/8_Conexion-Github-SSH.png
    teaser_home_page: true
    icon: /assets/images/icons/GitHub.png
categories:
    - GitHub
tags:
    - terminal
    - guía
---

Para conectarte a tu cuenta de GitHub por medio de SSH debes seguir los siguientes pasos:

1. Generar una clave.

	```BASH
	ssh-keygen -t ed25519 -C <email>
	```

	> "ssh-keygen" es una utilidad del paquete "openssh", por lo que si te aparece que el comando no se encontró, probablemente no tengas instalado este paquete en tu sistema.

	> `ed25519` hace referencia al algoritmo de encriptación.

	Al introducir el comando lo siguiente que te preguntará es la ubicación donde se guardará el archivo (por defecto es `/home/<user>/.ssh/`), finalmente te preguntará por una **frase** (que vendría siendo la contraseña) de tu key.

	> Necesitarás esta **frase** (contraseña) más adelante.

	El procedimiento anterior te dejará dos archivos, la clave privada y pública (sin extensión y la `.pub` respectivamente).

2. Agregar la clave a tu terminal y a GitHub.

	1. Terminal.

		`ssh-add <private key path>`.

		> Si te sale el error "Could not open a connection to your authentication agent", probablemente el proceso no este corriendo. Para ejecutar el proceso ingresa el comando `eval "$(ssh-agent -s)"`.

		> Por defecto, la key se agrega automáticamente, pero no está de más asegurarnos.

	2. GitHub.

		1. Settings.

		2. SSH and GPG key.

		3. New SSH.

			1. Pones el título que quieras.

			2. En el tipo escogemos "Authentication key".

			3. En la "key" ponemos nuestra llave pública (el contenido del archivo `.pub`).

		4. Add SSH key.

3. Probamos nuestra conexión.

	1. Nos conectamos con **GitHub**: `ssh -T git@github.com`.

	2. Escribimos "yes" al siguiente mensaje "Are you sure you want to continue connecting (yes/no)?".

	Hasta el momento nos debería aparecer el mensaje "Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access".

	> En caso de no aparecer el mensaje anterior, significa que no se agregó nuestra key correctamente.

	> Recuerda que debes ingresar tu clave cada vez que quieras administrar repositorios.
