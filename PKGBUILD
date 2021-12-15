# Maintainer: CODJointOps


case "${_compiler,,}" in
  "clang" | "gcc") _compiler=${_compiler,,} ;; # tolower, simplifes later checks
                *) _compiler=gcc            ;; # default to GCC
esac


## '_O3' -  Enable -O3 optimization - this isn't generally worth much, especially in the face of
##          -march=native (or -march=x86-64-v3) and clang ThinLTO; set _O3 to anything to enable
if [ -n "${_O3+x}" ]; then
  _O3=y
fi

## Disable NUMA since most users do not have multiple processors. Breaks CUDA/NvEnc.
## Archlinux and Xanmod enable it by default.
## Set variable "use_numa" to: n to disable (possibly increase performance)
##                             y to enable  (stock default)
if [ -z ${use_numa+x} ]; then
  use_numa=y
fi



### BUILD OPTIONS
# Set the next two variables to ANYTHING that is not null to enable them

# Tweak kernel options prior to a build via nconfig
_makenconfig=

# Only compile select modules to reduce the number of modules built
#
# To keep track of which modules are needed for your specific system/hardware,
# give module_db a try: https://aur.archlinux.org/packages/modprobed-db
# This PKGBUILD reads the database kept if it exists
# More at this wiki page ---> https://wiki.archlinux.org/index.php/Modprobed-db
_localmodcfg=

# Optionally select a sub architecture by number or leave blank which will
# require user interaction during the build. Note that the generic (default)
# option is 36.
#
#  1. AMD Opteron/Athlon64/Hammer/K8 (MK8)
#  2. AMD Opteron/Athlon64/Hammer/K8 with SSE3 (MK8SSE3) (NEW)
#  3. AMD 61xx/7x50/PhenomX3/X4/II/K10 (MK10) (NEW)
#  4. AMD Barcelona (MBARCELONA) (NEW)
#  5. AMD Bobcat (MBOBCAT) (NEW)
#  6. AMD Jaguar (MJAGUAR) (NEW)
#  7. AMD Bulldozer (MBULLDOZER) (NEW)
#  8. AMD Piledriver (MPILEDRIVER) (NEW)
#  9. AMD Steamroller (MSTEAMROLLER) (NEW)
#  10. AMD Excavator (MEXCAVATOR) (NEW)
#  11. AMD Zen (MZEN) (NEW)
#  12. AMD Zen 2 (MZEN2) (NEW)
#  13. AMD Zen 3 (MZEN3) (NEW)
#  14. Intel P4 / older Netburst based Xeon (MPSC)
#  15. Intel Core 2 (MCORE2)
#  16. Intel Atom (MATOM)
#  17. Intel Nehalem (MNEHALEM) (NEW)
#  18. Intel Westmere (MWESTMERE) (NEW)
#  19. Intel Silvermont (MSILVERMONT) (NEW)
#  20. Intel Goldmont (MGOLDMONT) (NEW)
#  21. Intel Goldmont Plus (MGOLDMONTPLUS) (NEW)
#  22. Intel Sandy Bridge (MSANDYBRIDGE) (NEW)
#  23. Intel Ivy Bridge (MIVYBRIDGE) (NEW)
#  24. Intel Haswell (MHASWELL) (NEW)
#  25. Intel Broadwell (MBROADWELL) (NEW)
#  26. Intel Skylake (MSKYLAKE) (NEW)
#  27. Intel Skylake X (MSKYLAKEX) (NEW)
#  28. Intel Cannon Lake (MCANNONLAKE) (NEW)
#  29. Intel Ice Lake (MICELAKE) (NEW)
#  30. Intel Cascade Lake (MCASCADELAKE) (NEW)
#  31. Intel Cooper Lake (MCOOPERLAKE) (NEW)
#  32. Intel Tiger Lake (MTIGERLAKE) (NEW)
#  33. Intel Sapphire Rapids (MSAPPHIRERAPIDS) (NEW)
#  34. Intel Rocket Lake (MROCKETLAKE) (NEW)
#  35. Intel Alder Lake (MALDERLAKE) (NEW)
#  36. Generic-x86-64 (GENERIC_CPU)
#  37. Generic-x86-64-v2 (GENERIC_CPU2) (NEW)
#  38. Generic-x86-64-v3 (GENERIC_CPU3) (NEW)
#  39. Generic-x86-64-v4 (GENERIC_CPU4) (NEW)
#  40. Intel-Native optimizations autodetected by GCC (MNATIVE_INTEL) (NEW)
#  41. AMD-Native optimizations autodetected by GCC (MNATIVE_AMD) (NEW)
_subarch=


