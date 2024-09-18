# REALM

## 1. Introduction to Realm
- The Realm SDK needs to be initialized before use.


## 2. Data Model
- Adalah cara untuk mendefinisikan struktur data yang akan disimpan di database. 
### RealmObject
- Setiap model di Realm adalah kelas Kotlin yang mewarisi RealmObject. Realm akan mengelola objek tersebut di database. 
- Contoh :
  ``` kotlin
  open class User(
      @PrimaryKey var id: String = "",
      var name: String = "",
      var email: String = ""
  ) : RealmObject()

  open class Post(
      @PrimaryKey var id: String = "",
      var content: String = "",
      var userId: String = "",  // ID pengguna yang membuat postingan
      var timestamp: Long = 0
  ) : RealmObject()
  ```

### Anotasi 
- **@PrimaryKey**
  - PK
- **@Embeded**
  - Digunakan untuk menyimpan objek di dalam objek lain tanpa membuat entitas terpisah.
- **@Ignore**
  - Digunakan untuk atribut yang tidak perlu disimpan di database. Cuma kesimpen di apps aja, misal atribut sementara atau hasil kalkulasi.

### Relasi Data 
- One to One
  - Post memiliki referensi ke user.
- One to Many
  - Satu objek memiliki daftar objek lain. User memiliki banyak post. 
- Contoh :
  ``` kotlin
  @RealmClass
  open class Post(
      @PrimaryKey var id: String = "",
      var content: String = "",
      var user: User? = null  // Referensi ke objek User
  ) : RealmObject()

  open class User(
      @PrimaryKey var id: String = "",
      var name: String = "",
      var email: String = "",
      var posts: RealmList<Post> = RealmList()  // Daftar postingan
  ) : RealmObject()
  ```

## 3. CRUD Operations
- Create
  ``` kotlin
  
  val realm = Realm.getDefaultInstance()

  realm.executeTransaction { transactionRealm ->
      // Membuat pengguna baru
      val newUser = User().apply {
          id = "user1"
          name = "John Doe"
          email = "john@example.com"
      }
      
      transactionRealm.insert(newUser)  // Menyimpan objek User ke Realm
  
      // Membuat postingan baru
      val newPost = Post().apply {
          id = "post1"
          content = "Hello, world!"
          userId = newUser.id  // Mengaitkan postingan dengan pengguna
          timestamp = System.currentTimeMillis()
      }
      
      transactionRealm.insert(newPost)  // Menyimpan objek Post ke Realm
  }
  
  ```

- Read
  ``` kotlin
  
  val realm = Realm.getDefaultInstance()
  
  val users = realm.where(User::class.java).findAll()  // Mengambil semua pengguna
  val specificPost = realm.where(Post::class.java)
    .equalTo("id", "post1")
    .findFirst()  // Mengambil postingan dengan ID spesifik
  
  ```
  
- Update
  ``` kotlin
  
    realm.executeTransaction { transactionRealm ->
        val userToUpdate = transactionRealm.where(User::class.java)
            .equalTo("id", "user1")
            .findFirst()
    
        userToUpdate?.let {
            it.name = "John Smith"  // Memperbarui nama pengguna
            transactionRealm.insertOrUpdate(it)  // Memperbarui objek
        }
    }
  ```

- Delete
  ``` kotlin
    realm.executeTransaction { transactionRealm ->
        // Menghapus pengguna
        val userToDelete = transactionRealm.where(User::class.java)
            .equalTo("id", "user1")
            .findFirst()
    
        userToDelete?.deleteFromRealm()  // Menghapus objek User dari Realm
    }
  ```

## 4. Querying Data
- Querying data di Realm Database adalah proses mengambil data dari database berdasarkan kriteria tertentu.
- Realm menyediakan API yang mudah digunakan untuk melakukan query dengan berbagai kondisi.
- Contoh :
  - equalTo (fieldname, value)
  - notEqualTo
  - greaterThan
  - lessThan
- **PERFORMA QUERY**
  - Indeksasi => Pertimbangkan untuk menambah indeks untuk kolom yang sering digunakan dalam query.
  - Hindari query berlebihan.
- **PENGGUNAAN di THREAD yang BERBEDA**
  - Setiap thread yang mengakses Realm harus menggunakan instance Realm-nya sendiri. Pastikan untuk menggunakan getDefaultInstance() di setiap thread.
  
