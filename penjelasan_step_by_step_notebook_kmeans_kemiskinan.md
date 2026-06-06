# Penjelasan Lengkap Notebook K-Means Clustering Tingkat Kemiskinan

Dokumen ini menjelaskan seluruh kode pada notebook secara rinci dan bertahap agar kamu siap mempresentasikan project serta menjawab pertanyaan dari dosen/guru/penguji.

---

# Gambaran Besar Project

Project ini bertujuan untuk:

1. Membaca data tingkat kemiskinan.
2. Memilih fitur yang digunakan.
3. Melakukan clustering menggunakan algoritma K-Means.
4. Menentukan jumlah cluster terbaik.
5. Mengevaluasi kualitas cluster.
6. Memvisualisasikan hasil cluster.
7. Menghubungkan hasil clustering dengan peta Indonesia menggunakan GeoPandas dan Folium.

Secara sederhana:

- Machine Learning biasanya dibagi menjadi:
  - Supervised Learning → ada label target.
  - Unsupervised Learning → tidak ada label.

K-Means termasuk:

> Unsupervised Learning

karena model mencoba menemukan pola sendiri dari data.

---

# CELL 0 — Import Library

```python
#import common library
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings('ignore')
```

## Penjelasan

Cell ini digunakan untuk mengimpor library yang dibutuhkan.

---

## 1. NumPy

```python
import numpy as np
```

NumPy digunakan untuk:

- operasi matematika
- array multidimensi
- perhitungan numerik
- manipulasi data numerik

Biasanya sangat penting di Machine Learning.

Contoh:

```python
np.array([1,2,3])
```

---

## 2. Pandas

```python
import pandas as pd
```

Pandas digunakan untuk:

- membaca CSV
- mengolah tabel data
- filtering
- manipulasi dataset

Objek utama:

- DataFrame → seperti tabel Excel

---

## 3. Matplotlib

```python
import matplotlib.pyplot as plt
```

Digunakan untuk membuat grafik.

Contoh:

- line chart
- scatter plot
- histogram

---

## 4. Seaborn

```python
import seaborn as sns
```

Seaborn adalah library visualisasi berbasis matplotlib.

Lebih bagus untuk:

- heatmap
- statistik visual
- visualisasi data modern

---

## 5. Warnings

```python
import warnings
warnings.filterwarnings('ignore')
```

Digunakan untuk menyembunyikan warning.

Kenapa dipakai?

Karena terkadang notebook menghasilkan warning yang tidak terlalu penting sehingga output menjadi lebih bersih.

---

# Pertanyaan Presentasi yang Mungkin Muncul

## Q: Kenapa memakai Pandas?

Karena dataset berbentuk tabel sehingga Pandas sangat cocok untuk manipulasi data.

## Q: Kenapa matplotlib dan seaborn dipakai bersamaan?

Karena seaborn dibangun di atas matplotlib.
Matplotlib fleksibel, sedangkan seaborn lebih mudah membuat visualisasi statistik.

---

# CELL 1 — Membaca Dataset

```python
df = pd.read_csv('/content/Data_Tingkat_Kemiskinan_CLEAN.csv')
```

## Penjelasan

Kode ini membaca file CSV.

CSV = Comma Separated Values.

Dataset disimpan ke variabel:

```python
df
```

`df` berarti DataFrame.

---

## Cara Kerja

```python
pd.read_csv()
```

akan:

1. membuka file CSV
2. membaca isi tabel
3. mengubah menjadi DataFrame pandas

---

## Kenapa dataset diberi nama CLEAN?

Biasanya artinya:

- data sudah dibersihkan
- missing value sudah diatasi
- data duplikat mungkin sudah dihapus
- format data sudah rapi

---

# CELL 2 — Melihat Informasi Dataset

```python
df.info()
```

## Fungsi

Menampilkan:

- jumlah baris
- jumlah kolom
- tipe data
- jumlah non-null
- penggunaan memori

---

## Hal Penting Saat Presentasi

Penguji sering menanyakan:

> “Bagaimana kamu tahu dataset sudah bersih?”

Jawaban:

