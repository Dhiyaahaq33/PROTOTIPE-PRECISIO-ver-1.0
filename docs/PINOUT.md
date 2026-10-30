# 🔌 Tabel Pemetaan Pin (Pinout) PROTOTIPE PRECISIO v1.0

Dokumen ini memuat konfigurasi pin perangkat keras lengkap mikrokontroler **Arduino Mega 2560** untuk sistem penyemai benih otomatis berbasis manipulator kartesian dengan mekanisme elektrostatik.

---

## 1. Ikhtisar Koneksi Pin Mikrokontroler

| Pin Arduino | Tipe I/O | Komponen Terhubung | Tegangan / Logika | Deskripsi Fungsi |
| :---: | :---: | :--- | :---: | :--- |
| **D2** | Input (Pull-up) | Limit Switch Sumbu X | Active LOW (NO ke GND) | Sensor batas awal (*Home*) gantri X |
| **D3** | Input (Pull-up) | Limit Switch Sumbu Z | Active LOW (NO ke GND) | Sensor batas atas (*Home*) sumbu Z |
| **D18** | Input (Interrupt) | Tombol Emergency Stop (*E-Stop*) | Active HIGH (NC ke GND) | Interupsi darurat pemutus seketika seluruh aktuator |
| **D4** | Output | Driver Motor Stepper X (A4988) | Pulsa STEP (5V) | Sinyal pulsa langkah motor gantri X |
| **D5** | Output | Driver Motor Stepper X (A4988) | Logika DIR (5V) | Sinyal arah putaran motor gantri X |
| **D6** | Output | Driver Motor Stepper Z (A4988) | Pulsa STEP (5V) | Sinyal pulsa langkah motor vertikal Z |
| **D7** | Output | Driver Motor Stepper Z (A4988) | Logika DIR (5V) | Sinyal arah putaran motor vertikal Z |
| **D8** | Output | Driver A4988 (X & Z) | Active LOW (5V) | Pin ENABLE bersama kedua driver motor |
| **D9** | Output (PWM) | Modul High Voltage (HV) | PWM 490 Hz -> RC Filter (0–5V) | Kontrol analog tegangan tinggi (0 s.d. -10 kV) |
| **D10** | Output | Modul High Voltage (HV) | Active HIGH (5V) | Pin Shutdown (SDN): LOW = Mati, HIGH = Aktif |
| **D11** | Output | Modul Relay 1 Kanal | Active LOW (5V) | Sakelar pemutus daya 24V DC input modul HV |
| **D12** | Output (PWM) | Driver MOSFET IRLZ44N | PWM (0–5V) | Pengatur kecepatan motor getar wadah benih (12V DC) |
| **D13** | Output | Driver Transistor Indikator | Logika HIGH (5V) | Lampu peringatan visual "HV ON" di panel operator |
| **D20** | I2C (SDA) | Modul LCD 20x4 I2C (PCF8574) | Serial Data (5V) | Komunikasi antarmuka layar HMI |
| **D21** | I2C (SCL) | Modul LCD 20x4 I2C (PCF8574) | Serial Clock (5V) | Komunikasi antarmuka layar HMI |

---

## 2. Antarmuka Keypad Matriks 4x4

Keypad matriks 4x4 dihubungkan langsung ke header digital Arduino Mega (tanpa resistor eksternal, memanfaatkan internal pull-up pustaka `Keypad.h`):

| Jalur Keypad | Pin Arduino Mega | Konfigurasi Baris / Kolom |
| :---: | :---: | :--- |
| **Row 1** | **D22** | Baris 1: `['1', '2', '3', 'A']` |
| **Row 2** | **D24** | Baris 2: `['4', '5', '6', 'B']` |
| **Row 3** | **D26** | Baris 3: `['7', '8', '9', 'C']` |
| **Row 4** | **D28** | Baris 4: `['*', '0', '#', 'D']` |
| **Col 1** | **D30** | Kolom 1 |
| **Col 2** | **D32** | Kolom 2 |
| **Col 3** | **D34** | Kolom 3 |
| **Col 4** | **D36** | Kolom 4 |

---

## 3. Rangkaian Pengondisi Sinyal Khusus

### A. Rangkaian Low-Pass RC Filter (Pin D9 -> Modul HV)
Sinyal PWM frekuensi standar Arduino Mega (490 Hz) dari pin D9 dihaluskan menjadi tegangan analog murni DC (0–5 V) sebelum masuk ke terminal kontrol modul HV:
- **Resistor Seri:** 10 kΩ (1/4 W)
- **Kapasitor Shunt:** 10 µF elektrolit / keramik (rating tegangan min. 16V ke GND)
- **Konversi:** $V_{ctrl} = 0\text{V} \rightarrow 0\text{ kV}$, $V_{ctrl} = 5\text{V} \rightarrow -10\text{ kV}$

### B. Pengaman Bertingkat Modul Tegangan Tinggi (HV)
Untuk memastikan keamanan operasional terhadap tegangan hingga -10 kV:
1. **Tingkat 1 (Relay Masukan 24V - Pin D11):** Memutus suplai daya utama modul tegangan tinggi. Modul tidak bertegangan saat dalam keadaan siaga atau darurat.
2. **Tingkat 2 (Pin Shutdown SDN - Pin D10):** Mematikan osilator tegangan tinggi internal modul.
3. **Tingkat 3 (Resistor Bleeder 100 MΩ):** Terpasang paralel pada tiap elektroda untuk segera membuang sisa muatan statis dalam hitungan milidetik saat SDN LOW.
4. **Tingkat 4 (Hardware E-Stop Interrupt - Pin D18):** Sakelar jamur darurat memutus seketika relay dan sinyal kontrol melalui rutinitas `isrEstop()`.

### C. Sirkuit Driver Motor Penggetar (Pin D12)
- Menggunakan logic-level N-Channel MOSFET **IRLZ44N**.
- Gate dihubungkan ke Pin D12 melalui resistor seri 220 Ω, dengan pull-down resistor 10 kΩ ke GND.
- Dioda flyback (1N4007 / UF4007) dipasang paralel terbalik melintasi terminal motor getar 12V untuk meredam tegangan induktif balik (*back-EMF*).