## 5. Real-Time Updates 
- Memungkinkan aplikasi menerima pembaruan data secara otomatis ketika data dalam database berubah. 
### Change Listener
- Mendengarkan perubahan pada **objek atau hasil query** secara realtime. Ketika data berubah, listener akan dipanggil, nah lalu bisa update UI.
- Contoh :
```kotlin
val realm = Realm.getDefaultInstance()

// Ambil objek Post yang ingin didengarkan
val post = realm.where(Post::class.java)
    .equalTo("id", "post1")
    .findFirst()

// Menambahkan Change Listener
post?.addChangeListener { updatedPost ->
    // Akan dipanggil setiap kali objek Post berubah
    println("Post updated: ${updatedPost.content}")
}

// Untuk mengubah konten dari postingan
realm.executeTransaction { transactionRealm ->
    post?.content = "Updated content"
    transactionRealm.insertOrUpdate(post)  // Memicu listener
}

```
- Penting untuk **menghapus listener** => Hapus listener ketika tidal lagi diperlukan untuk mencegah memori leaks.
```kotlin
post?.removeChangeListener { listener }  // Menghapus listener
```
  
### Live Data Integration
- LiveData adalah komponen arsitektur Android yang memungkinkan pengembangan aplikasi yang responsif dan memudahkan pengelolaan UI. Integrasi Realm dengan LiveData memungkinkan kamu untuk memanfaatkan keunggulan kedua teknologi tersebut, sehingga UI dapat merespon perubahan data secara otomatis.
- Contoh :
  
**1). Buat LiveData dari RealmResults**
```kotlin
class PostRepository(private val realm: Realm) {

    fun getPosts(): LiveData<List<Post>> {
        return object : LiveData<List<Post>>() {
            private var posts: RealmResults<Post>? = null

            override fun onActive() {
                posts = realm.where(Post::class.java).findAll()
                posts?.addChangeListener { results ->
                    value = results // Update LiveData ketika data berubah
                }
            }

            override fun onInactive() {
                posts?.removeChangeListener { results -> }
            }
        }
    }
}

```
**2). Menggunakan LiveData di ViewModel**
```kotlin
class PostViewModel(private val repository: PostRepository) : ViewModel() {
    val posts: LiveData<List<Post>> = repository.getPosts()
}

```

**3). Observasi di Activity/Fragment**
```kotlin
viewModel.posts.observe(viewLifecycleOwner) { postList ->
    // Update UI dengan data terbaru
    adapter.submitList(postList)
}
```

## 6. Offline Capabilities 
- Semua data disimpan dalam file database Realm di penyimpanan lokal perangkat.
- **Sinkronisasi Data**
  - Realm mendukung sinkronisasi data dengan backend ketika koneksi internet tersedia. Realm offer 2 ways :
  - a) Manual Sync
      - Kode logic di kotlin. 
  - b) Realm Sync
      - Realm menyediakan **Realm Object Server** (now called Realm Sync). 

## 7. Trasaction Management
- Transaksi? adalah unit kerja yang terdiri dari satu atau lebih operasi database. Dalam Realm, setiap perubahan yang dilakukan pada database (seperti menambahkan, memperbarui, atau menghapus data) harus dilakukan dalam konteks transaksi.
- Jika semua operasi dalam transaksi berhasil, perubahan disimpan; jika ada kesalahan, semua perubahan akan dibatalkan.
- How to use transaksi> pakai ``executeTranscation``
- Keuntungan? Kalau berhasil ya berhasil, klo gagal ya gagal.
- Jangan lupa pakai error handling
- Contoh :
```kotlin
val realm = Realm.getDefaultInstance()

try {
    realm.executeTransaction { transactionRealm ->
        val newPost = transactionRealm.createObject(Post::class.java, UUID.randomUUID().toString())
        newPost.content = "Hello, Realm!"
        // Misalnya, simpan data yang tidak valid untuk uji coba
        if (newPost.content.isEmpty()) {
            throw IllegalArgumentException("Content cannot be empty!")
        }
    }
} catch (e: Exception) {
    // Tangani kesalahan yang terjadi selama transaksi
    Log.e("RealmTransaction", "Transaction failed: ${e.message}")
}
```
- **Best Partice untuk menajemen transaksi**
- Always gunakan ``executeTransaction`` agar penulisan data tetap consistet
- Jaga transaksi sesingkat mungkin
- Validasi data sebelum menyimpan (untuk mengurangi kesalahan)
- Error Handling

## Data Migration
- Data migration adalah proses penting dalam pengelolaan database, terutama ketika ada perubahan pada skema data.
- Di Realm, migrasi skema memungkinkan untuk mengelola perubahan pada struktur data tanpa kehilangan data yang sudah ada. 
### Schema Migration
- Schema migration diperlukan ketika ada perubahan dalam model data, seperti penambahan atau penghapusan properti, perubahan tipe data, atau modifikasi struktur objek.
- Realm memerlukan informasi tentang versi skema yang sedang digunakan untuk melakukan migrasi data dengan benar.
### Migrations API 


