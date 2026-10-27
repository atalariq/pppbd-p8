# MyShop

Latihan Android untuk pertemuan 8 mata kuliah **Praktikum Pemrograman Perangkat Bergerak Dasar (PPPBD)**. Project ini mengikuti modul *Bottom Navigation View dan Fragment*: satu activity menampung beberapa fragment, pengguna berpindah halaman, lalu mengirim dan menerima data melalui Jetpack Navigation.

MyShop memakai contoh pembelian **Samsung M22**. Alurnya:

1. Buka **Home**, lalu tekan **Beli Sekarang**.
2. **Checkout** menerima nama produk melalui Safe Args.
3. Ketuk kolom **Alamat** untuk membuka **Address**.
4. Pilih provinsi, lalu tekan **Selesai**. Provinsi dikirim kembali ke Checkout melalui `SavedStateHandle`.
5. Tekan **Selesai** di Checkout untuk kembali ke Home.

Bottom navigation menyediakan **Home**, **Keranjang**, dan **Profile**. Keranjang dan Profile masih menampilkan layout fragment bawaan. Tombol Selesai hanya melakukan navigasi kembali; aplikasi belum menyimpan atau memproses pesanan.

## Alat dan konfigurasi

| Komponen | Yang dipakai dalam project |
| --- | --- |
| IDE | Android Studio dengan dukungan Android Gradle Plugin 9.3.3 |
| Bahasa dan UI | Kotlin, XML Android Views, Material Components 1.14.0 |
| Akses view | View Binding |
| Navigasi | AndroidX Navigation Fragment/UI dan Safe Args 2.10.2 |
| Build | Gradle Wrapper 9.8.0, Kotlin DSL |
| JDK untuk Gradle | 25, sesuai `gradle/gradle-daemon-jvm.properties` |
| Kompatibilitas source/target Java | Java 11; ini berbeda dari JDK yang menjalankan Gradle |
| Android SDK | compile/target API 37, minimum API 24 |

Versi di atas dicatat dari file konfigurasi repository. Modul kuliah memakai Navigation 2.9.4; kode dalam repository ini memakai 2.10.2 untuk plugin dan kedua library Navigation.

## Menjalankan project

```sh
git clone https://github.com/atalariq/pppbd-p8.git
cd pppbd-p8
```

1. Buka folder tersebut melalui **Open** di Android Studio.
2. Sediakan Android SDK Platform 37 beserta build tools yang diminta saat sinkronisasi. Atur lokasi SDK melalui Android Studio; `local.properties` bersifat lokal dan tidak masuk Git.
3. Gunakan JDK 25 untuk Gradle. File daemon JVM juga menyediakan konfigurasi pengunduhan toolchain melalui Foojay.
4. Tunggu Gradle Sync selesai. Unduhan pertama memerlukan koneksi internet.
5. Pilih emulator atau perangkat Android dengan API 24 atau lebih baru, lalu tekan **Run**.

Build dari terminal pada Linux/macOS:

```sh
./gradlew :app:assembleDebug
```

Pada Windows, gunakan `gradlew.bat :app:assembleDebug`. APK debug dihasilkan di `app/build/outputs/apk/debug/` setelah build berhasil. Kelas `HomeFragmentDirections`, `CheckoutFragmentDirections`, dan `CheckoutFragmentArgs` dibuat oleh Safe Args saat build; jangan membuatnya secara manual.

## Membaca implementasinya kembali

- `app/src/main/res/navigation/bottom_navigation.xml` menentukan destination, action, dan argumen `productName`.
- `app/src/main/res/menu/bottom_navigation_menu.xml` memakai ID yang sama dengan destination utama. Kesamaan ID membuat `setupWithNavController()` dapat menghubungkan pilihan menu dengan fragment tujuan.
- `MainActivity` memasang layout melalui View Binding dan menghubungkan bottom navigation ke `NavController`.
- `HomeFragment` mengambil nama produk dari teks yang tampil, lalu memasukkannya ke action hasil Safe Args.
- `CheckoutFragment` membaca argumen dengan `navArgs()`. Fragment ini juga mengamati hasil alamat pada `currentBackStackEntry.savedStateHandle` menggunakan `viewLifecycleOwner`.
- `AddressFragment` mengisi spinner dari resource `provinces`. Saat Selesai ditekan, hasil ditulis ke `previousBackStackEntry.savedStateHandle` dengan key `address`, lalu `navigateUp()` kembali ke Checkout.

## Catatan belajar

Hal yang paling perlu diingat dari latihan ini adalah perbedaan **tujuan navigasi**, **perpindahan**, dan **data yang dibawa**. Destination didaftarkan dalam graph; action menentukan perpindahan yang dapat dipanggil; argumen menentukan data yang dibutuhkan tujuan. Setelah argumen wajib ditambahkan, pemanggilan action juga harus memasok nilainya.

Pengiriman nama produk dan pengembalian alamat memakai dua jalur berbeda. Nama produk menjadi input Checkout melalui Safe Args. Alamat menjadi hasil dari Address yang ditulis ke entry Checkout sebelum Address ditutup. Mengirim ke `currentBackStackEntry` dari Address akan menyimpan hasil pada entry Address sendiri, sehingga tidak sesuai dengan observer di Checkout.

View Binding juga mengikuti umur view fragment. `AddressFragment` sudah mengosongkan `_binding` pada `onDestroyView()`, sedangkan Home dan Checkout belum melakukannya. Ini keterbatasan implementasi saat ini dan bahan perbaikan berikutnya; dokumentasi ini mempertahankan source praktikum yang ada.

## Riwayat dan cakupan

- [`46b05cc`](https://github.com/atalariq/pppbd-p8/commit/46b05ccb73ac027020a28a697dd9fb936252321e): project awal.
- [`f2ce2a1`](https://github.com/atalariq/pppbd-p8/commit/f2ce2a1d6ce6f265acd44db366b14c69d678d53f): penambahan fragment, navigation graph, bottom navigation, dan pertukaran data.

Tahapan dalam modul lebih rinci daripada dua commit tersebut. Repository ini ditujukan untuk mempelajari alur navigasi, bukan sebagai aplikasi toko siap digunakan. Belum ada katalog dinamis, login, keranjang belanja yang berfungsi, pembayaran, atau penyimpanan pesanan. Daftar provinsi juga mengikuti sepuluh contoh dalam modul, bukan seluruh provinsi Indonesia.

## Referensi

- [Fragments](https://developer.android.com/guide/fragments)
- [Menghubungkan UI dengan NavController](https://developer.android.com/guide/navigation/integrations/ui)
- [Mengirim data antardestination dan Safe Args](https://developer.android.com/guide/navigation/use-graph/pass-data)
- [Mengembalikan hasil melalui SavedStateHandle](https://developer.android.com/guide/navigation/use-graph/programmatic)
