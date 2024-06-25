---
lab:
  title: Membuat model Document Intelligence tersusun
  module: Module 6 - Document Intelligence
---

# Membuat model Document Intelligence tersusun

Dalam latihan ini, Anda akan membuat dan melatih dua model kustom yang menganalisis berbagai formulir pajak. Kemudian, Anda akan membuat model yang disusun yang menyertakan kedua model kustom ini. Anda akan menguji model dengan mengirimkan formulir, lalu memeriksa apakah model mengenali jenis dokumen dan bidang berlabel dengan benar.

## Menyiapkan sumber daya

Kita akan menggunakan skrip untuk membuat sumber daya Azure AI Document Intelligence, akun penyimpanan dengan formulir sampel, dan grup sumber daya:

1. Memulai Visual Studio Code.
1. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` ke folder lokal (tidak masalah folder mana).
1. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda untuk memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai penulisnya** pada pop-up tersebut.
    
    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**. Jika ada pop-up lain dari Visual Studio Code, Anda dapat menutupnya dengan aman.

1. Luaskan folder **Labfiles** di panel kiri, dan klik kanan direktori **model-tersusun-03** Pilih opsi untuk membuka di terminal terintegrasi, lalu jalankan skrip berikut:

    ```powershell
    az login --output none
    ```

    > **Catatan**: Jika Anda mendapatkan kesalahan terkait tidak ada langganan aktif dan telah mengaktifkan MFA, Anda mungkin perlu masuk ke portal Azure di `https://portal.azure.com` terlebih dahulu, lalu jalankan ulang `az login`.

1. Saat diminta, masuk ke langganan Azure Anda. Kemudian kembali ke Visual Studio Code dan tunggu proses masuk selesai.
1. Di terminal terintegrasi, jalankan perintah berikut untuk menyiapkan sumber daya:

   ```powershell
   ./setup.ps1
   ```

   > **PENTING**: Sumber daya terakhir yang dibuat oleh skrip adalah layanan Azure AI Document Intelligence Anda. Jika perintah tersebut gagal karena Anda sudah memiliki sumber daya tingkat F0, gunakan sumber daya tersebut untuk lab ini atau buat secara manual menggunakan tingkat S0 di portal Azure.

## Membuat model kustom 1040 Formulir

Untuk membuat model yang disusun, pertama-tama kita harus membuat dua model kustom atau lebih. Untuk membuat model kustom pertama:

1. Di tab browser baru, mulai **Azure AI Document Intelligence Studio** di `https://documentintelligence.ai.azure.com/studio`
1. Gulir ke bawah, lalu di bagian **Model kustom**, pilih **Model ekstraksi kustom**.
1. Jika Anda diminta untuk masuk ke akun Anda, gunakan kredensial Azure Anda.
1. Jika Anda ditanya sumber daya Azure AI Document Intelligence mana yang akan digunakan, pilih langganan dan nama sumber daya yang Anda gunakan saat membuat sumber daya Azure AI Document Intelligence.
1. Di bagian **Proyek Saya**, pilih **+ Buat proyek**.
1. Di kotak teks **Nama proyek**, ketik **1040 Formulir**, lalu pilih **Lanjutkan**.
1. Di halaman **Konfigurasikan sumber daya layanan**, di daftar drop-down **Langganan**, pilih langganan Azure Anda.
1. Di daftar drop-down **Grup sumber daya**, pilih **DocumentIntelligenceResources&lt;xxxx&gt;** yang dibuat untuk Anda.
1. Di daftar drop-down **Sumber Daya Layanan Azure AI Document Intelligence atau Azure AI**, pilih **DocumentIntelligence&lt;xxxx&gt;**.
1. Di daftar drop-down **Versi API**, pastikan bahwa **2023-10-31-pratinjau** dipilih, lalu pilih **Lanjutkan**.
1. Di halaman **Sambungkan sumber daya pelatihan**, di daftar drop-down **Langganan**, pilih langganan Azure Anda.
1. Di daftar drop-down **Grup sumber daya**, pilih **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Di daftar drop-down **Akun penyimpanan**, pilih nama satu-satunya akun penyimpanan yang terdaftar. Jika Anda memiliki beberapa akun penyimpanan di dalam langganan, pilih akun penyimpanan yang dimulai dengan *docintelstorage*
1. Di daftar drop-down **Kontainer blob**, pilih **1040examples**, lalu pilih **Lanjutkan**.
1. Di halaman **Tinjau dan buat**, pilih **Buat proyek**.
1. Pilih **Jalankan tata letak** di pop-up *Mulai pelabelan sekarang*, lalu tunggu hingga analisis selesai.

