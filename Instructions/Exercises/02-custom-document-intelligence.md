---
lab:
  title: Mengekstrak Data dari Formulir
  module: Module 6 - Document Intelligence
---

# Mengekstrak Data dari Formulir

Misalkan perusahaan saat ini mengharuskan karyawan untuk membeli lembar pesanan secara manual dan memasukkan data ke dalam database. Mereka ingin Anda menggunakan layanan AI untuk meningkatkan proses entri data. Anda memutuskan untuk membangun model pembelajaran mesin yang akan membaca formulir dan menghasilkan data terstruktur yang dapat digunakan untuk memperbarui database secara otomatis.

**Azure AI Document Intelligence** adalah layanan Azure AI yang memungkinkan pengguna membuat perangkat lunak pemrosesan data otomatis. Perangkat lunak ini dapat mengekstrak teks, pasangan kunci/nilai, dan tabel dari dokumen formulir menggunakan pengenalan karakter optik (OCR). Azure AI Document Intelligence memiliki model bawaan untuk mengenali faktur, tanda terima, dan kartu nama. Layanan ini juga menyediakan kemampuan untuk melatih model kustom. Dalam latihan ini, kita akan fokus membangun model kustom.

## Bersiap untuk mengembangkan aplikasi di Visual Studio Code

Sekarang mari gunakan SDK layanan untuk mengembangkan aplikasi menggunakan Visual Studio Code. File kode untuk aplikasi Anda telah disediakan di repositori GitHub.

> **Tips**: Jika Anda sudah mengkloning repositori **mslearn-ai-document-intelligence**, buka di Visual Studio Code. Jika tidak, ikuti langkah-langkah ini untuk mengkloningnya ke lingkungan pengembangan Anda.

1. Memulai Visual Studio Code.
1. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` ke folder lokal (tidak masalah folder mana).
1. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda untuk memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai penulisnya** pada pop-up tersebut.

1. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**. Jika Anda menerima Pesan *Terdeteksi Proyek Azure Function di folder*, Anda dapat menutup pesan tersebut dengan aman.

## Membuat sumber daya Azure AI Document Intelligence

Untuk menggunakan layanan Azure AI Document Intelligence, Anda memerlukan sumber daya Azure AI Document Intelligence atau Layanan Azure AI di langganan Azure Anda. Anda akan menggunakan portal Microsoft Azure untuk membuat sumber daya.

1. Di tab browser, buka portal Azure di `https://portal.azure.com`, masuk dengan akun Microsoft yang terkait dengan langganan Azure Anda.
1. Di beranda portal Azure, navigasikan ke kotak pencarian atas dan ketik **Document Intelligence** lalu tekan **Enter**.
1. Di halaman **Document Intelligence**, pilih **Buat**.
1. Di halaman **Buat Document Intelligence**, gunakan hal berikut untuk mengonfigurasi sumber daya Anda:
    - **Langganan**: Langganan Azure Anda.
    - **Grup sumber daya**: Pilih atau buat grup sumber daya dengan nama unik seperti *DocIntelligenceResources*.
    - **Wilayah**: Pilih wilayah di dekat Anda.
    - **Nama**: Masukkan nama yang unik secara global.
    - **Tingkat harga**: pilih **F0 gratis** (jika Anda tidak memiliki tingkat Gratis yang tersedia, pilih **S0 standar**).
1. Lalu pilih **Tinjau + buat** dan **Buat**. Tunggu selagi Azure membuat sumber daya Azure AI Document Intelligence.
1. Saat penyebaran selesai, pilih **Buka sumber daya** untuk melihat halaman **Gambaran umum** sumber daya. 

## Mengumpulkan dokumen untuk pelatihan

Anda akan menggunakan formulir sampel seperti formulir ini untuk melatih pengujian model: 

![Gambar faktur yang digunakan dalam proyek ini.](../media/Form_1.jpg)

