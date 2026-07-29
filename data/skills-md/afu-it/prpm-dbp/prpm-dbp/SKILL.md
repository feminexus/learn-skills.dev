---
name: prpm-dbp
description: "Sahkan ejaan Bahasa Melayu dan Jawi terhadap PRPM Dewan Bahasa dan Pustaka sebelum menghantar output. Guna apabila menulis, menyemak, atau mentransliterasi teks BM/Jawi, terutama bahan pendidikan, RPH, nota, atau apa-apa yang perlu ikut ejaan rasmi DBP. Trigger: jawi, kamus dewan, ejaan rasmi, DBP, PRPM, transliterasi, bahasa melayu baku."
---

# prpm-dbp

Sumber kebenaran tunggal untuk ejaan BM dan Jawi ialah PRPM DBP, bukan ingatan model.

Model boleh hasilkan Jawi yang nampak betul tetapi salah, dan pembaca tidak dapat kesan. Skill ini tukar tekaan jadi semakan.

## Sebelum menghantar apa-apa

Jalankan `npx prpm-dbp gate <fail> --luar-talian` ke atas **keseluruhan teks**. Bukan sebahagian. Bukan perkataan yang anda rasa berisiko.

Ejaan yang salah ialah tepat ejaan yang anda **tidak** syak. Kalau anda syak, anda sudah menyemaknya.

Ini termasuk ejaan yang anda tulis dalam **kod, ujian dan dokumentasi**, bukan hanya dalam teks untuk pengguna. Semasa membina pakej ini sendiri, satu nilai Jawi ditulis ke dalam ujian daripada ingatan tanpa disemak. Nilainya kebetulan betul. Kebetulan bukan proses.

**Apa yang berlaku apabila langkah ini dilangkau:**

Satu agen menggunakan skill ini sepanjang sesi penuh untuk menukar Jawi ke Rumi, kemudian menghantar 549 kad RPH Pendidikan Islam ke pangkalan data produksi dengan **273 ejaan yang salah**.

```
pusingan 1   sunnah x206, hadith x16, fardhu x2        -> masuk produksi
pusingan 2   tayammum, dhuha, qadha, redha, iddah x90  -> masuk produksi
```

Semuanya kata lazim dalam bahan Pendidikan Islam. Setiap satu ada jawapan tegas dalam PRPM: `sunnah` ialah rujukan silang ke `sunah`, `hadith` sepatutnya `hadis`, `fardhu` sepatutnya `fardu`, `tayammum` sepatutnya `tayamum`, `dhuha` sepatutnya `duha`, `qadha` sepatutnya `qada`.

Alat untuk menangkap kesemuanya ada di tangan agen itu sepanjang masa. Ia tidak pernah dijalankan ke atas teks Rumi keluarannya sendiri. Kedua-dua pusingan ditemui kerana **pengguna bertanya**, bukan kerana proses menangkapnya.

`gate` keluar dengan kod 1 apabila ada ralat, jadi ia boleh dipasang sebagai hook atau langkah CI. Peraturan yang bergantung pada agen ingat sendiri akan gagal, dan gagal secara senyap.

## Kuatkuasa automatik

```bash
npx prpm-dbp install --hook
```

Memasang hook PostToolUse yang menjalankan `gate --luar-talian` ke atas setiap fail `.md`, `.txt` atau `.json` yang ditulis atau disunting. Ia **melaporkan** ralat kepada agen tanpa menyekat tulisan: menyekat atas kamus yang tidak lengkap akan menghalang kerja yang sah, manakala melaporkan sudah memadai kerana agen membaca output hook.

Untuk menampal sendiri: `npx prpm-dbp hook-json`.

Panaskan cache **sekali** semasa pemasangan, bukan semasa gate berjalan:

```bash
npx prpm-dbp warm kosa-kata-projek.txt
```

Gate atas cache panas mengambil 0.19 saat bagi 406 perkataan. Itu cukup pantas untuk setiap penyuntingan. Gate yang perlahan akan dimatikan orang.

## Tiga keadaan gate, bukan dua

| Medan | Maksud | Kesan |
|---|---|---|
| `lulusDbp` | ada dalam PRPM, ejaan padan | — |
| `ralat` | ada dalam PRPM dengan ejaan lain, entri rujukan silang `®`, atau tiada dalam PRPM mahupun dataset | **exit 1** |
| `tidakDapatDisahkan` | tiada dalam cache, nama khas, akronim, atau tiada entri PRPM tetapi wujud dalam dataset | dilapor; exit 1 jika melebihi `--ambang` |

`tidakDapatDisahkan` **tidak pernah lulus senyap**. Gate yang lulus atas cache sejuk memberi jaminan palsu, dan itu lebih bahaya daripada tiada gate. Gunakan `--ambang 10` supaya cache sejuk menggagalkan gate.

PRPM tiada entri untuk kata tugas paling asas: `dan`, `di`, `ke`, `yang`, `atau`. Menandakannya sebagai ralat menjadikan gate mustahil dipakai. Pembezanya bersumber, bukan senarai putih: kata tugas itu **semua** wujud dalam dataset, manakala `hadith`, `fardhu`, `tayammum`, `dhuha`, `qadha`, `redha` dan `iddah` **tiada satu pun**.

## Prosedur wajib

Ikut ini setiap kali, jangan langkau langkah.

**Menulis Jawi daripada Rumi:**

1. `npx prpm-dbp jawi <fail> --luar-talian --homograf --json`
2. Baca `belumDisahkan` dan `namaKhas`. Jangan reka ejaan untuknya. Sahkan dengan `lookup`, atau tanya pengguna, atau tandakan dalam hasil akhir.
3. Baca `mencurigakan`. Setiap satu bermakna PRPM sendiri mungkin tersilap. Semak di pautan yang diberi sebelum menggunakannya.
4. Baca `homograf`. Beritahu pengguna perkataan mana yang akan taksa apabila dibaca.
5. Laporkan `semuanyaDbp`. Jika `false`, nyatakan bahagian mana bukan daripada sumber rasmi.

**Merumikan daripada Jawi:**

1. `npx prpm-dbp rumi <fail> --pilih --konteks --json`
2. Untuk **setiap** item dalam `kabur`: baca ayat yang mengandunginya, baca makna setiap calon, tentukan yang mana sesuai.
3. Jika `dipilih` salah bagi ayat itu, **gantikan** dalam hasil akhir. Kekerapan kalah kepada konteks.
4. Nyatakan kepada pengguna berapa banyak kekaburan wujud dan mana yang anda ubah.
5. `tidakDikenali` bermakna perkataan itu tiada dalam cache. Jalankan `warm` di luar talian, bukan tinggalkan bertanda.

**Berhenti dan tanya pengguna apabila:** ejaan `mencurigakan` mengubah makna, nama khas tiada rujukan bertulis, atau dua calon sama-sama munasabah dalam ayat itu.

Semua perintah menyokong `--json`, jadi langkah di atas boleh dibuat secara berulang tanpa menghurai teks.

## Peraturan

