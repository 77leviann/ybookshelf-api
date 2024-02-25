# Submission Bookshelf API
![Nilai](./screenshots/penilaian.png)

```bash
# Perintah untuk menginstal semua paket yang diperlukan

npm run install
```

```bash
# Perintah untuk menjalankan project

npm run start
```

## Terdapat 7 kriteria utama yang harus Anda penuhi dalam membuat proyek Bookshelf API.
### Kriteria 1 : Aplikasi menggunakan port 9000
Aplikasi yang Anda buat harus menggunakan port 9000. Jika komputer yang Anda gunakan untuk membuat submission tidak bisa memakai port 9000,  buatlah submission dengan port lain, lalu ketika submission hendak dikirimkan silakan ganti portnya ke 9000.

### Kriteria 2 : Aplikasi dijalankan dengan perintah <span style="color:red">npm run start</span>.
Aplikasi yang Anda buat harus memiliki runner script <span style="color:red"> start</span>. Cara membuatnya, Anda tambahkan properti <span style="color:red"> start</span> ke dalam properti <span style="color:red"> scripts </span>pada package.json seperti berikut:

```json
{
  "name": "submission",
  ...
  "scripts": {
    "start": "node src/server.js",
  }
}
```

Pastikan aplikasi **tidak** dijalankan dengan menggunakan **nodemon**. Jika Anda ingin menggunakan nodemon dalam proses development, masukkan nodemon kedalam runner script lain, contohnya:

```json
{
  "name": "submission",
  ...
  "scripts": {
    "start": "node src/server.js",
    "start-dev": "nodemon src/server.js",
  }
}
```

### Kriteria 3 : API dapat menyimpan buku
API yang Anda buat harus dapat menyimpan buku melalui route:

Method : **POST**
URL : **/books**
Body Request:

```json
{
    "name": string,
    "year": number,
    "author": string,
    "summary": string,
    "publisher": string,
    "pageCount": number,
    "readPage": number,
    "reading": boolean
}
```

Objek buku yang disimpan pada server harus memiliki struktur seperti contoh di bawah ini:

```json
{
    "id": "Qbax5Oy7L8WKf74l",
    "name": "Buku A",
    "year": 2010,
    "author": "John Doe",
    "summary": "Lorem ipsum dolor sit amet",
    "publisher": "Dicoding Indonesia",
    "pageCount": 100,
    "readPage": 25,
    "finished": false,
    "reading": false,
    "insertedAt": "2021-03-04T09:11:44.598Z",
    "updatedAt": "2021-03-04T09:11:44.598Z"
}
```

Properti yang ditebalkan diolah dan didapatkan di sisi server. Berikut penjelasannya:

- <span style="color:red">id </span>: nilai<span style="color:red"> id </span> haruslah unik. Untuk membuat nilai unik, Anda bisa memanfaatkan nanoid. Untuk Anda yang menggunakan CommonJS untuk sistem modularisasi, pastikan memasang nanoid versi 3 melalui perintah: <span style="color:red">npm install nanoid@3</span>.
- <span style="color:red">finished</span> : merupakan properti boolean yang menjelaskan apakah buku telah selesai dibaca atau belum.
- Nilai <span style="color:red">finished</span> didapatkan dari observasi <span style="color:red">pageCount === readPage</span>.
- <span style="color:red">insertedAt</span>  : merupakan properti yang menampung tanggal dimasukkannya buku. Anda bisa gunakan <span style="color:red">new Date().toISOString()</span> untuk menghasilkan nilainya.
- <span style="color:red">updatedAt</span>  : merupakan properti yang menampung tanggal diperbarui buku. Ketika buku baru dimasukkan, berikan nilai properti ini sama dengan <span style="color:red">insertedAt</span> .

Server harus merespons **gagal** bila:
- Client tidak melampirkan properti <span style="color:red">name</span> pada request body. Bila hal ini terjadi, maka server akan merespons dengan:
    - Status Code : **400**
    - Response Body:

```json
{
    "status": "fail",
    "message": "Gagal menambahkan buku. Mohon isi nama buku"
}
```

