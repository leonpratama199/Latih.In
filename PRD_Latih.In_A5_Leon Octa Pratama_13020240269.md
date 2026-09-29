# PRD: Kuis Latihan Personal Lintas Mata Kuliah (v2)

## 1. Ringkasan Produk

**Nama produk:** Kuis Latihan Personal (nama sementara)

**Deskripsi:** Aplikasi web sederhana untuk berlatih soal dari berbagai mata kuliah (Algoritma, Basis Data, RPLO, Jaringan, dll) secara mandiri. Aplikasi menyimpan riwayat skor dan riwayat kesalahan per mata kuliah sehingga progres belajar terlihat dari waktu ke waktu.

**Target pengguna:** Mahasiswa (khususnya Teknik Informatika) yang ingin latihan soal mandiri.

**Platform:** Web application, desain minimalis dan responsif.

**Perubahan dari versi sebelumnya:**
- Tidak ada login, akun, maupun identifier pengguna. Aplikasi bersifat **single-user** (satu pengguna per instalasi).
- Program dibangun dengan struktur **MVC** menggunakan framework.
- Stack teknologi ditetapkan agar implementasi tetap sederhana.

---

## 2. Latar Belakang & Masalah

- Kuis online seperti Kahoot dan Quizizz dirancang untuk sesi langsung bersama orang lain, bukan latihan mandiri jangka panjang.
- Mahasiswa sulit melacak progres latihan per mata kuliah.
- Soal yang dijawab salah biasanya hilang setelah sesi selesai, padahal perlu dipelajari ulang.

---

## 3. Tujuan Produk

1. Menyediakan tempat latihan soal mandiri untuk beberapa mata kuliah dalam satu aplikasi.
2. Menyimpan riwayat skor per mata kuliah.
3. Menyediakan daftar soal yang pernah dijawab salah untuk dipelajari ulang.

---

## 4. Lingkup Produk

### 4.1 Termasuk (MVP)
- Daftar mata kuliah
- Soal per mata kuliah (pilihan ganda dan benar/salah)
- Sesi kuis: pilih mata kuliah, jawab sejumlah soal, dapat skor di akhir
- Pembahasan singkat setelah menjawab tiap soal
- Riwayat skor (tanggal, mata kuliah, skor)
- Riwayat kesalahan (daftar soal yang pernah dijawab salah)

### 4.2 Tidak termasuk
- **Login/autentikasi dan manajemen pengguna**
- Mode kompetisi/multiplayer
- Rekomendasi soal adaptif (spaced repetition)
- Pembuatan soal otomatis dari materi/slide
- Grafik progres kompleks (cukup tabel sederhana)
- Integrasi dengan sistem akademik kampus
- Dashboard admin (soal diisi lewat seed data)

---

## 5. Pengguna

Hanya satu peran: **Pengguna (mahasiswa)**. Tanpa login, seluruh riwayat tersimpan untuk satu pengguna pada instalasi tersebut. Data soal dikelola langsung lewat seed data (JSON/seeder), tanpa halaman admin.

---

## 6. Alur Pengguna

1. Pengguna membuka aplikasi dan langsung melihat daftar mata kuliah.
2. Pengguna memilih satu mata kuliah.
3. Sistem menampilkan 10 soal (acak) satu per satu.
4. Setelah menjawab, sistem menampilkan benar/salah dan pembahasan singkat.
5. Setelah semua soal selesai, sistem menampilkan skor akhir.
6. Skor dan detail jawaban disimpan ke riwayat.
7. Pengguna dapat membuka **Riwayat Skor** per mata kuliah.
8. Pengguna dapat membuka **Riwayat Kesalahan** untuk belajar ulang.

---

## 7. Kebutuhan Fungsional

| ID | Kebutuhan | Prioritas |
|---|---|---|
| F-01 | Menampilkan daftar mata kuliah | Wajib |
| F-02 | Menampilkan soal berdasarkan mata kuliah yang dipilih | Wajib |
| F-03 | Memvalidasi jawaban (benar/salah) untuk tiap tipe soal | Wajib |
| F-04 | Menampilkan pembahasan singkat setelah soal dijawab | Wajib |
| F-05 | Menghitung dan menyimpan skor akhir tiap sesi | Wajib |
| F-06 | Menyimpan dan menampilkan riwayat sesi (tanggal, mata kuliah, skor) | Wajib |
| F-07 | Menampilkan daftar soal yang pernah dijawab salah | Wajib |
| F-08 | Mendukung dua tipe soal: pilihan ganda dan benar/salah | Wajib |
| F-09 | Menandai soal yang paling sering dijawab salah | Opsional |

---

## 8. Kebutuhan Non-Fungsional

- **Sederhana:** antarmuka minimalis, tanpa elemen visual berlebihan.
- **Responsif:** nyaman diakses dari laptop maupun HP.
- **Performa:** halaman dan validasi jawaban cepat.
- **Skala data:** 3-5 mata kuliah dengan puluhan soal per mata kuliah.

---

## 9. Stack Teknologi

| Lapisan | Pilihan | Alasan |
|---|---|---|
| Backend | **Laravel (PHP)** | Sudah berbasis MVC, ada Eloquent ORM (cocok untuk OOP), migration, dan seeder |
| Frontend | **Blade + Bootstrap 5** | Tampilan responsif cepat tanpa build rumit; Blade menjadi View pada MVC |
| Database | **SQLite** | Tanpa instalasi server database, cukup satu file |

Alternatif jika lebih nyaman dengan JavaScript: Node.js + Express (backend, template EJS) dengan Bootstrap 5 dan SQLite. Struktur MVC tetap sama.

---

## 10. Model Data

### `MataKuliah`
- `id` (PK), `nama`, `deskripsi` (opsional)

