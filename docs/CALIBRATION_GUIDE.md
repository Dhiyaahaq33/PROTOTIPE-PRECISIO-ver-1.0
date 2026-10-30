# ⚙️ Panduan Kalibrasi Lengkap PRECISIO v1.0

Panduan langkah demi langkah untuk teknisi dan operator dalam melakukan kalibrasi mekanik, elektrostatik, dan penggetar pada unit penyemai benih otomatis **PRECISIO v1.0**.

---

## 1. Kalibrasi Sumbu Mekanik (Manipulator Kartesian)

### Langkah 1: Verifikasi Gerak dan Homing
1. Buka **Menu 5 (Kalibrasi Stepper)** -> Pilih **1: Homing**.
2. Amati pergerakan:
   - Sumbu Z wajib bergerak ke **atas** terlebih dahulu hingga menyentuh limit switch Z, lalu mundur 3 mm.
   - Sumbu X wajib bergerak ke **kiri** hingga menyentuh limit switch X, lalu mundur 3 mm.
   *(Jika arah terbalik, ubah parameter pembalik arah di `setup()`: `stZ.setPinsInverted(true, false, false)`).*

### Langkah 2: Menentukan Posisi Wadah Benih (`xAmbil`)
1. Pilih **2: Jog Sumbu X**.
2. Gunakan tombol `1` (0.1 mm), `2` (1.0 mm), atau `3` (10.0 mm) untuk memilih resolusi langkah.
3. Tekan `A` atau `B` hingga barisan 13 sekrup berada tepat konsentris di atas deretan lekukan wadah benih.
4. Tekan `C` untuk keluar ke menu kalibrasi.
5. Pilih **4: Set X ambil=posisi**. Sistem akan menyimpan nilai koordinat tersebut ke parameter `P.xAmbil`.

### Langkah 3: Menentukan Posisi Baris Pertama Nampan (`xBaris1`)
1. Pilih kembali **2: Jog Sumbu X**.
2. Gerakkan gantri hingga barisan 13 sekrup berada tepat konsentris di atas deretan lubang tanam **Baris 1** pada media rockwool yang dijepit di nampan.
3. Tekan `C` untuk keluar.
4. Pilih **5: Set X baris1=posisi**. Sistem akan menyimpan nilai koordinat tersebut ke parameter `P.xBaris1`.

### Langkah 4: Kalibrasi Nol Permukaan (`h = 0 mm`)
1. Pilih **3: Jog Sumbu Z**.
2. Turunkan elektroda secara perlahan hingga ujung sekrup M3 **tepat menyentuh** permukaan atas rockwool (gunakan selembar kertas sebagai feeler gauge tipis).
3. Tekan `C` untuk keluar.
4. Pilih **6: Set h=0 (permukaan)**. Sistem akan mereset acuan tinggi sehingga titik sentuh tersebut menjadi referensi mutlak $h = 0.0\text{ mm}$.

---

## 2. Kalibrasi Elektrostatik (Penarikan Benih)

Mekanisme elektrostatik sangat dipengaruhi oleh massa jenis benih, kadar air, dan bentuk benih.

### A. Rekomendasi Profil Parameter Awal

| Parameter | Benih Bayam (*Spinacia oleracea*) | Benih Pakcoy (*Brassica rapa*) | Satuan |
| :--- | :---: | :---: | :---: |
| **Tegangan Elektroda ($kV$)** | `-6.0` | `-5.0` | $\text{kV}$ |
| **Tinggi Ambil ($h_{\text{Ambil}}$)** | `3.0` | `2.0` | $\text{mm}$ |
| **Tinggi Tanam ($h_{\text{Tanam}}$)** | `2.0` *(atau `0.0` s.d. `-2.0`)* | `1.0` *(atau `0.0` s.d. `-1.0`)* | $\text{mm}$ |
| **Waktu Tahan ($t_{\text{Tahan}}$)** | `500` | `500` | $\text{ms}$ |
| **Waktu Lepas ($t_{\text{Lepas}}$)** | `500` | `500` | $\text{ms}$ |
| **Intensitas Getar** | `60` | `80` | $\%$ |
| **Lama Getar ($t_{\text{Getar}}$)** | `1500` | `1500` | $\text{ms}$ |
| **Jeda Tenang ($t_{\text{Tenang}}$)** | `400` | `300` | $\text{ms}$ |

### B. Prosedur Uji Ambil Benih
1. Masukkan benih ke dalam wadah secukupnya.
2. Buka **Menu 6 (Kalibrasi Elektrostatis)** -> Pilih **5: Uji ambil di wadah**.
3. Sistem akan melakukan satu siklus pengambilan benih dan menahan elektroda di atas wadah selama maksimal 20 detik dengan HV tetap aktif.
4. Periksa visual:
   - **Jika benih tidak menempel:** Naikkan tegangan sebesar $0.5\text{ kV}$ atau turunkan tinggi ambil sebesar $0.5\text{ mm}$.
   - **Jika benih menempel ganda (>1 butir per sekrup):** Turunkan tegangan sebesar $0.5\text{ kV}$ atau perbesar jeda tenang wadah.
5. Tekan sembarang tombol pada keypad untuk melepaskan benih kembali ke wadah.

> [!CAUTION]
> **Batas Kuat Medan Listrik Kritis ($2.5\text{ kV/mm}$):**
> Kuat medan dielektrik udara kering adalah $\approx 3.0\text{ kV/mm}$. Selalu pastikan rasio tegangan terhadap jarak ambil memenuhi:
> $$E = \frac{kV}{h_{\text{Ambil}}} \le 2.5\text{ kV/mm}$$
> Jika rasio ini terlampaui, sistem akan mengeluarkan peringatan potensi terjadinya percikan bunga api (*electrical breakdown/arcing*).

---

## 3. Kalibrasi Penggetar Wadah (*Vibrator Tuning*)

Tujuan penggetar adalah menyebar benih secara merata ke dalam cekungan pelat tanpa melontarkan benih keluar dari wadah:
1. Masuk ke **Menu 7 (Penggetar Wadah)** -> Pilih **6: Uji penggetar**.
2. Motor getar akan berputar. Tekan tombol `A` untuk menaikkan intensitas $+10\%$ atau tombol `B` untuk menurunkan $-10\%$.
3. Amati perilaku benih:
   - Benih harus mengalir lincah ke arah cekungan individual dalam waktu 1.5 detik.
   - Bila benih terlempar keluar wadah, turunkan intensitas getar.
4. Tekan tombol `D` untuk menyimpan nilai intensitas optimal tersebut.