- Dicek menggunakan `df.info()`
- Melihat apakah ada null value
- Memastikan tipe data sesuai

---

## Contoh Tipe Data

- int64 → bilangan bulat
- float64 → desimal
- object → teks/string

---

# CELL 3 — Menampilkan Isi Dataset

```python
df.head()
```

## Fungsi

Menampilkan 5 baris pertama dataset.

Tujuannya:

- melihat struktur data
- memastikan kolom benar
- memahami isi dataset

---

## Kenapa penting?

Sebelum modeling kita harus memahami:

- nama kolom
- bentuk data
- isi data

---

# CELL 4 — Memisahkan Feature dan Target

```python
from sklearn.model_selection import train_test_split

feature = df.drop(columns=['Tingkat_Penduduk_Miskin', 'Kabupaten_Kota'])
target = df['Tingkat_Penduduk_Miskin']
```

---

# Penjelasan Konsep

Dalam Machine Learning:

- Feature = variabel input
- Target = variabel output

---

## Baris Pertama

```python
from sklearn.model_selection import train_test_split
```

Mengimpor fungsi untuk membagi data training dan testing.

---

## Membuat Feature

```python
feature = df.drop(columns=['Tingkat_Penduduk_Miskin', 'Kabupaten_Kota'])
```

Artinya:

- semua kolom dipakai
- kecuali:
  - Tingkat_Penduduk_Miskin
  - Kabupaten_Kota

---

## Kenapa Kabupaten_Kota dihapus?

Karena berisi teks.

K-Means hanya bisa bekerja dengan data numerik.

Contoh:

❌ "Surabaya"

✅ 10.5

---

## Membuat Target

```python
target = df['Tingkat_Penduduk_Miskin']
```

Namun sebenarnya di project ini target tidak terlalu dipakai.

Kenapa?

Karena K-Means adalah unsupervised learning.

Artinya:

- model tidak membutuhkan label target
- model mencari pola sendiri

---

# Pertanyaan Penting Presentasi

## Q: Kenapa masih membuat target padahal K-Means unsupervised?

Jawaban bagus:

> Target dibuat untuk analisis tambahan dan referensi, tetapi proses clustering K-Means sebenarnya hanya menggunakan feature.

---

# CELL 5 — Membagi Data Train dan Test

```python
feature_train, feature_test, target_train, target_test = train_test_split(feature, target, test_size=0.2, random_state=42)
```

## Fungsi

Membagi dataset menjadi:

- training data
- testing data

---

## Penjelasan Parameter

### 1. test_size=0.2

Berarti:

- 80% training
- 20% testing

---

### 2. random_state=42

Agar hasil pembagian selalu sama.

Kenapa penting?

Supaya eksperimen reproducible.

---

# Hal Sangat Penting

Pada K-Means sebenarnya train-test split tidak terlalu wajib.

Karena clustering berbeda dengan klasifikasi.

Jadi jika ditanya:

## Q: Apakah train-test split wajib di K-Means?

Jawaban:

> Tidak wajib. Clustering biasanya menggunakan seluruh data karena tidak memiliki label target seperti supervised learning.

Ini poin penting sekali.

---

# CELL 6 — Elbow Method

```python
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score

#elbow method for menentukan jumlah cluster terbaik
inertia = []

K_range = range(1, 11)

for k in K_range:

    kmeans = KMeans(
        n_clusters=k,
        random_state=42,
        n_init=10
    )

    kmeans.fit(feature)

    inertia.append(kmeans.inertia_)
```

---

# Konsep Sangat Penting

## Apa itu K-Means?

K-Means adalah algoritma clustering.

Tujuan:

Mengelompokkan data berdasarkan kemiripan.

---

## Cara Kerja K-Means

1. Tentukan jumlah cluster (K)
2. Pilih centroid awal
3. Hitung jarak data ke centroid
4. Masukkan data ke cluster terdekat
5. Update centroid
6. Ulangi sampai stabil

---

# Apa itu Centroid?

Centroid = titik pusat cluster.

---

# Apa itu Elbow Method?

Digunakan untuk mencari jumlah cluster terbaik.

---

# Apa itu Inertia?

