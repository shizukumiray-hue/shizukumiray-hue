# Build Instructions

## Masalah Saat Ini

Build gagal karena libbox.aar yang ada tidak compatible dengan source code versi 1.14.0-rc.1.

Error utama: `Unresolved reference` untuk berbagai API libbox seperti:
- BridgeOptions
- NeighborEntry  
- goVersion
- PlatformUser
- ShellSession
- dll

## Solusi

### Opsi 1: Build Libbox dari Source (Recommended)

Anda perlu environment dengan Android SDK + NDK lengkap:

```bash
# Clone sing-box yang match
git clone https://github.com/SagerNet/sing-box.git
cd sing-box
git checkout v1.14.0-rc.1

# Set environment
export ANDROID_HOME=/path/to/android-sdk
export ANDROID_NDK_HOME=/path/to/android-sdk/ndk/26.1.10909125

# Build libbox.aar
make lib_android

# Copy hasil ke Android project
cp libbox/build/outputs/aar/*.aar ../sing-box-for-android/app/libs/
```

### Opsi 2: Downgrade Android App Source

Gunakan versi source code yang lebih lama yang compatible dengan libbox.aar yang sudah ada:

```bash
cd sing-box-for-android
git checkout v1.13.9  # atau versi stabil lainnya
# Lalu build dengan workflow yang sudah ada
```

### Opsi 3: Gunakan Docker

```bash
# Gunakan Docker image dengan Android SDK lengkap
docker run --rm -v $(pwd):/work -w /work \
  androidsdk/android-31 \
  sh -c "cd sing-box && make lib_android"
```

## Cara Build Setelah Libbox Fixed

Setelah punya libbox.aar yang benar:

1. Copy ke `app/libs/`:
   ```bash
   cp libbox.aar sing-box-for-android/app/libs/
   cp libbox.aar sing-box-for-android/app/libs/libbox-legacy.aar
   ```

2. Commit dan push:
   ```bash
   git add app/libs/*.aar
   git commit -m "Add compatible libbox.aar"
   git push
   ```

3. GitHub Actions akan otomatis build APK

## Download APK

Setelah build berhasil:
1. Buka https://github.com/shizukumiray-hue/shizukumiray-hue/actions
2. Pilih workflow run yang sukses
3. Download artifact "apk-output" atau "sing-box-apks"

## Catatan

- Versi current: sing-box v1.14.0-rc.1, Android app v1.14.0-rc.1
- Libbox yang ada: built from commit cc79da2a (newer dev branch)
- Source Android: commit e49285c (1.14.0-rc.1 release)
- **Mismatch ini yang menyebabkan compile error**