0. **Semak ejaan ikut DBP, bukan ikut ingatan.** Ini peraturan pertama kerana ia yang paling kerap dilanggar. Perkataan yang anda "tahu" ejaannya masih perlu disemak. Contoh sebenar daripada penggunaan: `sunah` ialah `سنة` tetapi `sunat` ialah `سونت`; `bena` langsung tiada ejaan Jawi dalam PRPM walaupun ia perkataan yang sah; `salat` hanyalah rujukan silang kepada `solat`. Tiada satu pun daripada perbezaan ini boleh diteka daripada bentuk Rumi. Jalankan `lookup` atau `jawi`, jangan tulis daripada ingatan.

0b. **Dua langkah untuk menentukan sesuatu perkataan betul.** Pertama, pastikan ia **ada dalam PRPM**. Kedua, semak **konteks ayat**. Langkah kedua tidak boleh dilangkau: ejaan Jawi menggunakan empat huruf vokal berbanding enam dalam Rumi, jadi `مڠيکوت` ialah ejaan sah bagi `mengikut` dan `mengekot` sekali gus. Kamus boleh mengesahkan perkataan itu wujud; hanya ayat boleh menentukan yang mana satu dimaksudkan.

0c. **PRPM sendiri boleh tersilap.** Entri `menyebuk` membawa sebutan dan definisi yang betul tetapi ejaan Jawi `مڽبوت`, iaitu ejaan `menyebut`. Pakej menandakannya dalam medan `mencurigakan` apabila huruf akhir Jawi tidak sepadan dengan hujung Rumi. Tandaan itu bermakna semak sendiri di PRPM, bukan buang.

0d. **Merumikan ialah keputusan peringkat AYAT, bukan peringkat perkataan.** Jangan tukar token demi token dan anggap ia selesai. Ejaan Jawi menggunakan empat huruf vokal berbanding enam dalam Rumi, jadi satu ejaan kerap membawa lebih daripada satu bacaan sah. `مڠيکوت` ialah `mengikut` **dan** `mengekot`, dua-duanya entri PRPM sebenar dengan sebutan sendiri. Kamus mengesahkan kedua-duanya wujud; hanya ayat menentukan yang mana dimaksudkan.

   Gunakan `--konteks` supaya ayat dan makna setiap calon dipaparkan bersama:

   ```
   مڠيکوت  ->  mengikut  atau  mengekot
     ayat: itu mengikut|mengekot sukatan pelajaran.
       mengikut    2439 rujukan  pergi bersama-sama dgn, menurut, mengiring
       mengekot       6 rujukan  merapatkan paha dan tangan ke badan
   ```

   Bilangan rujukan itu datang daripada panel korpus DBP sendiri, bukan anggaran. Ia **mengisih** calon supaya yang lazim dilihat dahulu; ia **tidak memilih**. Perkataan jarang tetap betul apabila ayat menghendakinya.

0e. **Pembahagian kerja: alat mengemukakan, ANDA memutuskan.** CLI tidak boleh membaca konteks; anda boleh. Jangan sesekali menghantar `mengikut|mengekot` sebagai hasil akhir kepada pengguna.

   Aliran yang betul:

   ```bash
   npx prpm-dbp rumi teks.txt --pilih --konteks
   ```

   `--pilih` menghasilkan teks bersih dengan memilih calon paling lazim, dan menyenaraikan setiap pilihan itu:

   ```
   itu mengikut sukatan pelajaran.
   dipilih ikut kekerapan (SEMAK konteks): مڠيکوت -> mengikut  [lain: mengekot]
   ```

   Setiap baris `dipilih` ialah tekaan berasaskan kekerapan, **bukan pemahaman konteks**. Baca ayatnya. Dalam contoh di atas `mengikut sukatan pelajaran` jelas betul dan `mengekot` mustahil. Tetapi dalam ayat tentang orang kesejukan merapatkan tangan ke badan, `mengekot` yang betul walaupun kekerapannya 6 berbanding 2439. Kekerapan ialah tekaan terbaik tanpa konteks; ia kalah kepada konteks setiap kali.

1. **Jangan hantar Jawi yang belum disemak.** Setiap perkataan Jawi dalam output mesti datang dari PRPM atau ditanda belum disahkan.
2. **Tanda, jangan sekat.** Kalau satu perkataan tiada dalam PRPM, hantar kerja itu dan senaraikan perkataan berkenaan di hujung. Jangan tahan seluruh dokumen sebab satu nama khas.
3. **Sebut sumber.** PRPM ada beberapa kamus. Bila petik definisi, sebut kamus mana (Kamus Dewan Edisi Keempat, Kamus Pelajar Edisi Kedua, dan lain-lain).
4. **Jangan percaya glosari auto.** Kamus rumi ke Jawi yang dijana secara automatik mengandungi kesilapan. Sahkan dengan `check-glosari` sebelum guna.
5. **Akronim dan nama khas dikecualikan.** PBD, KBAT, KSSM, nama orang, nama tempat memang tiada dalam PRPM. Ini bukan kegagalan, cuma tiada rujukan.

## Perintah

Panggil melalui npx tanpa pemasangan, atau `node bin/cli.js` dari repo.

```bash
# satu atau beberapa perkataan
npx prpm-dbp lookup murid sekolah

# semak setiap perkataan Rumi dalam fail wujud dalam PRPM
npx prpm-dbp audit nota.txt

# semak ejaan Jawi lawan teks Rumi sejajar
npx prpm-dbp audit-jawi rph-jawi.txt rph-rumi.txt

# panaskan cache dari senarai perkataan, bayar sekali guna selamanya
npx prpm-dbp warm senarai-kata.txt

# audit kamus rumi->jawi sedia ada terhadap PRPM
npx prpm-dbp check-glosari jawi-dictionary.json

# silang semak dua sumber rasmi DBP
npx prpm-dbp banding murid kemahiran

npx prpm-dbp cache
```

Semua perintah keluarkan JSON kecuali `lookup` tanpa `--json`.

Pilihan: `--refresh` abai cache, `--had <n>` hadkan bilangan kata, `--delay <ms>` jarak antara permintaan (default 400ms), `--json`.

## Menukar Rumi dan Jawi

**Ini bukan transliterator.** Tiada peraturan huruf demi huruf digunakan. Setiap perkataan datang dari kamus DBP atau ditanda `«begini»`. Jangan buang penanda itu tanpa menyemak.

```bash
npx prpm-dbp jawi nota.txt     # Rumi -> Jawi
npx prpm-dbp rumi jawi.txt     # Jawi -> Rumi
```

Tanda baca, baris baru dan jarak dikekalkan, jadi susun atur dokumen tidak rosak.

**Bila `jawi` menandakan perkataan**, ada dua sebab berbeza dan ia dilaporkan berasingan:

| Laporan | Maksud | Tindakan |
|---|---|---|
| `belum disahkan` | tiada dalam DBP, dan bukan terbitan mana-mana kata dasar | semak ejaan, atau isytihar nama khas |
| `terbitan sah tanpa ejaan Jawi` | bentuk sah daripada kata dasar yang wujud, tetapi DBP tiada ejaan Jawi untuknya | cari kata dasar di KamusDBP, tab kata terbitan |

