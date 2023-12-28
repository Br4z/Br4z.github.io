---
layout: single
title:  Visual Studio Code - Configuración básica
excerpt: "Conoce algunas opciones de configuración para hacer tu experiencia con Visual Studio Code más cómoda."
date: 2023-12-25
classes: wide
header:
    teaser: /assets/images/post thumbnails/11_Configuracion-basica-Visual-Studio-Code.png
    teaser_home_page: true
    icon: /assets/images/icons/Visual_Studio_Code.png
categories:
    - VSC
    - personalización
tags:
    - guía
---

Visual Studio Code es uno de los editores de texto más populares actualmente, esto se debe en gran parte a su fácil uso y gran comunidad (temas, extensiones y tutoriales). En este post quiero mostrarte algunas de las configuraciones que me he encontrado usando el editor  y me han parecido útiles. La configuración la haré a través del `settings.json`, pero siéntete libre de usar la GUI.

## `setting.json`

- `terminal.integrated.enablePersistentSessions`: determina si las sesiones de terminal serán persistentes, lo que "conserva" el estado de la terminal aunque hayas cerrado el editor.

	> `false`.

- `workbench.statusBar.visible`: determina la visibilidad de la barra de estado (la que está en la parte inferior del editor).

	> `false`.

- `workbench.sideBar.location`: determina la localización de la barra lateral.

	> Me gusta tenerla a la derecha (`"right"`) porque no desplaza el contenido del archivo cuando la cierro.

- `workbench.editor.showTabs`: determina la visibilidad de las pestañas (archivos que tengas abiertos).

	> `"single"`, esta opción hace que en la parte superior (donde irían las pestañas), solo se muestre el nombre del archivo que tengo abierto.

- `workbench.startupEditor`: determina lo que se va a mostrar al abrir el editor.

	> `"none"`, lo que me deja en la sesión previa.

- `workbench.list.smoothScrolling`: determina si el desplazamiento suave está activado en la barra lateral.

	> `true`.

- `workbench.tree.enableStickyScroll`: determina si hay "sticky scroll" (los directorios se quedan mientras te desplazas) en el árbol de archivos (el de la barra lateral).

	> `true`.

- `editor.scrollbar.vertical`: determina la visibilidad de la barra vertical de desplazamiento.

	> `"auto"`.

- `editor.cursorBlinking`: determina la animación de parpadeo del cursor.

	> `"expand"`.

- `editor.cursorSmoothCaretAnimation`: determina si el desplazamiento suave del cursor está activado.

	> `true`.

- `editor.linkedEditing`: determina si la edición en etiquetas HTML debería afectar su contraparte (cierre o apertura).

	> `true`.

- `editor.detectIndentation`: determina si la indentación se debe ajustar respecto al archivo.

	> `false` (me gusta usar tabs en vez de espacios).

- `editor.inlayHints.enabled`: determina si las "pistas" están activadas.

	> Lo referente a estas pistas lo podrás encontrar en la GUI de las settings buscando "inlay hints".

	> `"on"`.

- `editor.bracketPairColorization.enabled`: determina si el coloreado de paréntesis, corchetes y llaves está activado. Esta opción hará que sus contrapartes (apertura o cierre) se sean del mismo color.

	> `true`.

- `editor.guides.bracketPairs`: determina el modo del coloreado de las guías de paréntesis, corchetes y llaves.

	> `"active"` (solo colorea la sección en la que tienes el cursor).

- `editor.minimap.enabled`: determina si el mini mapa está activado.

	> `false`.

- `editor.glyphMargin`: determina la visibilidad de la barra de símbolos (la que se encuentra al lado de la enumeración de las líneas).

	> `false`, debuggear no es algo que hago muy a menudo.

- `editor.overviewRulerBorder`: determina la visibilidad de los bordes de la barra vertical de desplazamiento.

	> `false`.

- `editor.hideCursorInOverviewRuler`: determina si la ubicación del cursor respecto a la parte visible del archivo se marca en la barra vertical de desplazamiento.

	> `true`.

- `editor.wordWrap`: determina el modo de ajuste de línea del editor.

	`"off"`, me gusta que las líneas se muestren tan largas como son (excepto en los archivos Markdown).

- `editor.insertSpaces`: determina si se deben insertar espacios en lugar de tabulaciones al presionar la tecla de tabulación.

	> `false`.

- `editor.stickyScroll.enabled`: determina si hay "sticky scroll" (visibilidad de ciertas líneas en la parte superior del editor mientras te desplazas por el código) en la navegación de un archivo.

	> `true`.

- `files.autoSave`: determina el modo de guardado automático de los archivos.

	> `"onFucusChange"`.

- `security.workspace.trust.untrustedFiles`: determina cómo se manejan los archivos no confiables en un espacio de trabajo.

	> `"open"`, los archivos no confiables se abrirán automáticamente en la misma ventana. De forma predeterminada, VS Code te pedirá que confirmes si deseas abrir un archivo no confiable.

- `explorer.confirmDragAndDrop`: determina si se requiere confirmación cuando se arrastran y sueltan archivos en el explorador de archivos.

	> `false`.

- `explorer.confirmDelete`: determina si se requiere confirmación cuando se quieren eliminar archivos.

	> `false`.

- `files.trimTrailingWhitespace`: determina si se eliminan los espacios en blanco al final de las líneas al guardar un archivo.

	> `true`.

- `editor.formatOnPaste`: determina si el contenido pegado se debe formatear.

	> `true`.

> Si quieres consultar mi `settings.json` personal, puedes encontrarlo [aquí](https://gist.github.com/Br4z/618bd822c9277c24bd4e5c63df216e02).

Próximamente, traeré un post dedicado a los shortcuts en este editor. Estos pequeños trucos no solo aceleran tu productividad, sino que también te permiten trabajar de manera más fluida y eficiente.
