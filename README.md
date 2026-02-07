# Panduan Perbaikan Speaker Laptop ADVAN 1701 (Fedora 40/41/42/43)

Jika speaker internal laptop ADVAN 1701 Anda tidak berbunyi (tapi headphone berfungsi) dan di pengaturan suara muncul keterangan **"unavailable"**, ikuti langkah-langkah di bawah ini.

## Masalah Dasar
Chip audio **Realtek ALC269VB** pada laptop ini sering mengalami masalah deteksi jack (phantom jack detection). Sistem mengira headphone selalu terpasang, sehingga mematikan speaker internal secara permanen.

## Solusi Menggunakan `hdajackretask`

### 1. Persiapan
Pasang paket yang diperlukan jika belum ada:
```bash
sudo dnf install alsa-tools -y
```

### 2. Konfigurasi Lanjut
Buka aplikasi retask dengan hak akses root:
```bash
sudo hdajackretask
```

Di dalam jendela aplikasi, lakukan langkah berikut:
1. Pilih Codec: **Realtek ALC269VB**.
2. Di bagian kanan (**Options**), centang kotak **"Advanced override"**.
3. Cari **Pin ID: 0x14** (Internal Speaker):
   - Centang **Override**.
   - Pastikan terpilih **Internal Speaker**.
4. Cari **Pin ID: 0x21** (Black Headphone):
   - Centang **Override**.
   - Di baris **Jack detection**, ubah menjadi **Not present**.
5. Klik tombol **"Install boot override"** di pojok kanan bawah.
6. Masukkan password jika diminta.

### 3. Aktivasi
Lakukan **Reboot** (Mulai Ulang) laptop Anda.

---

## Tips Tambahan (Jika dibutuhkan)
Jika suara masih belum keluar, pastikan "Auto-Mute" dimatikan melalui terminal:
```bash
amixer -c 0 sset 'Auto-Mute Mode' Disabled
```

## Referensi Hardware
- **Manufacturer**: ADVAN
- **Model**: 1701
- **Codec**: Realtek ALC269VB
- **Subsystem ID**: 0x1e507007
