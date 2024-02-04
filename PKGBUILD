#!/bin/bash

# Defina sua senha de sudo
SUDO_PASSWORD="sua_senha_sudo"

# Baixe a versão mais recente do kernel
KERNEL_VERSION=$(curl -sL https://www.kernel.org/ | grep -oP 'stable: <strong>\K[^<]+')
KERNEL_SOURCE_URL="https://www.kernel.org/pub/linux/kernel/v6.x/linux-$KERNEL_VERSION.tar.xz"

# Baixe e extraia o código-fonte do kernel
curl -O $KERNEL_SOURCE_URL
tar -xf "linux-$KERNEL_VERSION.tar.xz"
cd "linux-$KERNEL_VERSION"

# Crie uma configuração padrão
make defconfig

# Configure o kernel
make menuconfig

# Compile o kernel
make -j$(nproc --all)

# Instale os módulos do kernel
echo "$SUDO_PASSWORD" | sudo -S make modules_install

# Instale o kernel
echo "$SUDO_PASSWORD" | sudo -S make install

# Crie um pacote usando o PKGBUILD
cd /usr/src/linux-$KERNEL_VERSION
sudo mkdir -p PKGBUILD
cat <<EOL | sudo tee PKGBUILD
pkgname=biglinux
pkgver=$KERNEL_VERSION
pkgrel=1
arch=('x86_64')
url='https://www.kernel.org/'
license=('GPL')
source=("https://www.kernel.org/pub/linux/kernel/v5.x/linux-$KERNEL_VERSION.tar.xz")
sha256sums=('SKIP')

build() {
  cd "\$srcdir/linux-$KERNEL_VERSION"
  make -j\$(nproc) bzImage modules
}

package() {
  cd "\$srcdir/linux-$KERNEL_VERSION"
  make INSTALL_MOD_PATH="\$pkgdir" modules_install
  install -Dm644 System.map "\$pkgdir"/boot/System.map-$KERNEL_VERSION
  install -Dm644 arch/x86/boot/bzImage "\$pkgdir"/boot/vmlinuz-$KERNEL_VERSION
  cp -Rt "\$pkgdir/usr/src/linux-$KERNEL_VERSION" \
    include \
    scripts \
    arch/x86 \
    System.map
}
EOL

# Compile e instale o pacote
echo "$SUDO_PASSWORD" | sudo -S makepkg -si

# Limpeza
cd ..
sudo rm -rf "linux-$KERNEL_VERSION" "linux-$KERNEL_VERSION.tar.xz"