### IMPORTANT: Do no edit below this line unless you know what you're doing
pkgbase=linux-ck-rog
pkgver=5.15.8+hotfix
pkgverion=5.15.8
pkgrel=1
arch=(x86_64)
url="https://wiki.archlinux.org/index.php/Linux-ck"
license=(GPL2)
makedepends=(
  bc kmod libelf pahole cpio perl tar xz
)
if [ "$_compiler" = "clang" ]; then
  pkgver="${pkgver}+clang"
  makedepends+=(clang llvm lld python)
  _LLVM=1
fi
options=('!strip')
_localversion=${pkgver##*\.}

# https://ck-hack.blogspot.com/2021/08/514-and-future-of-muqss-and-ck-once.html
# thankfully xanmod keeps the hrtimer patches up to date
_commit=8ba6612318090567422d49ccc79bc7bbe5484cfc
_xan=linux-5.15.y-xanmod

_gcc_more_v=20211114
source=(
  "https://www.kernel.org/pub/linux/kernel/v5.x/linux-$pkgverion.tar".{xz,sign}
  config         # the main kernel config file
  "more-uarches-$_gcc_more_v.tar.gz::https://github.com/graysky2/kernel_compiler_patch/archive/$_gcc_more_v.tar.gz"
  "xanmod-patches-from-ck-$_commit.tar.gz::https://github.com/xanmod/linux-patches/archive/$_commit.tar.gz"
  0001-ZEN-Add-sysctl-and-CONFIG-to-disallow-unprivileged-C.patch
  0002-PCI-Add-more-NVIDIA-controllers-to-the-MSI-masking-q.patch
  0003-iommu-intel-do-deep-dma-unmapping-to-avoid-kernel-fl.patch
  0004-cpufreq-intel_pstate-ITMT-support-for-overclocked-sy.patch
  0005-Bluetooth-btintel-Fix-bdaddress-comparison-with-garb.patch
  0006-lg-laptop-Recognize-more-models.patch
  sphinx-workaround.patch
  PCI-Add-more-NVIDIA-controllers-to-the-MSI-masking-q.patch
  iommu-intel-do-deep-dma-unmapping-to-avoid-kernel-fl.patch
  cpufreq-intel_pstate-ITMT-support-for-overclocked-sy.patch
  Bluetooth-btintel-Fix-bdaddress-comparison-with-garb.patch
  lg-laptop-Recognize-more-models.patch
  zstd-udpate-fixes.patch
  x86-ACPI-State-Optimize-C3-entry-on-AMD-CPUs.patch
  x86-change-default-to-spec_store_bypass_disable-prct.patch
  acpi-battery-Always-read-fresh-battery-state-on-update.patch
  cfg80211-dont-WARN-if-a-self-managed-device.patch
  HID-asus-Reduce-object-size-by-consolidating-calls.patch
  v16-asus-wmi-Add-support-for-custom-fan-curves.patch
  mt76-mt7921-Add-mt7922-support.patch
  1-2-mt76-mt7915-send-EAPOL-frames-at-lowest-rate.patch
  2-2-mt76-mt7921-send-EAPOL-frames-at-lowest-rate.patch
  mt76-mt7921-enable-VO-tx-aggregation.patch
  1-2-mt76-mt7921-robustify-hardware-initialization-flow.patch
  1-2-Bluetooth-btusb-Add-Mediatek-MT7921-support-for-Foxconn.patch
  2-2-Bluetooth-btusb-Add-Mediatek-MT7921-support-for-IMC-Network.patch
  Bluetooth-btusb-Add-support-for-IMC-Networks-Mediatek-Chip.patch
  Bluetooth-btusb-Add-support-for-Foxconn-Mediatek-Chip.patch
  Bluetooth-btusb-Add-support-for-IMC-Networks-Mediatek-Chip-MT7921.patch
  9001-v5.15.8-s0ix-patch-2021-12-14.patch
)
validpgpkeys=(
  'ABAF11C65A2970B130ABE3C479BE3E4300411886'  # Linus Torvalds
  '647F28654894E3BD457199BE38DBBDC86092693E'  # Greg Kroah-Hartman
)
b2sums=('e487a060254abee0939ed4643db64dc7f2f7bf132946ee0e79ea25c2b0797665545c878b399a62d140472ea3bef416cff996ead417e09f955328db8113d85ccb'
        'SKIP'
        '4b6759fae0abc440292b310aed12bd12bbfbc172f968572ee9b8a9164c844b3f2a12f81b1fdf5ed86d75403f0c5be5d57933292e22c2973ef14f481bf78a3f7e'
        '534091fb5034226d48f18da2114305860e67ee49a1d726b049a240ce61df83e840a9a255e5b8fa9279ec07dd69fb0aea6e2e48962792c2b5367db577a4423d8d'
        'cf589ec357a96b9e573bce298bb1d64fa50339ea047767f2a730a8dc9808e2316b3e7c885d730233ba50d570725d4c72632d1b74a371ef02ac471d4c944fe63e'
        '4e8607f63c08ccb80e27de7af7eda49fb74212f2900d62d0b1dc67100c720ed6f4bd5423d71198a790e92bdc49998d78dd8bbb1cf25f449ada38c00ae1318467'
        '6d52e045fef48f882d8a1dd96aff084e46f28b0bf719f664030f210a5f236b0d8ea3a4151597ccc7a7d404d39a5f965a4bdbe18c9cbaea7687355ec0515908f9'
        '186cbcbe54d3135aadc704eeaba4453f30fbc495262c9f753c53ba25e895c71da25c432fe716795f1ca69199718d3920bda937889f84430c5db2795ae61440d9'
        '716bce9038188e4f49f2ac9fcd399a1f8d1730af4442ff028977522e9d42994f25e22f4d41238d43ab73afc896e7b177a4d581b7f40af36a3e41e3a6ac2afa46'
        'c0f4b5f99c96a482c224043f0b901458dc9bbf5ae87f4456bc3553a608c8222b4ca7bb05bd9c71a674465ba6a09b27fa2762f4a458f6be49793a5a9952910a95'
        '9a0763fee4e54076c721a0d0de53ed152f6ba24381e35d81ba023ce576ed89568be3b5afb56d489609a897971b9db6fa86646ae98be744dbff3b33b35b66cebb'
        'db64b425139c107c69f44624901ae50b5e604d4c9fdfe84f78c298f8ed7a7739033a72ec678c5c3c0e82e59809d97799d0c25f96c64ef5ae79910cb890fc7bfb'
        'd4be09f159c6d0d33b2b4a91da5e04e2815c7091549ff2ca9268c7546fa39af688638a983db434db9a8357be2ff7c95926accf64428664a122b7ede82b782b33'
        '62168cc422c73ddcda8c4b64d5546422d62c14c86f0eefafe53634be9ab231969e79f9bf0cb6161a05130428de90d41a2c2e1d18283294d8b446644b0da57b48'
        '3607b209ffe43085987955131262107706f1900c8b81ab02b21899eedea2ab6eea91897fcd0aa301a66bb6275ffe1d66995f411825881ce695e657c8f6a84b02'
        '41d1cbfe692dad3bca6667d0407c8366fe913fd70ebc7530f73283d0f482a589563a8ef5154e3f88fef9587295a9f70a57eca0246f047d8e2ed7b959bd5c80da'
        'a95008246b7f703dd0f905b694426d3281c365175c96e50d6ac2a0ba0d4177b98d88116529c614d06810c7246d9f101c1ed65c68f03c8b0f1f8cf8a69140d80e'
        '8e9957af42b76720beff4c09d6ec0585b632578e14768b83c064b3cacb42f8ba5dc360da4c88cb1539992bfd37618b1bd599468ab8b1785202740d8ed6603eb7'
        'ba20adaccd4623ad6313880c5c6160894cc784f2c657213262958f5ff783abfa88a5c3c3e8dd5e59d63936d5372c9a0036f5a9b868078bca9a95b1d063948246'
        '1e7604552fb457400d40dc7e578c30940444090d7339020421b4c0f51c5db4c694a5d8791cd7a59464a04a6939b5b295509a4a7442e725caf287768642cab40b'
        '069515160926c57d54fd6ae6b179af03c00e5e87fb85a25c2a7ad0cb05431c9e306dee8f6060f6a002c06a26fa24474ea3eeb14c5cc015caf1f92c637fd9f5cd'
        '006ec152c2864356eb9fd4dc59fd0ab3e4106878d2e82e93b6774eefbbb5dfc27f226113831435faa90f4d2978a542f272cfb972b85f6312705f80e9cfe23d95'
        '381fa9e3b7bc32d255af4e615b989e1518b17d6a626573d7886cd5af3b922da11924e819e6be51c33089015f374d4ee654062d430e37ac9870d96b42f4d1eef7'
        '080619ae5aab57e332623a3ecb895e556ce81a87e75ab114f508e2459dcfaa67cfbed29159878015833fe281abcf6ce6864856788bc37bae5cc946350f085520'
        '5d199b121d77e7ee1f19e0de1d27f5e7be44c574c9b347a408c75bc8dd77be91900c220cc06d639a64f4bc2019f93d156a850322ac0119ead38316d16bc62d9c'
        'bb845ac96c4b1b623fc6953cd466fbb7b6002914a7392dadb6a6e4192821572dbf4cc2f75a59ec2317c7e195571f3bedff077119184038d0a9a3b872c0c6709a'
        '92e15668a718400dc88942460f66d2cfa66b2e56a369929798808cb90b2c95f7d52af875863a2b860f9dbbb198451d0de36b31b1b16f43ad0e0b95cda3bc54e0'
        '4cdbf23b35154929db0a81936b52a4501d935efa87034c1e01aa05ddccd9a8715fe16fcd95378f36f5e641e93059b0a7862b3589b9ad4bb965c55a2471acfd57'
        '406c94d6524b3b16faed8407073da168a201746b2f88ef1bfd45f9e08d5f5762653e793297e09e4eb4b415c6c64a34f41a84c36e213eece54964c40cf3f374ab'
        '2b467266bee080b91340ea7f3aa522c618993951d0d37fb169c09ad840da5d4d828065e664ffc22c0262486d1e965890a61d185bf8f49413daa6bf4f5bc71aac'
        'f1ecd7722a124fe8f7e3f7ffd48a747c2174f55e2ce696439217d0025203f695e332bd15e488a47a59f0cb09dde9eeed07183de07f8049c2f39cfff0b5830cb7'
        '91d2516e4d6f23a095ff8b6b75f91ff86b779a1ccc6e608dfefc7c18bb5c01370e55b81f5df9cd276eccd0dd2a1760c1a73a080a8016f1b260e5d0947ea2f56f'
        '18dd356f02f24c1eaf540ffdbd564c35da119348d597785b0ca73d0cdd6e357615ab169865a0791e5feb9f891d21d03ae945466cfbb8191ea41c1867a8e3914f'
        '3468367be1340f3b6de4272a1b5f6ee1b328e136d28203b9cab698779780ffcf3056d8884f0469da7d58fbe5d3a5bd33474c0e2464a262c718945df3ddc8efee'
        'c885c8ca766a20702544cde78502e594a33b11af03066263aac7209d0889ac5a6dfd19d19775db80f0c17ccefe3457a9da00e74de774774ceaa2fc5711a8f16a')

export KBUILD_BUILD_HOST=archlinux
export KBUILD_BUILD_USER=$pkgbase
export KBUILD_BUILD_TIMESTAMP="$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})"

prepare() {
  cd linux-${pkgverion}

  echo "Setting version..."
  scripts/setlocalversion --save-scmversion
  echo "-$pkgrel" > localversion.10-pkgrel
  echo "${pkgbase#linux}" > localversion.20-pkgname


  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = 0*.patch ]] || continue
    echo "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  echo "Setting config..."
  cp ../config .config
  
  if [[ "$_compiler" == "clang" ]]; then
    msg2 "Applying Clang ThinLTO configuration..."
    scripts/config  --module  CONFIG_LTO \
                    --enable  CONFIG_LTO_CLANG \
                    --enable  CONFIG_HAS_LTO_CLANG \
                    --disable CONFIG_LTO_NONE \
                    --disable CONFIG_LTO_CLANG_FULL \
                    --enable  CONFIG_LTO_CLANG_THIN \
                    --disable CONFIG_INIT_STACK_ALL_PATTERN \
                    --disable CONFIG_INIT_STACK_ALL_ZERO \
                    --disable CONFIG_REGULATOR_DA903X 
                    # this module is incompatible with clang, disable to avoid a warning
  fi

  

  # disable CONFIG_DEBUG_INFO=y at build time otherwise memory usage blows up
  # and can easily overwhelm a system with 32 GB of memory using a tmpfs build
  # partition ... this was introduced by FS#66260, see:
  # https://git.archlinux.org/svntogit/packages.git/commit/trunk?h=packages/linux&id=663b08666b269eeeeaafbafaee07fd03389ac8d7
  scripts/config --enable CONFIG_BPF_LSM
  scripts/config --disable CONFIG_DEBUG_INFO
  scripts/config --disable CONFIG_CGROUP_BPF
  scripts/config --disable CONFIG_BPF_LSM
  scripts/config --disable CONFIG_BPF_PRELOAD
  scripts/config --disable CONFIG_BPF_LIRC_MODE2
  scripts/config --disable CONFIG_BPF_KPROBE_OVERRIDE

  # https://bbs.archlinux.org/viewtopic.php?pid=1824594#p1824594
  scripts/config --enable CONFIG_PSI_DEFAULT_DISABLED

  # https://bbs.archlinux.org/viewtopic.php?pid=1863567#p1863567
  scripts/config --disable CONFIG_LATENCYTOP
  scripts/config --disable CONFIG_SCHED_DEBUG

  # FS#66613
  # https://bugzilla.kernel.org/show_bug.cgi?id=207173#c6
  scripts/config --disable CONFIG_KVM_WERROR
  
  # ck recommends 1000 Hz tick and the hrtimer patches in lieu of ck1
  scripts/config --enable CONFIG_HZ_1000

  # these are ck's htrimer patches
  echo "Patching with ck hrtimer patches..."

  

  for i in ../linux-patches-"$_commit"/"$_xan"/ck-hrtimer/0*.patch; do
    patch -Np1 -i $i
  done

  # non-interactively apply ck1 default options
  # this isn't redundant if we want a clean selection of subarch below
  make LLVM=$_LLVM LLVM_IAS=$_LLVM olddefconfig
  diff -u ../config .config || :

  # https://github.com/graysky2/kernel_gcc_patch
  # make sure to apply after olddefconfig to allow the next section
  echo "Patching to enable GCC optimization for other uarchs..."
  patch -Np1 -i "$srcdir/kernel_compiler_patch-$_gcc_more_v/more-uarches-for-kernel-5.15+.patch"

  if [ -n "$_subarch" ]; then
    # user wants a subarch so apply choice defined above interactively via 'yes'
    yes "$_subarch" | make oldconfig
  else
    # no subarch defined so allow user to pick one
    make oldconfig
  fi

  ### Optionally load needed modules for the make localmodconfig
  # See https://aur.archlinux.org/packages/modprobed-db
    if [ -n "$_localmodcfg" ]; then
      if [ -f $HOME/.config/modprobed.db ]; then
        echo "Running Steven Rostedt's make localmodconfig now"
        make LSMOD=$HOME/.config/modprobed.db localmodconfig
      else
        echo "No modprobed.db data found"
        exit
      fi
    fi

  make -s kernelrelease > version
  echo "Prepared $pkgbase version $(<version)"

  [[ -z "$_makenconfig" ]] || make nconfig

  # save configuration for later reuse
  cat .config > "${startdir}/config.last"

  # uncomment if you want to build with distcc
  ### sed -i '/HAVE_GCC_PLUGINS/d' arch/x86/Kconfig
}