## Memberi label model kustom 1040 Formulir

Sekarang, mari kita beri label bidang dalam formulir contoh:

1. Di halaman **Beri label data**, di kanan atas halaman, pilih **+ Tambahkan bidang**, lalu pilih **Bidang**.
1. Ketikkan **FirstName**, lalu tekan *Enter*.
1. Pilih dokumen bernama **f1040_1.pdf** di daftar sebelah kiri, pilih **John** lalu pilih **FirstName**.
1. Di kanan atas halaman, pilih **+ Tambahkan bidang**, lalu pilih **Bidang**.
1. Ketikkan **LastName**, lalu tekan *Enter*.
1. Dalam dokumen, pilih **Doe**, lalu pilih **LastName**.
1. Di kanan atas halaman, pilih **+ Tambahkan bidang**, lalu pilih **Bidang**.
1. Ketikkan **City**, lalu tekan *Enter*.
1. Dalam dokumen, pilih **Los Angeles**, lalu pilih **Kota**.
1. Di kanan atas halaman, pilih **+ Tambahkan bidang**, lalu pilih **Bidang**.
1. Ketikkan **NegaraBagian**, lalu tekan *Enter*.
1. Dalam dokumen, pilih **CA**, lalu pilih **NegaraBagian**.
1. Ulangi proses pemberian label untuk formulir yang tersisa dalam daftar di sebelah kiri, menggunakan label yang telah Anda buat. Beri label empat bidang yang sama: *FirstName*, *LastName*, *City*, dan *State*.

> **PENTING** Untuk tujuan latihan ini, kita hanya menggunakan lima formulir contoh dan hanya memberi label empat bidang. Dalam model dunia nyata, Anda harus menggunakan sebanyak mungkin sampel untuk memaksimalkan akurasi dan keyakinan prediksi Anda. Anda juga harus memberi label semua bidang yang tersedia dalam formulir, bukan hanya empat bidang.

## Melatih model kustom 1040 Formulir

Sekarang setelah formulir sampel diberi label, kita dapat melatih model kustom pertama:

1. Di Azure AI Document Intelligence Studio, pilih **Latih**.
1. Dalam dialog **Latih model baru**, di kotak teks **ID Model**, ketik **1040FormsModel**.
1. Dalam daftar drop-down **Mode build**, pilih **Template**, lalu pilih **Latih**. 
1. Dalam dialog **Pelatihan sedang berlangsung**, pilih **Buka Model**.

## Membuat model kustom 1099 Formulir

Sekarang, Anda harus membuat model kedua, yang akan Anda latih pada contoh formulir pajak 1099:

1. Di Azure AI Document Intelligence Studio, pilih **Model ekstraksi kustom**.
1. Di bagian **Proyek Saya**, pilih **+ Buat proyek**.
1. Di kotak teks **Nama proyek**, ketik **1099 Formulir**, lalu pilih **Lanjutkan**.
1. Di halaman **Konfigurasikan sumber daya layanan**, di daftar drop-down **Langganan**, pilih langganan Azure Anda.
1. Di daftar drop-down **Grup sumber daya**, pilih **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Di daftar drop-down **Sumber Daya Layanan Azure AI Document Intelligence atau Azure AI**, pilih **DocumentIntelligence&lt;xxxx&gt;**.
1. Di daftar drop-down **Versi API**, pastikan bahwa **20223-10-31-pratinjau** dipilih, lalu pilih **Lanjutkan**.
1. Di halaman **Sambungkan sumber daya pelatihan**, di daftar drop-down **Langganan**, pilih langganan Azure Anda.
1. Di daftar drop-down **Grup sumber daya**, pilih **DocumentIntelligenceResources&lt;xxxx&gt;**.
1. Di daftar drop-down **Akun penyimpanan**, pilih nama satu-satunya akun penyimpanan yang terdaftar.
1. Dalam daftar drop-down **Kontainer blob**, pilih **1099examples**, lalu pilih **Lanjutkan**.
1. Di halaman **Tinjau dan buat**, pilih **Buat proyek**.
1. Pilih **Jalankan tata letak** di pop-up *Mulai pelabelan sekarang*, lalu tunggu hingga analisis selesai.

## Memberi label model kustom 1099 Formulir

