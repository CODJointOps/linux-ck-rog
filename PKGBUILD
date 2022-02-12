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
pkgver=5.16.9
pkgverion=5.16.9
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
options=('!strip' '!ccache')
_localversion=${pkgver##*\.}

# https://ck-hack.blogspot.com/2021/08/514-and-future-of-muqss-and-ck-once.html
# thankfully xanmod keeps the hrtimer patches up to date
_commit=6b08df20f31708099a7fbccf5448958b4836118f
_xan=linux-5.16.y-xanmod

_gcc_more_v=20211114
source=(
  "https://www.kernel.org/pub/linux/kernel/v5.x/linux-$pkgverion.tar".{xz,sign}
  config         # the main kernel config file
  "more-uarches-$_gcc_more_v.tar.gz::https://github.com/graysky2/kernel_compiler_patch/archive/$_gcc_more_v.tar.gz"
  "xanmod-patches-from-ck-$_commit.tar.gz::https://github.com/xanmod/linux-patches/archive/$_commit.tar.gz"
  Bluetooth-btintel-Fix-bdaddress-comparison-with-garb.patch
  acpi-battery-Always-read-fresh-battery-state-on-update.patch
  cfg80211-dont-WARN-if-a-self-managed-device.patch
  HID-asus-Reduce-object-size-by-consolidating-calls.patch
  v16-asus-wmi-Add-support-for-custom-fan-curves.patch
  mt76-mt7921-enable-VO-tx-aggregation.patch
  1-2-Bluetooth-btusb-Add-Mediatek-MT7921-support-for-Foxconn.patch
  2-2-Bluetooth-btusb-Add-Mediatek-MT7921-support-for-IMC-Network.patch
  Bluetooth-btusb-Add-support-for-IMC-Networks-Mediatek-Chip.patch
  Bluetooth-btusb-Add-support-for-Foxconn-Mediatek-Chip.patch
  Bluetooth-btusb-Add-support-for-IMC-Networks-Mediatek-Chip-MT7921.patch
  9001-v5.16.9-s0ix-patch-2022-02-10.patch
  v2-drm-amdgpu-Use-correct-VIEWPORT_DIMENSION-for-DCN2.patch
  mt76-mt7921e-fix-possible-probe-failure-after-reboot.patch
  0001-ZEN-Add-sysctl-and-CONFIG-to-disallow-unprivileged-C.patch
  Bluetooth-fix-deadlock-for-RFCOMM-sk-state-change.patch
  udp-ipv6-optimisations-v2-net-next.patch
  Bluetooth-Read-codec-capabilities-only-if-supported.patch
  CONFIG_RCU_FAST_NO_HZ-removal-for-v5.17.patch
  Parallel-boot-v4-on-5.16.5.patch
  af_unix-Replace-unix_table_lock-with-per-hash-locks.patch
  implement-threaded-console-printing.patch
  xor-enable-auto-vectorization-in-Clang.patch
  iwlwifi-fix-use-after-free.patch
  Revert-XANMOD-fair-Remove-all-energy-efficiency-functions.patch
  cpufreq-CPPC-Fix-performance-frequency-conversion.patch
  btrfs-fix-autodefrag-on-5.16.9.patch
)
validpgpkeys=(
  'ABAF11C65A2970B130ABE3C479BE3E4300411886'  # Linus Torvalds
  '647F28654894E3BD457199BE38DBBDC86092693E'  # Greg Kroah-Hartman
)
b2sums=('ce4544512021eebfb8d7c87e176ae1e4ba741021c60484369af5280c51c64df48553ab2d7b53de2c3e358ea9bfbb8545a1dc7ef694628668bfcab329c59fb0fc'
        'SKIP'
        '3c802735a8e9628ce9b3cd7f6bde305efe1110a80658f1d1e664c3d71b086e36ef4389f1bcf6ea51b29426b63c589c6216ae6ef143292fcae2360078b988c9b1'
        '534091fb5034226d48f18da2114305860e67ee49a1d726b049a240ce61df83e840a9a255e5b8fa9279ec07dd69fb0aea6e2e48962792c2b5367db577a4423d8d'
        '7e12da62ddc8535b044f57447e15b550dc2d1421bba4fc830dfad7b328b01f21190f63c5534b9af6a8c09f56bfb9c21014b07645569a6c7b93b950aca07ade5a'
        '6e7dcac4bd0e05f6e7ae43f08f9757b5ed8893c91f81502f8cc6ef70cc8c53ca59d2c196806bdfb797d94369a75e397aa1eea4a6d48fdfaaf6ac6c49dfed02fa'
        '069515160926c57d54fd6ae6b179af03c00e5e87fb85a25c2a7ad0cb05431c9e306dee8f6060f6a002c06a26fa24474ea3eeb14c5cc015caf1f92c637fd9f5cd'
        '006ec152c2864356eb9fd4dc59fd0ab3e4106878d2e82e93b6774eefbbb5dfc27f226113831435faa90f4d2978a542f272cfb972b85f6312705f80e9cfe23d95'
        '381fa9e3b7bc32d255af4e615b989e1518b17d6a626573d7886cd5af3b922da11924e819e6be51c33089015f374d4ee654062d430e37ac9870d96b42f4d1eef7'
        '080619ae5aab57e332623a3ecb895e556ce81a87e75ab114f508e2459dcfaa67cfbed29159878015833fe281abcf6ce6864856788bc37bae5cc946350f085520'
        '4cdbf23b35154929db0a81936b52a4501d935efa87034c1e01aa05ddccd9a8715fe16fcd95378f36f5e641e93059b0a7862b3589b9ad4bb965c55a2471acfd57'
        '2b467266bee080b91340ea7f3aa522c618993951d0d37fb169c09ad840da5d4d828065e664ffc22c0262486d1e965890a61d185bf8f49413daa6bf4f5bc71aac'
        'f1ecd7722a124fe8f7e3f7ffd48a747c2174f55e2ce696439217d0025203f695e332bd15e488a47a59f0cb09dde9eeed07183de07f8049c2f39cfff0b5830cb7'
        '91d2516e4d6f23a095ff8b6b75f91ff86b779a1ccc6e608dfefc7c18bb5c01370e55b81f5df9cd276eccd0dd2a1760c1a73a080a8016f1b260e5d0947ea2f56f'
        '18dd356f02f24c1eaf540ffdbd564c35da119348d597785b0ca73d0cdd6e357615ab169865a0791e5feb9f891d21d03ae945466cfbb8191ea41c1867a8e3914f'
        '3468367be1340f3b6de4272a1b5f6ee1b328e136d28203b9cab698779780ffcf3056d8884f0469da7d58fbe5d3a5bd33474c0e2464a262c718945df3ddc8efee'
        '6a132c13619b032d7d3df94262f5f4b39127c37d0c9c786e3e669e2e74488b51d9ebd0418e2dff9b0243f5eff5532431a4e4363d684f11756ab2c475074a4510'
        '89991cbda31929159c51cca208dd36647f543fc927b8253a7b2d40fca167b5618f633e22a0ea5339df926707bc91e5eb225cb015453e0fcddd59330981449247'
        'd9b22e7b552c7e8b072b7a113d970080c11255d164ddaf494241c789e1632316a431d2ec04a7a27e8c4383a6455c0d5a01eabe0ebe90a9bfd90ed5e764b4dbb4'
        '3a5138cc28ad21dd1dbe867bc90f89bc85fc4f8a778af431be04eb392e3b8b0dc2b42936a2b6e3cfc37735f5c0843e0cea7be4749dec26a2a24d6b79ef834cd4'
        '2423427b9bca27eb8ac4cb9238900a7b713563c91ac680658597e68e80a99766a9f98d0e747c9ed8dc3ef9206d5b1fd0392a2c1cf43264c7caf031019bddfee9'
        '306d93a7618af0c1b2e0b57ea0a9913ce1540b45c3d04b50bd0d15071ace5c87f62bd8a2680e8ebcb8e6f43d945f17948fb178613617185310e6a426e290f019'
        '65215033f21491e37b8ce7dc2153b2e3f2443a1763c39ae987f073b32a40a9452d8cf9a45bf5c17cb5ce0b68666fb55af670295f8c0832d03bff2f6ca199f7fa'
        '26997f243c478553568b9ece5cbfbb9abb36fa76cb85065c7916dbe1102b9a0fc2a4b1d06db3152715d1b5fc1e91fcaa72a5bfd0af5708f7d204fc995607d823'
        '27d65c6b06aee5c53791b47885f475a283d0d2057142032e67ec929b90f80d0b3332298ba405358c98d3587c5ae03f619e016342ad9fa0580c65a66055a65044'
        'd40b39b8ed925f932d7a6b30a8c32f36ec7ac41b302850085c42a29a3c5d8a2daa1d7e09fba06b2546a89f216becd0adca58482289a748daa5d5ab801564ca65'
        '2700d09f376dfab9fe6800003b3edce623faab668ddbe8371cf59415fe7463b0567de1d30e47c836f6b3e2dbd80865c736d90f8fcc40956c55bdb9eb079a61be'
        'cb66d5f4f496ee6dd936eb95c23f3501728e77807c84f2b59fb0bcb17352f940490bf4e8a987a0d26c2edf6b55e7ecbafed8d00bf29c63eca4e810cd98c005ad'
        'aba418e9344bda838523202a11afb731f142caa9d1b4179e6f29158ec5e11f0e7c48c93085b5e17354788a86b0c1aa79f687c89413eecc8fd6af4e2174afa6eb'
        'b3576ecd6b9dffed700354568fad38a1c1ea4dc3352b2452b273ecf3a647a1be9edb25b4dc9a259977d76fc0153c044453198dac2c3b69a8b7e9c0276b2fed09'
        'c5e971beeaeda64d70bf0411968853531b66d0f38d69c9d8eb8e675af43a9df531231b8fa1c2335a60045b8f5e1dae0724634e1a08e4baa665e6d8caa935d663'
        'd3660ba49a4d33bcd56cd4604d3a564022ba735e9ec4fe804ed0d9ededdae8460be6a63a6d02e28917f7c95c4383cd11b45adfee9b5ef416dd67b70e7878eda3')

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
  #scripts/config --disable CONFIG_BPF_LSM
  #scripts/config --disable CONFIG_BPF_PRELOAD
  #scripts/config --disable CONFIG_BPF_LIRC_MODE2
  #scripts/config --disable CONFIG_BPF_KPROBE_OVERRIDE
  scripts/config --module CONFIG_VFIO
  scripts/config --module CONFIG_VFIO_VIRQFD
  scripts/config --disable CONFIG_VFIO_NOIOMMU
  scripts/config --module CONFIG_VFIO_PCI
  
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

  #5.16 Update

  #for i in ../linux-patches-"$_commit"/"$_xan"/ck-hrtimer/0*.patch; do
  #  patch -Np1 -i $i
  #done

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


