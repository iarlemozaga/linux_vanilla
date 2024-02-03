#!/bin/bash

# Verifique se o script é executado como root
if [ "$EUID" -ne 0 ]; then
  echo "Por favor, execute como root"
  exit
fi

# Instale as dependências necessárias
pacman -S --needed base-devel

# Baixe a versão mais recente do kernel
KERNEL_VERSION="latest-stable"
wget "https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-${KERNEL_VERSION}.tar.xz"

# Descompacte o kernel
tar -xf "linux-${KERNEL_VERSION}.tar.xz"
cd "linux-${KERNEL_VERSION}"

# Crie o arquivo de configuração padrão
make defconfig

# Personalize o arquivo de configuração conforme necessário
# Exemplo: habilitar suporte a drivers específicos, sistemas de arquivos, etc.
# make menuconfig

# Compile o kernel
make -j$(nproc)

# Instale os módulos do kernel
make modules_install

# Empacote o kernel com o nome "biglinux"
KERNEL_PACKAGE_NAME="biglinux"
mkdir /boot/${KERNEL_PACKAGE_NAME}

# Copie os arquivos necessários para o diretório do pacote
cp arch/x86_64/boot/bzImage /boot/${KERNEL_PACKAGE_NAME}/vmlinuz-${KERNEL_PACKAGE_NAME}
cp System.map /boot/${KERNEL_PACKAGE_NAME}/System.map-${KERNEL_PACKAGE_NAME}
cp .config /boot/${KERNEL_PACKAGE_NAME}/config-${KERNEL_PACKAGE_NAME}

# Crie o arquivo de initramfs, ajuste conforme necessário
mkinitcpio -k ${KERNEL_PACKAGE_NAME} -g /boot/${KERNEL_PACKAGE_NAME}/initramfs-${KERNEL_PACKAGE_NAME}.img

# Atualize o GRUB (ou o gerenciador de inicialização que você está usando)
grub-mkconfig -o /boot/grub/grub.cfg

# Reinicie o sistema para aplicar as alterações
echo "O kernel foi compilado, empacotado como 'biglinux' e instalado. Reinicie o sistema para usar a nova versão do kernel."