1. Kembali ke **Visual Studio Code**. Di panel *Penjelajah*, buka folder **Labfiles/02-custom-document-intelligence** dan perluas folder **sample-forms**. Perhatikan ada file yang berakhiran **.json** dan **.jpg** di folder.

    Anda akan menggunakan file **.jpg** untuk melatih model Anda.  

    File **.json** telah dibuat untuk Anda dan berisi informasi label. File akan diunggah ke dalam kontainer penyimpanan blob Anda bersama formulir.

    Anda dapat melihat gambar yang kami gunakan di folder *sample-forms* dengan memilihnya di Visual Studio Code.

1. Kembali ke **portal Azure** dan navigasikan ke halaman **Gambaran umum** sumber daya Anda jika Anda belum membukanya. Di bawah bagian *Esensial*, lihat **Grup sumber daya** tempat Anda membuat sumber daya Document Intelligence.

1. Pada halaman **Ringkasan** untuk grup sumber daya Anda, catat **ID Langganan** dan **Lokasi**. Anda akan memerlukan nilai-nilai ini, bersama dengan nama **grup sumber daya** Anda dalam langkah-langkah berikutnya.

    ![Contoh halaman grup sumber daya.](../media/resource_group_variables.png)

1. Di Visual Studio Code, di panel *Penjelajah*, telusuri folder **Labfiles/02-custom-document-intelligence** dan perluas folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi tempat Anda akan mengintegrasikan fungsionalitas Azure OpenAI.

1. Klik kanan folder **CSharp** atau **Python** yang berisi file kode Anda dan pilih **Buka terminal terintegrasi**. 

1. Di terminal, jalankan perintah berikut untuk masuk ke Azure. Perintah **az login** akan membuka browser Microsoft Edge, masuk dengan akun yang sama dengan yang Anda gunakan untuk membuat sumber daya Azure AI Document Intelligence. Setelah Anda masuk, tutup jendela browser.

    ```powershell
    az login
    ```

1. Kembali ke Visual Studio Code. Di panel terminal, jalankan perintah berikut untuk membuat daftar lokasi Azure.

    ```powershell
    az account list-locations -o table
    ```

1. Pada output, temukan nilai **Name** yang sesuai dengan lokasi grup sumber daya Anda (misalnya, untuk *East US* nama yang sesuai adalah *eastus*).

    > **Penting**: Rekam nilai **Nama** dan gunakan di Langkah 11.

1. Di Visual Studio Code, di folder **Labfiles/02-custom-document-intelligence**, pilih **setup.cmd**. Anda akan menggunakan skrip ini untuk menjalankan perintah antarmuka baris perintah (CLI) Azure yang diperlukan untuk membuat sumber daya Azure lain yang Anda butuhkan.

1. Di skrip **setup.cmd**, tinjau perintah. Program ini akan:
    - Buat akun penyimpanan di grup sumber daya Azure Anda
    - Upload file dari folder *sampleforms* lokal Anda ke kontainer yang disebut *sampleforms* di akun penyimpanan
    - Cetak URI Tanda Tangan Akses Bersama

1. Ubah deklarasi variabel **subscription_id**, **resource_group**, dan **lokasi** dengan nilai yang sesuai untuk langganan, grup sumber daya, dan nama lokasi tempat Anda menyebarkan sumber daya Document Intelligence.
Kemudian **simpan** perubahan Anda.

    Biarkan variabel **expiry_date** seperti untuk latihan. Variabel ini digunakan saat membuat URI Tanda Tangan Akses Bersama (SAS). Dalam praktiknya, Anda akan ingin menetapkan tanggal kedaluwarsa yang sesuai untuk SAS Anda. Anda dapat mempelajari lebih lanjut tentang SAS [di sini](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).  

1. Di terminal untuk folder **Labfiles/02-custom-document-intelligence**, masukkan perintah berikut untuk menjalankan skrip:

    ```PowerShell
    $currentdir=(Get-Item .).FullName
    cd ..
    ./setup.cmd
    cd $currentdir

    ```

1. Saat skrip selesai, tinjau output yang ditampilkan.

