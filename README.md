# Millions of Realistic passwords

```markdown
<div align="center">
  <img src="https://img.shields.io/badge/Status-Production_Ready-brightgreen?style=for-the-badge" alt="Status">
  <img src="https://img.shields.io/badge/Python-3.8%2B-blue?style=for-the-badge&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Purpose-Academic_Research-informational?style=for-the-badge" alt="Purpose">
  <img src="https://img.shields.io/badge/License-Educational_Only-orange?style=for-the-badge" alt="License">
  
  <h1>🛡️ Realistic Human Password Generator (RHPG)</h1>
  
  <p>
    <strong>High-Scalable Synthetic Dataset Generator</strong><br>
    Membangun dataset password realistis berbasis perilaku kognitif manusia untuk keperluan riset keamanan siber.
  </p>
</div>

---

## 📖 Daftar Isi
- [📍 Latar Belakang](#-latar-belakang)
- [🧠 Mengapa "Realistis"?](#-mengapa-realistis)
- [⚙️ Arsitektur & Fitur Unggulan](#-arsitektur--fitur-unggulan)
- [🔬 15 Pola Perilaku Manusia](#-15-pola-perilaku-manusia)
- [🚀 Instalasi & Penggunaan](#-instalasi--penggunaan)
- [📊 Performa & Benchmark](#-performa--benchmark)
- [📚 Landasan Akademis](#-landasan-akademis)
- [⚠️ Disclaimer](#-disclaimer)

---

## 📍 Latar Belakang

Menguji kekuatan algoritma *password cracking* (seperti Hashcat atau John the Ripper) membutuhkan dataset yang merepresentasikan bagaimana manusia membangun password. 

Generator acak standar (`random_string()`) menghasilkan entropi tinggi seperti `xK9$mPq2`, yang **tidak pernah** digunakan oleh pengguna biasa. RHPG hadir untuk menjembatani kesenjangan ini dengan memodelkan **bias kognitif** manusia, menghasilkan miliaran password sintetis yang memiliki distribusi statistik mendekati basis data kebocoran dunia nyata (seperti *RockYou* atau *Breached* databases).

---

## 🧠 Mengapa "Realistis"?

Password yang dibuat manusia tidak acak. Mereka mengikuti heuristik mental tertentu untuk mengingatnya. RHPG mensimulasikan 3 pilar utama kebiasaan manusia:

1. **Ketergantungan Kamus (*Dictionary Dependency*)**
   Manusia memilih kata yang familiar: `dragon`, `cinta`, `sunshine`.
2. **Transformasi Prediktabel (*Mangling*)**
   Untuk memenuhi syarat keamanan, manusia melakukan modifikasi minimal:
   - *Capitalization*: `Dragon`
   - *Leet Speak*: `dr4g0n`
   - *Appending*: `dragon123`, `dragon!`
3. **Konteks Personal (*Contextual Anchoring*)**
   Penggunaan informasi yang mudah diingat: Nama (`john`), Tahun (`1998`), atau pola keyboard (`qwerty`).

> **Note:** Generator ini dirancang untuk menghasilkan dataset murni sintetis. Tidak ada password asli yang dicuri atau di-hardcode di dalamnya.

---

## ⚙️ Arsitektur & Fitur Unggulan

RHPG dibangun dengan arsitektur *streaming pipeline* yang mampu menangani skala miliaran record tanpa *Out of Memory* (OOM).

```text
┌──────────────┐     ┌─────────────────┐     ┌───────────────────┐     ┌──────────────┐
│  RNG Engine  │────▶│ Pattern Selector│────▶│  Bloom Filter     │────▶│  Disk I/O    │
│ (Weighted)   │     │ (15 Human Rules)│     │  (Deduplication)  │     │  (Buffered)  │
└──────────────┘     └─────────────────┘     └───────────────────┘     └──────────────┘
                                                      │
                                              ┌───────▼────────┐
                                              │ State Manager  │
                                              │ (Checkpointing)│
                                              └────────────────┘
