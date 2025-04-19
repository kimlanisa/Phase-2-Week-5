# Phase-2-Week-5

# 💾 Local Storage & Cache di Web

## 1. Apa itu Local Storage?
Local Storage adalah bagian dari **Web Storage API** yang memungkinkan kita menyimpan data di browser pengguna **secara lokal dan permanen** (hingga dihapus secara manual atau oleh script).

---

## 2. Karakteristik Local Storage
- ✅ Tersimpan di browser
- 📦 Kapasitas sekitar 5-10MB per domain
- 🕒 Tidak memiliki waktu kedaluwarsa
- 📘 Data disimpan sebagai string (key-value pair)
- 🚫 Tidak bisa diakses lintas domain

---

## 3. Perbandingan: Local Storage vs Session Storage vs Cookie

| Fitur              | Local Storage | Session Storage | Cookie |
|-------------------|---------------|------------------|--------|
| Persisten         | Ya            | Tidak (hapus saat tab ditutup) | Bisa diatur |
| Kapasitas         | ~5MB          | ~5MB             | ~4KB   |
| Dikirim ke server | Tidak         | Tidak            | Ya     |
| Akses JavaScript  | Ya            | Ya               | Ya     |

---

## 4. Cara Menggunakan Local Storage

### Menyimpan Data
```js
localStorage.setItem('username', 'johnDoe');
```

### Mengambil Data
```js
const username = localStorage.getItem('username');
```

### Menghapus Data
```js
localStorage.removeItem('username');
```

### Menghapus Semua Data
```js
localStorage.clear();
```

### Menyimpan Object/Array (harus diubah ke string)
```js
const user = { name: 'John', age: 25 };
localStorage.setItem('user', JSON.stringify(user));
```

### Mengambil dan Parse Object
```js
const user = JSON.parse(localStorage.getItem('user'));
```

---

## 5. Contoh Data yang Umum Disimpan di Local Storage
Berikut ini adalah beberapa contoh data yang sering disimpan di localStorage:

- 🌓 Preferensi tema (dark/light mode)
- 🌐 Bahasa yang dipilih user
- 🛒 Data cart belanja sementara (produk, jumlah, harga)
- 👤 Data user yang sudah login (nama, ID, avatar)
- 📄 Draft form / konten yang belum dikirim
- 📍 Koordinat lokasi terakhir user
- 📚 Cache hasil fetch API (misalnya: daftar produk)
- 🔑 Token autentikasi (misalnya: JWT token) — ⚠️ **Hanya jika tidak ada opsi lain, karena menyimpan token di localStorage rawan XSS. Lebih aman menggunakan httpOnly cookie.**

Contoh:
```js
localStorage.setItem('auth_token', 'abc123');
localStorage.setItem('theme', 'dark');
localStorage.setItem('language', 'id');
```

---

## 6. Tips Menggunakan Local Storage
- Simpan hanya data sederhana dan tidak sensitif
- Gunakan `try/catch` untuk mencegah error JSON parse
- Pertimbangkan fallback jika browser tidak mendukung Web Storage

```js
if (typeof Storage !== 'undefined') {
  // bisa pakai localStorage
} else {
  // fallback atau peringatan
}
```

---

## 7. Apa itu Caching di Web?
**Cache** adalah teknik menyimpan salinan data sehingga saat dibutuhkan kembali, kita tidak perlu mengambil ulang dari sumber aslinya (server/API) → mempercepat loading dan menghemat resource.

---

## 8. Jenis-Jenis Cache di Frontend

| Jenis Cache     | Penjelasan |
|------------------|------------|
| **Browser Cache** | Otomatis menyimpan file statis seperti gambar, CSS, JS |
| **Service Worker Cache** | Cache yang dikelola manual lewat Service Worker (PWA) |
| **Memory Cache (RAM)** | Data sementara yang disimpan di memory aplikasi (misalnya React state) |
| **Custom Cache (via localStorage/IndexedDB)** | Cache buatan menggunakan storage API |

---

## 9. Contoh: Caching Data API di Local Storage

```js
async function fetchWithCache(url) {
  const cached = localStorage.getItem(url);
  if (cached) {
    return JSON.parse(cached);
  }

  const res = await fetch(url);
  const data = await res.json();

  localStorage.setItem(url, JSON.stringify(data));
  return data;
}
```

---

## 10. Expired Cache / TTL (Time to Live)
Local Storage tidak punya TTL, jadi kita bisa membuat manual:

```js
function setCache(key, data, ttl) {
  const record = {
    data,
    expiry: new Date().getTime() + ttl,
  };
  localStorage.setItem(key, JSON.stringify(record));
}

function getCache(key) {
  const record = JSON.parse(localStorage.getItem(key));
  if (!record) return null;
  if (new Date().getTime() > record.expiry) {
    localStorage.removeItem(key);
    return null;
  }
  return record.data;
}
```
