# Analisis Project Individu: Sentiment Analysis dengan Caikit dan HuggingFace

## Pendahuluan
Project ini bertujuan untuk menjalankan model analisis sentimen menggunakan framework Caikit dan library HuggingFace di lingkungan Google Colab. Dalam project ini, saya mencoba mengeksekusi server gRPC dan client dalam satu sel menggunakan threading atau subprocess untuk mempermudah pengujian. Namun, eksekusi ini menemui berbagai kendala yang memerlukan analisis lebih mendalam.

## Penjelasan Kode
### 1. requirements.txt:
- File ini berisi daftar dependensi yang dibutuhkan untuk menjalankan project.
- Library utama adalah `caikit[all]==0.9.2` yang menyediakan kerangka kerja untuk inference model.
- `scipy`, `torch`, dan `transformers` adalah library tambahan yang diperlukan untuk integrasi dengan model Hugging Face.
### 2. start_runtime.py:
- Script ini digunakan untuk memulai server runtime gRPC yang akan melayani permintaan inference.
- Struktur kode melibatkan import modul, konfigurasi logging, menambahkan `text_sentiment` ke `sys.path`, dan memulai server dengan `grpc_server.main()`.
### 3. client.py:
- Script ini berfungsi sebagai client gRPC untuk mengirimkan permintaan ke server yang telah dijalankan oleh `start_runtime.py`.
- Script membuat koneksi dengan server pada `localhost:8085`, membuat request dengan input teks, dan mencetak respons hasil analisis sentimen.

## Modifikasi untuk Eksekusi di Google Colab
Google Colab hanya dapat menjalankan satu sel kode secara bersamaan. Oleh karena itu, menjalankan `start_runtime.py` dan `client.py` di dua sel terpisah akan menyebabkan client tidak dapat terhubung dengan server. Solusinya adalah menjalankan server gRPC sebagai proses latar belakang di Colab.

## Analisis Kendala dan Solusi
### 1. Eksekusi Server dan Client secara Bersamaan
Selama percobaan, dijalankan server `gRPC` menggunakan skrip `start_runtime.py` dan client `gRPC` dari `client.py` dalam satu sel kode di Google Colab dengan threading atau subprocess. Namun, eksekusi ini menemui error seperti:
- **`_InactiveRpcError` dengan status `UNAVAILABLE`** yang mengindikasikan kegagalan koneksi ke server.
- Kegagalan koneksi pada `localhost` dengan port tertentu.

### 2. Analisis Penyebab Kendala
- **Isolasi Proses di Google Colab**: Google Colab memiliki batasan dalam menjalankan proses secara bersamaan dalam satu notebook. Threading atau subprocess di dalam satu sel tidak selalu dapat menjamin bahwa server `gRPC` akan berjalan stabil dan siap menerima permintaan dari client.
- **Pengaturan Port dan Koneksi**: Port yang digunakan server gRPC di lingkungan Colab mungkin dibatasi atau tidak mendukung koneksi localhost sepenuhnya.
- **Dependency**: Beberapa library yang digunakan oleh server mungkin tidak sepenuhnya kompatibel dengan eksekusi di Google Colab.

### 3. Solusi yang Dicoba
- **Menggunakan Sleep/Delay**: Menambahkan delay untuk memberi waktu kepada server agar siap menerima koneksi sebelum client mulai dijalankan.

## Kesimpulan
Percobaan ini menunjukkan bahwa menjalankan server dan client `gRPC` secara bersamaan dalam satu sel di Google Colab sulit dilakukan karena:
- Lingkungan eksekusi di Colab memiliki keterbatasan pada proses multi-threading.
- `gRPC` memerlukan pengaturan koneksi dan port yang stabil yang terkadang tidak didukung oleh Colab.

Solusi yang direkomendasikan adalah menjalankan server dan client di sel terpisah atau menggunakan framework server lain yang lebih fleksibel untuk pengujian di Colab. Hal ini mempermudah debugging dan memastikan bahwa server siap sepenuhnya sebelum client dihubungkan.

## Saran Pengembangan Lebih Lanjut
- **Deploy ke Server Eksternal**: Menjalankan server `gRPC` di server eksternal (contoh: VM di cloud) agar client di Colab dapat terhubung dengan lebih stabil.
- **Framework Alternatif**: Menggunakan server berbasis HTTP seperti Flask atau FastAPI untuk kemudahan integrasi dan pengujian di Colab.