```

### Fitur Sistem:
- **🎯 Probabilistic Pattern Modeling:** Setiap pola memiliki bobot persentase berdasarkan riset keamanan (e.g., pola `kata+angka` memiliki peluang 25% untuk muncul).
- **🌸 Bloom Filter Deduplication:** Menggunakan algoritma probabilitas ruang yang sangat efisien. Mampu mengecek miliaran password duplikat hanya dengan memakan ruang ~1.8GB RAM/Disk, menghindari *paralisis birthday problem*.
- **💾 Stateful Processing (Resume):** Mampu dihentikan (Ctrl+C) dan dilanjutkan kembali tepat di posisi terakhir tanpa menghasilkan file ganda atau password duplikat di file `.txt`.
- **🧮 Memory-Mapped Files (mmap):** Akses disk yang sangat cepat untuk Bloom Filter tanpa me-load seluruh file ke RAM.

---

## 🔬 15 Pola Perilaku Manusia

Generator ini tidak hanya mengacak string, tetapi memanggil fungsi spesifik yang mensimulasikan 15 pola kebiasaan pengguna:

| No | Pola Perilaku | Contoh Output | Proporsi |
|:--:|---------------|---------------|:--------:|
| 1 | Kata Dasar + Angka | `dragon123`, `shadow99` | 25.0% |
| 2 | Kata Dasar + Tahun | `password1998`, `love2005` | 15.0% |
| 3 | Nama + Angka | `john123`, `sari44` | 12.0% |
| 4 | *Keyboard Walking* | `qwerty123`, `asdfgh` | 8.0% |
| 5 | Numerik Murni | `123456`, `11111111` | 7.0% |
| 6 | *Capitalization* | `Dragon123`, `Shadow` | 6.0% |
| 7 | Kata + Simbol | `password!`, `@dragon` | 5.0% |
| 8 | *Leet Speak* | `p@ssw0rd`, `h4ck3r` | 4.0% |
| 9 | Dua Kata Gabungan | `sunmoon`, `darkforest` | 3.5% |
| 10| Nama + Tahun | `budi1995`, `emma2000` | 3.0% |
| 11| Frasa Umum | `iloveyou`, `letmein` | 2.5% |
| 12| Kata Berulang | `catcat`, `lololo` | 2.0% |
| 13| Kompleks Prediktabel | `Dragon123!`, `Word99@me` | 2.5% |
| 14| Kata Bahasa Indonesia | `cinta123`, `jakarta99` | 2.5% |
| 15| Nama + Kata | `johnlove`, `darkrudi` | 2.0% |

*(Proporsi disesuaikan berdasarkan distribusi statistik kebocoran data global)*

---

## 🚀 Instalasi & Penggunaan

### Prasyarat
- Python 3.8 atau lebih baru
- Ruang disk kosong: Minimal ~15 GB (untuk target 1 Miliar password + file sistem)

### Menjalankan Generator

**1. Mode Interaktif (Rekomendasi)**
```bash
python password_generator.py
```
*Sistem akan otomatis mendeteksi apakah ada proses yang pernah terhenti dan menanyakan apakah ingin melanjutkan (Resume) atau memulai baru (Fresh).*

**2. Command Line Interface (CLI)**
```bash
# Memulai dari awal (menghapus progress lama)
python password_generator.py --fresh

# Melanjutkan proses yang terhenti
python password_generator.py --resume

# Melihat statistik progress saat ini
python password_generator.py --status

# Membersihkan semua file output dan temporary
python password_generator.py --clean
```

### Konfigurasi
Anda dapat menyesuaikan target dan perilaku generator di dalam class `Config` pada file `.py`:
```python
class Config:
    TOTAL_PASSWORDS = 100_000_000  # Target jumlah password unik
    OUTPUT_FILE = "dataset_password_realistis.txt"
    RANDOM_SEED = 42               # Untuk reproduktibilitas penelitian
    BLOOM_FPR = 0.001              # False Positive Rate Bloom Filter (0.1%)
```

---

## 📊 Performa & Benchmark

Pengujian dilakukan pada sistem standar (Consumer-grade Laptop). 
*(Catatan: Kecepatan sangat bergantung pada *False Positive Rate* duplikat yang dihasilkan oleh luasnya *wordlist* yang digunakan).*

| Skala Target | Estimasi Waktu | Ukuran File `.txt` | Kebutuhan Disk Total |
|:------------:|:--------------:|:------------------:|:--------------------:|
| 1 Juta       | ~30 detik      | ~10 MB             | ~200 MB              |
| 10 Juta      | ~5 menit       | ~100 MB            | ~500 MB              |
| 100 Juta     | ~45 menit      | ~1 GB              | ~3 GB                |
| 1 Miliar     | ~7 jam         | ~10 GB             | ~13 GB               |

**File yang Dihasilkan:**
- `dataset_password_realistis.txt` : File utama berisi password unik (1 password per baris).
- `.bloom_filter.bin` : *(Temporary)* File pendukung deduplikasi (Otomatis terhapus saat selesai).
- `.generator_state.bin` : *(Temporary)* Checkpoint untuk resume (Otomatis terhapus saat selesai).

---

## 📚 Landasan Akademis

Algoritma dan pola dalam riset ini dibangun berdasarkan literatur keamanan siber terkemuka:

1. **NIST SP 800-63B** - *Digital Identity Guidelines* (Mengenai entropi password dan memori manusia).
2. **Weir et al. (2010)** - *"Using Secret-Sharing for Detecting Compromised Passwords"*. (Mengenai struktur probabilistic context-free grammar untuk password).
3. **Florêncio & Herley (2007)** - *"A Large-Scale Study of Web Password Habits"*. (Mengenai distribusi panjang dan pengulangan password).
4. **Bloom, B. H. (1970)** - *Space/Time Trade-offs in Hash Coding with Allowable Errors*. (Dasar teori Bloom Filter yang digunakan dalam deduplikasi).

---

## ⚠️ Disclaimer

Proyek dan repository ini dikembangkan **murni untuk keperluan akademis, edukasi, dan riset keamanan defensif** (Tugas Akhir). 

Generator ini **TIDAK** menyimpan, mendistribusikan, atau menggunakan kredensial asli yang bocor. Semua data yang dihasilkan adalah 100% sintetis. Dilarang keras menggunakan tools atau dataset yang dihasilkan untuk aktivitas tidak sah, brute-force akun tanpa izin, atau kegiatan berbahaya lainnya. Gunakan dengan tanggung jawab etis.

---

<div align="center">
  <sub>Dibuat dengan 🐍 Python untuk Keperluan Penelitian Keamanan Informasi</sub>
</div>
```