Sekarang, beri label formulir contoh dengan beberapa bidang:

1. Di halaman **Beri label data**, di kanan atas halaman, pilih **+ Tambahkan bidang**, lalu pilih **Bidang**.
1. Ketikkan **FirstName**, lalu tekan *Enter*.
1. Pilih dokumen bernama **f1099msc_payer.pdf** di daftar sebelah kiri, pilih **John** lalu pilih **FirstName**.
1. Di kanan atas halaman, pilih **+ Tambahkan bidang**, lalu pilih **Bidang**.
1. Ketikkan **LastName**, lalu tekan *Enter*.
1. Dalam dokumen, pilih **Doe**, lalu pilih **LastName**.
1. Di kanan atas halaman, pilih **+ Tambahkan bidang**, lalu pilih **Bidang**.
1. Ketikkan **City**, lalu tekan *Enter*.
1. Dalam dokumen, pilih **Haven baru** lalu pilih **Kota**.
1. Di kanan atas halaman, pilih **+ Tambahkan bidang**, lalu pilih **Bidang**.
1. Ketikkan **NegaraBagian**, lalu tekan *Enter*.
1. Dalam dokumen, pilih **CT**, lalu pilih **NegaraBagian**.
1. Ulangi proses pemberian label untuk formulir yang tersisa dalam daftar di sebelah kiri. Beri label empat bidang yang sama: *FirstName*, *LastName*, *City*, dan *State*.

## Melatih model kustom 1099 Formulir

Sekarang, Anda dapat melatih model kustom kedua:

1. Di Azure AI Document Intelligence Studio, pilih **Latih**.
1. Dalam dialog **Latih model baru**, di kotak teks **ID Model**, ketik **1099FormsModel**.
1. Dalam daftar drop-down **Mode build**, pilih **Template**, lalu pilih **Latih**.
1. Dalam dialog **Pelatihan sedang berlangsung**, pilih **Buka Model**.
1. Proses ini memerlukan waktu beberapa menit. Refresh browser sesekali hingga kedua model menampilkan status **berhasil**.

## Membuat dan merakit model yang disusun

Dua model kustom, yang menganalisis 1040 dan 1099 formulir pajak, sekarang telah selesai. Anda dapat melanjutkan untuk membuat model yang disusun:

1. Di halaman Model Azure AI Document Intelligence, pilih **1040FormsModel** dan **1099FormsModel**.
1. Di bagian atas daftar model, pilih **Buat**.
1. Dalam dialog **Buat model baru**, di kotak teks **ID Model**, ketik **TaxFormsModel**, lalu pilih **Buat**. Azure AI Document Intelligence membuat model tersusun dan menampilkannya dalam daftar model kustom:

## Menggunakan model yang disusun

Sekarang setelah model yang disusun selesai, mari kita uji dengan formulir contoh:

1. Di Azure AI Document Intelligence Studio, pilih halaman **Uji**, pilih **TaxFormsModel** dari menu dropdown.
1. Pilih **Telusuri file** lalu telusuri lokasi tempat Anda mengkloning repositori.
1. Pilih **03-composed-model/trainingdata/TestDoc/f1040_7.pdf**, lalu pilih **Buka**.
1. Pilih **Jalankan analisis**. Azure AI Document Intelligence menganalisis formulir dengan menggunakan model tersusun.
1. Dokumen yang Anda analisis adalah contoh formulir pajak 1040. Periksa properti **DocType** untuk melihat apakah model kustom yang benar telah digunakan. Periksa juga nilai **FirstName**, **LastName**, **City**, dan **State** yang diidentifikasi oleh model.

## Membersihkan sumber daya

Sekarang setelah mengetahui cara kerja model yang disusun, mari hapus sumber daya yang Anda buat di langganan Azure.

1. Di **portal Azure** di `https://portal.azure.com/`, pilih **Grup sumber daya**.
1. Di daftar **Grup sumber daya**, pilih **DocumentIntelligenceResources&lt;xxxx&gt;** yang telah Anda buat, lalu pilih **Hapus grup sumber daya**.
1. Di kotak teks **KETIK NAMA GRUP SUMBER DAYA**, ketik nama grup sumber daya, lalu pilih **Hapus** untuk menghapus sumber daya dan akun penyimpanan Document Intelligence.

## Pelajari lebih lanjut

- [Buat model kustom](/azure/ai-services/document-intelligence/concept-composed-models)
