# 🖥️ Panduan Antarmuka & Menu HMI PRECISIO v1.0

Antarmuka pengguna (*Human Machine Interface*) menggunakan layar **LCD Karakter 20x4 I2C** dipadukan dengan **Keypad Matriks 4x4**, tombol darurat fisik (*E-Stop*), dan lampu indikator **HV ON**.

---

## 1. Pemetaan Tombol Keypad 4x4

| Tombol | Fungsi Mode Navigasi Menu | Fungsi Mode Input Angka |
| :---: | :--- | :--- |
| **`A`** | Geser Kursor ke Atas / Naik | Hapus satu digit terakhir (*Backspace*) |
| **`B`** | Geser Kursor ke Bawah / Turun | - |
| **`C`** | Kembali (*Cancel* / *Back*) / Batal Siklus | Batalkan pengisian (*Cancel*) |
| **`D`** | Pilih (*Select* / *OK*) / Konfirmasi | Konfirmasi & Simpan Angka (*Enter*) |
| **`1` s.d. `9`** | Pintasan langsung ke item menu ke-*n* | Memasukkan angka `1` s.d. `9` |
| **`0`** | - | Memasukkan angka `0` |
| **`*`** | - | Tanda titik desimal (`.`) |
| **`#`** | - | Tanda minus (`-`) pada digit pertama |

---

## 2. Struktur Menu Utama

Saat alat dinyalakan, layar splash menampilkan:
```
+--------------------+
|   PENYEMAI BENIH   |
|   ELEKTROSTATIK    |
|13 Soket + Penggetar|
|v3.1 IPB University |
+--------------------+
```
Setelah inisialisasi, sistem masuk ke **`== MENU UTAMA ==`**:

```
== MENU UTAMA ==
1. Mulai Penyemaian
2. Parameter Aktif
3. Sekrup Terpasang
4. Param. Penyemai
5. Kalibrasi Stepper
6. Kalib. Elektrostat
7. Penggetar Wadah
8. Pengaturan Sistem
```

---

### Menu 1: Mulai Penyemaian
Menjalankan siklus penyemaian otomatis dari baris awal hingga baris akhir nampan.
- **Konfirmasi Awal:** Menampilkan jenis benih, tegangan HV, pola sekrup aktif, rentang baris, dan total estimasi butir benih.
- **Pengecekan Kuat Medan:** Jika $\frac{\text{kV}}{h_{\text{Ambil}}} > 2.5\text{ kV/mm}$, sistem menampilkan peringatan risiko loncatan bunga api (*spark warning*).
- **Alur Siklus:**
  1. Homing otomatis sumbu Z kemudian sumbu X.
  2. Gantri bergerak ke wadah benih (`xAmbil`) sambil menggetarkan wadah.
  3. Diam sesaat (`tTenang`) agar benih tenang di cekungan.
  4. Sumbu Z turun ke `hAmbil`, HV dinyalakan (`kV`), tahan `tTahan` ms.
  5. Sumbu Z naik ke tinggi aman `hAman`.
  6. Gantri meluncur ke baris nampan $b$.
  7. Sumbu Z turun ke lubang tanam `hTanam`.
  8. HV dimatikan (muatan dibuang oleh bleeder), tahan `tLepas` ms agar benih jatuh sempurna.
  9. Sumbu Z naik kembali ke `hAman` dan berulang ke baris berikutnya.

---

### Menu 2: Parameter Aktif
Layar inspeksi cepat (*Dashboard Ringkasan*) tanpa mengubah nilai:
- **Halaman 1:** Jenis benih, pola sekrup aktif, tinggi ambil & tanam, baris awal & jumlah baris, jarak baris tray.
- **Halaman 2:** Kecepatan X & Z, tegangan HV, waktu tahan & waktu lepas.
- **Halaman 3:** Intensitas getar %, durasi getar ms, jeda tenang ms, opsi getar saat jalan/ambil.
*(Tekan `A` atau `B` untuk ganti halaman, tekan `C` untuk kembali).*

---

### Menu 3: Sekrup Terpasang
Mengonfigurasi catatan status ke-13 soket elektroda:
- `Soket  1` s.d. `Soket 13`: Status `[ADA]` atau `[ - ]`.
- Opsi pintasan cepat:
  - **Semua terpasang:** Mengaktifkan seluruh 13 sekrup (`0x1FFF`).
  - **Kosongkan semua:** Menonaktifkan seluruh sekrup (`0x0000`).
  - **Balik pilihan:** Melakukan inversi bitmask.
  - **Ganjil 1,3,5..13:** Pola selang-seling 7 lubang (`0x1555`).

---