- Client melampirkan nilai properti <span style="color:red">readPage</span>  yang lebih besar dari nilai properti pageCount. Bila hal ini terjadi, maka server akan merespons dengan:
    - Status Code : 400
    - Response Body:

```json
{
    "status": "fail",
    "message": "Gagal menambahkan buku. readPage tidak boleh lebih besar dari pageCount"
}
```

Bila buku **berhasil** dimasukkan, server harus mengembalikan respons dengan:
- Status Code : **201**
- Response Body:

```json
{
    "status": "success",
    "message": "Buku berhasil ditambahkan",
    "data": {
        "bookId": "1L7ZtDUFeGs7VlEt"
    }
}
```

### Kriteria 4 : API dapat menampilkan seluruh buku
API yang Anda buat harus dapat menampilkan seluruh buku yang disimpan melalui route:
- Method : **GET**
- URL: **/books**

Server harus mengembalikan respons dengan:

- Status Code : **200**
- Response Body:

```json
{
    "status": "success",
    "data": {
        "books": [
            {
                "id": "Qbax5Oy7L8WKf74l",
                "name": "Buku A",
                "publisher": "Dicoding Indonesia"
            },
            {
                "id": "1L7ZtDUFeGs7VlEt",
                "name": "Buku B",
                "publisher": "Dicoding Indonesia"
            },
            {
                "id": "K8DZbfI-t3LrY7lD",
                "name": "Buku C",
                "publisher": "Dicoding Indonesia"
            }
        ]
    }
}
```

Jika **belum** terdapat buku yang dimasukkan, server bisa merespons dengan array <span style="color:red">books</span> kosong.

```json
{
    "status": "success",
    "data": {
        "books": []
    }
}
```

### Kriteria 5 : API dapat menampilkan detail buku
API yang Anda buat harus dapat menampilkan seluruh buku yang disimpan melalui route:
- Method : **GET**
- URL: **/books/{bookId}**

Bila buku dengan <span style="color:red">id</span> yang dilampirkan oleh client tidak ditemukan, maka server harus mengembalikan respons dengan:
- Status Code : **404**
- Response Body:

```json
{
    "status": "fail",
    "message": "Buku tidak ditemukan"
}
```

Bila buku dengan <span style="color:red">id</span> yang dilampirkan **ditemukan**, maka server harus mengembalikan respons dengan:
- Status Code : **200**
- Response Body:

```json
{
    "status": "success",
    "data": {
        "book": {
            "id": "aWZBUW3JN_VBE-9I",
            "name": "Buku A Revisi",
            "year": 2011,
            "author": "Jane Doe",
            "summary": "Lorem Dolor sit Amet",
            "publisher": "Dicoding",
            "pageCount": 200,
            "readPage": 26,
            "finished": false,
            "reading": false,
            "insertedAt": "2021-03-05T06:14:28.930Z",
            "updatedAt": "2021-03-05T06:14:30.718Z"
        }
    }
}
```

### Kriteria 6 : API dapat mengubah data buku
API yang Anda buat harus dapat mengubah data buku berdasarkan id melalui route:
- Method : **PUT**
- URL : **/books/{bookId}**
- Body Request:

```json
{
    "name": string,
    "year": number,
    "author": string,
    "summary": string,
    "publisher": string,
    "pageCount": number,
    "readPage": number,
    "reading": boolean
}
```

Server harus merespons **gagal** bila:
- Client tidak melampirkan properti <span style="color:red">name</span> pada request body. Bila hal ini terjadi, maka server akan merespons dengan:
    - Status Code : **400**
    - Response Body:

```json
{
    "status": "fail",
    "message": "Gagal memperbarui buku. Mohon isi nama buku"
}
```

- Client melampirkan nilai properti <span style="color:red">readPage</span> yang lebih besar dari nilai properti <span style="color:red">pageCount</span>. Bila hal ini terjadi, maka server akan merespons dengan:
    - Status Code : **400**
    - Response Body:

```json
{
    "status": "fail",
    "message": "Gagal memperbarui buku. readPage tidak boleh lebih besar dari pageCount"
}
```

