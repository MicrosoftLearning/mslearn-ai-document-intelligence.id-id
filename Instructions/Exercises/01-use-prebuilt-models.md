---
lab:
  title: Menggunakan model Document Intelligence bawaan
  module: Module 6 - Document Intelligence
---

# Menggunakan model Document Intelligence bawaan

Dalam latihan ini, Anda akan menyiapkan sumber daya Azure AI Document Intelligence di langganan Azure Anda. Anda akan menggunakan Azure AI Document Intelligence Studio dan C# atau Python untuk mengirimkan formulir ke sumber daya tersebut untuk dilakukan analisis.

## Membuat sumber daya Azure AI Document Intelligence

Sebelum dapat memanggil layanan Azure AI Document Intelligence, Anda harus membuat sumber daya untuk menghosting layanan tersebut di Azure:

1. Di tab browser, buka portal Azure di [https://portal.azure.com](https://portal.azure.com?azure-portal=true), masuk dengan akun Microsoft yang terkait dengan langganan Azure Anda.
1. Di beranda portal Azure, navigasikan ke kotak pencarian atas dan ketik **Document Intelligence** lalu tekan **Enter**.
1. Di halaman **Document Intelligence**, pilih **Buat**.
1. Di halaman **Buat Document Intelligence**, gunakan hal berikut untuk mengonfigurasi sumber daya Anda:
    - **Langganan**: Langganan Azure Anda.
    - **Grup sumber daya**: Pilih atau buat grup sumber daya dengan nama unik seperti *DocIntelligenceResources*.
    - **Wilayah**: Pilih wilayah di dekat Anda.
    - **Nama**: Masukkan nama yang unik secara global.
    - **Tingkat harga**: pilih **F0 gratis** (jika Anda tidak memiliki tingkat Gratis yang tersedia, pilih **S0 standar**).
1. Lalu pilih **Tinjau + buat** dan **Buat**. Tunggu selagi Azure membuat sumber daya Azure AI Document Intelligence.
1. Setelah penyebaran selesai, pilih **Buka sumber daya**. Tetap buka halaman ini selama sisa latihan ini.

## Menggunakan model Baca

Mari mulai dengan menggunakan **Azure AI Document Intelligence Studio** dan model Baca untuk menganalisis dokumen dengan beberapa bahasa. Anda akan menyambungkan Azure AI Document Intelligence Studio ke sumber daya yang baru saja Anda buat untuk melakukan analisis:

1. Buka tab browser baru lalu buka **Azure AI Document Intelligence Studio** di [https://documentintelligence.ai.azure.com/studio](https://documentintelligence.ai.azure.com/studio).
1. Di bawah **Analisis Dokumen**, pilih petak **Baca**.
1. Jika Anda diminta untuk masuk ke akun Anda, gunakan kredensial Azure Anda.
1. Jika Anda ditanya sumber daya Azure AI Document Intelligence mana yang akan digunakan, pilih langganan dan nama sumber daya yang Anda gunakan saat membuat sumber daya Azure AI Document Intelligence.
1. Dalam daftar dokumen di sebelah kiri, pilih **read-german.pdf**.

    ![Cuplikan layar memperlihatkan halaman Baca di Azure AI Document Intelligence Studio.](../media/read-german-sample.png#lightbox)

1. Di kiri atas, pilih **Opsi analisis**, lalu aktifkan kotak centang **Bahasa** (di bawah **Deteksi opsional**) di panel **Opsi analisis** lalu klik **Simpan**. 
1. Di kiri atas, pilih **Jalankan Analisis**.
1. Ketika analisis selesai, teks yang diekstrak dari gambar ditampilkan di sebelah kanan di tab **Konten**. Tinjau teks ini dan bandingkan dengan teks dalam gambar asli untuk akurasi.
1. Pilih tab **Hasil**. Tab ini menampilkan kode JSON yang diekstrak. 
1. Gulir ke bagian bawah kode JSON di tab **Hasil** Perhatikan bahwa model baca telah mendeteksi bahasa setiap rentang yang ditunjukkan oleh`locale`. Sebagian besar rentang menggunakan bahasa Jerman (kode bahasa `de`) tetapi Anda dapat menemukan kode bahasa lain dalam rentang (misalnya bahasa Inggris - kode bahasa `en` - dalam salah satu rentang terakhir).

    ![Cuplikan layar memperlihatkan deteksi bahasa untuk dua rentang dalam hasil dari model baca di Azure AI Document Intelligence Studio.](../media/language-detection.png#lightbox)

## Bersiap untuk mengembangkan aplikasi di Visual Studio Code

Sekarang mari kita jelajahi aplikasi yang menggunakan SDK layanan Azure Document Intelligence. Anda akan mengembangkan aplikasi menggunakan Visual Studio Code. File kode untuk aplikasi Anda telah disediakan di repositori GitHub.

> **Tips**: Jika Anda sudah mengkloning repositori **mslearn-ai-document-intelligence**, buka di Visual Studio Code. Jika tidak, ikuti langkah-langkah ini untuk mengkloningnya ke lingkungan pengembangan Anda.

1. Memulai Visual Studio Code.
1. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` ke folder lokal (tidak masalah folder mana).
1. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda untuk memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai penulisnya** pada pop-up tersebut.

1. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**. Jika Anda menerima Pesan *Terdeteksi Proyek Azure Function di folder*, Anda dapat menutup pesan tersebut dengan aman.

## Mengonfigurasi aplikasi Anda

Aplikasi untuk C# dan Python telah disediakan, serta sampel file pdf yang akan Anda gunakan untuk menguji Document Intelligence. Kedua aplikasi memiliki fungsionalitas yang sama. Pertama, Anda akan melengkapi beberapa bagian utama aplikasi untuk memungkinkan menggunakan sumber daya Azure Document Intelligence.

1. Periksa faktur berikut dan catat beberapa bidang dan nilainya. Ini adalah faktur yang akan dianalisis kode Anda.

    ![Cuplikan layar memperlihatkan sampel dokumen faktur.](../media/sample-invoice.png#lightbox)

1. Di Visual Studio Code, di panel **Penjelajah**, telusuri folder **Labfiles/01-prebuild-models** dan perluas folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi tempat Anda akan mengintegrasikan fungsionalitas Azure Document Intelligence.

1. Klik kanan folder **CSharp** atau **Python** yang berisi file kode Anda dan pilih **Buka terminal terintegrasi**. Kemudian instal paket SDK Azure Form Recognizer (nama sebelumnya untuk Document Intelligence) dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**:

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

## Menambahkan kode untuk menggunakan layanan Azure Document Intelligence

Sekarang Anda siap menggunakan SDK untuk mengevaluasi file pdf.

1. Beralih ke tab browser yang menampilkan gambaran umum Azure AI Document Intelligence di portal Azure. Di panel kiri, di bawah *Manajemen Sumber Daya*, pilih **Kunci dan Titik Akhir**. Di sebelah kanan nilai **Titik Akhir**, klik tombol **Salin ke papan klip**.
1. Di panel **Penjelajah**, di folder **CSharp** atau **Python**, buka file kode untuk bahasa pilihan Anda lalu ganti `<Endpoint URL>` dengan string yang baru saja Anda salin:

    **C#**: ***Program.cs***

    ```csharp
    string endpoint = "<Endpoint URL>";
    ```

    **Python**: ***document-analysis.py***

    ```python
    endpoint = "<Endpoint URL>"
    ```

1. Beralih ke tab browser yang menampilkan **Kunci dan Titik Akhir** Azure AI Document Intelligence di portal Azure. Di sebelah kanan nilai **KEY 1**, klik tombol *Salin ke clipboard**.
1. Dalam file kode di Visual Studio Code, temukan baris ini dan ganti `<API Key>` dengan string yang baru saja Anda salin:

    **C#**

    ```csharp
    string apiKey = "<API Key>";
    ```

    **Python**

    ```python
    key = "<API Key>"
    ```

1. Temukan komentar `Create the client`. Setelah itu, pada baris baru, masukkan kode berikut:

    **C#**

    ```csharp
    var cred = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), cred);
    ```

    **Python**

    ```python
    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )
    ```

1. Temukan komentar `Analyze the invoice`. Setelah itu, pada baris baru, masukkan kode berikut:

    **C#**

    ```csharp
    AnalyzeDocumentOperation operation = await client.AnalyzeDocumentFromUriAsync(WaitUntil.Completed, "prebuilt-invoice", fileUri);
    ```

    **Python**

    ```python
    poller = document_analysis_client.begin_analyze_document_from_url(
        fileModelId, fileUri, locale=fileLocale
    )
    ```

1. Temukan komentar `Display invoice information to the user`. Setelah itu, pada baris berita, masukkan kode berikut:

    **C#**

    ```csharp
    AnalyzeResult result = operation.Value;
    
    foreach (AnalyzedDocument invoice in result.Documents)
    {
        if (invoice.Fields.TryGetValue("VendorName", out DocumentField? vendorNameField))
        {
            if (vendorNameField.FieldType == DocumentFieldType.String)
            {
                string vendorName = vendorNameField.Value.AsString();
                Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}.");
            }
        }
    ```

    **Python**

    ```python
    receipts = poller.result()
    
    for idx, receipt in enumerate(receipts.documents):
    
        vendor_name = receipt.fields.get("VendorName")
        if vendor_name:
            print(f"\nVendor Name: {vendor_name.value}, with confidence {vendor_name.confidence}.")
    ```

    > [!NOTE]
    > Anda telah menambahkan kode untuk menampilkan nama vendor. Proyek pemula juga menyertakan kode untuk menampilkan *nama pelanggan* dan *total faktur*.

1. Simpan perubahan terhadap file kode.

1. Di panel terminal interaktif, pastikan konteks folder adalah folder untuk bahasa pilihan Anda. Lalu masukkan perintah berikut untuk menjalankan aplikasi.

1. ***Khusus C#***, untuk membangun proyek Anda, masukkan perintah ini:

    **C#**:

    ```powershell
    dotnet build
    ```

1. Untuk menjalankan kode Anda, masukkan perintah ini:

    **C#**:

    ```powershell
    dotnet run
    ```

    **Python**:

    ```powershell
    python document-analysis.py
    ```

Program ini menampilkan nama vendor, nama pelanggan, dan total faktur dengan tingkat keyakinan. Bandingkan nilai yang dilaporkannya dengan faktur sampel yang Anda buka di awal bagian ini.

## Penghapusan

Jika sudah selesai dengan sumber daya Azure Anda, ingatlah untuk menghapus sumber daya di [portal Azure](https://portal.azure.com/?azure-portal=true) untuk menghindari biaya lebih lanjut.

## Informasi selengkapnya

Untuk mengetahui informasi selengkapnya tentang layanan Document Intelligence, lihat [Dokumentasi Document Intelligence](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true).
