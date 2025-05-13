---
lab:
  title: Menganalisis konten dengan Pemahaman Konten Azure AI
  module: Multimodal analysis with Content Understanding
---

# Menganalisis konten dengan Pemahaman Konten Azure AI

Dalam latihan ini, Anda menggunakan portal Azure AI Foundry untuk membuat proyek Pemahaman Konten yang dapat mengekstrak informasi dari faktur. Anda kemudian akan menguji penganalisis konten Anda di Portal Azure AI Foundry dan menggunakannya melalui antarmuka REST Pemahaman Konten.

Latihan ini memakan waktu sekitar **30** menit.

## Membuat proyek Azure OpenAI

Mari kita mulai dengan membuat proyek Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tip atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke halaman beranda, yang terlihat sama dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](../media/ai-foundry-portal.png)

1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, masukkan nama proyek yang valid untuk proyek Anda, dan jika hub yang ada disarankan, pilih opsi untuk membuat yang baru. Kemudian tinjau sumber daya Azure yang akan dibuat secara otomatis untuk mendukung hub dan proyek Anda.
1. Pilih **Kustomisasi** dan tentukan pengaturan berikut untuk hub Anda:
    - **Nama hub** : *Nama yang valid untuk hub Anda*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Buat atau pilih grup sumber daya*
    - **Wilayah**: Pilih salah satu wilayah berikut\*
        - US Barat
        - Swedia Tengah
        - Australia Timur
    - **Sambungkan Azure AI Services atau Azure OpenAI**: *Buat sumber daya Layanan AI baru*
    - **Menyambungkan Pencarian Azure AI**: *Membuat sumber daya Pencarian Azure AI baru dengan nama unik*

    > \*Pada saat penulisan, pemahaman Konten Azure AI hanya dapat diakses di wilayah ini.

1. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Lalu pilih **Buat** dan tunggu hingga prosesnya selesai.
1. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek di portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](../media/ai-foundry-project.png)

## Membuat penganalisis Pemahaman Konten

Anda akan membangun penganalisis yang dapat mengekstrak informasi dari faktur. Anda akan mulai dengan menentukan skema berdasarkan faktur sampel.

1. Di tab browser baru, unduh formulir sampel [invoice-1234.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf) dari `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf` dan simpan di folder lokal.
1. Kembali ke tab yang berisi halaman beranda untuk proyek Azure AI Foundry Anda; dan di panel navigasi di sebelah kiri, pilih **Pemahaman Konten**.
1. Pada halaman **Pemahaman Konten**, pilih tab **Penganalisis kustom** di bagian atas.
1. Pada halaman Penganalisis kustom Pemahaman Konten, pilih **+ Buat**, dan buat tugas dengan pengaturan berikut:
    - **Nama tugas**: Analisis faktur
    - **Deskripsi**: Mengekstrak data dari faktur
    - **Koneksi Layanan Azure AI**: *sumber daya Layanan Azure AI di hub Azure AI Foundry Anda*
    - **Akun Azure Blob Storage**: *Akun penyimpanan default di hub Azure AI Foundry Anda*
1. Tunggu hingga tugas dibuat.

    > **Tips**: Jika terjadi kesalahan saat mengakses penyimpanan, tunggu sebentar dan coba lagi.

1. Pada halaman **Tentukan skema** , unggah file **invoice-1234.pdf** yang baru saja Anda unduh.
1. Pilih templat **Analisis faktur**, lalu pilih **Buat**.

    Penyewa *Analisis faktur* mencakup bidang umum yang ditemukan dalam faktur. Anda dapat menggunakan editor skema untuk menghapus salah satu bidang yang disarankan yang tidak Anda butuhkan, dan menambahkan bidang kustom apa pun yang Anda butuhkan.