Contoh kedua: `dipelajari` ialah terbitan sah daripada `ajar`. Alat ini **tidak** akan menulis Jawi bagi `ajar` di tempatnya. Itu perkataan lain, dan ejaannya berbeza.

**Calon pembacaan songsang diisih ikut rantai kepercayaan.** Perkataan hanya layak menjadi pembacaan bagi sesuatu ejaan Jawi jika PRPM memberikannya ejaan Jawi **sendiri**. Kata yang PRPM catatkan tanpa ejaan Jawi (`bena`), atau yang definisinya rujukan silang (`salat` → `® solat`), dibuang daripada senarai calon walaupun dataset komuniti memetakannya kepada ejaan yang sama.

Perkataan yang **belum diketahui** statusnya, iaitu tiada dalam cache, tidak pernah dibuang. Ketiadaan bukan bukti kegagalan. Ia kekal sebagai calon dan sumbernya dilabel `dataset`. Kerana itu memanaskan cache mengurangkan kekaburan: bukan kerana peraturan berubah, tetapi kerana bukti bertambah.

**Bila `rumi` jumpa kekaburan**, satu ejaan Jawi memetakan kepada beberapa perkataan Rumi. Semua calon dipaparkan dipisah `|`. Pilih ikut konteks ayat, jangan ambil yang pertama secara membuta.

Sebahagian kekaburan itu palsu dan diselesaikan sendiri. DBP menandakan bentuk varian dengan **rujukan silang**: definisi `kalkalah` ialah `®qalqalah.`, bukan definisi sebenar. Varian begitu dinyahutamakan, jadi `قلقله` dibaca `qalqalah` terus.

Isyaratnya mestilah tanda `®` itu, **bukan** kehadiran ejaan Jawi. `pada` tiada ejaan Jawi dalam PRPM manakala `pad` (pinjaman Inggeris) ada. Mengutamakan "yang ada Jawi" akan menukar setiap `pada` menjadi `pad`.

Jawi Melayu menulis vokal dengan alif, wau dan ya, tidak seperti Arab. Kerana itu kekaburan jarang berlaku, tetapi ia tetap wujud.

`rumi` bekerja dari cache sahaja dan tidak menyentuh rangkaian. Perkataan yang belum pernah disemak dalam arah Rumi ke Jawi tidak akan dikenali. Jalankan `warm` atau `audit` dahulu pada kosa kata yang berkaitan.

## Istilah Arab

Kebanyakan istilah agama ialah **kata Melayu**, bukan kata asing. Diuji terhadap PRPM: 29 daripada 30 istilah lazim ada entri, termasuk `yasin`, `fatihah`, `baqarah`, `rahman`, `rasulullah`. Jangan andaikan sesuatu itu Arab semata-mata kerana ia berbunyi Arab; tanya DBP dahulu.

Tiga keadaan berbeza, dilaporkan berasingan:

| Keadaan | Contoh | Maksud |
|---|---|---|
| Ada entri dan ejaan Jawi | `solat` صلاة, `zakat` زکاة | guna terus |
| `adaEntriTanpaJawi` | `yasin`, `fatihah`, `baqarah`, `wuduk` | DBP iktiraf sebagai kata Melayu, cuma tiada ejaan Jawi disimpan |
| `belumDisahkan` | `ahzab` | bukan kata Melayu; nama khas Arab |

Ejaan Jawi bagi pinjaman Arab **mengekalkan ejaan Arab asal**, bukan dieja secara fonetik: `صلاة` bukan `سولت`, `زکاة` bukan `زکات`. Ta marbutah dan alef maksura dikekalkan. Sebab itu ta marbutah tidak pernah dilipat semasa perbandingan.

Sesetengah entri memulangkan **dua perkataan Jawi untuk satu perkataan Rumi**: `rasulullah` → `رسول الله`. Pembacaan songsang memadan jujukan, jadi ia tidak pecah menjadi `rasul allah`.

**Teks Arab yang dipetik dibiarkan sepenuhnya.** Jawi Melayu tidak menggunakan baris tanda vokal, jadi kehadiran fathah, kasrah, dammah, sukun atau tanwin bermakna potongan itu Arab, bukan Jawi. Ayat al-Quran dan hadis dalam dokumen Melayu lulus tanpa disentuh dan dilaporkan dalam medan `arab`.

## Nama khas

Diuji terhadap PRPM: **nama tokoh dan nama surah tiada sumber berwibawa.** `muhammad`, `ibrahim`, `musa`, `umar`, `aisyah`, `khadijah` semuanya tiada entri. Nama surah seperti `fatihah`, `baqarah`, `yasin` ada entri tetapi tanpa ejaan Jawi. Dataset komuniti meliputi nama tempat (`mekah` مکة, `mesir` مسير, `pahang` ڤاهڠ) tetapi bukan nama orang.

Alat ini **tidak mereka ejaan nama**. Sebaliknya:

- Perkataan berhuruf besar di **tengah** ayat dikira nama khas dan dilaporkan dalam medan `namaKhas`, berasingan daripada `belumDisahkan`. Nama bukan salah eja.
- Sahkan sekali, guna selamanya:

```bash
npx prpm-dbp nama Muhammad <ejaan-jawi>
npx prpm-dbp nama          # senaraikan yang sudah disahkan
```

Ejaan yang disimpan begini ditandakan datang daripada pengguna, bukan daripada DBP. Sahkan dengan guru atau rujukan bertulis dahulu.

**Jangan keliru antara dua benda:**

| | Layanan |
|---|---|
| Petikan bahasa Arab (ayat, doa, hadis) | kekal Arab, tidak disentuh |
| Nama khas dalam ayat Melayu | **ditulis dalam Jawi**, bukan dikekalkan |

Bagi nama berasal Arab, ejaan Jawinya memang ejaan Arab asal, jadi ia nampak seperti dikekalkan. Bagi nama bukan Arab ia ditransliterasi penuh: `Kuala Lumpur` menjadi `کوالا لومڤور`.

## Sebelum menghantar apa-apa

Jalankan `npx prpm-dbp gate <fail> --luar-talian` ke atas **keseluruhan teks**. Bukan sebahagian. Bukan perkataan yang anda rasa berisiko.

Ejaan yang salah ialah tepat ejaan yang anda **tidak** syak. Kalau anda syak, anda sudah menyemaknya.

Ini termasuk ejaan yang anda tulis dalam **kod, ujian dan dokumentasi**, bukan hanya dalam teks untuk pengguna. Semasa membina pakej ini sendiri, satu nilai Jawi ditulis ke dalam ujian daripada ingatan tanpa disemak. Nilainya kebetulan betul. Kebetulan bukan proses.

**Apa yang berlaku apabila langkah ini dilangkau:**

Satu agen menggunakan skill ini sepanjang sesi penuh untuk menukar Jawi ke Rumi, kemudian menghantar 549 kad RPH Pendidikan Islam ke pangkalan data produksi dengan **273 ejaan yang salah**.

```
pusingan 1   sunnah x206, hadith x16, fardhu x2        -> masuk produksi
pusingan 2   tayammum, dhuha, qadha, redha, iddah x90  -> masuk produksi
```

