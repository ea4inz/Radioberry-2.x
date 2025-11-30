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
```
Hermes-Lite2/gateware/variants/radioberry_pio_cl016
```

### ¿Por qué esta variante?

- El gateware **PIO** proporciona temporización más estable que GPIO.  
- El nuevo SoC bcm2712 introduce latencias y cambios en los offsets de GPIO.  
- Radioberry necesita precisión estricta para su interfaz de reloj y señal.  
- La comunidad Radioberry (incluyendo autores originales) recomienda este enfoque.  
- La variante `radioberry_pio_cl016` está probada y estable con Raspberry Pi 5.

Este gateware reemplaza a las versiones anteriores basadas en GPIO.

---

## 🔧 3. Actualización del instalador: GPIO → PIO  
**Commit: `3550f24` — Update radioberry_install.sh**

El script de instalación ahora utiliza:

```
rpi-5/device_driver/pio-mode/driver
```

en lugar de:

```
rpi-5/device_driver/gpio-mode/driver
```

### Razones del cambio

- El modo GPIO no es fiable en Raspberry Pi 5 debido a cambios en bcm2712.
- El modo PIO ofrece:
  - Señal más estable  
  - Menos jitter  
  - Mejor sincronización ADC/DAC  
  - Compatibilidad total con el gateware Hermes-Lite2  
  - Mejor rendimiento SDR en RPi 5  

Este ajuste asegura coherencia entre gateware, driver y entorno de ejecución.

---

## 🔧 4. Integración específica para Raspberry Pi OS Bookworm (64 bits)

Las modificaciones se han adaptado al nuevo entorno de RPi OS:

- Uso de `/boot/firmware` como ruta correcta en Bookworm  
- Compatibilidad obligatoria con **aarch64**  
- Actualización del layout de DTBs (bcm2712)  
- Estructura correcta para cargar overlays en RPi 5  

---

## 📌 5. Justificación técnica (resumen del debate oficial)

Del mensaje clave en el grupo Radioberry:

> Raspberry Pi 5 necesita cambios específicos en el kernel para permitir que Radioberry acceda correctamente a los registros y sistemas IRQ.  
> El PR #6927 es necesario.  
> Además, las implementaciones basadas en GPIO dejan de ser válidas en RPi 5, por lo que debe usarse la variante PIO.

Esto respalda todos los cambios introducidos en este repositorio.

---

## 🧪 6. Estado actual

- ✔ Gateware PIO funcional con RPi 5  
- ✔ Driver Radioberry (PIO mode) carga correctamente  
- ✔ Instalador actualizado  
- ❗ A falta de pruebas intensivas en TX/RX continuas  
- ⏳ A la espera de que Raspberry Pi integre el PR #6927 en el kernel estable  
- ➕ Pendiente de pruebas con diferentes fuentes de reloj y configuraciones HPSDR

---

## 🙌 7. Créditos

Agradecimientos especiales a:

- **PA3GSB (Guus)** — desarrollador del proyecto Radioberry, por su trabajo continuo, sus aportaciones en el grupo oficial y su guía técnica para adaptar Radioberry a Raspberry Pi 5.  
- La comunidad Radioberry por las pruebas, la retroalimentación y el desarrollo colaborativo.  
- Los desarrolladores de **Hermes-Lite2**, cuya variante de gateware PIO ha hecho posible esta adaptación para Pi 5.  
- Usuarios y testers que han compartido sus experiencias, problemas y soluciones en los foros y listas de correo.

Su dedicación me ha permitido adaptar Radioberry a la Raspberry Pi 5 con éxito y documentarlo en este repositorio.
