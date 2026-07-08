# Tutorial Lengkap: Membuat Apps Kontak Darurat dari Nol (Scratch)

Tutorial ini akan memandu Anda membuat aplikasi Android sederhana untuk praktik UI, Logic, dan Data Lokal (SharedPreferences) tanpa download library tambahan.

---

Saat Anda membuat project di Android Studio, ada ratusan file yang dibuat secara otomatis. Namun, untuk tutorial ini, Anda hanya perlu fokus pada folder dan file utama berikut:

```text
KlikHelpApps/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── com/example/klikhelpapps/
│   │   │   │       └── MainActivity.kt        (File Logika Utama)
│   │   │   ├── res/
│   │   │   │   ├── layout/
│   │   │   │   │   ├── activity_main.xml      (Tampilan Layar Utama)
│   │   │   │   │   └── item_contact.xml       (Tampilan per-Baris Kontak)
│   │   │   │   └── values/                    (Tempat simpan warna/teks bawaan)
│   │   │   └── AndroidManifest.xml            (File Konfigurasi Sistem App)
│   └── build.gradle.kts                       (Pengaturan Module/Library)

## Langkah 1: Persiapan Project Baru
1. Buka **Android Studio**.
2. Pilih **New Project** -> **Empty Views Activity** (Penting: pilih yang "Views", bukan "Compose" agar sesuai tutorial ini).
3. Isi detail project:
   - **Name**: KlikHelpApps
   - **Package name**: `com.example.klikhelpapps`
   - **Language**: Kotlin
   - **Minimum SDK**: API 24 (atau yang direkomendasikan).
4. Klik **Finish** dan tunggu Gradle selesai sinkronisasi.

---

## Langkah 2: Membuat Tampilan (UI)
Kita butuh dua layout. Satu untuk layar utama, satu untuk tampilan setiap baris kontak.

### A. Tampilan Utama
Buka file `app/src/main/res/layout/activity_main.xml`, hapus isinya dan ganti dengan kode berikut:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Profil Saya"
        android:textSize="24sp"
        android:textStyle="bold" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Nama: Budi Santoso"
        android:paddingTop="8dp"
        android:textSize="18sp" />

    <View
        android:layout_width="match_parent"
        android:layout_height="1dp"
        android:background="#CCCCCC"
        android:layout_marginVertical="16dp" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Daftar Kontak Darurat"
        android:textSize="20sp"
        android:textStyle="bold" />

    <!-- Tempat list kontak akan muncul secara dinamis -->
    <LinearLayout
        android:id="@+id/llContactList"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:orientation="vertical"
        android:paddingTop="8dp" />

    <EditText
        android:id="@+id/etContactName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Masukkan Nama Kontak"
        android:inputType="textPersonName" />

    <Button
        android:id="@+id/btnAddContact"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Tambah ke Database Lokal" />
</LinearLayout>
```

### B. Tampilan Baris Kontak (Item)
Klik kanan folder `layout` -> **New** -> **Layout Resource File**. Beri nama `item_contact.xml`. Isi dengan kode ini:
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="8dp"
    android:gravity="center_vertical">

    <TextView
        android:id="@+id/tvContactName"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:textSize="16sp" />

    <Button
        android:id="@+id/btnConnect"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hubungkan" />
</LinearLayout>
```

---

## Langkah 3: Menulis Logika & Database (Logic & Data)
Buka file `app/src/main/java/com/example/klikhelpapps/MainActivity.kt`. Ganti seluruh isinya dengan kode ini:

```kotlin
package com.example.klikhelpapps

import android.content.Context
import android.os.Bundle
import android.view.LayoutInflater
import android.widget.Button
import android.widget.EditText
import android.widget.LinearLayout
import android.widget.TextView
import androidx.appcompat.app.AlertDialog
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    // Nama file storage lokal kita
    private val PREFS_NAME = "MyLocalData"
    private val KEY_CONTACTS = "saved_contacts"

    private lateinit var llContactList: LinearLayout
    private lateinit var etContactName: EditText
    private lateinit var btnAddContact: Button

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. Hubungkan variabel dengan UI
        llContactList = findViewById(R.id.llContactList)
        etContactName = findViewById(R.id.etContactName)
        btnAddContact = findViewById(R.id.btnAddContact)

        // 2. Muat data yang sudah tersimpan sebelumnya
        loadDataDariLokal()

        // 3. Logika ketika tombol tambah diklik
        btnAddContact.setOnClickListener {
            val nama = etContactName.text.toString()
            if (nama.isNotEmpty()) {
                simpanKeLokal(nama)
                etContactName.text.clear()
                loadDataDariLokal() // Update tampilan list
            }
        }
    }

    private fun loadDataDariLokal() {
        llContactList.removeAllViews() // Bersihkan list lama di layar

        // Ambil data dari SharedPreferences
        val sharedPref = getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
        val listKontak = sharedPref.getStringSet(KEY_CONTACTS, emptySet()) ?: emptySet()

        // Tampilkan setiap nama ke dalam UI
        for (nama in listKontak) {
            tambahItemKeListUI(nama)
        }
    }

    private fun simpanKeLokal(namaBaru: String) {
        val sharedPref = getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
        val dataLama = sharedPref.getStringSet(KEY_CONTACTS, emptySet())?.toMutableSet() ?: mutableSetOf()

        dataLama.add(namaBaru) // Tambah nama baru ke kumpulan data

        // Simpan permanen
        sharedPref.edit().putStringSet(KEY_CONTACTS, dataLama).apply()
    }

    private fun tambahItemKeListUI(nama: String) {
        // "Inflate" layout item_contact.xml menjadi object
        val viewItem = LayoutInflater.from(this).inflate(R.layout.item_contact, llContactList, false)

        val tvNama = viewItem.findViewById<TextView>(R.id.tvContactName)
        val btnHubung = viewItem.findViewById<Button>(R.id.btnConnect)

        tvNama.text = nama
        btnHubung.setOnClickListener {
            munculkanPopUp(nama)
        }

        // Masukkan item ke dalam container list utama
        llContactList.addView(viewItem)
    }

    private fun munculkanPopUp(nama: String) {
        AlertDialog.Builder(this)
            .setTitle("Simulasi Koneksi")
            .setMessage("Menghubungkan ke kontak: $nama...")
            .setPositiveButton("Selesai", null)
            .show()
    }
}
```

---

## Langkah 4: Registrasi di AndroidManifest
Buka `app/src/main/AndroidManifest.xml`. Pastikan `MainActivity` terdaftar di dalam tag `<application>` seperti ini:

```xml
<activity
    android:name=".MainActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

---

## Langkah 5: Running Apps
1. Hubungkan HP Android atau nyalakan Emulator.
2. Klik ikon **Play** di toolbar atas.
3. Setelah muncul, coba ketik nama, klik Tambah.
4. Coba matikan apps (force close) lalu buka lagi. Datanya akan tetap ada karena tersimpan di `SharedPreferences`!

---

### Penjelasan Singkat:
- **UI (XML)**: Struktur visual aplikasi.
- **Logic (MainActivity)**: Mengatur apa yang terjadi saat tombol diklik.
- **Data (SharedPreferences)**: "Laci" penyimpanan di memori HP agar data tidak hilang.
- **Inflation**: Proses mengubah kode XML menjadi tampilan nyata di layar secara dinamis (saat runtime).