Semuanya kata lazim dalam bahan Pendidikan Islam. Setiap satu ada jawapan tegas dalam PRPM: `sunnah` ialah rujukan silang ke `sunah`, `hadith` sepatutnya `hadis`, `fardhu` sepatutnya `fardu`, `tayammum` sepatutnya `tayamum`, `dhuha` sepatutnya `duha`, `qadha` sepatutnya `qada`.

Alat untuk menangkap kesemuanya ada di tangan agen itu sepanjang masa. Ia tidak pernah dijalankan ke atas teks Rumi keluarannya sendiri. Kedua-dua pusingan ditemui kerana **pengguna bertanya**, bukan kerana proses menangkapnya.

`gate` keluar dengan kod 1 apabila ada ralat, jadi ia boleh dipasang sebagai hook atau langkah CI. Peraturan yang bergantung pada agen ingat sendiri akan gagal, dan gagal secara senyap.

## Kuatkuasa automatik

```bash
npx prpm-dbp install --hook
```

Memasang hook PostToolUse yang menjalankan `gate --luar-talian` ke atas setiap fail `.md`, `.txt` atau `.json` yang ditulis atau disunting. Ia **melaporkan** ralat kepada agen tanpa menyekat tulisan: menyekat atas kamus yang tidak lengkap akan menghalang kerja yang sah, manakala melaporkan sudah memadai kerana agen membaca output hook.

Untuk menampal sendiri: `npx prpm-dbp hook-json`.

Panaskan cache **sekali** semasa pemasangan, bukan semasa gate berjalan:

```bash
npx prpm-dbp warm kosa-kata-projek.txt
```

Gate atas cache panas mengambil 0.19 saat bagi 406 perkataan. Itu cukup pantas untuk setiap penyuntingan. Gate yang perlahan akan dimatikan orang.

## Tiga keadaan gate, bukan dua

| Medan | Maksud | Kesan |
|---|---|---|
| `lulusDbp` | ada dalam PRPM, ejaan padan | — |
| `ralat` | ada dalam PRPM dengan ejaan lain, entri rujukan silang `®`, atau tiada dalam PRPM mahupun dataset | **exit 1** |
| `tidakDapatDisahkan` | tiada dalam cache, nama khas, akronim, atau tiada entri PRPM tetapi wujud dalam dataset | dilapor; exit 1 jika melebihi `--ambang` |

`tidakDapatDisahkan` **tidak pernah lulus senyap**. Gate yang lulus atas cache sejuk memberi jaminan palsu, dan itu lebih bahaya daripada tiada gate. Gunakan `--ambang 10` supaya cache sejuk menggagalkan gate.

PRPM tiada entri untuk kata tugas paling asas: `dan`, `di`, `ke`, `yang`, `atau`. Menandakannya sebagai ralat menjadikan gate mustahil dipakai. Pembezanya bersumber, bukan senarai putih: kata tugas itu **semua** wujud dalam dataset, manakala `hadith`, `fardhu`, `tayammum`, `dhuha`, `qadha`, `redha` dan `iddah` **tiada satu pun**.

## Prosedur wajib

Ikut ini setiap kali, jangan langkau langkah.

**Menulis Jawi daripada Rumi:**

1. `npx prpm-dbp jawi <fail> --luar-talian --homograf --json`
2. Baca `belumDisahkan` dan `namaKhas`. Jangan reka ejaan untuknya. Sahkan dengan `lookup`, atau tanya pengguna, atau tandakan dalam hasil akhir.
3. Baca `mencurigakan`. Setiap satu bermakna PRPM sendiri mungkin tersilap. Semak di pautan yang diberi sebelum menggunakannya.
4. Baca `homograf`. Beritahu pengguna perkataan mana yang akan taksa apabila dibaca.
5. Laporkan `semuanyaDbp`. Jika `false`, nyatakan bahagian mana bukan daripada sumber rasmi.

**Merumikan daripada Jawi:**

1. `npx prpm-dbp rumi <fail> --pilih --konteks --json`
2. Untuk **setiap** item dalam `kabur`: baca ayat yang mengandunginya, baca makna setiap calon, tentukan yang mana sesuai.
3. Jika `dipilih` salah bagi ayat itu, **gantikan** dalam hasil akhir. Kekerapan kalah kepada konteks.
4. Nyatakan kepada pengguna berapa banyak kekaburan wujud dan mana yang anda ubah.
5. `tidakDikenali` bermakna perkataan itu tiada dalam cache. Jalankan `warm` di luar talian, bukan tinggalkan bertanda.

**Berhenti dan tanya pengguna apabila:** ejaan `mencurigakan` mengubah makna, nama khas tiada rujukan bertulis, atau dua calon sama-sama munasabah dalam ayat itu.

Semua perintah menyokong `--json`, jadi langkah di atas boleh dibuat secara berulang tanpa menghurai teks.

## Peraturan Pedoman DBP

Satu fail sahaja dalam pakej ini **menjana** ejaan dan bukan mencarinya: `src/pedoman.js`. Ia dibenarkan kerana setiap peraturan di dalamnya datang daripada *Pedoman Umum Ejaan Jawi Bahasa Melayu* terbitan DBP, dan dirujuk pada seksyennya.

**§14.1 — kata sendi `di` dan `ke` ditulis sebagai satu kata dengan kata yang mengikutinya.**

```
di Mekah   ->  دمکة      bukan  د مکة
ke sekolah ->  کسکوله    bukan  ک سکوله
```

Nota seksyen yang sama: jika kata berikutnya bermula dengan alif, hamzah dibubuh di atas alif itu. `di Asrama` menjadi `دأسراما`, bukan `داسراما`.

Kodpoint hamzah (U+0623) disahkan secara empirik daripada dataset, bukan diandaikan: `abai` bermula `ا` U+0627, `diabaikan` bermula `د` + `أ` U+0623. Corak sama pada `diambil`, `diajar`, `diurus`.

Bentuk bercantum tidak wujud sebagai entri kamus, jadi pembacaan songsang memisahkannya semula dan memulihkan hamzah kepada alif. Peraturan ini simetri.

Kata sendi **tidak** dicantum apabila kata berikutnya belum disahkan, kerana hasilnya akan menjadi Jawi bercantum dengan teks Rumi bertanda.

**§11 — kata serapan Arab, dua peringkat:**

| Seksyen | Jenis | Cara eja | Contoh |
|---|---|---|---|
| §11.1 | istilah khusus | ikut ejaan asal Arab | `wuduk`, `Qur'an`, `sunah`, `hadis`, `tayamum` |
| §11.2 | kata umum yang sudah terserap | ikut kaedah kata jati Melayu | `sabun` |

Pakej tidak perlu memilih antara dua ini. DBP sudah memilih, dan pilihan itu terkandung dalam ejaan yang dipulangkan PRPM. Ia didokumenkan supaya tiada siapa "membetulkan" `صلاة` kepada ejaan fonetik Melayu kemudian hari.