1. Di portal Azure, refresh grup sumber daya dan verifikasi bahwa grup sumber daya tersebut berisi akun Azure Storage yang baru saja dibuat. Buka akun penyimpanan dan di panel sebelah kiri, pilih **Browser penyimpanan**. Kemudian di Browser Penyimpanan, perluas **Kontainer blob** dan pilih kontainer **sampleforms** untuk memverifikasi bahwa file telah diunggah dari folder **02-custom-document-intelligence/sample-forms** lokal Anda.

## Melatih model menggunakan Document Intelligence Studio

Sekarang Anda akan melatih model menggunakan file yang diunggah ke akun penyimpanan.

1. Di browser Anda, navigasikan ke Document Intelligence Studio di `https://documentintelligence.ai.azure.com/studio`.
1. Gulir ke bawah ke bagian **Model kustom** dan pilih petak **Model ekstraksi kustom**.
1. Jika Anda diminta untuk masuk ke akun Anda, gunakan kredensial Azure Anda.
1. Jika Anda ditanya sumber daya Azure AI Document Intelligence mana yang akan digunakan, pilih langganan dan nama sumber daya yang Anda gunakan saat membuat sumber daya Azure AI Document Intelligence.
1. Di bagian **Proyek Saya**, pilih **Buat proyek**. Gunakan konfigurasi berikut:

    - **Nama proyek**: Masukkan nama yang unik.
        - Pilih *Lanjutkan*.
    - **Konfigurasikan sumber daya layanan**: Pilih langganan, grup sumber daya, dan sumber daya document intelligence yang Anda buat sebelumnya di lab ini. Centang kotak *Atur sebagai default*. Pertahankan versi API default. 
        - Pilih *Lanjutkan*.
    - **Menyambungkan sumber data pelatihan**: Pilih langganan, grup sumber daya, dan akun penyimpanan yang dibuat oleh skrip penyiapan. Centang kotak *Atur sebagai default*. Pilih kontainer blob `sampleforms`, dan biarkan jalur folder kosong.
        - Pilih *Lanjutkan*.
    - Pilih *Buat proyek*

1. Setelah proyek Anda dibuat, pilih **Latih** untuk melatih model Anda. Gunakan konfigurasi berikut:
    - **ID Model**: *Berikan nama unik secara global (Anda memerlukan nama ID model di langkah berikutnya)*. 
    - **Mode Pembuatan**: Templat.
1. Pelatihan dapat memerlukan waktu lama. Anda akan melihat pemberitahuan ketika sudah selesai.

## Menguji model Document Intelligence kustom

1. Kembali ke Visual Studio Code. Di terminal, instal paket Document Intelligence dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**:

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

1. Di Visual Studio Code, di folder **Labfiles/02-custom-document-intelligence**, pilih bahasa yang Anda gunakan. Edit file konfigurasi (**appsettings.json** atau **.env**, bergantung pada preferensi bahasa Anda) dengan nilai berikut:
    - Titik akhir Document Intelligence Anda.
    - Kunci Document Intelligence Anda.
    - ID Model yang dibuat yang Anda berikan saat melatih model Anda. Anda dapat menemukannya di halaman **Model** dari Document Intelligence Studio. **Simpan** perubahan Anda.

1. Di Visual Studio Code, buka file kode untuk aplikasi klien Anda (*Program.cs* untuk C#, *test-model.py* untuk Python) dan tinjau kode yang menampungnya, terutama gambar di URL yang mengacu pada file dalam repositori GitHub ini di web.

1. Kembali ke terminal, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```powershell
    dotnet build
    dotnet run
    ```

    **Python**

    ```powershell
    python test-model.py
    ```

1. Lihat output dan amati bagaimana output untuk model menyediakan nama bidang seperti `Merchant` dan `CompanyPhoneNumber`.

## Penghapusan

Jika sudah selesai dengan sumber daya Azure Anda, ingatlah untuk menghapus sumber daya di [portal Azure](https://portal.azure.com/?azure-portal=true) untuk menghindari biaya lebih lanjut.

## Informasi selengkapnya

Untuk mengetahui informasi selengkapnya tentang layanan Document Intelligence, lihat [Dokumentasi Document Intelligence](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true).