### Menu 4: Parameter Penyemai
Mengatur profil spesifik benih dan dimensi tray:
1. **Jenis Benih:** Beralih antara `Bayam`, `Pakcoy`, dan `Kustom`.
2. **Tinggi Ambil (mm):** Jarak ujung sekrup di atas pelat benih ($0.5 - 30.0\text{ mm}$).
3. **Tinggi Tanam (mm):** Kedalaman sekrup saat tanam ($-8.0 - 30.0\text{ mm}$).
4. **Jarak Baris Tray (mm):** Spasi antar lubang baris ($20.0 - 40.0\text{ mm}$, default $27.0\text{ mm}$).
5. **Kecepatan X (mm/s):** Kecepatan gerak horizontal ($5 - 150\text{ mm/s}$).
6. **Kecepatan Z (mm/s):** Kecepatan gerak vertikal ($2 - 60\text{ mm/s}$).
7. **Baris Awal:** Baris pertama nampan ($1 - 17$).
8. **Jumlah Baris:** Total baris yang disemai ($1 - 17$).

---

### Menu 5: Kalibrasi Stepper
Menu teknisi untuk penyetelan fisik mekanik:
1. **Homing:** Menguji pergerakan homing sensor limit switch Z & X.
2. **Jog Sumbu X / Sumbu Z:** Menggerakkan motor secara manual bertahap ($0.1\text{ mm}$, $1.0\text{ mm}$, atau $10.0\text{ mm}$).
3. **Set X ambil = posisi:** Menyimpan koordinat gantri saat elektroda tepat di atas wadah benih.
4. **Set X baris1 = posisi:** Menyimpan koordinat gantri saat elektroda tepat di atas baris 1 rockwool.
5. **Set h=0 (permukaan):** Menyimpan posisi kontak ujung sekrup dengan permukaan rockwool.
6. **Langkah/mm X:** Kalibrasi rasio langkah sumbu X ($1.0 - 400.0\text{ step/mm}$).
7. **Langkah/mm Z:** Kalibrasi rasio langkah sumbu Z ($1.0 - 400.0\text{ step/mm}$).
8. **Uji ke Baris n:** Menggerakkan manipulator langsung ke baris target untuk verifikasi visual presisi titik tanam.

---

### Menu 6: Kalibrasi Elektrostatis
Penyetelan parameter penarikan benih dengan modul tegangan tinggi:
1. **Tegangan -kV:** Menyetel voltase elektroda ($0.0 - 10.0\text{ kV}$ dengan inkremen $0.5\text{ kV}$).
2. **Waktu Tahan (ms):** Durasi muatan HV aktif sebelum elektroda diangkat ($50 - 5000\text{ ms}$).
3. **Waktu Lepas (ms):** Durasi jeda di lubang tanam setelah HV OFF agar benih jatuh ($50 - 5000\text{ ms}$).
4. **Uji Tegangan Tinggi:** Menyalakan HV statis selama maks 10 detik (dengan peringatan keselamatan).
5. **Uji Ambil di Wadah:** Melakukan 1 siklus simulasi ambil benih agar operator dapat menghitung langsung persentase keberhasilan per elektroda.

---

### Menu 7: Penggetar Wadah
1. **Intensitas Getar %:** Daya motor getar via PWM MOSFET ($0 - 100\%$).
2. **Lama Getar (ms):** Durasi penggetaran sebelum proses ambil ($0 - 10000\text{ ms}$).
3. **Jeda Tenang (ms):** Waktu tunggu setelah getar mati agar benih stabil ($0 - 5000\text{ ms}$).
4. **Saat Jalan:** Menggetarkan wadah sembari gantri meluncur dari nampan ke wadah (menghemat siklus waktu).
5. **Saat Ambil:** Memberikan sentakan getar kecil saat HV menyala agar benih melompat ke elektroda.
6. **Uji Penggetar:** Menguji putaran motor getar secara interaktif dengan penyesuaian $\pm 10\%$.

---

### Menu 8: Pengaturan Sistem
1. **Simpan Parameter:** Menyimpan seluruh konfigurasi aktif ke memori non-volatile EEPROM.
2. **Kembali ke Default:** Reset seluruh parameter ke setelan pabrik bawaan FreeCAD v3.3.
3. **Percepatan X (mm/s²):** Akselerasi motor X ($50 - 3000\text{ mm/s²}$).
4. **Percepatan Z (mm/s²):** Akselerasi motor Z ($20 - 1500\text{ mm/s²}$).
5. **Tinggi Aman (mm):** Ketinggian minimum lintasan saat bergerak bebas ($15.0 - 70.0\text{ mm}$).
6. **Uji Keluaran:** Menguji fungsi relay 24V HV, lampu indikator, dan motor getar selama 1 detik tanpa memicu tegangan tinggi.
7. **Info Sistem:** Menampilkan versi firmware, jumlah soket, dan odometer akumulasi total nampan selesai.