**§11.6 — alif maqsurah dikekalkan.** `takwa` تقوى, `fatwa` فتوى, `Musa`, `Isa`. Ia diganti dengan alif hanya apabila dibentuk kata terbitan: `ketakwaan`, `memfatwakan`, `pendakwaan`. Perbezaan itu membawa maklumat, jadi `ى` **tidak pernah dilipat** semasa perbandingan. Melipatnya akan membuat `تقوي` yang salah dibandingkan sama dengan `تقوى` yang betul.

**§10 — enam belas perkataan dikecualikan daripada aturan pola:** `ada`, `apa`, `beta`, `bin`, `binti`, `dari`, `daripada`, `ini`, `itu`, `jika`, `kepada`, `kita`, `lima`, `maka`, `pada`, `demikian`. Ejaannya mesti datang daripada kamus dan tidak boleh dibetulkan oleh mana-mana peraturan pola.

**§17.1 — kata ulang penuh menggunakan angka dua Arab `٢`**, bukan sempang dan bukan kata dasar ditulis dua kali.

```
Cikgu-cikgu  ->  چيقݢو٢
murid-murid  ->  موريد٢
```

Kata dasar dicari dalam kamus seperti biasa, jadi ejaannya tetap datang daripada sumber. Hanya penambahan `٢` itu dijana oleh peraturan. Peraturan ini simetri: `چيقݢو٢` dibaca semula sebagai `cikgu-cikgu`.

§17.2 mengecualikan kata ulang yang **berubah bentuk** (berimbuhan atau berentak) seperti `bermaaf-maafan`. Di situ sempang kekal dan peraturan `٢` tidak terpakai, jadi padanan menuntut kedua-dua bahagian benar-benar serupa.

## Penjanaan imbuhan (pilihan, mati secara lalai)

`--pedoman` membenarkan pakej **menjana** ejaan kata berimbuhan menurut §13 dan §15, apabila tiada sumber lain mempunyai jawapan. Ejaan kata dasar tetap dicari dalam kamus; hanya imbuhan yang dijana.

Ia mati secara lalai kerana ketepatannya diukur, bukan diandaikan. Diuji terhadap 66,000 perkataan sebenar:

| Imbuhan | Kes diuji | Betul |
|---|---|---|
| `-mu` | 333 | 98% |
| `-nya` | 3,697 | 97% |
| `di-` | 2,435 | 92% |
| `se-` | 1,605 | 90% |
| `ke-` | 976 | 88% |
| `-kan` | 3,046 | 92% |

Sebahagian kegagalan itu artifak ukuran (`tamu` bukan `ta`+`mu`), jadi ketepatan sebenar lebih tinggi. Walaupun begitu, 98% tidak cukup untuk menjana ejaan secara senyap dalam alat yang seluruh tujuannya menghalang Jawi yang nampak betul tetapi salah.

Bila dihidupkan, hasilnya **tetap ditanda** dan dilaporkan dalam medan `dijanaPedoman` berserta rujukan seksyennya:

```
$ npx prpm-dbp jawi nota.txt --pedoman
«اباديڽ» ککل.
dijana ikut Pedoman, BELUM disahkan DBP (1): Abadinya=اباديڽ [Pedoman DBP §15.1]
```

Peraturan yang dilaksanakan: §13.5-13.7 (awalan `di-`, `se-`, `ke-`), §13.9 (akhiran `-kan`), §13.10 (tiga cara akhiran `-an`), §13.11 (tiga cara akhiran `-i`), §15.1 (kata ganti singkat `ku`, `mu`, `nya`).

**Awas terhadap nota panduan tidak rasmi.** Nota sekolah sering menyatakan "huruf alif ditambah semula apabila menerima imbuhan" sebagai peraturan menyeluruh. Diuji terhadap data sebenar, itu tidak benar: `suka` سوک menjadi `sukakan` سوککن dan `lukanya` لوکڽ, kedua-duanya tanpa alif. Penambahan alif berlaku bagi `-an` dan `-i` sahaja, menurut Catatan §13.10(ii) dan §13.11(i).

**Kodpoint setiap imbuhan disahkan empirik daripada dataset, bukan dibaca daripada peraturan.** Hamzah dalam akhiran ialah `ء` U+0621 berdiri sendiri, manakala dalam awalan ia `أ` U+0623 di atas alif. Peraturan bertulis tidak membezakan dua itu.

## Apa yang sebenarnya tercicir

Diuji terhadap satu pelajaran Pendidikan Islam Tingkatan 4 sebenar (406 perkataan):

```
disahkan DBP  223   |  dari dataset  38  |  kata sendi §14.1  3
nama khas      15   |  belum disahkan 10
```

**Tiada satu pun kata Melayu biasa tercicir.** Yang tinggal terbahagi kepada dua kategori sahaja:

| Kategori | Contoh | Selesaian |
|---|---|---|
| Istilah tajwid Arab | `Qasirah`, `Tawilah`, `Mukhaffaf`, `Muthaqqal`, `Sughra`, `Kubra`, `Waqaf`, `hijaiyah` | `nama` dengan `jenis: istilah`, sahkan sekali |
| Akronim | `KPM`, `DBP`, `KSSM`, `PBD`, `KBAT` | dilaporkan berasingan, kekal Rumi |

Inilah sebab transliterator pola suku kata (§7 hingga §9) **tidak dilaksanakan**. Ia tidak akan menyentuh satu pun perkataan di atas. Lebih buruk lagi, istilah tajwid itu kata serapan Arab, dan §11.1 menetapkan ejaan Jawinya ialah ejaan Arab asal — peraturan pola Melayu akan menghasilkan ejaan fonetik yang salah untuk setiap satu, iaitu tepat di tempat ia paling merosakkan.

**`audit-jawi` menggunakan enjin penukaran yang sama dengan `jawi`.** Teks Rumi ditukar dahulu, kemudian dibandingkan. Ini penting kerana §14.1 dan §17.1 mengubah bilangan token: `Murid ke sekolah` ialah tiga perkataan Rumi tetapi dua perkataan Jawi. Perbandingan kedudukan yang naif akan melaporkan output alat ini sendiri sebagai salah.

Dengan cara ini hanya ada **satu takrif "Jawi yang betul"** dalam pakej, bukan dua yang boleh menyimpang.

## Pengesahan pusingan penuh

Diuji terhadap 406 perkataan pelajaran Pendidikan Islam Tingkatan 4 sebenar, ditukar ke Jawi kemudian dipusingkan balik ke Rumi:

```
token asal 336  ->  token pusing balik 336
padan tepat        314 (93%)
padan antara calon  22 (7%)
TIDAK padan          0 (0%)
```

Dua puluh dua kes kabur yang tinggal itu sah: `قلقله` boleh dibaca `qalqalah` atau `kalkalah`, dua ejaan Rumi yang sama-sama betul bagi istilah Arab yang sama. Alat melaporkan kedua-duanya dan tidak memilih.

**Sifar perkataan hilang atau rosak.**

## Sebelum menghantar apa-apa

Jalankan `npx prpm-dbp gate <fail> --luar-talian` ke atas **keseluruhan teks**. Bukan sebahagian. Bukan perkataan yang anda rasa berisiko.

