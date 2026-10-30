# 🏗️ Arsitektur Sistem & Prinsip Kerja PRECISIO v1.0

## 1. Latar Belakang & Inovasi Elektrostatik

Pada mesin penyemai benih konvensional, mekanisme pemungutan benih umumnya mengandalkan **pompa vakum** dengan nosel hisap mikro (*needle suction*). Pendekatan vakum memiliki beberapa kendala serius:
1. Jarum hisap rentan tersumbat oleh debu benih, serbuk kapur, atau kotoran media tanam.
2. Memerlukan pompa hisap kontinu yang bising, bergetar, dan boros daya listrik.
3. Kerap menyedot benih ganda (*double picking*) atau menjatuhkan benih saat nosel tersenggol.

**PRECISIO v1.0** memecahkan masalah ini dengan mengadopsi mekanisme **Gaya Elektrostatik (Coulomb)**:
- Muatan elektrostatik tegangan tinggi negatif (0 s.d. -10 kV) dialirkan ke ujung elektroda sekrup M3.
- Polarisasi muatan terinduksi pada kulit benih menarik benih secara presisi ke ujung elektroda tanpa memerlukan aliran udara hisap.
- Ketika gantri tiba di atas lubang tanam rockwool, pin Shutdown (SDN) dimatikan dan resistor *bleeder* 100 MΩ membuang muatan dalam sekejap, melepaskan benih secara gravitasi jatuh tepat ke sasaran.

---

## 2. Sistem Koordinat & Kinematika Manipulator Kartesian

Sistem bergerak pada 2 sumbu ortogonal (X dan Z):

```
                   [ Limit Switch Home X ]
                             | (X = 0 mm)
                             v
+----------------------------+--------------------------------------------+
|                            |   --> Sumbu X (Gantri Horizontal)          |
|    Wadah Benih Bergetar    |============================================|
|     (X = xAmbil ~8.5 mm)   |             Nampan Rockwool                |
|                            |  Baris 1 (X = xBaris1) ... Baris 17        |
+----------------------------+--------------------------------------------+
                             ^
                             | (Sumbu Z Vertikal)
                     [ Home Z = hHome = 74.0 mm ]
                             |
                      hAman = 20.0 mm
                             |
                     Permukaan = h = 0 mm (Rockwool / Pelat Cekungan)
                             |
                     Masuk Lubang: h = -2.0 mm
```

### A. Sumbu X (Gantri Horizontal)
- **Mekanisme Penggerak:** Timing Belt GT2 (Pitch 2 mm) dengan Pulley 20 Gigi (20T).
- **Rasio Translasi:** $20 \times 2\text{ mm} = 40\text{ mm/putaran}$.
- **Driver Stepper:** A4988 dengan microstepping $1/8$.
- **Resolusi Langkah:**
  $$\text{langkahX} = \frac{200 \text{ step/rev} \times 8}{40 \text{ mm/rev}} = 40.0 \text{ step/mm}$$
- **Rentang Kerja:** $X = 0\text{ mm}$ (Home limit switch) hingga $X_{\max} = 538.0\text{ mm}$.

### B. Sumbu Z (Vertikal)
- **Mekanisme Penggerak:** Rack & Pinion Modul 1 (m=1), Jumlah Gigi Pinion $z=20$.
- **Diameter Pitch Pinion:** $D = m \times z = 1 \times 20 = 20\text{ mm}$.
- **Keliling Putaran:** $C = \pi \times D = 20\pi \approx 62.83185\text{ mm/putaran}$.
- **Driver Stepper:** A4988 dengan microstepping $1/16$.
- **Resolusi Langkah:**
  $$\text{langkahZ} = \frac{200 \times 16}{62.83185} \approx 50.93 \text{ step/mm}$$
- **Sistem Referensi $h$:**
  - $h$ adalah tinggi ujung sekrup terhadap permukaan rockwool / pelat cekungan benih.
  - Saat Z di posisi paling atas (*Home Switch Z*), $h = h_{\text{Home}} = 74.0\text{ mm}$.
  - Nilai $h = 0\text{ mm}$ dikalibrasi saat ujung sekrup tepat menyentuh permukaan media tanam.
  - Nilai $h < 0$ (negatif) menandakan elektroda masuk ke lubang tanam rockwool.

---

## 3. Sistem Elektroda Modular (13 Soket Terpasang)

- Gantri Z memuat **13 soket kuningan berulir M3** dengan jarak antar soket yang disesuaikan dengan lubang tanam rockwool standar.
- Setiap elektroda berupa stud sekrup M3 dengan kenop isolator yang dapat dilepas-pasang dengan tangan secara independen.
- Jika petani/operator ingin menyemai pola renggang (misal ganjil 1, 3, 5... atau hanya 7 lubang), elektroda yang tidak digunakan cukup diputar lepas secara fisik.
- Firmware menyediakan menu **Sekrup Terpasang (Masking)** sebagai pencatat status soket untuk menghitung total benih yang tertanam secara akurat dan mencegah perhitungan palsu.

---

## 4. Mekanisme Penggetar Wadah (*Active Vibration Hopper*)

Untuk mengatasi sifat benih yang mudah menggumpal atau menumpuk:
1. Wadah benih dilengkapi pelat berlekuk (*dimple tray*) dengan motor getar eksentrik 12V DC dan peredam karet silikon.
2. **Fase Getar 1 (Saat Jalan / Persiapan):** Wadah digetarkan dengan PWM variabel untuk meratakan benih agar masing-masing jatuh tepat pada cekungan individual.
3. **Fase Tenang (*Settling Period*):** Motor dimatikan selama $t_{\text{Tenang}}$ (default 300–400 ms) agar benih stabil dan tidak memantul saat elektroda turun mendekat.
4. **Fase Ambil:** Elektroda turun ke $h_{\text{Ambil}}$ (2–3 mm di atas benih), HV dinyalakan, dan benih melompat menempel pada ujung sekrup karena gaya tarik medan listrik.