### `Soal`
- `id` (PK), `mata_kuliah_id` (FK), `tipe_soal` ("pilihan_ganda" / "benar_salah"), `pertanyaan`, `pilihan_jawaban` (JSON), `jawaban_benar`, `pembahasan`

### `SesiKuis`
- `id` (PK), `mata_kuliah_id` (FK), `tanggal`, `skor`, `total_soal`

### `JawabanUser`
- `id` (PK), `sesi_kuis_id` (FK), `soal_id` (FK), `jawaban_dipilih`, `benar` (boolean)

Catatan: tidak ada entitas `Mahasiswa` karena tidak ada login dan aplikasi single-user.

---

## 11. Struktur Berorientasi Objek

```
Soal (kelas induk/abstrak)
 - atribut: id, pertanyaan, pembahasan, jawabanBenar
 - method abstrak: validasiJawaban(jawaban)
 - method abstrak: tampilkanPilihan()

SoalPilihanGanda extends Soal
 - override validasiJawaban(): cocokkan dengan salah satu dari beberapa opsi
 - override tampilkanPilihan(): tampilkan 4 opsi

SoalBenarSalah extends Soal
 - override validasiJawaban(): cocokkan Benar/Salah
 - override tampilkanPilihan(): tampilkan 2 opsi

MataKuliah   -> memiliki banyak Soal, banyak SesiKuis
SesiKuis     -> milik satu MataKuliah, memiliki banyak JawabanUser; method hitungSkor()
JawabanUser  -> milik satu SesiKuis, merujuk satu Soal
```

Kelas induk `Soal` dengan dua turunan menunjukkan **inheritance** dan **polymorphism**: cara validasi dan tampilan berbeda per tipe, tetapi dipanggil lewat method yang sama.

---

## 12. Arsitektur MVC

### Model
Berisi class data dan logika inti: `MataKuliah`, `Soal` (+ `SoalPilihanGanda`, `SoalBenarSalah`), `SesiKuis`, `JawabanUser`. Bertanggung jawab atas validasi jawaban, perhitungan skor, dan relasi antar entitas. Tidak berisi kode tampilan.

### View
Template Blade + Bootstrap: daftar mata kuliah, sesi kuis, hasil sesi, riwayat skor, riwayat kesalahan. Hanya menampilkan data dari Controller, tanpa logika bisnis.

### Controller
- `MataKuliahController` — menampilkan daftar mata kuliah
- `KuisController` — memulai sesi, mengambil soal, menerima jawaban, memanggil Model untuk validasi dan skor, menampilkan hasil
- `RiwayatController` — menampilkan riwayat skor dan riwayat kesalahan

### Alur satu permintaan (menjawab soal)
1. Pengguna mengirim jawaban dari **View**.
2. **Controller** (`KuisController`) menerima input.
3. Controller memanggil **Model** (`Soal->validasiJawaban()`), menyimpan `JawabanUser`, dan memperbarui `SesiKuis`.
4. Controller mengirim hasil ke **View** untuk ditampilkan.

### Struktur folder (Laravel)
```
app/
  Models/            MataKuliah, Soal, SoalPilihanGanda, SoalBenarSalah, SesiKuis, JawabanUser
  Http/Controllers/  MataKuliahController, KuisController, RiwayatController
resources/views/     matkul/index, kuis/soal, kuis/hasil, riwayat/skor, riwayat/kesalahan
routes/web.php
database/
  migrations/
  seeders/           data soal awal
```

### Rute utama
| Method | URL | Controller |
|---|---|---|
| GET | `/` | MataKuliahController@index |
| POST | `/kuis/mulai/{matkul}` | KuisController@mulai |
| GET | `/kuis/{sesi}` | KuisController@tampilSoal |
| POST | `/kuis/{sesi}/jawab` | KuisController@jawab |
| GET | `/kuis/{sesi}/hasil` | KuisController@hasil |
| GET | `/riwayat/skor` | RiwayatController@skor |
| GET | `/riwayat/kesalahan` | RiwayatController@kesalahan |

---

## 13. Halaman

1. **Dashboard** — daftar mata kuliah
2. **Sesi Kuis** — soal satu per satu, pilihan jawaban, pembahasan setelah menjawab
3. **Hasil Sesi** — skor akhir
4. **Riwayat Skor** — tabel sesi sebelumnya per mata kuliah
5. **Riwayat Kesalahan** — daftar soal yang pernah salah beserta pembahasan

---

## 14. Batasan

- Soal diisi manual oleh pembuat aplikasi lewat seed data untuk beberapa mata kuliah awal.
- Tanpa login, tanpa multi-user, tanpa fitur sosial/kompetisi.
- Tanpa algoritma rekomendasi soal.
- Desain minimalis tanpa ilustrasi atau animasi rumit.

---

## 15. Kriteria Keberhasilan (MVP)

- [ ] Pengguna dapat melihat daftar mata kuliah
- [ ] Pengguna dapat mengerjakan sesi kuis dan mendapat skor di akhir
- [ ] Riwayat sesi (tanggal, mata kuliah, skor) tersimpan dan bisa dilihat kembali
- [ ] Daftar soal yang pernah dijawab salah tersimpan dan ditampilkan
- [ ] Kode mengikuti pemisahan Model-View-Controller
- [ ] Ada minimal 3 mata kuliah dengan masing-masing minimal 10 soal (seed data)

---

## 16. Catatan untuk Pengembang

- Prioritaskan kesederhanaan; tujuan utama adalah menunjukkan penerapan OOP dan MVC pada aplikasi yang benar-benar berjalan.
- Tidak perlu membuat login, registrasi, atau halaman admin.
- Simpan seed data soal dalam format yang mudah diedit (seeder Laravel atau file JSON).
