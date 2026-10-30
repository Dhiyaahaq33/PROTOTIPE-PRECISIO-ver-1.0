# 🌱 PROTOTIPE PRECISIO v1.0
### Sistem Penyemai Benih Otomatis Berbasis Manipulator Kartesian dengan Mekanisme Elektrostatik pada Media Rockwool

[![Firmware](https://img.shields.io/badge/Firmware-v3.1-blue.svg)](PRECISIO_v1_0/PRECISIO_v1_0.ino)
[![Platform](https://img.shields.io/badge/Platform-Arduino%20Mega%202560-00979C.svg)](https://www.arduino.cc/)
[![Institution](https://img.shields.io/badge/Institution-IPB%20University-1B365D.svg)](https://ipb.ac.id/)
[![Mechanism](https://img.shields.io/badge/Mekanisme-Elektrostatik%2010kV-orange.svg)](#-mekanisme-elektrostatik-inovasi-tanpa-vakum)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-green.svg)](LICENSE)

---

## 📌 Ringkasan Proyek

**PROTOTIPE PRECISIO v1.0** adalah sistem penyemaian benih hidroponik otomatis tingkat lanjut yang dikembangkan oleh civitas akademika **Departemen Teknik Mesin dan Biosistem, Fakultas Teknologi Pertanian, IPB University**.

Sistem ini memadukan **Manipulator Kartesian 2-Sumbu (X-Z)** berpresisi tinggi dengan inovasi **Mekanisme Penarikan Elektrostatik (0 s.d. -10 kV)**. Inovasi ini menggantikan pompa vakum konvensional, sehingga menghasilkan proses penyemaian yang senyap, bebas risiko nosel tersumbat, hemat energi, serta mampu menempatkan benih berbutir kecil (seperti bayam dan pakcoy) satu per satu secara konsisten ke dalam balok media tanam *rockwool*.

---

## ✨ Fitur & Keunggulan Utama

- ⚡ **Mekanisme Elektrostatik Tanpa Pompa Vakum:** Memanfaatkan gaya tarik Coulomb tegangan tinggi (-10 kV) dengan *resistor bleeder* 100 MΩ untuk pelepasan seketika tanpa sisa muatan statis.
- 🔩 **13 Soket Elektroda Modular (Lepas-Pasang):** Menggunakan soket kuningan berulir M3. Elektroda stud M3 dapat dipasang/dilepas secara manual sesuai pola jarak tanam yang diinginkan (didukung fitur software *bitmasking*).
- 📳 **Wadah Benih Aktif (*Vibration Hopper*):** Motor getar DC eksentrik terkontrol PWM IRLZ44N dengan suspensi karet silikon meratakan benih pada pelat cekungan sehingga benih siap diambil satu per satu tanpa menggumpal.
- 📐 **Manipulator Kartesian Kaku 2-Axis:**
  - **Sumbu X:** Timing belt GT2 20T (40 mm/putaran, resolusi 40.0 step/mm).
  - **Sumbu Z:** Rack & Pinion Modul 1 gigi 20 (62.83 mm/putaran, resolusi 50.93 step/mm).
- 🖥️ **Antarmuka Lengkap Tanpa Laptop (*Standalone HMI*):** Layar LCD 20x4 I2C dipadukan dengan Keypad 4x4, tombol Emergency Stop, dan lampu visual HV ON memungkinkan pengaturan, kalibrasi, dan operasional penuh langsung di rumah kaca (*greenhouse*).
- 💾 **Manajemen Profil Multi-Benih & Memori Non-Volatile (EEPROM):** Menyimpan setelan preset khusus untuk benih Bayam, Pakcoy, serta slot Kustom yang tersimpan permanen saat daya mati.

---

## 🏗️ Diagram Sistem & Alur Kerja

```
                           +----------------------------+
                           |  PANEL HMI & MIKROKONTROLER|
                           |     (Arduino Mega 2560)    |
                           +--------------+-------------+
                                          |
                +-------------------------+-------------------------+
                |                         |                         |
                v                         v                         v
     +--------------------+    +--------------------+    +--------------------+
     |   MANIPULATOR X-Z  |    |     MODUL HV DC    |    |   PENGGETAR WADAH  |
     |   - Stepper X (GT2)|    |   - Tegangan: -10kV|    |   - Motor Getar 12V|
     |   - Stepper Z (R&P)|    |   - Relay Cutoff   |    |   - Driver MOSFET  |
     |   - Limit Switch   |    |   - 13 Resistor Bld|    |   - Pelat Cekungan |
     +---------+----------+    +----------+---------+    +--------------------+
               |                          |
               +------------+-------------+
                            v
               +--------------------------+
               |  13 ELEKTRODA SEKRUP M3  |
               | (Ambil Benih & Semai ke  |
               | Media Rockwool 17 Baris) |
               +--------------------------+
```

---

## 🔌 Pemetaan Pin (Pinout Summary)

| Pin Arduino Mega | Jalur / Perangkat | Logika / Fungsi |
| :---: | :--- | :--- |
| **D2** | Limit Switch X | Active LOW (Homing Sumbu X) |
| **D3** | Limit Switch Z | Active LOW (Homing Sumbu Z Atas) |
| **D18** | Tombol E-Stop | Active HIGH (Interupsi Darurat Perangkat Keras) |
| **D4, D5** | Driver Motor X (A4988) | Sinyal STEP & DIR Sumbu X |
| **D6, D7** | Driver Motor Z (A4988) | Sinyal STEP & DIR Sumbu Z |
| **D8** | Driver Motor A4988 | Sinyal ENABLE bersama (Active LOW) |
| **D9** | Modul HV Control | PWM 490 Hz -> RC Low-pass (0–5V setara 0 s.d. -10 kV) |
| **D10** | Modul HV Shutdown | SDN: HIGH = Nyala, LOW = Mati |
| **D11** | Modul Relay 1 Kanal | Pemutus Catu Daya Utama 24V DC Modul HV |
| **D12** | Driver MOSFET Penggetar | PWM Pengatur Intensitas Motor Getar 12V |
| **D13** | Lampu Indikator Panel | Indikator visual "HV ON" (via transistor) |
| **D20, D21** | Modul I2C (SDA, SCL) | Layar LCD Karakter 20x4 (Alamat `0x27`) |
| **D22 - D36** | Keypad Matriks 4x4 | Baris (D22, D24, D26, D28) & Kolom (D30, D32, D34, D36) |

*Rincian diagram skematik lengkap dan pengondisi sinyal tersedia di [docs/PINOUT.md](docs/PINOUT.md).*

---

## 📋 Struktur Menu HMI

1. **`1. Mulai Penyemaian`** : Memulai siklus tanam otomatis dari baris awal ke baris akhir.
2. **`2. Parameter Aktif`** : Dashboard ringkasan 3-halaman status dan setelan saat ini.
3. **`3. Sekrup Terpasang`** : Pengaturan *bitmask* soket 1 s.d. 13 (Semua, Selang-seling, atau Kustom).
4. **`4. Param. Penyemai`** : Pengaturan tinggi ambil/tanam, jarak baris, baris awal/jumlah, kecepatan X/Z.
5. **`5. Kalibrasi Stepper`** : Homing manual, *Jogging* presisi, set koordinat `xAmbil`, `xBaris1`, dan nol media `h=0`.
6. **`6. Kalib. Elektrostat`** : Setel tegangan kV (0–10 kV), waktu tahan/lepas, dan uji sedot benih di wadah.
7. **`7. Penggetar Wadah`** : Setel intensitas PWM, durasi penggetaran, jeda tenang, dan uji getar interaktif.
8. **`8. Pengaturan Sistem`** : Simpan memori EEPROM, reset bawaan pabrik, percepatan akselerasi, tinggi aman, dan uji output.

*Panduan pengoperasian tombol dan menu selengkapnya tersedia di [docs/MENU_HMI_GUIDE.md](docs/MENU_HMI_GUIDE.md).*

---

## 🚀 Panduan Memulai Cepat (Quick Start)

### 1. Kebutuhan Pustaka Arduino IDE
Pastikan pustaka berikut telah terpasang melalui *Library Manager* Arduino IDE:
- **`AccelStepper`** (oleh Mike McCauley)
- **`Keypad`** (oleh Mark Stanley, Alexander Brevig)
- **`LiquidCrystal_I2C`** (oleh Frank de Brabander)
- **`Wire`** & **`EEPROM`** *(Bawaan Core AVR Arduino Mega)*

### 2. Membuka dan Mengunggah Sketch
1. Hubungkan papan **Arduino Mega 2560** ke port USB komputer.
2. Buka berkas sketch resmi:
   📂 [`PRECISIO_v1_0/PRECISIO_v1_0.ino`](PRECISIO_v1_0/PRECISIO_v1_0.ino)
3. Pada Arduino IDE, pilih:
   - **Tools > Board > Arduino Mega or Mega 2560**
   - **Tools > Processor > ATmega2560 (Mega 2560)**
   - **Tools > Port > (Pilih port COM yang sesuai)**
4. Klik tombol **Upload** (Ctrl + U).

### 3. Kalibrasi Awal Sebelum Operasi
Sebelum menjalankan siklus penyemaian pertama:
1. Buka **Menu 5 (Kalibrasi Stepper)** -> Jalankan **Homing**.
2. Lakukan kalibrasi nol permukaan rockwool (`h = 0`) dan titik ambil wadah (`xAmbil`).
3. Buka **Menu 6 (Kalibrasi Elektrostatis)** -> Jalankan **Uji ambil di wadah** untuk memverifikasi daya lekat benih.
*(Lihat panduan terperinci di [docs/CALIBRATION_GUIDE.md](docs/CALIBRATION_GUIDE.md)).*

---

## ⚠️ Peringatan Keselamatan Tegangan Tinggi (High Voltage Safety)

> [!CAUTION]
> - Sistem ini menghasilkan tegangan elektrostatik hingga **-10.000 Volt DC (-10 kV)**.
> - **Dilarang menyentuh** area elektroda, kabel tegangan tinggi, atau wadah benih saat lampu indikator merah **"HV ON"** menyala.
> - Sistem dilengkapi algoritma proteksi otomatis: jika rasio kuat medan $\frac{kV}{h_{\text{Ambil}}} > 2.5\text{ kV/mm}$, sistem akan memblokir siklus dan menampilkan peringatan risiko bunga api (*spark breakdown*).
> - Selalu pastikan tombol **Emergency Stop** dalam jangkauan tangan operator selama proses penyemaian berlangsung.

---

## 📁 Struktur Berkas Repositori

```
PROTOTIPE-PRECISIO-ver-1.0/
├── .gitignore                      # Konfigurasi filter berkas Git
├── LICENSE                         # Lisensi Open Source GNU GPL-3.0
├── README.md                       # Dokumentasi utama repositori
├── docs/                           # Dokumentasi teknis & arsitektur mendalam
│   ├── ARCHITECTURE.md             # Prinsip kerja elektrostatik & kinematika kartesian
│   ├── CALIBRATION_GUIDE.md        # Panduan kalibrasi stepper, tegangan HV & getar
│   ├── MENU_HMI_GUIDE.md           # Panduan lengkap struktur menu LCD 20x4 & Keypad
│   └── PINOUT.md                   # Tabel pemetaan pinout lengkap & pengondisi sinyal
├── hardware/                       # Spesifikasi perangkat keras
│   └── BOM.md                      # Bill of Materials (komponen, aktuator, power supply)
└── PRECISIO_v1_0/                  # Folder resmi Arduino Sketch
    └── PRECISIO_v1_0.ino           # Firmware utama C++/Arduino (bebas error kompilasi)
```

---

## 📜 Lisensi & Atribusi

Proyek ini dilisensikan di bawah **GNU General Public License v3.0 (GPL-3.0)** - lihat berkas [LICENSE](LICENSE) untuk detail ketentuan lisensi.

Dikembangkan sebagai bagian dari penelitian tugas akhir / prototipe inovasi pertanian cerdas di lingkungan **Departemen Teknik Mesin dan Biosistem, Fakultas Teknologi Pertanian, IPB University**.
