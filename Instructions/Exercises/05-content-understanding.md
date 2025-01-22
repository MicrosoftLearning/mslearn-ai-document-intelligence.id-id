---
lab:
  title: Menganalisis konten dengan Pemahaman Konten Azure AI
  module: Multimodal analysis with Content Understanding
---

# Menganalisis konten dengan Pemahaman Konten Azure AI

Dalam latihan ini, Anda menggunakan portal Azure AI Foundry untuk membuat proyek Pemahaman Konten yang dapat mengekstrak informasi dari formulir kebijakan asuransi perjalanan. Anda kemudian akan menguji penganalisis konten Anda di Portal Azure AI Foundry dan menggunakannya melalui antarmuka REST Pemahaman Konten.

Latihan ini memakan waktu sekitar **30** menit.

## Membuat proyek Pemahaman Konten

Mari kita mulai dengan menggunakan portal Azure AI Foundry untuk membuat proyek Pemahaman Konten.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda.

    Beranda Azure AI Foundry terlihat mirip dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](../media/ai-foundry-portal.png)

1. Di bagian **Temukan dengan cepat** dari halaman beranda, di bagian bawah, pilih **Pemahaman Konten**.
1. Pada halaman **Pemahaman Konten**, pilih tombol **Buat proyek Pemahaman Konten baru**.
1. Pada langkah **Ringkasan proyek**, atur properti berikut untuk proyek Anda; lalu pilih **Berikutnya**:
    - **Nama Proyek**:`travel-insurance`
    - **Deskripsi**: `Insurance policy data extraction`
    - **Hub**: Buat hub baru
1. Pada langkah **Buat hub**, atur properti berikut ini lalu pilih **Berikutnya**:
    - **Sumber Daya Hub Azure AI**: `content-understanding-hub`
    - **Langganan Azure**: *Pilih langganan Azure Anda*
    - **Grup sumber daya**: *Buat grup sumber daya baru dengan nama yang sesuai*
    - **Lokasi**: *Pilih lokasi yang tersedia*
    - **Layanan Azure AI**: *Buat sumber daya layanan Azure AI baru dengan nama yang sesuai*
1. Pada langkah **Pengaturan penyimpanan**, tentukan akun penyimpanan Hub AI baru lalu pilih **Berikutnya**.
1. Pada halaman **Tinjau**, pilih **Buat proyek**. Kemudian tunggu hingga proyek dan sumber daya terkait dibuat.

    Ketika proyek siap, proyek akan terbuka di halaman **Tentukan skema**.

    ![Cuplikan layar proyek Pemahaman Konten baru.](../media/content-understanding-project.png)

## Tinjau sumber daya Azure

Saat Anda membuat AI Hub dan proyek, berbagai sumber daya dibuat di langganan Azure Anda untuk mendukung proyek.

