#!/usr/bin/env bash
set -e

# Opcional: pequeñas comprobaciones de entorno
if [ "$(uname -m)" != "aarch64" ]; then
  echo "Este script está pensado para Raspberry Pi OS 64 bits (aarch64)."
  exit 1
fi

echo "Clonando kernel de Raspberry Pi (rama rpi-6.12.y)..."
git clone --branch rpi-6.12.y https://github.com/raspberrypi/linux.git
cd linux

echo "Trayendo el PR 6927 y cambiando a esa rama..."
git fetch origin pull/6927/head:pr-6927
git checkout pr-6927

echo "Instalando dependencias de compilación..."
sudo apt update
sudo apt install -y flex bison bc libssl-dev libncurses5-dev \
    make libc6-dev dwarves

echo "Usando la configuración del kernel actual si está disponible..."
cp /boot/config-$(uname -r) .config 2>/dev/null \
  || cp /boot/firmware/config-$(uname -r) .config 2>/dev/null \
  || make bcm2712_defconfig

echo "Actualizando configuración a las nuevas opciones..."
make olddefconfig

echo "Compilando kernel, módulos y DTBs (esto puede tardar)..."
make -j"$(nproc)" Image.gz modules dtbs

echo "Instalando módulos..."
sudo make modules_install

# IMPORTANTE: copia de seguridad del kernel actual
echo "Haciendo copia de seguridad del kernel actual..."
sudo cp /boot/firmware/Image.gz /boot/firmware/Image.gz.bak-$(date +%F-%H%M%S) 2>/dev/null || true

echo "Instalando el nuevo kernel..."
sudo cp arch/arm64/boot/Image.gz /boot/firmware/Image.gz

# Opcional: instalar también DTBs y overlays (recomendable si el PR toca device tree)
# echo "Instalando DTBs y overlays..."
# sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/firmware/
# sudo cp arch/arm64/boot/dts/overlays/*.dtb* /boot/firmware/overlays/
# sudo cp arch/arm64/boot/dts/overlays/README /boot/firmware/overlays/

echo "Terminado. Reinicia la Raspberry Pi para usar el nuevo kernel."
