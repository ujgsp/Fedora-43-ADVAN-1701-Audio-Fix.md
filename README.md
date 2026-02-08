# Fedora 43 Setup & Fixes (ADVAN 1701 & Epson)

Repository ini berisi kumpulan dokumentasi teknis dan panduan untuk mengoptimalkan **Fedora 43**, khususnya untuk laptop **ADVAN 1701** dan perangkat pendukung seperti printer **Epson L3210**.

## 📑 Daftar Isi
1. [Perbaikan Audio ADVAN 1701](#-perbaikan-audio-advan-1701)
2. [Instalasi Driver Epson L3210](#-instalasi-driver-epson-l3210)

---

## 🔊 Perbaikan Audio ADVAN 1701

Masalah ini sering terjadi di mana speaker internal tidak berbunyi setelah headset dicabut, meskipun sistem mendeteksi perangkat audio dengan benar pada laptop ADVAN 1701.

### 📌 Overview
- Speaker laptop tidak mengeluarkan suara sama sekali.
- Headset/Headphone berfungsi normal saat dicolokkan.
- Perangkat audio Stack: PipeWire + WirePlumber.
- Hardware Codec: Realtek ALC269VB.

### 🛠️ Langkah Perbaikan Audio
1. **Install alsa-tools**: `sudo dnf install alsa-tools -y`
2. **Buka hdajackretask**: `sudo hdajackretask`
3. **Advanced Override**:
   - Centang **"Advanced override"**.
   - **Pin ID: 0x14**: Set ke **Internal Speaker**.
   - **Pin ID: 0x21**: Set **Jack detection** ke **"Not present"**.
   - Klik **"Install boot override"** dan **Reboot**.

---

## 🖨️ Instalasi Driver Epson L3210

Panduan untuk menginstal driver Epson L3210 dan mengatasi error verifikasi RPM pada Fedora terbaru.

### 1. Instalasi Driver (Bypass Digest)
Fedora terbaru menolak RPM lama karena masalah "no digest". Gunakan perintah ini untuk memaksa instalasi:
```bash
sudo rpm -ivh --nodigest --nofiledigest ~/Downloads/epson-inkjet-printer-202101w-1.0.2-1.x86_64.rpm
```

### 2. Konfigurasi CUPS & Driver PPD
Agar bisa mencetak grafis (bukan hanya teks mentah), ganti driver printer ke PPD resmi Epson:

1. **Pastikan CUPS Aktif**: `sudo systemctl enable --now cups`
2. **Cari Path Driver**: `lpinfo -m | grep L3210`
3. **Terapkan Driver**:
   ```bash
   sudo lpadmin -p L3210 -m lsb/usr/epson-inkjet-printer-202101w/Epson/Epson-L3210_Series-epson-driver.ppd.gz
   ```
4. **Restart CUPS**: `sudo systemctl restart cups`

### 3. Troubleshooting
Jika printer "Could not start" di LibreOffice, pastikan library pendukung terinstal:
```bash
sudo dnf install libnsl
```

---

## 🖥️ Device Information
- **Laptop**: ADVAN 1701
- **Printer**: Epson L3210 (Ink Tank Series)
- **OS**: Fedora 43 (Workstation)

## 🧩 Status Project
- [x] Perbaikan Audio ADVAN 1701 (Speaker Fixed)
- [x] Instalasi Driver Epson L3210 (Printing Fixed)