1. Di browser web, buka [portal Azure](https://portal.azure.com) di `https://portal.azure.com` dan masuk menggunakan kredensial Azure Anda.
1. Telusuri grup sumber daya yang Anda buat untuk hub Anda, dan catat sumber daya Azure yang telah dibuat.

    ![Cuplikan layar sumber daya Azure.](../media/azure-resources.png)

## Menentukan skema kustom

Anda akan membangun penganalisis yang dapat mengekstrak informasi dari formulir asuransi perjalanan. Anda akan mulai dengan menentukan skema berdasarkan formulir sampel.

1. Unduh formulir sampel [train-form.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/train-form.pdf) dari `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/train-form.pdf` dan simpan di folder lokal.
1. Kembali ke tab browser yang berisi proyek Pemahaman konten Anda, dan pada halaman **Tentukan skema**, unggah file **train-form.pdf** yang baru saja Anda unduh.
1. Pilih templat **Analisis dokumen**, lalu pilih **Buat**.

    Editor skema menyediakan cara untuk menentukan bidang data yang akan diekstrak dari formulir, yang ditampilkan di sebelah kanan. Formatnya terlihat seperti ini:

    ![Cuplikan layar formulir asuransi sampel.](../media/train-form.png)

    Bidang data dalam formulir terdiri dari:
    
    - Kumpulan detail pribadi yang berkaitan dengan pemegang kebijakan.
    - Kumpulan detail yang terkait dengan perjalanan yang diperlukan asuransi.
    - Tanda tangan dan tanggal

    Kita akan mulai dengan menambahkan bidang yang mewakili detail pribadi sebagai tabel, di mana kita kemudian akan menentukan subbidang untuk detail individual.

1. Pilih **+ Tambahkan bidang baru**, lalu buat bidang baru dengan nilai berikut:
    - **Nama bidang**: `PersonalDetails`
    - **Deskripsi bidang**: `Policyholder information`
    - **Jenis nilai**: Tabel
1. Pilih **Simpan Perubahan** (&#10004;) dan perhatikan bahwa subbidang baru dibuat secara otomatis.
1. Konfigurasikan subbidang baru dengan nilai berikut:
    - **Nama bidang**: `PolicyholderName`
    - **Deskripsi bidang**: `Policyholder name`
    - **Jenis nilai**: String
    - **Metode**: Ekstrak
1. Gunakan tombol **+ Tambahkan subbidang baru** untuk menambahkan subbidang tambahan berikut:

    | Nama bidang | Deskripsi bidang | Jenis nilai | Metode |
    |--|--|--|--|
    | `StreetAddress` | `Policyholder address` | String | Ekstrak |
    | `City` | `Policyholder city` | String | Ekstrak |
    | `PostalCode` | `Policyholder post code` | String | Ekstrak |
    | `CountryRegion` | `Policyholder country or region` | String | Ekstrak |
    | `DateOfBirth` | `Policyholder birth date` | Publikasi | Ekstrak |

1. Ketika Anda telah menambahkan semua subbidang detail pribadi, gunakan tombol **Kembali** untuk kembali ke tingkat atas skema.
1. Tambahkan bidang *tabel* baru bernama **`TripDetails`** untuk merepresentasikan rincian perjalanan yang diasuransikan. Kemudian tambahkan subbidang berikut ke dalamnya.

    | Nama bidang | Deskripsi bidang | Jenis nilai | Metode |
    |--|--|--|--|
    | `DestinationCity` | `Trip city` | String | Ekstrak |
    | `DestinationCountry` | `Trip country or region` | String | Ekstrak |
    | `DepartureDate` | `Date of departure` | Publikasi | Ekstrak |
    | `ReturnDate` | `Date of return` | Publikasi | Ekstrak |

1. Kembali ke tingkat atas skema dan tambahkan dua bidang individual berikut:

    | Nama bidang | Deskripsi bidang | Jenis nilai | Metode |
    |--|--|--|--|
    | `Signature` | `Policyholder signature` | String | Ekstrak |
    | `Date` | `Date of signature` | Publikasi | Ekstrak |

1. Verifikasi bahwa skema Anda yang telah selesai terlihat seperti ini, lalu simpan.

    ![Cuplikan layar skema dokumen.](../media/completed-schema.png)

1. Pada halaman **Pengujian Penganalisis**, jika analisis tidak dimulai secara otomatis, pilih **Jalankan analisis**. Kemudian tunggu hingga analisis selesai dan tinjau nilai teks pada formulir yang diidentifikasi cocok dengan bidang dalam skema.

    ![Cuplikan layar hasil pengujian penganalisis.](../media/test-analyzer.png)

    Layanan Pemahaman Konten seharusnya telah mengidentifikasi dengan benar teks yang sesuai dengan bidang dalam skema. Jika belum melakukannya, Anda dapat menggunakan halaman **Data label** untuk mengunggah formulir sampel lain dan secara eksplisit mengidentifikasi teks yang benar untuk setiap bidang.

## Membangun dan menguji penganalisis

Setelah Anda melatih model untuk mengekstrak bidang dari formulir asuransi, Anda dapat membangun penganalisis untuk digunakan dengan formulir serupa.

1. Pada panel navigasi di sebelah kiri, pilih halaman **Bangun penganalisis**.
1. Pilih **+ Bangun penganalisis** dan bangun penganalisis baru dengan properti berikut (diketik persis seperti yang ditunjukkan di sini):
    - **Nama**: `travel-insurance-analyzer`
    - **Deskripsi**: `Insurance form analyzer`
1. Tunggu hingga penganalisis baru siap (gunakan tombol **Refresh** untuk memeriksa).
1. Unduh [test-form.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/test-form.pdf) dari `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/test-form.pdf` dan simpan di folder lokal.
1. Kembali ke halaman **Bangun penganalisis** dan pilih tautan **travel-insurance-analyzer** . Bidang yang ditentukan dalam skema penganalisis akan ditampilkan.
1. Di halaman **travel-insurance-analyzer**, pilih **Pengujian**.
1. Gunakan tombol **+ Unggah file pengujian** untuk mengunggah **test-form.pdf** dan menjalankan analisis untuk mengekstrak data bidang dari formulir pengujian.

    ![Cuplikan layar hasil analisis formulir pengujian.](../media/test-form-results.png)

1. Lihat tab **Hasil** untuk melihat hasil berformat JSON yang dikembalikan oleh penganalisis. Dalam tugas berikutnya, Anda akan menggunakan REST API Pemahaman Konten untuk mengirimkan formulir ke penganalisis Anda dan mengembalikan hasil dalam format ini.
1. Tutup halaman **travel-insurance-analyzer** .

## Menggunakan REST API Pemahaman Konten

Setelah membuat penganalisis, Anda dapat menggunakannya dari aplikasi klien melalui REST API Pemahaman Konten.

1. Beralih ke tab browser yang berisi portal Azure (atau buka `https://portal.azure.com` di tab baru jika Anda telah menutupnya).
1. Di grup sumber daya untuk hub Pemahaman Konten Anda, buka sumber daya **layanan Azure AI**.
1. Pada halaman **Gambaran Umum**, di bagian **Kunci dan titik akhir**, lihat tab **Pemahaman Konten**.

    ![Cuplikan layar kunci dan titik akhir untuk Pemahaman Konten.](../media/keys-and-endpoint.png)

    Anda akan memerlukan titik akhir Pemahaman Konten dan salah satu kunci untuk terhubung ke penganalisis Anda dari aplikasi klien.

1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***PowerShell***. Shell cloud menyediakan antarmuka baris perintah di panel di bagian bawah portal Azure, seperti yang ditunjukkan di sini:

    ![Cuplikan layar portal Azure dengan panel cloud shell.](../media/cloud-shell.png)

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *Bash* , alihkan ke ***PowerShell***.

1. Perhatikan bahwa Anda dapat mengubah ukuran cloud shell dengan menyeret bilah pemisah di bagian atas panel, atau menggunakan ikon **&#8212;**, **&#10530;**, dan **X** di kanan atas panel untuk meminimalkan, memaksimalkan, dan menutup panel. Untuk informasi selengkapnya tentang menggunakan Azure Cloud Shell, lihat [dokumentasi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).
1. Di toolbar cloud shell, di menu **Pengaturan**, pilih **Buka versi Klasik** (ini diperlukan untuk menggunakan editor kode).

1. Di panel PowerShell, masukkan perintah berikut untuk mengkloning repositori GitHub untuk latihan ini:

    ```
    rm -r mslearn-ai-doc -f
    git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

1. Setelah repositori dikloning, navigasikan ke folder **mslearn-ai-doc/Labfiles/05-content-understanding/code**:

    ```
    cd mslearn-ai-doc/Labfiles/05-content-understanding/code
    ```

1. Masukkan perintah berikut untuk mengedit file kode Python **analyze_doc.py** yang telah disediakan:

    ```
    code analyze_doc.py
    ```
    File kode Python dibuka di editor kode:

    ![Cuplikan layar editor kode dengan kode Python.](../media/code-editor.png)

1. Dalam file kode, ganti **\<CONTENT_UNDERSTANDING_ENDPOINT\>** tempat penampung dengan titik akhir Pemahaman Konten, dan **\<CONTENT_UNDERSTANDING_KEY\>** tempat penampung dengan salah satu kunci untuk sumber daya layanan Azure AI Anda.

    > **Tips**: Anda harus mengubah ukuran atau meminimalkan jendela cloud shell untuk menyalin titik akhir dan kunci dari halaman sumber daya layanan Azure AI di portal Azure - berhati-hatilah untuk tidak *menutup* cloud shell (atau Anda harus mengulangi langkah-langkah di atas)

1. Setelah Anda mengganti tempat penampung, gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda lalu tinjau kode yang telah selesai, yang mana:
    - Mengirimkan permintaan HTTP POST ke titik akhir Pemahaman Konten Anda, menginstruksikan **travel-insurance-analyzer** untuk menganalisis formulir berdasarkan URL-nya.
    - Memeriksa respons dari operasi POST untuk mengambil ID untuk operasi analisis.
    - Berulang kali mengirimkan permintaan HTTP GET ke layanan Pemahaman Konten Anda hingga operasi tidak lagi berjalan.
    - Jika operasi telah berhasil, menampilkan respons JSON.
1. Gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell terbuka.
1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menginstal pustaka **permintaan** Python (yang digunakan dalam kode):

    ```
    pip install requests
    ```

1. Setelah pustaka diinstal, di panel baris perintah cloud shell, masukkan perintah berikut untuk menjalankan kode Python:

    ```
    python analyze_doc.py
    ```

1. Tinjau output dari program, yang mencakup hasil JSON dari analisis dokumen.

    > **Tips**: Buffer layar di konsol cloud shell mungkin tidak cukup besar untuk menampilkan seluruh output. Jika Anda ingin meninjau seluruh output, jalankan program menggunakan perintah `python analyze_doc.py > output.txt`. Kemudian, ketika program selesai, gunakan perintah `code output.txt` untuk membuka output di editor kode.

## Penghapusan

Setelah selesai bekerja dengan layanan Pemahaman Konten, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Di portal Azure AI Foundry, navigasikan ke proyek **asuransi perjalanan**, lalu hapus.
1. Di portal Azure, hapus grup sumber daya yang Anda buat dalam latihan ini.