- <span style="color:red">Id</span> yang dilampirkan oleh client tidak ditemukkan oleh server. Bila hal ini terjadi, maka server akan merespons dengan:
    - Status Code : **404**
    - Response Body:

```json
{
    "status": "fail",
    "message": "Gagal memperbarui buku. Id tidak ditemukan"
}
```

Bila buku **berhasil diperbarui**, server harus mengembalikan respons dengan:
- Status Code : **200**
- Response Body:

```json
{
    "status": "success",
    "message": "Buku berhasil diperbarui"
}
```

### Kriteria 7 : API dapat menghapus buku
API yang Anda buat harus dapat menghapus buku berdasarkan id melalui route berikut:
- Method : **DELETE**
- URL: /books/{bookId}
  
Bila <span style="color:red">id</span> yang dilampirkan tidak dimiliki oleh buku manapun, maka server harus mengembalikan respons berikut:
- Status Code : **404**
- Response Body:

```json
{
    "status": "fail",
    "message": "Buku gagal dihapus. Id tidak ditemukan"
}
```

Bila <span style="color:red">id</span> dimiliki oleh salah satu buku, maka buku tersebut harus dihapus dan server mengembalikan respons berikut:
- Status Code : **200**
- Response Body:

```json
{
    "status": "success",
    "message": "Buku berhasil dihapus"
}
```

## Pengujian 
Ketika membangun Bookshelf API, tentu Anda perlu menguji untuk memastikan API berjalan sesuai dengan kriteria yang ada. Kami sudah menyediakan berkas Postman Collection dan Environment yang dapat Anda gunakan untuk pengujian. Silakan unduh berkasnya pada tautan berikut:

Postman Bookshelf API Test Collection dan Environment

Anda perlu meng-import kedua berkas tersebut pada Postman untuk menggunakannya. Caranya, ekstrak berkas yang sudah diunduh hingga menghasilkan dua berkas file JSON.

Kemudian pada aplikasi Postman, klik tombol **import** yang berada di atas panel kiri aplikasi Postman.

Kemudian klik tombol **Upload Files** untuk meng-import kedua berkas JSON hasil ekstraksi.

Setelah itu, Bookshelf API Test Collection dan Environment akan tersedia pada Postman Anda.


## Submission Anda akan dinilai oleh Reviewer guna menentukkan kelulusan Anda. Untuk lulus dari kelas ini, proyek Bookshelf API harus memenuhi seluruh pengujian otomatis pada Postman request yang bertanda **[Mandatory]**. Bila salah satu pengujiannya gagal, maka proyek Anda akan kami tolak.

Submission Anda akan dinilai oleh Reviewer dengan **skala 1-5**. Untuk mendapatkan nilai tinggi, silakan penuhi pengujian otomatis pada request yang bertanda **[Optional]**. Berikut detail dari fitur atau persyaratan opsional dari submission ini:

- Tambahkan fitur query parameters pada route GET /books (Mendapatkan seluruh buku).
    - <span style="color:red">?name</span> : Tampilkan seluruh buku yang mengandung nama berdasarkan nilai yang diberikan pada query ini. Contoh /books?name=”dicoding”, maka akan menampilkan daftar buku yang mengandung nama “dicoding” secara non-case sensitive  (tidak peduli besar dan kecil huruf).
    - <span style="color:red">?reading</span> : Bernilai 0 atau 1. Bila 0, maka tampilkan buku yang sedang tidak dibaca (reading: false). Bila 1, maka tampilkan buku yang sedang dibaca (reading: true). Selain itu, tampilkan buku baik sedang dibaca atau tidak.
    - <span style="color:red">?finished</span> : Bernilai 0 atau 1. Bila 0, maka tampilkan buku yang sudah belum selesai dibaca (finished: false). Bila 1, maka tampilkan buku yang sudah selesai dibaca (finished: true). Selain itu, tampilkan buku baik yang sudah selesai atau belum dibaca.
- Menggunakan ESLint dan salah satu style guide agar gaya penulisan kode JavaScript lebih konsisten. Serta ketika dijalankan perintah <span style="color:red">npx eslint</span> . tidak terdapat error yang muncul.