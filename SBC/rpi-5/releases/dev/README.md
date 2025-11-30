## Radioberry software installation script

This is a developement release of versions of the Radioberry sofware.

Initialy it was a script where you could select the individual software components.

Nowadays people have to choose too much...hi, the whole software stack will be installed.

https://github.com/pa3gsb/Radioberry-2.x/wiki/Radioberry-Software-stack

This avoids possible problems by selecting the wrong set of software components.
 
Installation is easy: 

Open a command window and executing the following commands:


cd /tmp
wget https://raw.githubusercontent.com/pa3gsb/Radioberry-2.x/master/SBC/rpi-5/releases/dev/radioberry_install.sh
sudo chmod +x radioberry_install.sh
./radioberry_install.sh


# 📌 NOTAS ADICIONALES PARA RASPBERRY PI 5  
*(Extensión del README original)*

Este apartado documenta las modificaciones incorporadas en este repositorio para permitir el funcionamiento correcto de **Radioberry en Raspberry Pi 5**, así como los cambios realizados en los últimos commits. Estas adaptaciones son necesarias debido a los cambios en hardware y software introducidos en la RPi 5 y a las recomendaciones oficiales de la comunidad Radioberry.

Las justificaciones técnicas proceden, entre otros, del hilo:  
👉 https://groups.google.com/g/radioberry/c/NgDVT0k-Qdw/m/72fJNVtnAQAJ

---

## 🔧 1. Kernel personalizado con PR #6927 (compatibilidad RPi 5)

La Raspberry Pi 5 incorpora el nuevo SoC **Broadcom bcm2712**, que requiere modificaciones específicas en el kernel para que Radioberry funcione correctamente.  
Por este motivo, el repositorio incluye instrucciones y scripts para:

- Compilar el kernel desde la rama `rpi-6.12.y`  
- Aplicar el **PR #6927** desde el repositorio oficial de Raspberry Pi  
- Instalar el kernel, módulos y DTBs compatibles con la Pi 5 y con Radioberry

Este PR corrige:

- Mapeos MMIO utilizados por Radioberry  
- Enrutado IRQ actualizado para bcm2712  
- Cambios en el subsistema PCIe  
- Ajustes relacionados con temporización y periféricos

Sin este patch, el driver no funciona en Raspberry Pi 5.

---

## 🔧 2. Incorporación del gateware basado en Hermes-Lite2 (PIO)  
**Commit: `a87e8bc` — Add files via upload**

Este commit añade al repositorio un gateware **compilado desde el proyecto Hermes-Lite2**:

🔗 https://github.com/softerhardware/Hermes-Lite2.git  
Ruta original del gateware:  
