# Fedora 43 ADVAN 1701 Audio Fix

Repository ini berisi dokumentasi teknis untuk memperbaiki masalah audio pada laptop **ADVAN 1701** yang menjalankan **Fedora 43**. Masalah ini sering terjadi di mana speaker internal tidak berbunyi setelah headset dicabut, meskipun sistem mendeteksi perangkat audio dengan benar.

## 📌 Overview
Masalah yang diatasi dalam panduan ini meliputi:
- Speaker laptop tidak mengeluarkan suara sama sekali.
- Headset/Headphone berfungsi normal saat dicolokkan.
- Masalah muncul/menetap setelah headset dicabut (status speaker tetap "unavailable").
- Layanan PipeWire dan WirePlumber terlihat berjalan normal di latar belakang.

## 🧠 Root Cause (Ringkas)
Penyebab utama masalah ini bukan pada perangkat lunak audio (seperti PipeWire), melainkan pada tingkat **ALSA / Kernel**:
- **Sticky Jack Detection**: Sensor fisik atau driver pada chip audio mendeteksi bahwa headphone "masih terpasang" padahal sudah dicabut.
- **ALC269VB Quirk**: Chip Realtek ALC269VB pada laptop ini memerlukan konfigurasi pin khusus agar bisa melakukan *switching* output secara otomatis.
- **Regression**: Perubahan pada kernel atau firmware di Fedora 43 terkadang menyebabkan konfigurasi amplifier internal (EAPD) tidak tersetel dengan benar saat booting.

## 🧪 Gejala Umum
- Perintah `speaker-test -t wav -c 2` berjalan tanpa error, tapi tidak ada suara terdengar.
- Di pengaturan suara (GNOME Settings), port speaker tertulis **"(unavailable)"**.
- Audio hanya mau keluar ketika headset terdeteksi terpasang.
- Aplikasi seperti Discord, Firefox, atau Spotify terlihat memutar lagu tapi senyap di speaker.

## 🛠️ Solusi (Langkah demi Langkah)

### 1. Verifikasi Audio Device
Pastikan layanan audio aktif dengan perintah:
```bash
wpctl status
```
Jika device **Built-in Audio Analog Stereo** terlihat, namun speaker mati, lanjut ke langkah berikutnya.

### 2. Gunakan `hdajackretask` (Advanced Override)
Ini adalah langkah paling ampuh untuk memaksa sistem mengabaikan sensor jack yang bermasalah.

1. **Install alsa-tools**:
   ```bash
   sudo dnf install alsa-tools -y
   ```
2. **Buka Aplikasi**:
   ```bash
   sudo hdajackretask
   ```
3. **Konfigurasi Retasking**:
   - Pastikan Codec yang terpilih adalah **Realtek ALC269VB**.
   - Di sisi kanan (**Options**), centang **"Advanced override"**.
   - Cari **Pin ID: 0x14** (Internal Speaker), centang **Override**, dan pastikan pilih **Internal Speaker**.
   - Cari **Pin ID: 0x21** (Black Headphone), centang **Override**, lalu pada baris **Jack detection** pilih **"Not present"**.
   - Klik **"Install boot override"** di pojok kanan bawah.
4. **Simpan & Reboot**:
   Simpan pengaturan dan lakukan **Reboot** laptop Anda.

### 3. Matikan Auto-Mute (Opsional)
Jika setelah reboot masih belum keluar, coba matikan mode Auto-Mute via terminal:
```bash
amixer -c 0 sset 'Auto-Mute Mode' Disabled
```

## ⚠️ Catatan Penting
- **Workaround**: Ini adalah solusi sementara (workaround), bukan perbaikan permanen dari pengembang kernel (upstream).
- **Hati-hati**: Mengubah pin audio secara sembarangan bisa menyebabkan hardware tidak terbaca. Ikuti ID Pin (0x14 & 0x21) dengan teliti.
- **Backup**: Selalu disarankan melakukan backup data penting Anda sebelum melakukan perubahan sistem yang mendalam.

## 🖥️ Device Information
- **Laptop**: ADVAN 1701
- **OS**: Fedora 43 (Workstation Edition)
- **Desktop**: GNOME 46+
- **Audio Stack**: PipeWire + WirePlumber
- **Codec Hardware**: Realtek ALC269VB

## 📚 Referensi
- **Fedora Discussion**: Diskusi komunitas mengenai masalah audio ALC269.
- **Reddit r/Fedora**: Laporan bug audio pada Fedora 42/43.
- **ALSA Documentation**: Dokumentasi mengenai HDA jack retasking.

## 🧩 Status
- [x] Speaker berfungsi kembali normal
- [x] Headset tetap berfungsi sebagaimana mestinya (manual switch jika perlu)


