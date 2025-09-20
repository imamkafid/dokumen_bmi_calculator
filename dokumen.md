
# **Aplikasi BMI Calculator**
# **IMAM NOR MUHAMMAD KHAFID 221240001242**
# **MUHAMMAD DANDHY NUR R 221240001344**


### **Daftar Isi**
1.  [Product Requirements Document (PRD)](#1-product-requirements-document-prd)
2.  [Entity-Relationship Diagram (ERD) & Skema Database](#2-entity-relationship-diagram-erd--skema-database)
3.  [Software Requirements Specification (SRS)](#3-software-requirements-specification-srs)
4.  [Software Design Document (SDD)](#4-software-design-document-sdd)
5.  [Checklist Timeline Sprint MVP (2 Minggu)](#5-checklist-timeline-sprint-mvp-2-minggu)

--

## **1. Product Requirements Document (PRD)**

### **1.1 Visi & Latar Belakang**
Menyediakan aplikasi kalkulator BMI yang **cepat, akurat, minimalis, dan menghargai privasi** bagi pengguna yang ingin memantau status berat badannya tanpa kerumitan.

### **1.2 Target Pengguna**
*   Individu sadar kesehatan yang butuh alat cepat.
*   Pengguna non-teknis yang menginginkan antarmuka lugas.
*   Pengguna yang mengutamakan privasi data.

### **1.3 Fitur & Fungsionalitas (MVP)**

| ID | User Story | Kriteria Penerimaan (Acceptance Criteria) |
| :--- | :--- | :--- |
| **US-01**| Sebagai pengguna, saya ingin memasukkan berat (kg) dan tinggi (cm) saya. | - Field input numerik untuk berat & tinggi.<br>- Input tidak boleh kosong atau nol. |
| **US-02**| Sebagai pengguna, saya ingin menekan satu tombol untuk menghitung BMI. | - Tombol "Hitung BMI" yang jelas.<br>- Logika kalkulasi berjalan saat tombol ditekan.<br>- Menangani input tidak valid dengan pesan error. |
| **US-03**| Sebagai pengguna, saya ingin melihat hasil BMI yang jelas dan informatif. | - Menampilkan nilai BMI (1 desimal).<br>- Menampilkan kategori (Kurus, Normal, Gemuk, Obesitas).<br>- Latar belakang hasil diberi kode warna sesuai kategori.<br>- Menampilkan rentang berat badan ideal. |

### **1.4 Di Luar Lingkup (Out of Scope) MVP**
*   Dukungan unit imperial (pounds, inches).
*   Mode Gelap (Dark Mode).
*   Profil pengguna ganda.
*   Sinkronisasi data ke cloud.

---

## **2. Entity-Relationship Diagram (ERD) & Skema Database**

### **2.1 ERD Grafis**

#### **Skema MVP**
erDiagram
    bmi_records {
        INTEGER id PK "Primary Key, Auto-increment"
        REAL weight_kg "Berat badan dalam kg"
        REAL height_cm "Tinggi badan dalam cm"
        REAL bmi_value "Hasil nilai BMI"
        TEXT category "Kategori (Normal, Overweight, etc.)"
        DATETIME created_at "Waktu pencatatan"
    }
```

#### **Skema Potensial Masa Depan (Dengan Profil)**
erDiagram
    user_profiles ||--o bmi_records : "memiliki"

    user_profiles {
        INTEGER id PK
        TEXT name
    }

    bmi_records {
        INTEGER id PK
        INTEGER user_id FK
        REAL weight_kg
        REAL height_cm
        REAL bmi_value
        TEXT category
        DATETIME created_at
    }
```

### **2.2 Skema Database / Data Dictionary (Format Tabel)**

**Tabel: `bmi_records`**
| Nama Kolom | Tipe Data | Constraint / Key | Deskripsi |
| :--- | :--- | :--- | :--- |
| `id` | `INTEGER` | **Primary Key**, Auto-increment, NOT NULL | Pengenal unik catatan. |
| `weight_kg`| `REAL` | NOT NULL | Berat badan pengguna (kg). |
| `height_cm`| `REAL` | NOT NULL | Tinggi badan pengguna (cm). |
| `bmi_value`| `REAL` | NOT NULL | Nilai BMI yang dihitung. |
| `category` | `TEXT` | NOT NULL | Kategori berat badan. |
| `created_at`| `DATETIME` | NOT NULL | Waktu catatan dibuat. |

---

## **3. Software Requirements Specification (SRS)**

### **3.1 Persyaratan Fungsional**

| ID | Deskripsi Persyaratan |
| :--- | :--- |
| **FR-001** | **Input Data:** Sistem menyediakan field input numerik untuk berat (kg) dan tinggi (cm). |
| **FR-002** | **Validasi:** Sistem menolak kalkulasi jika input kosong, nol, atau negatif dan menampilkan pesan error. |
| **FR-003** | **Kalkulasi:** Sistem menghitung BMI menggunakan formula standar: `berat(kg) / (tinggi(m))^2`. |
| **FR-004** | **Tampilan Hasil:** Sistem menampilkan nilai BMI (1 desimal), kategori, rentang berat ideal, dan kode warna (Biru: Kurus, Hijau: Normal, Kuning: Gemuk, Merah: Obesitas). |
| **FR-005** | **Penyimpanan Riwayat:** Sistem secara otomatis menyimpan setiap hasil kalkulasi yang berhasil ke database lokal. |
| **FR-006** | **Tampilan Riwayat:** Sistem menampilkan daftar riwayat perhitungan, diurutkan dari yang terbaru. |

### **3.2 Persyaratan Non-Fungsional**

| ID | Kategori | Deskripsi Persyaratan |
| :--- | :--- | :--- |
| **NFR-001** | Kinerja | Waktu startup < 2 detik. Kalkulasi < 500ms. UI berjalan mulus (60 FPS). |
| **NFR-002** | Privasi | 100% data disimpan secara lokal di perangkat. Tidak ada transmisi data melalui jaringan. |
| **NFR-003** | Keandalan | Tingkat crash-free users > 99.5%. |
| **NFR-004** | Kegunaan | Alur kerja utama (menghitung BMI) dapat diselesaikan dalam 3 interaksi. |

---

## **4. Software Design Document (SDD)**

### **4.1 Arsitektur & Teknologi**

*   **Pola Arsitektur:** Feature-First Layered Architecture (mirip Clean Architecture).
*   **Lapisan:** Presentation (UI), Application (State), Domain (Logic), Infrastructure (Data).
*   **Tech Stack:**
    *   **Framework:** Flutter
    *   **State Management:** Riverpod (dengan Riverpod Generator)
    *   **Model Data:** Freezed
    *   **Navigasi:** Go_Router
    *   **Database:** Drift (SQLite)
    *   **Error Handling:** fpdart (`Either`)

### **4.2 Desain Rinci Komponen Kunci**

#### **Manajemen State (Application Layer)**
State kalkulator akan dikelola oleh sebuah `Notifier` Riverpod dengan state yang didefinisikan menggunakan Freezed.
```dart
@freezed
class BmiCalculatorState with _$BmiCalculatorState {
  const factory BmiCalculatorState.initial() = _Initial;
  const factory BmiCalculatorState.loading() = _Loading;
  const factory BmiCalculatorState.data(BmiResult result) = _Data;
  const factory BmiCalculatorState.error(String message) = _Error;
}
```

#### **Definisi Database (Infrastructure Layer)**
Tabel database didefinisikan secara type-safe menggunakan Drift.
```dart
import 'package:drift/drift.dart';

class BmiRecords extends Table {
  IntColumn get id => integer().autoIncrement()();
  RealColumn get weightKg => real()();
  RealColumn get heightCm => real()();
  RealColumn get bmiValue => real()();
  TextColumn get category => text()();
  DateTimeColumn get createdAt => dateTime()();
}
```

### **4.3 Struktur Proyek**
```
lib/
├── core/               # Kode bersama (router, db, theme)
└── features/
    ├── bmi_calculator/
    │   ├── application/    # Riverpod Notifiers
    │   ├── domain/         # Models (Freezed) & Business Logic
    │   └── presentation/   # UI (Screens & Widgets)
    └── bmi_history/
        ├── ... (struktur serupa)
```
---

## **5. Checklist Timeline Sprint MVP (2 Minggu)**

### **Minggu 1: Fondasi, Logika Inti, dan UI Dasar**
| Hari | Fokus Utama | Detail Tugas | Status |
| :--- | :--- | :--- | :--- |
| **1** | **Setup Proyek** | `[ ]` Inisialisasi proyek Flutter, dependensi, struktur folder, Git. | `[ ]` |
| **2** | **Database & Model** | `[ ]` Definisikan tabel Drift & model Freezed, jalankan `build_runner`. | `[ ]` |
| **3** | **Logika & State**| `[ ]` Buat logika kalkulasi (Domain) & `BmiCalculatorNotifier` (Application). | `[ ]` |
| **4**| **UI Skeleton** | `[ ]` Buat `BmiCalculatorScreen` dengan layout statis (TextFields, Button). | `[ ]` |
| **5**| **Integrasi & Review** | `[ ]` Hubungkan UI dengan Notifier. Pastikan alur kalkulasi berfungsi. | `[ ]` |

### **Minggu 2: Fitur Riwayat, Polishing, dan Pengujian**
| Hari | Fokus Utama | Detail Tugas | Status |
| :--- | :--- | :--- | :--- |
| **6**| **Backend Riwayat** | `[ ]` Buat DAO & Repository untuk Drift. Simpan hasil ke DB. | `[ ]` |
| **7**| **UI Riwayat** | `[ ]` Buat `BmiHistoryScreen` & Notifier untuk menampilkan data dari DB. | `[ ]` |
| **8**| **UI/UX Polishing**| `[ ]` Terapkan kode warna dinamis, animasi, & perbaiki layout. | `[ ]` |
| **9**| **Pengujian & Bug Fix**| `[ ]` Lakukan pengujian manual end-to-end, perbaiki bug yang ditemukan. | `[ ]` |
| **10**| **Finalisasi & Demo** | `[ ]` Pengujian regresi, siapkan build, & demonstrasi MVP. | `[ ]` |

---