Ejaan yang salah ialah tepat ejaan yang anda **tidak** syak. Kalau anda syak, anda sudah menyemaknya.

Ini termasuk ejaan yang anda tulis dalam **kod, ujian dan dokumentasi**, bukan hanya dalam teks untuk pengguna. Semasa membina pakej ini sendiri, satu nilai Jawi ditulis ke dalam ujian daripada ingatan tanpa disemak. Nilainya kebetulan betul. Kebetulan bukan proses.

**Apa yang berlaku apabila langkah ini dilangkau:**

Satu agen menggunakan skill ini sepanjang sesi penuh untuk menukar Jawi ke Rumi, kemudian menghantar 549 kad RPH Pendidikan Islam ke pangkalan data produksi dengan **273 ejaan yang salah**.

```
pusingan 1   sunnah x206, hadith x16, fardhu x2        -> masuk produksi
pusingan 2   tayammum, dhuha, qadha, redha, iddah x90  -> masuk produksi
```

Semuanya kata lazim dalam bahan Pendidikan Islam. Setiap satu ada jawapan tegas dalam PRPM: `sunnah` ialah rujukan silang ke `sunah`, `hadith` sepatutnya `hadis`, `fardhu` sepatutnya `fardu`, `tayammum` sepatutnya `tayamum`, `dhuha` sepatutnya `duha`, `qadha` sepatutnya `qada`.

Alat untuk menangkap kesemuanya ada di tangan agen itu sepanjang masa. Ia tidak pernah dijalankan ke atas teks Rumi keluarannya sendiri. Kedua-dua pusingan ditemui kerana **pengguna bertanya**, bukan kerana proses menangkapnya.

`gate` keluar dengan kod 1 apabila ada ralat, jadi ia boleh dipasang sebagai hook atau langkah CI. Peraturan yang bergantung pada agen ingat sendiri akan gagal, dan gagal secara senyap.

## Kuatkuasa automatik

```bash
npx prpm-dbp install --hook
```

Memasang hook PostToolUse yang menjalankan `gate --luar-talian` ke atas setiap fail `.md`, `.txt` atau `.json` yang ditulis atau disunting. Ia **melaporkan** ralat kepada agen tanpa menyekat tulisan: menyekat atas kamus yang tidak lengkap akan menghalang kerja yang sah, manakala melaporkan sudah memadai kerana agen membaca output hook.

Untuk menampal sendiri: `npx prpm-dbp hook-json`.

Panaskan cache **sekali** semasa pemasangan, bukan semasa gate berjalan:

```bash
npx prpm-dbp warm kosa-kata-projek.txt
```

Gate atas cache panas mengambil 0.19 saat bagi 406 perkataan. Itu cukup pantas untuk setiap penyuntingan. Gate yang perlahan akan dimatikan orang.

## Tiga keadaan gate, bukan dua

| Medan | Maksud | Kesan |
|---|---|---|
| `lulusDbp` | ada dalam PRPM, ejaan padan | — |
| `ralat` | ada dalam PRPM dengan ejaan lain, entri rujukan silang `®`, atau tiada dalam PRPM mahupun dataset | **exit 1** |
| `tidakDapatDisahkan` | tiada dalam cache, nama khas, akronim, atau tiada entri PRPM tetapi wujud dalam dataset | dilapor; exit 1 jika melebihi `--ambang` |

`tidakDapatDisahkan` **tidak pernah lulus senyap**. Gate yang lulus atas cache sejuk memberi jaminan palsu, dan itu lebih bahaya daripada tiada gate. Gunakan `--ambang 10` supaya cache sejuk menggagalkan gate.

PRPM tiada entri untuk kata tugas paling asas: `dan`, `di`, `ke`, `yang`, `atau`. Menandakannya sebagai ralat menjadikan gate mustahil dipakai. Pembezanya bersumber, bukan senarai putih: kata tugas itu **semua** wujud dalam dataset, manakala `hadith`, `fardhu`, `tayammum`, `dhuha`, `qadha`, `redha` dan `iddah` **tiada satu pun**.

## Prosedur wajib

Ikut ini setiap kali, jangan langkau langkah.

**Menulis Jawi daripada Rumi:**

1. `npx prpm-dbp jawi <fail> --luar-talian --homograf --json`
2. Baca `belumDisahkan` dan `namaKhas`. Jangan reka ejaan untuknya. Sahkan dengan `lookup`, atau tanya pengguna, atau tandakan dalam hasil akhir.
3. Baca `mencurigakan`. Setiap satu bermakna PRPM sendiri mungkin tersilap. Semak di pautan yang diberi sebelum menggunakannya.
4. Baca `homograf`. Beritahu pengguna perkataan mana yang akan taksa apabila dibaca.
5. Laporkan `semuanyaDbp`. Jika `false`, nyatakan bahagian mana bukan daripada sumber rasmi.

**Merumikan daripada Jawi:**

1. `npx prpm-dbp rumi <fail> --pilih --konteks --json`
2. Untuk **setiap** item dalam `kabur`: baca ayat yang mengandunginya, baca makna setiap calon, tentukan yang mana sesuai.
3. Jika `dipilih` salah bagi ayat itu, **gantikan** dalam hasil akhir. Kekerapan kalah kepada konteks.
4. Nyatakan kepada pengguna berapa banyak kekaburan wujud dan mana yang anda ubah.
5. `tidakDikenali` bermakna perkataan itu tiada dalam cache. Jalankan `warm` di luar talian, bukan tinggalkan bertanda.

**Berhenti dan tanya pengguna apabila:** ejaan `mencurigakan` mengubah makna, nama khas tiada rujukan bertulis, atau dua calon sama-sama munasabah dalam ayat itu.

Semua perintah menyokong `--json`, jadi langkah di atas boleh dibuat secara berulang tanpa menghurai teks.

## Peraturan yang sengaja TIDAK dilaksanakan

| Seksyen | Sebab |
|---|---|
| §7–§9 pola suku kata | Diuji terhadap dokumen sebenar: tiada kata Melayu biasa tercicir, jadi ia takkan bertindak. Yang tinggal ialah istilah serapan Arab, yang menurut §11.1 dieja ikut Arab asal — peraturan pola Melayu akan salah tepat di situ. |
| §19.3 akronim jadi huruf Jawi | §2.2 memetakan **qaf dan kaf kedua-duanya** kepada Rumi `k`. Bagi `KPM`, tiada apa dalam dokumen menentukan huruf mana. Akronim dilaporkan, tidak ditukar. |
| §12 serapan Inggeris | Kamus sudah meliputinya; tiada penjanaan diperlukan. |
| §16.2, §18 | Tidak memerlukan kod: perbezaannya sudah dikodkan dalam jarak teks Rumi. Ada ujian yang mengunci tingkah laku ini. |

## Menyambung ke aplikasi

Kelajuan diukur pada 406 perkataan:

| Keadaan | Masa |
|---|---|
| Semua dari cache | **0.19 saat** |
| Dua perkataan baharu (perlu rangkaian) | **30.7 saat** |