1. Dalam daftar bidang yang disarankan, pilih **AlamatPenagihan**. Bidang ini tidak diperlukan untuk format faktur yang telah Anda unggah, jadi gunakan ikon **Hapus bidang** (**&#128465;**) yang muncul untuk menghapusnya.
1. Sekarang hapus bidang yang disarankan berikut, yang tidak diperlukan:
    - BillingAddressRecipient
    - CustomerAddressRecipient
    - ID Pelanggan
    - CustomerTaxId
    - DueDate
    - InvoiceTotal
    - PaymentTerm
    - PreviousUnpaidBalance
    - PurchaseOrder
    - RemittanceAddress
    - ServiceAddressRecipient
    - ServiceAddress
    - ServiceAddressRecipient
    - ShippingAddress
    - ShippingAddressRecipient
    - TotalDiskon
    - VendorAddressRecipient
    - VendorTaxId
    - DetailPajak
1. Gunakan **tombol + Tambahkan bidang baru** untuk menambahkan bidang berikut:

    | Nama bidang | Deskripsi bidang | Jenis nilai | Metode |
    |--|--|--|--|
    | `VendorPhone` | `Vendor telephone number` | String | Ekstrak |
    | `ShippingFee` | `Fee for shipping` | Number | Ekstrak |

1. verifikasi bahwa skema Anda yang telah selesai terlihat seperti ini, dan pilih **Simpan**.
    ![Tangkapan layar skema untuk sebuah faktur.](../media/invoice-schema.png)

1. Pada halaman **Pengujian Penganalisis**, jika analisis tidak dimulai secara otomatis, pilih **Jalankan analisis**. Kemudian tunggu hingga analisis selesai dan tinjau nilai teks pada faktur yang diidentifikasi cocok dengan bidang dalam skema.
1. Tinjau hasil analisis, yang harusnya terlihat mirip dengan ini:

    ![Cuplikan layar hasil pengujian penganalisis.](../media/analysis-results.png)

1. Tampilkan detail bidang yang diidentifikasi di panel **Bidang**, lalu tampilkan tab **Hasil** untuk melihat representasi JSON.

## Membangun dan menguji penganalisis

Sekarang, setelah Anda melatih model untuk mengekstrak bidang dari contoh faktur, Anda dapat membuat penganalisis untuk digunakan dengan formulir serupa.

1. Pilih halaman **Penganalisis pembangunan**, dab lalu pilih **+ Penganalisis pembangunan** dan bangun penganalisis baru dengan properti berikut (diketik persis seperti yang ditunjukkan di sini):
    - **Nama**: `contoso-invoice-analyzer`
    - **Deskripsi**: `Contoso invoice analyzer`
1. Tunggu hingga penganalisis baru siap (gunakan tombol **Refresh** untuk memeriksa).
1. Unduh [invoice-1235.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf) dari `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf` dan simpan di folder lokal.
1. Kembali ke halaman **Penganalisis pembangunan** dan pilih tautan **contoso-invoice-analyzer**. Bidang yang ditentukan dalam skema penganalisis akan ditampilkan.
1. Di halaman **contoso-invoice-analyzer**, pilih tab **Test**.
1. Gunakan tombol **+ Unggah file pengujian** untuk mengunggah **invoice-1235.pdf** dan menjalankan analisis untuk mengekstrak data bidang dari formulir pengujian.
1. Tinjau hasil pengujian, dan verifikasi bahwa penganalisis mengekstrak bidang yang benar dari faktur pengujian.
1. Tutup halaman **contoso-invoice-analyzer***.

## Menggunakan REST API Pemahaman Konten

Setelah membuat penganalisis, Anda dapat menggunakannya dari aplikasi klien melalui REST API Pemahaman Konten.

1. Di area **Detail proyek**, perhatikan **string koneksi Proyek**. Anda akan menggunakan string koneksi ini untuk menyambungkan ke proyek Anda di aplikasi klien.
1. Buka tab browser baru (biarkan portal Azure AI Foundry tetap terbuka di tab yang sudah ada). Kemudian di tab baru, telusuri [Portal Azure](https://portal.azure.com) di `https://portal.azure.com`; masuk menggunakan kredensial Azure Anda jika diminta.

    Tutup pemberitahuan selamat datang apa pun untuk melihat halaman beranda portal Azure.

1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***PowerShell*** dengan tidak ada penyimpanan pada langganan Anda.

    Cloud shell menyediakan antarmuka baris perintah dalam panel di bagian bawah portal Azure. Anda dapat mengubah ukuran atau memaksimalkan panel ini untuk mempermudah pekerjaan.

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *Bash* , alihkan ke ***PowerShell***.

1. Di toolbar cloud shell, di menu **Pengaturan**, pilih **Buka versi Klasik** (ini diperlukan untuk menggunakan editor kode).

    **<font color="red">Pastikan Anda telah beralih ke versi klasik cloud shell sebelum melanjutkan.</font>**

1. Di panel cloud shell, masukkan perintah berikut untuk mengkloning repo GitHub yang berisi file kode untuk latihan ini (ketik perintah, atau salin ke clipboard lalu klik kanan di baris perintah dan tempel sebagai teks biasa):

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

    > **Tips**: Saat Anda memasukkan perintah ke cloudshell, ouputnya mungkin mengambil sejumlah besar buffer layar. Anda dapat menghapus layar dengan memasukkan `cls` perintah untuk mempermudah fokus pada setiap tugas.

1. Setelah repositori dikloning, navigasikan ke folder yang berisi file kode untuk aplikasi Anda:

    ```
   cd mslearn-ai-doc/Labfiles/05-content-understanding/code
   ls -a -l
    ```

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menginstal pustaka yang akan Anda gunakan:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install dotenv azure-identity azure-ai-projects
    ```

1. Masukkan perintah berikut untuk mengedit file konfigurasi yang telah disediakan:

    ```
   code .env
    ```

    File dibuka dalam editor kode.

1. Dalam file kode, ganti tempat penampung **YOUR_PROJECT_CONNECTION_STRING** dengan string koneksi untuk proyek Anda (disalin dari halaman **Gambaran Umum** proyek di portal Azure AI Foundry), dan pastikan bahwa **PENGANALISIS** diatur ke nama yang Anda tetapkan ke penganalisis Anda (yang seharusnya *contoso-invoice-analyzer*)
1. Setelah Anda mengganti tempat penampung, dalam editor kode, gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda lalu gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell tetap terbuka.

1. Masukkan perintah berikut untuk mengedit file kode Python **analyze_doc.py** yang telah disediakan di baris perintah cloud shell:

    ```
    code analyze_invoice.py
    ```
    File kode Python dibuka di editor kode:

1. Tinjau kode, yang:
    - Mengidentifikasi file faktur yang akan dianalisis, dengan default **invoice-1236.pdf**.
    - Mengambil titik akhir dan kunci untuk sumber daya Layanan Azure AI Anda dari proyek.
    - Mengirimkan permintaan HTTP POST ke titik akhir Pemahaman Konten Anda, menginstruksikan untuk menganalisis gambar tersebut.
    - Memeriksa respons dari operasi POST untuk mengambil ID untuk operasi analisis.
    - Berulang kali mengirimkan permintaan HTTP GET ke layanan Pemahaman Konten Anda hingga operasi tidak lagi berjalan.
    - Jika operasi telah berhasil, lakukan parsing terhadap respons JSON dan menampilkan nilai yang diambil
1. Gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell terbuka.
1. Di panel cloud shell, masukkan perintah berikut untuk menjalankan kode Python:

    ```
    python analyze_invoice.py invoice-1236.pdf
    ```

1. Meninjau output dari program.
1. Gunakan perintah berikut untuk menjalankan program dengan faktur lain:

    ```
    python analyze_invoice.py invoice-1235.pdf
    ```

    > **Tips**: Ada tiga file faktur di folder kode yang dapat Anda gunakan (invoice-1234.pdf, invoice-1235.pdf, dan invoice-1236.pdf) 

## Penghapusan

Setelah selesai bekerja dengan layanan Pemahaman Konten, Anda harus menghapus sumber daya yang telah Anda buat di latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Di portal Azure AI Foundry, navigasikan ke proyek **asuransi perjalanan**, lalu hapus.
1. Di portal Azure, hapus grup sumber daya yang Anda buat dalam latihan ini.

