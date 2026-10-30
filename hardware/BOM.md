# 📦 Bill of Materials (BOM) & Spesifikasi Perangkat Keras

Daftar komponen, sensor, aktuator, dan komponen mekanik yang digunakan pada **PROTOTIPE PRECISIO v1.0**.

---

## 1. Komponen Elektronika & Kontrol

| No | Nama Komponen | Spesifikasi / Model | Jumlah | Keterangan / Fungsi |
| :---: | :--- | :--- | :---: | :--- |
| 1 | **Mikrokontroler** | Arduino Mega 2560 R3 (ATmega2560, 16 MHz) | 1 unit | Otak pemrosesan utama, memuat pustaka AccelStepper |
| 2 | **Driver Stepper** | A4988 Stepper Motor Driver Module | 2 unit | Penggerak motor sumbu X ($1/8$ step) & Z ($1/16$ step) |
| 3 | **Modul Tegangan Tinggi** | High Voltage DC Power Supply (Output: $0$ s.d. $-10\text{ kV}$, Input: $24\text{ V DC}$) | 1 unit | Pembangkit muatan elektrostatik penarik benih |
| 4 | **Modul Relay** | Modul Relay 1 Kanal 5V (Active LOW) | 1 unit | Sakelar pemutus daya $24\text{ V}$ input modul HV |
| 5 | **Driver MOSFET** | N-Channel Power MOSFET IRLZ44N (Logic Level) | 1 unit | Pengatur PWM motor penggetar wadah benih ($12\text{ V}$) |
| 6 | **Dioda Proteksi** | 1N4007 / UF4007 (Fast Recovery) | 1 unit | Dioda flyback pelindung induksi balik motor getar |
| 7 | **Resistor Bleeder** | Resistor High Voltage $100\text{ M}\Omega$ ($1/2\text{ W}$) | 13 unit | Pembuang muatan statis residu pada tiap elektroda sekrup |
| 8 | **Filter Low-Pass** | Resistor $10\text{ k}\Omega$ + Kapasitor Elektrolit $10\ \mu\text{F}$ | 1 set | Pengubah sinyal PWM D9 menjadi tegangan DC analog ($0-5\text{ V}$) |
| 9 | **Layar LCD HMI** | LCD Karakter 20x4 Biru/Kuning + Backpack I2C (PCF8574, $0\text{x}27$) | 1 unit | Layar informasi menu dan status penyemaian |
| 10 | **Keypad Input** | Keypad Membran / Matriks 4x4 | 1 unit | Tombol navigasi dan input nilai numerik |
| 11 | **Tombol Darurat** | Emergency Stop Mushroom Push Button (NC Switch) | 1 unit | Pemutus darurat via interupsi perangkat keras Pin D18 |
| 12 | **Lampu Indikator** | Lampu LED Panel 12V/24V + Transistor Driver (2N2222) | 1 unit | Lampu visual peringatan aktif "HV ON" |
| 13 | **Sensor Batas** | Micro Limit Switch Mekanik (Roller Lever NO/NC) | 2 unit | Sensor homing sumbu X dan Z |

---

## 2. Aktuator & Mekanik

| No | Nama Komponen | Spesifikasi / Model | Jumlah | Keterangan / Fungsi |
| :---: | :--- | :--- | :---: | :--- |
| 1 | **Motor Stepper X** | NEMA 17 Stepper Motor ($1.8^\circ$, Arus $\approx 1.5\text{ A}$) | 1 unit | Penggerak translasi gantri horizontal X |
| 2 | **Motor Stepper Z** | NEMA 17 Stepper Motor ($1.8^\circ$, Torsi Tinggi) | 1 unit | Penggerak translasi vertikal Z |
| 3 | **Motor Penggetar** | Mini Vibration DC Motor $12\text{ V}$ Eksentrik | 1 unit | Pengaduk dan perata benih pada pelat cekungan |
| 4 | **Sistem Sabuk X** | Timing Belt GT2 (Pitch $2\text{ mm}$, Lebar $6\text{ mm}$) + Pulley 20T | 1 set | Translasi $40\text{ mm/putaran}$ |
| 5 | **Sistem Rack Z** | Rack & Pinion Modul 1 ($m=1$, Pinion $z=20$) | 1 set | Translasi $62.83\text{ mm/putaran}$ vertikal |
| 6 | **Elektroda Sekrup** | Soket Kuningan Berulir M3 + Stud Sekrup M3 dengan Kenop | 13 set | 13 soket lepas-pasang penyalur tegangan elektrostatik |
| 7 | **Wadah Benih** | Pelat Akrilik/Aluminium dengan 13 Cekungan Sesuai Matriks | 1 unit | Wadah penampung benih hidroponik |
| 8 | **Rangka Mesin** | Profil Aluminium V-Slot 2020 & Plat Penghubung | 1 set | Konstruksi rangka kaku manipulator kartesian |
| 9 | **Penjepit Nampan** | Penjepit Media Rockwool Adjustable (Sumbu X-Y) | 1 set | Pemegang balok rockwool 17 baris selama proses semai |

---

## 3. Catu Daya (Power Supply)

| Jalur Tegangan | Sumber Catu Daya | Penggunaan Arus | Sasaran Beban |
| :---: | :--- | :---: | :--- |
| **5V DC** | Regulator Step-down / USB Arduino | $\approx 500\text{ mA}$ | Logika Arduino Mega, LCD 20x4, Driver A4988 VDD |
| **12V DC** | Switching Power Supply 12V 5A | $\approx 3.0\text{ A}$ | Daya motor stepper VMOT, motor getar, lampu LED panel |
| **24V DC** | Switching Power Supply 24V 2A | $\approx 1.0\text{ A}$ | Daya input modul High Voltage (-10 kV) |
