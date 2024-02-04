#!/bin/bash

# Defina o número de threads para o máximo disponível
NUM_THREADS=$(nproc)

# Baixe a versão mais recente do kernel
KERNEL_VERSION=$(curl -sL https://www.kernel.org/ | grep -oP 'stable: <strong>\K[^<]+')
KERNEL_SOURCE_URL="https://www.kernel.org/pub/linux/kernel/v6.x/linux-$KERNEL_VERSION.tar.xz"

# Baixe e extraia o código-fonte do kernel
curl -O $KERNEL_SOURCE_URL
tar -xf "linux-$KERNEL_VERSION.tar.xz"
cd "linux-$KERNEL_VERSION"

# Crie uma configuração padrão e ajuste o nome do pacote
make defconfig
echo 'CONFIG_LOCALVERSION="-biglinux"' >> .config

# Configure o kernel
make menuconfig

# Compile o kernel usando todos os threads disponíveis
make -j$NUM_THREADS

# Instale os módulos do kernel
sudo make modules_install

# Instale o kernel com o nome personalizado sem solicitar senha
echo -e "\nCompilando e instalando o kernel...\n"
echo -e "\n"

# Adicione a linha ao sudoers para evitar a senha
echo "iarlemozaga ALL=(ALL) NOPASSWD: ALL" | sudo tee -a /etc/sudoers

sudo make install

# Remova a linha do sudoers adicionada
sudo sed -i "/iarlemozaga ALL=(ALL) NOPASSWD: ALL/d" /etc/sudoers

# Limpeza
cd ..
rm -rf "linux-$KERNEL_VERSION" "linux-$KERNEL_VERSION.tar.xz"