Inertia adalah:

> total jarak data terhadap centroid cluster.

Semakin kecil inertia:

- cluster makin rapat
- data makin mirip dalam cluster

---

## Kenapa tidak langsung pilih cluster banyak?

Karena:

- terlalu banyak cluster → over segmentation
- terlalu sedikit → data terlalu umum

Elbow Method mencari titik optimal.

---

## Penjelasan Kode

### 1. Membuat list inertia

```python
inertia = []
```

Untuk menyimpan nilai inertia.

---

### 2. Range cluster

```python
K_range = range(1, 11)
```

Menguji cluster:

- 1
- 2
- 3
- ...
- 10

---

### 3. Perulangan

```python
for k in K_range:
```

Loop untuk mencoba setiap jumlah cluster.

---

### 4. Membuat model

```python
kmeans = KMeans(
    n_clusters=k,
    random_state=42,
    n_init=10
)
```

---

## Penjelasan Parameter

### n_clusters

Jumlah cluster.

---

### random_state

Agar hasil konsisten.

---

### n_init=10

K-Means mencoba centroid awal sebanyak 10 kali.

Lalu memilih hasil terbaik.

Kenapa penting?

Karena centroid awal random.

---

### 5. Training model

```python
kmeans.fit(feature)
```

Model belajar pola data.

---

### 6. Simpan inertia

```python
inertia.append(kmeans.inertia_)
```

Mengambil nilai inertia.

---

# Pertanyaan Presentasi

## Q: Kenapa memakai Elbow Method?

Karena digunakan untuk menentukan jumlah cluster optimal.

## Q: Apa itu inertia?

Total jarak data ke centroid cluster.

## Q: Kenapa inertia selalu turun saat cluster bertambah?

Karena semakin banyak cluster maka data semakin dekat dengan centroid.

---

# CELL 7 — Visualisasi Elbow Method

```python
plt.figure(figsize=(8,5))

plt.plot(K_range, inertia, marker='o')

plt.xlabel('Jumlah Cluster (k)')
plt.ylabel('Inertia')
plt.title('Elbow Method')

plt.show()
```

---

# Fungsi

Menampilkan grafik elbow.

---

# Cara Membaca Grafik Elbow

Cari titik “siku”.

Biasanya:

- sebelum siku → penurunan drastis
- sesudah siku → penurunan kecil

Titik siku dianggap jumlah cluster terbaik.

---

# Penjelasan Detail

## plt.figure(figsize=(8,5))

Mengatur ukuran grafik.

---

## plt.plot()

Membuat line chart.

---

## marker='o'

Memberi titik bulat.

---

## xlabel dan ylabel

Memberi nama sumbu.

---

# CELL 8 — Silhouette Score

```python
from sklearn.metrics import silhouette_score

for k in range(2,11):

    kmeans = KMeans(
        n_clusters=k,
        random_state=42
    )

    labels = kmeans.fit_predict(feature)

    score = silhouette_score(feature, labels)

    print(f"k={k}, score={score}")
```

---

# Konsep Penting

## Apa itu Silhouette Score?

Digunakan untuk mengevaluasi kualitas cluster.

Nilai range:

- -1 sampai 1

Interpretasi:

| Nilai | Arti |
|---|---|
| Mendekati 1 | Cluster bagus |
| Sekitar 0 | Cluster tumpang tindih |
| Negatif | Salah cluster |

---

# Kenapa range mulai dari 2?

```python
range(2,11)
```

Karena silhouette score tidak bisa dihitung jika hanya ada 1 cluster.

---

## fit_predict()

```python
labels = kmeans.fit_predict(feature)
```

Melakukan:

1. training
2. prediksi cluster

sekaligus.

---

# Pertanyaan Presentasi

## Q: Bedanya Elbow dan Silhouette?

### Elbow

Melihat penurunan inertia.

### Silhouette

Mengukur kualitas pemisahan cluster.

---

# CELL 9 — Membuat Model Final

```python
kmeans = KMeans(
    n_clusters=2,
    random_state=42
)
```

## Penjelasan

Model final dibuat dengan:

```python
n_clusters=2
```

