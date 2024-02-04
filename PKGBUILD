#!/bin/bash

# Defina o número de threads para o máximo disponível
NUM_THREADS=$(nproc)

# Baixe a versão mais recente do kernel
KERNEL_VERSION=$(curl -sL https://www.kernel.org/ | grep -oP 'stable: <strong>\K[^<]+')
KERNEL_SOURCE_URL="https://www.kernel.org/pub/linux/kernel/v5.x/linux-$KERNEL_VERSION.tar.xz"

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

# Instale o kernel com o nome personalizado
sudo make install

# Crie um pacote usando o PKGBUILD
cd /usr/src/linux-$KERNEL_VERSION

# Verifique se o PKGBUILD existe
if [ -f PKGBUILD ]; then
  sudo mkdir -p PKGBUILD
  cat <<EOL | sudo tee PKGBUILD
pkgname=biglinux
pkgver=$KERNEL_VERSION
pkgrel=1
arch=('x86_64')
url='https://www.kernel.org/'
license=('GPL')
source=("https://www.kernel.org/pub/linux/kernel/v6.x/linux-$KERNEL_VERSION.tar.xz")
sha256sums=('SKIP')

build() {
  cd "\$srcdir/linux-$KERNEL_VERSION"
  make -j$NUM_THREADS bzImage modules
}

package() {
  cd "\$srcdir/linux-$KERNEL_VERSION"
  make INSTALL_MOD_PATH="\$pkgdir" modules_install
  install -Dm644 System.map "\$pkgdir"/boot/System.map-$KERNEL_VERSION
  install -Dm644 arch/x86/boot/bzImage "\$pkgdir"/boot/vmlinuz-$KERNEL_VERSION-biglinux
  cp -Rt "\$pkgdir/usr/src/linux-$KERNEL_VERSION" \
    include \
    scripts \
    arch/x86 \
    System.map
}
EOL

  # Compile e instale o pacote
  sudo makepkg -si

  # Limpeza
  sudo rm -rf /usr/src/linux-$KERNEL_VERSION
else
  echo "Erro: O PKGBUILD não foi gerado corretamente."
fi