Perbezaan itu menentukan cara ia disambung. **Dalam laluan permintaan aplikasi, gunakan `--luar-talian` sentiasa.** Mod itu tidak pernah menyentuh rangkaian: perkataan yang tiada dalam cache dilaporkan belum disahkan, bukan diambil secara segerak sambil pengguna menunggu.

Urutan yang disyorkan:

1. **Panaskan cache di luar talian** daripada kosa kata projek anda: `npx prpm-dbp warm senarai.txt`
2. **Hantar cache itu bersama aplikasi** (salin `~/.cache/prpm-dbp/cache.json`, atau tetapkan `PRPM_CACHE`)
3. **Jalankan dengan `--luar-talian`** dalam laluan permintaan
4. **Kumpul medan `belumDisahkan`** dan panaskan cache secara berkala di luar talian

Dengan cara ini pengguna tidak pernah menunggu PRPM, dan PRPM tidak pernah menerima trafik daripada aplikasi anda.

## Pengesahan bebas kodpoint

Keputusan lipatan kodpoint disahkan terhadap sumber kedua yang bebas: *Kaedah Pembelajaran Jawi: Peringkat Asas* (Haji Azman bin Haji Ahmad, Perpustakaan Negara Malaysia, 2015), Siri Ke-2 "Huruf Gubalan Melayu".

| Huruf | Kodpoint | Keputusan pakej |
|---|---|---|
| `ڤ` p | U+06A4 | bukan `پ` U+067E |
| `ڽ` ny | U+06BD | `پ` huruf berlainan, tidak pernah dilipat |
| `ݢ` g | U+0762 | varian `ڬ` U+06AC dilipat |
| `ک` kaf Jawi | U+06A9 | `ك` Arab U+0643 dilipat |

Siri yang sama membezakan kaf Arab daripada kaf Jawi secara eksplisit, iaitu perbezaan yang menyebabkan PRPM dan KamusDBP kelihatan bercanggah.

Buku itu berhak cipta terpelihara (PNM). Ia dirujuk di sini sebagai pengesahan, bukan disalin.

## Homograf

Ejaan Jawi menggunakan empat huruf vokal berbanding enam dalam Rumi, jadi perkataan berbeza kerap berkongsi ejaan. Diukur pada 64,251 ejaan Jawi dalam data: **2,009 (3%) ialah homograf**.

Panduan DBP membahagikannya kepada dua:

| Jenis | Layanan | Contoh |
|---|---|---|
| **Hakiki** | **dibenarkan kekal**, tidak dibezakan | `biru`/`biro` بيرو, `burung`/`borong` بوروڠ, `satu`/`sato` ساتو |
| **Tidak hakiki** | dibezakan dengan alif | `kampung` کامڤوڠ / `kempung` کمڤوڠ |

Jenis kedua ialah Pedoman §8.17 (juga Klinik Jawi Siri ke-35): alif melambangkan [a] di suku kata tertutup untuk membezakannya daripada pepet yang tidak dilambangkan. **Ejaan DBP sudah mengandungi peraturan ini**, jadi pakej tidak perlu menjananya. Sembilan pasangan rujukan diuji, kesemuanya dibezakan dengan betul.

**Istilah "hakiki" dan "tidak hakiki" tidak digunakan dalam pakej ini.** Sumber pengajaran bercanggah tentang mana satu bermaksud apa: satu panduan menyatakan hakiki bermaksud tidak dibezakan dan dibenarkan kekal, satu lagi menyatakan hakiki bermaksud boleh diselesaikan dengan huruf saksi. Pakej melaporkan fakta yang boleh diperhatikan, iaitu ejaan Jawi ini dikongsi oleh perkataan-perkataan berikut, dan membiarkan pengelasan itu kepada guru.

Sebahagian homograf ejaan lama telah diselesaikan oleh ejaan baharu dengan menambah huruf vokal: `tulang` تولڠ dan `tolong` تولوڠ, `tujuh` توجوه dan `tujah` توجه. Pembezaan itu ada dalam ejaan yang kita ambil dan dikunci dengan ujian.

Jenis yang tinggal pula tidak boleh diselesaikan oleh mana-mana peraturan. Itulah sebab `rumi` melaporkan semua calon dan enggan memilih: bagi homograf hakiki, DBP sendiri menyatakan kekaburan itu dibenarkan dan hanya konteks ayat boleh menyelesaikannya.

`--homograf` menandakan perkataan yang ejaan Jawinya dikongsi, supaya penulis tahu sebelum menghantar bahawa pembaca akan bergantung pada konteks:

```
$ npx prpm-dbp jawi nota.txt --homograf
اݢام ايت ساتو دان بوروڠ تربڠ.
homograf, ejaan Jawi dikongsi (3): اݢام=agama/igama, ساتو=satu/sato, بوروڠ=burung/borong
```

## Laporan check-glosari

Empat kelas berasingan, kerana menggabungkannya menenggelamkan yang penting:

| Medan | Maksud |
|---|---|
| `bercanggah` | ejaan benar-benar berbeza daripada PRPM |
| `ejaanLama` | berbeza **hanya** pada pasangan ortografi lama/baharu yang boleh dipetakan: `ف`→`ڤ`, `ج`→`چ`, `ة`→`ه`, `ك`→`ک` |
| `homograf` | dua kata Rumi dalam glosari berkongsi ejaan Jawi yang sama |
| `mencurigakan` | glosari sepadan dengan PRPM, tetapi huruf akhir PRPM sendiri tidak munasabah |

`homograf` ialah satu-satunya kelas yang lolos senyap daripada semakan pasangan biasa: glosari boleh sepadan dengan PRPM dan **tetap salah** kerana ejaan itu dikongsi. `اية` sepadan dengan `ayah` dalam DBP, tetapi dalam teks al-Quran ia `آية` bermaksud `ayat`. Menerima DBP membuta di situ menghasilkan "menyatakan takrif **ayah** 1-5 Surah Al-Baqarah".

Penambahan huruf vokal **tidak** dikira ejaan lama. Kalau kehadiran `ي` diabaikan, `بينا` (bina) dan `بنا` (bena) akan kelihatan sama, sedangkan itu dua perkataan berlainan.

## Ejaan bukan piawai DBP

Dokumen kurikulum Malaysia kerap menggunakan ortografi Jawi yang berbeza daripada piawai DBP. Pasangan yang disahkan daripada DSKP Pendidikan Islam KPM sebenar:

| Rumi | DBP | DSKP | Beza |
|---|---|---|---|
| surah | `سورة` | `سوره` | ta marbutah jadi ha |
| iktibar | `اعتبار` | `اعتبر` | alif digugurkan |
| bersifat | `برصيفت` | `برصفة` | ya digugurkan, ta marbutah |
| dalil | `دليل` | `داليل` | alif ditambah |
| syirik | `شيريک` | `شرك` | ya digugurkan, kaf Arab |

Kesemuanya berbeza hanya pada huruf vokal dan bentuk huruf, bukan pada rangka konsonan. `--rangka` memadan rangka itu:

```
$ npx prpm-dbp rumi dskp.txt --luar-talian --rangka
ejaan bukan piawai DBP (209):
  اعتبر -> iktibar  [DBP eja: اعتبار]
  سوره  -> surah    [DBP eja: سورة]
  کتب   -> kitab|katib|kutub  (rangka sama, PILIH ikut konteks)
```

Diukur pada 168 baris DSKP sebenar: token yang langsung tidak dikenali turun daripada 100 kepada 47.

**Ini lapisan terakhir dan ia LOSSY.** 29% rangka dikongsi lebih daripada satu perkataan, purata 4.1 calon. Ia hanya digunakan selepas padanan tepat gagal, hasilnya sentiasa berlabel `ejaanBukanPiawai` berserta ejaan DBP untuk perbandingan, dan ia **tidak pernah memilih sendiri** apabila calon berbilang.

Kegunaannya bukan menjadikan alat lebih longgar. Ia membezakan **"ini surah, cuma dieja lain"** daripada **"saya tidak kenal benda ini"** — dua keadaan yang dahulu keluar sama sebagai `«...»`.

## Kekaburan dalam teks domain

`--pilih` menggunakan kekerapan korpus. Dalam teks am ia betul hampir sentiasa. Dalam **teks domain ia gagal tepat di tempat paling penting**: setiap istilah khusus kalah kepada kata lazim yang berkongsi rangka konsonan.

Diukur pada DSKP KKQ/PQS: salah untuk 5 daripada 16 pasangan taksa, dan kelima-limanya ialah inti pelajaran.

```
اسم  -> asam    patut isim      "hamzah wasal pada kalimah asam dan faal"
فعل  -> faal    patut fiil
رسم  -> rasam   patut Rasm (Uthmani)
سنة  -> sanat   patut sunah
```

Gunakan glosari domain. Ia **mengatasi segalanya**, termasuk lapisan 1:

```bash
npx prpm-dbp rumi dskp.txt --pilih --glosari istilah-kkq.json
```

```json
{ "اسم": "isim", "فعل": "fiil", "رسم": "rasm", "سنة": "sunah" }
```

Setiap penggunaan direkod dalam medan `dariGlosari`, jadi ia boleh diaudit. Bina glosari itu sekali untuk domain anda; ia lebih murah daripada membetulkan output berulang kali, dan ia menjadi rekod: siapa membenarkan istilah apa.

Kekerapan kekal sebagai pemecah seri sahaja, dan setiap keputusan tetap dilapor dalam `kabur` untuk semakan.

## Rantai kepercayaan

Setiap ejaan Jawi yang dikeluarkan membawa asal-usulnya. Jangan runtuhkan lapisan ini.

| Lapisan | Sumber | Status |
|---|---|---|
| 1 | PRPM DBP | rasmi |
| 2 | KamusDBP | rasmi |
| 3 | dataset diimport pengguna | komuniti, berlabel `sumber: dataset` |
| 4 | tekaan model | **tidak pernah dibenarkan** |

Medan `semuanyaDbp` bernilai `true` hanya apabila setiap perkataan datang dari lapisan 1 atau 2. Untuk bahan yang akan dicetak atau diajar, semak medan itu.

Dataset lapisan 3 tidak dihantar bersama pakej kerana sebab lesen. Import sendiri:

```bash
npx prpm-dbp import-tsv rumi-jawi.tsv
```

Format: `rumi<TAB>jawi` setiap baris. Sumber yang diketahui: github.com/goodmami/rumi-jawi, 66k entri termasuk bentuk berimbuhan. Lesennya **belum ditetapkan**, jadi jangan edarkan semula tanpa menyemak dengan penyelenggaranya.

Sempang tidak pernah dinormalkan semasa import. `abad` (ابد) dan `ab-ad` (ابعاض) ialah perkataan berlainan. Menggabungkannya ialah punca sebenar ejaan salah dalam glosari yang dijana automatik.

## Cara guna dalam kerja menulis

Bila menulis teks BM atau Jawi yang akan dihantar kepada orang:

1. Karang dalam Rumi dahulu, penuh dan siap.
2. `audit` teks Rumi itu. Perkataan yang tiada dalam PRPM biasanya salah eja atau istilah rekaan.
3. Untuk Jawi, ambil ejaan dari medan `jawiRasmi` dalam laporan audit. Jangan transliterasi sendiri.
4. Kalau Jawi sudah ditulis, `audit-jawi` bandingkan lawan Rumi sejajar.
5. Senaraikan apa-apa dalam `belumDisahkan` atau `tiadaRujukan` kepada pengguna, dengan pautan PRPM.

## Tafsiran keputusan

| Medan | Maksud | Tindakan |
|---|---|---|
| `lulus` / `padan` | ada dalam PRPM, ejaan sepadan | tiada |
| `belumDisahkan` | tiada entri dalam PRPM | semak ejaan, atau isytihar sebagai nama khas |
| `salahEjaan` | Jawi ditulis tidak sama dengan hasil penukaran | ganti dengan medan `sepatutnya` |
| `tiadaRujukan` | PRPM tiada ejaan Jawi untuk kata itu | kekalkan, tanda kepada pengguna |
| `gagalSemak` | rangkaian gagal | cuba semula, jangan anggap salah |

## Dua sumber DBP

| Sumber | Guna untuk | Nota |
|---|---|---|
| PRPM (`prpm.dbp.gov.my`) | utama, semua perintah | liputan luas, banyak kamus, ada tesaurus dan peribahasa |
| KamusDBP (`kamus.dbp.gov.my`) | silang semak `banding` | definisi lebih moden, liputan lebih kecil |

Keputusan `banding`:

| Status | Maksud | Keyakinan |
|---|---|---|
| `sepakat` | dua sumber beri ejaan Jawi sama | tinggi |
| `satu_sumber` | satu ada, satu tiada entri | sederhana |
| `bercanggah` | dua sumber beri ejaan berbeza | rendah, medan `jawi` sengaja `null` |
| `tiada_langsung` | dua-dua tiada | rendah |

Bila `bercanggah`, jangan pilih sendiri. Lapor dua-dua ejaan dengan pautan dan biar manusia putuskan.

**Jangan banding medan sebutan.** PRPM beri pemisahan suku kata (`a.bad`), KamusDBP beri transkripsi IPA (`ɑ.bɑd`). Dua-dua betul, cuma benda berlainan.

## Nota teknikal

- Endpoint: `GET https://prpm.dbp.gov.my/Cari1?keyword=<kata>`. Tiada auth, tiada VIEWSTATE, tiada JS. Browser automation tidak diperlukan.
- **PRPM balas HTTP 200 walaupun perkataan tiada dalam kamus.** Status code tidak boleh dipercayai. Penanda sebenar ialah ketiadaan `div.tab-pane`.
- Ejaan Jawi ada dalam `<font class="cadr">`, satu per tab kamus.
- Kegagalan rangkaian tidak dicache. Hanya jawapan sebenar dicache.
- Cache di `~/.cache/prpm-dbp/cache.json`, boleh ubah dengan `PRPM_CACHE`.
- Kandungan PRPM hak milik DBP. Skill ini untuk semakan dan rujukan. Jangan salin pukal kandungan kamus untuk diedar semula.