Artinya data akan dibagi menjadi 2 kelompok.

Biasanya dipilih berdasarkan:

- elbow method
- silhouette score

---

# CELL 10 — Training dan Membuat Cluster

```python
df['Cluster'] = kmeans.fit_predict(feature)
```

---

# Penjelasan

## fit_predict()

Melakukan:

1. training model
2. menentukan cluster tiap data

---

## Menambahkan Kolom Baru

```python
df['Cluster']
```

DataFrame sekarang memiliki kolom cluster.

Contoh:

| Kabupaten | Cluster |
|---|---|
| A | 0 |
| B | 1 |

---

# CELL 11 — Menampilkan Hasil Cluster

```python
print(df[['Kabupaten_Kota', 'Cluster']].head())
```

## Fungsi

Menampilkan:

- nama kabupaten/kota
- cluster hasil K-Means

---

# CELL 12 — Evaluasi Akhir

```python
score = silhouette_score(feature, df['Cluster'])
print('Silhouette Score:', score)
```

## Fungsi

Mengukur kualitas cluster final.

---

# Interpretasi

Semakin tinggi:

- cluster makin baik
- pemisahan makin jelas

---

# CELL 13 — Visualisasi Cluster

```python
plt.figure(figsize=(8,6))

plt.scatter(
    df['Tingkat_Penduduk_Miskin'],
    df['PengeluaranPerKapita'],
    c=df['Cluster']
)

plt.xlabel('Tingkat Penduduk Miskin')
plt.ylabel('Pengeluaran Per Kapita')
plt.title('Visualisasi Cluster K-Means')

plt.show()
```

---

# Penjelasan

Menggunakan scatter plot untuk melihat persebaran cluster.

---

# Penjelasan Parameter

## Sumbu X

```python
df['Tingkat_Penduduk_Miskin']
```

---

## Sumbu Y

```python
df['PengeluaranPerKapita']
```

---

## Warna

```python
c=df['Cluster']
```

Warna berbeda menunjukkan cluster berbeda.

---

# Interpretasi Visualisasi

Jika warna terpisah jelas:

- clustering bagus

Jika bercampur:

- cluster kurang baik

---

# CELL 14 dan 15 — Mapping Label Cluster

Di bagian ini cluster angka diubah menjadi label.

Contoh:

```python
label_map = {0: 'Miskin', 1: 'Sangat Miskin'}
```

---

# Kenapa dilakukan?

Karena:

- angka cluster sulit dipahami
- label lebih mudah dibaca manusia

---

# Penting Sekali

K-Means sebenarnya tidak tahu:

- mana miskin
- mana sangat miskin

K-Means hanya memberi:

- Cluster 0
- Cluster 1

Manusia yang menginterpretasikan.

Ini sering ditanyakan saat presentasi.

---

# Pertanyaan Presentasi Penting

## Q: Apakah cluster 0 pasti miskin?

Tidak.

Nomor cluster hanyalah label otomatis.
Interpretasi dilakukan setelah analisis data.

---

# CELL 16 — GeoPandas dan Folium

```python
import geopandas as gpd
import pandas as pd
import folium
import re
```

---

# Penjelasan Library

## GeoPandas

Untuk mengolah data geospasial/peta.

---

## Folium

Untuk membuat peta interaktif.

---

## re

Untuk regular expression.

Digunakan membersihkan teks.

---

# Membaca Data Clustering

```python
df_clustering = pd.read_csv("/content/hasil_clustering_kemiskinan.csv")
```

Membaca hasil clustering.

---

# Rename Kolom

```python
df_clustering.rename(columns={'Kategori': 'Label'}, inplace=True)
```

Mengganti nama kolom.

---

# Membaca GeoJSON

```python
gdf_kota = gpd.read_file("/content/all_kabkota_ind(1).json")
```

Membaca data peta Indonesia.

---

# Fungsi clean_nama()

Digunakan untuk membersihkan nama wilayah.

Contoh:

- huruf besar
- menghapus simbol
- menyamakan format

Kenapa penting?

Karena nama wilayah sering tidak konsisten.

Contoh:

- Kab. Malang
- Kabupaten Malang

Kalau berbeda format:

- merge data gagal.

---

# CELL 17 — Wilayah Abu-Abu

```python
abu_abu = merged[merged["Label"].isna()]
```

## Penjelasan

Mencari wilayah yang gagal mendapatkan label.

Biasanya karena:

- nama tidak cocok
- data tidak ditemukan

---

# CELL 18 — Mengecek Jumlah Data

```python
print("Jumlah data clustering :", df_clustering["Kab/Kota"].nunique())
print("Jumlah geojson :", gdf_kota["name"].nunique())
```

## Fungsi

Memastikan jumlah wilayah sesuai.

---

# CELL 19 — Menghitung Jumlah Tiap Label

```python
print(df_clustering["Label"].value_counts())
```

## Fungsi

Menghitung jumlah tiap kategori.

Contoh:

| Label | Jumlah |
|---|---|
| Miskin | 40 |
| Sangat Miskin | 20 |

---

# Kekurangan Project yang Bisa Ditanyakan

## 1. Tidak Ada Scaling

Feature belum dinormalisasi.

Padahal K-Means sensitif terhadap skala.

Contoh:

- Pengeluaran = jutaan
- Persentase = puluhan

Maka fitur besar bisa mendominasi.

---

# Solusi yang Lebih Benar

Gunakan:

```python
from sklearn.preprocessing import StandardScaler
```

Lalu:

```python
scaler = StandardScaler()
feature_scaled = scaler.fit_transform(feature)
```

---

# Pertanyaan Presentasi Sangat Penting

## Q: Kenapa scaling penting pada K-Means?

Karena K-Means menggunakan jarak Euclidean.
Feature dengan nilai besar akan lebih dominan.

---

# 2. Train-Test Split Kurang Relevan

Untuk clustering biasanya tidak wajib.

---

# 3. Interpretasi Cluster Subjektif

Label “Miskin” dan “Sangat Miskin” adalah interpretasi manusia.

---

# Kelebihan Project

- Menggunakan evaluasi cluster
- Menggunakan elbow method
- Menggunakan silhouette score
- Ada visualisasi
- Ada integrasi peta geografis

---

# Ringkasan Alur Project

## Tahap 1 — Import Library

Mengimpor semua tools.

---

## Tahap 2 — Load Data

Membaca dataset CSV.

---

## Tahap 3 — Eksplorasi Data

Melihat info dan isi dataset.

---

## Tahap 4 — Menentukan Feature

Memilih variabel numerik.

---

## Tahap 5 — Menentukan Jumlah Cluster

Menggunakan:

- Elbow Method
- Silhouette Score

---

## Tahap 6 — Membuat Model K-Means

Melatih model clustering.

---

## Tahap 7 — Evaluasi

Mengukur kualitas cluster.

---

## Tahap 8 — Visualisasi

Scatter plot dan peta.

---

# Jawaban Cepat untuk Presentasi

## Apa tujuan project ini?

Mengelompokkan wilayah berdasarkan karakteristik kemiskinan menggunakan K-Means.

---

## Kenapa memakai K-Means?

Karena cocok untuk clustering data numerik.

---

## Apa itu cluster?

Kelompok data dengan karakteristik mirip.

---

## Apa fungsi elbow method?

Menentukan jumlah cluster optimal.

---

## Apa fungsi silhouette score?

Mengukur kualitas cluster.

---

## Apa kelemahan K-Means?

- sensitif terhadap outlier
- sensitif terhadap scaling
- harus menentukan jumlah cluster

---

## Kenapa memakai visualisasi?

Agar pola cluster lebih mudah dipahami.

---

# Penutup

Kalau kamu benar-benar memahami:

- alur project
- konsep K-Means
- inertia
- silhouette score
- centroid
- clustering
- scaling

maka kamu sudah jauh lebih siap untuk presentasi dan menghadapi pertanyaan teknis.

Yang paling penting saat presentasi bukan menghafal kode, tetapi memahami:

> “Kenapa langkah itu dilakukan?”

Karena biasanya penguji akan menggali alasan di balik setiap langkah.

