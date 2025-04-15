

# Proyek Pembersihan Data: Dataset Kendaraan Listrik dan Perusahaan

## Gambaran Umum
Proyek ini bertujuan untuk melakukan analisis awal dan pembersihan data pada dua dataset:
1. **Dataset Kendaraan Listrik** (`Electric_Vehicle_Population_Data.csv`): Berisi informasi kendaraan listrik yang terdaftar di Washington State, termasuk kolom seperti `Make`, `Model Year`, dan `City`.
2. **Dataset Perusahaan** (`company_esg_financial_dataset.csv`): Berisi data finansial dan ESG perusahaan, termasuk kolom seperti `CompanyName`, `Revenue`, dan `Industry`.

Proses meliputi eksplorasi awal dataset, penanganan masalah data (missing values, outliers, noise, duplikat), dan pembersihan inkonsistensi nama antara kolom `Make` dan `CompanyName`.

---

## Struktur Proyek
Proyek ini terdiri dari tiga skrip Python utama:

1. **`explore_data.py`**: Eksplorasi awal dataset kendaraan untuk memahami struktur dan statistik dasar.
2. **`clean_data.py`**: Penanganan masalah data seperti missing values, outliers, noise, dan duplikat pada dataset kendaraan.
3. **`clean_names.py`**: Pembersihan inkonsistensi nama pada kolom `Make` (dataset kendaraan) dan `CompanyName` (dataset perusahaan).

### File Pendukung
- **Dataset**:
  - `Electric_Vehicle_Population_Data.csv` (57.08 MB)
  - `company_esg_financial_dataset.csv` (1.23 MB)
- **Output**:
  - `Cleaned_Electric_Vehicle_Population_Data.csv`: Dataset kendaraan setelah pembersihan.
  - `Cleaned_Company_ESG_Financial_Dataset.csv`: Dataset perusahaan setelah pembersihan nama.

---

## Langkah-Langkah dan Kode

### 1. Eksplorasi Awal Dataset (`explore_data.py`)
**Tujuan**: Memahami struktur dataset kendaraan dengan menampilkan informasi dasar, statistik, dan 5 baris pertama.

```python
import pandas as pd

df = pd.read_csv('path_to_file/Electric_Vehicle_Population_Data.csv')

print("Informasi Struktur Dataset:")
print(df.info())
print("\nStatistik Deskriptif Dataset:")
print(df.describe(include='all'))
print("\n5 Baris Pertama Dataset:")
print(df.head())
print("\nJumlah Baris dan Kolom:", df.shape)
```

**Hasil**:
- Dataset memiliki 10 kolom, termasuk `Make`, `Model Year`, dan `City`.
- `Model Year` berkisar 2000-2025, dengan mayoritas di tahun terbaru.

---

### 2. Penanganan Masalah Data (`clean_data.py`)
**Tujuan**: Mengidentifikasi dan menangani missing values, outliers, noise, dan duplikat pada dataset kendaraan.

```python
import pandas as pd
import numpy as np

df = pd.read_csv('path_to_file/Electric_Vehicle_Population_Data.csv')

# Missing Values
print("Jumlah Missing Values per Kolom:")
print(df.isnull().sum())
df['County'] = df['County'].fillna('Unknown')
df['City'] = df['City'].fillna('Unknown')
df['Clean Alternative Fuel Vehicle (CAFV) Eligibility'] = df['Clean Alternative Fuel Vehicle (CAFV) Eligibility'].fillna('Eligibility unknown')

# Outliers di 'Model Year' menggunakan IQR
Q1 = df['Model Year'].quantile(0.25)
Q3 = df['Model Year'].quantile(0.75)
IQR = Q3 - Q1
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR
outliers = df[(df['Model Year'] < lower_bound) | (df['Model Year'] > upper_bound)]
print("\nJumlah Outliers di Model Year:", len(outliers))
print("Contoh Outliers di Model Year:")
print(outliers[['Model Year']].head())
df = df[(df['Model Year'] >= lower_bound) & (df['Model Year'] <= upper_bound)]

# Noise: 'Model Year' < 1900
noise = df[df['Model Year'] < 1900]
print("\nJumlah Noise di Model Year (< 1900):", len(noise))
df = df[df['Model Year'] >= 1900]

# Duplikat
print("\nJumlah Duplikat:", df.duplicated().sum())
df = df.drop_duplicates()

# Simpan hasil
df.to_csv('path_to_file/Cleaned_Electric_Vehicle_Population_Data.csv', index=False)
print("\nDataset setelah pembersihan disimpan.")
```

**Hasil**:
- Missing values diisi dengan nilai default.
- Outliers dan noise pada `Model Year` dihapus.
- Duplikat dihapus, menghasilkan dataset yang lebih bersih.

---

### 3. Pembersihan Inkonsistensi Nama (`clean_names.py`)
**Tujuan**: Membersihkan inkonsistensi pada kolom `Make` dan `CompanyName` dengan menghapus karakter khusus, spasi, dan underscore, lalu menstandarisasi ke huruf kapital.

```python
import pandas as pd
import re

# Membaca dataset
df_vehicle = pd.read_csv('path_to_file/Electric_Vehicle_Population_Data.csv')
df_company = pd.read_csv('path_to_file/company_esg_financial_dataset.csv')

# Tampilkan nilai unik sebelum pembersihan
print("Nilai Unik di 'Make' (Dataset Kendaraan) Sebelum Pembersihan:")
print(df_vehicle['Make'].unique()[:10])
print("\nNilai Unik di 'CompanyName' (Dataset Perusahaan) Sebelum Pembersihan:")
print(df_company['CompanyName'].unique()[:10])

# Fungsi pembersihan
def clean_name(name):
    if pd.isna(name):
        return name
    cleaned = re.sub(r'[^a-zA-Z0-9]', '', str(name))
    return cleaned.strip().upper()

# Terapkan pembersihan
df_vehicle['Make_Cleaned'] = df_vehicle['Make'].apply(clean_name)
df_company['CompanyName_Cleaned'] = df_company['CompanyName'].apply(clean_name)

# Tampilkan nilai unik setelah pembersihan
print("\nNilai Unik di 'Make_Cleaned' (Dataset Kendaraan) Setelah Pembersihan:")
print(df_vehicle['Make_Cleaned'].unique()[:10])
print("\nNilai Unik di 'CompanyName_Cleaned' (Dataset Perusahaan) Setelah Pembersihan:")
print(df_company['CompanyName_Cleaned'].unique()[:10])

# Simpan hasil
df_vehicle.to_csv('path_to_file/Cleaned_Electric_Vehicle_Population_Data.csv', index=False)
df_company.to_csv('path_to_file/Cleaned_Company_ESG_Financial_Dataset.csv', index=False)
print("\nDataset dengan nama yang sudah dibersihkan disimpan.")
```

**Hasil**:
- `Make_Cleaned`: ['TESLA' 'HYUNDAI' 'BMW' 'TOYOTA' 'NISSAN' 'KIA' 'POLESTAR' 'MAZDA' 'CHEVROLET' 'VOLVO']
- `CompanyName_Cleaned`: ['COMPANY1' 'COMPANY2' 'COMPANY3' 'COMPANY4' 'COMPANY5' 'COMPANY6' 'COMPANY7' 'COMPANY8' 'COMPANY9' 'COMPANY10']
- Nama sudah bersih dari karakter khusus, spasi, dan underscore, tetapi isi data tetap tidak cocok.

---

## Cara Menjalankan
1. **Prasyarat**:
   - Pastikan Python terinstal.
   - Instal pustaka yang diperlukan:
     ```bash
     pip install pandas
     ```

2. **Unduh Dataset**:
   - Simpan `Electric_Vehicle_Population_Data.csv` dan `company_esg_financial_dataset.csv` di direktori proyek.

3. **Jalankan Skrip**:
   - Ganti `'path_to_file'` dengan lokasi file dataset Anda.
   - Jalankan masing-masing skrip:
     ```bash
     python explore_data.py
     python clean_data.py
     python clean_names.py
     ```

---

## Temuan
- Dataset kendaraan menunjukkan dominasi Tesla (43%) dan konsentrasi di King County (50%).
- Pembersihan data berhasil menangani missing values, outliers, noise, dan duplikat.
- Pembersihan nama menghilangkan inkonsistensi format, tetapi `Make` dan `CompanyName` tidak dapat dicocokkan karena perbedaan isi data (nama nyata vs nama sintetis).

## Rekomendasi
- Gunakan informasi tambahan untuk menghubungkan "COMPANY1" dengan "TESLA" jika penggabungan diperlukan.
- Dataset kendaraan yang sudah dibersihkan siap untuk analisis lebih lanjut, seperti tren adopsi kendaraan listrik.

---