build() {
  cd linux-${pkgverion}
  make LLVM=$_LLVM LLVM_IAS=$_LLVM all
}

_package() {
  pkgdesc="The ${pkgbase/linux/Linux} kernel and modules with ck's hrtimer patches"
  depends=(coreutils kmod initramfs)
  optdepends=('crda: to set the correct wireless channels of your country'
              'linux-firmware: firmware images needed for some devices')
  provides=(VIRTUALBOX-GUEST-MODULES WIREGUARD-MODULE)
  #groups=('ck-generic')

  cd linux-${pkgverion}

  local kernver="$(<version)"
  local modulesdir="$pkgdir/usr/lib/modules/$kernver"

  echo "Installing boot image..."
  # systemd expects to find the kernel here to allow hibernation
  # https://github.com/systemd/systemd/commit/edda44605f06a41fb86b7ab8128dcf99161d2344
  #install -Dm644 "$(make -s image_name)" "$modulesdir/vmlinuz"
  #
  # hard-coded path in case user defined CC=xxx for build which causes errors
  # see this FS https://bugs.archlinux.org/task/64315
  install -Dm644 arch/x86/boot/bzImage "$modulesdir/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "$pkgbase" | install -Dm644 /dev/stdin "$modulesdir/pkgbase"

  echo "Installing modules..."
  #make INSTALL_MOD_PATH="$pkgdir/usr" INSTALL_MOD_STRIP=1 modules_install
  # not needed since not building with CONFIG_DEBUG_INFO=y

  make INSTALL_MOD_PATH="$pkgdir/usr" modules_install

  # remove build and source links
  rm "$modulesdir"/{source,build}
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for ${pkgbase/linux/Linux} kernel"
  depends=("$pkgbase") # added to keep kernel and headers packages matched
  #groups=('ck-generic')

  cd linux-${pkgverion}
  local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  echo "Installing build files..."
  install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
    localversion.* version vmlinux
  install -Dt "$builddir/kernel" -m644 kernel/Makefile
  install -Dt "$builddir/arch/x86" -m644 arch/x86/Makefile
  cp -t "$builddir" -a scripts

  # add objtool for external module building and enabled VALIDATION_STACK option
  install -Dt "$builddir/tools/objtool" tools/objtool/objtool

  # add xfs and shmem for aufs building
  mkdir -p "$builddir"/{fs/xfs,mm}

  echo "Installing headers..."
  cp -t "$builddir" -a include
  cp -t "$builddir/arch/x86" -a arch/x86/include
  install -Dt "$builddir/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
  install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

  # https://bugs.archlinux.org/task/13146
  install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # https://bugs.archlinux.org/task/20402
  install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # https://bugs.archlinux.org/task/71392
  install -Dt "$builddir/drivers/iio/common/hid-sensors" -m644 drivers/iio/common/hid-sensors/*.h

  echo "Installing KConfig files..."
  find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

  echo "Removing unneeded architectures..."
  local arch
  for arch in "$builddir"/arch/*/; do
    [[ $arch = */x86/ ]] && continue
    echo "Removing $(basename "$arch")"
    rm -r "$arch"
  done

  echo "Removing documentation..."
  rm -r "$builddir/Documentation"

  echo "Removing broken symlinks..."
  find -L "$builddir" -type l -printf 'Removing %P\n' -delete

  echo "Removing loose objects..."
  find "$builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

  echo "Stripping build tools..."
  local file
  while read -rd '' file; do
    case "$(file -bi "$file")" in
      application/x-sharedlib\;*)      # Libraries (.so)
        strip -v $STRIP_SHARED "$file" ;;
      application/x-archive\;*)        # Libraries (.a)
        strip -v $STRIP_STATIC "$file" ;;
      application/x-executable\;*)     # Binaries
        strip -v $STRIP_BINARIES "$file" ;;
      application/x-pie-executable\;*) # Relocatable binaries
        strip -v $STRIP_SHARED "$file" ;;
    esac
  done < <(find "$builddir" -type f -perm -u+x ! -name vmlinux -print0)

  #echo "Stripping vmlinux..."
  #strip -v $STRIP_STATIC "$builddir/vmlinux"
  # not needed since not building with CONFIG_DEBUG_INFO=y

  echo "Adding symlink..."
  mkdir -p "$pkgdir/usr/src"
  ln -sr "$builddir" "$pkgdir/usr/src/$pkgbase"

}

pkgname=("$pkgbase" "$pkgbase-headers")
for _p in "${pkgname[@]}"; do
  eval "package_$_p() {
    $(declare -f "_package${_p#$pkgbase}")
    _package${_p#$pkgbase}
  }"
done

# vim:set ts=8 sts=2 sw=2 et:


