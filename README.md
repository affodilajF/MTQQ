# COROUTINES

https://www.youtube.com/watch?v=unkgAYV9SpI

## Preq
- Suspend function adalah fungsi yang dapat ditangguhkan, artinya fungsi tersebut dapat dihentikan dan dilanjutkan di kemudian hari tanpa memblokir thread yang menjalankannya.
- Dengan mendukung penangguhan, Kotlin memungkinkan pengembang untuk menulis kode yang lebih bersih dan mudah dipahami tanpa perlu menggunakan callback yang rumit atau pengelolaan thread yang manual.

# 1. Coroutines Basics
- A coroutine is an instance of a suspendable computation.
- It is conceptually similar to a thread, in the sense that it takes a block of code to run that works concurrently with the rest of the code.
- **However, a coroutine is not bound to any particular thread.** It may suspend its execution in one thread and resume in another one.

# 2. Channels
- Allows to pass a stream of values from coroutine A to coroutine B.
- The elements inside the channel are processed in the same order as they arrive in.
- Functions used in channels :
  - **send** => to send exact value to another coroutine.
  - **receive** => to receive exact value from another coroutine.
 
  ```kotlin
  private val channel : String = String()

  init {
    viewModelScope.launch {
      channel.send("Aha")
    }
    viewModelScope.launch {
      Log.d("TAG", channel.receive()) //will be logged "Aha"
    }
  }
  ```
  - **close()**
  - **consumeEach{}** => adalah fungsi yang digunakan pada channel dalam Kotlin Coroutines untuk memproses setiap item yang diterima dari channel secara berurutan. Ini adalah cara yang nyaman untuk mengiterasi dan memproses semua nilai yang ada dalam channel tanpa harus menulis loop eksplisit.
  - **produce** => kalo pake ini, gausah di close secara manual channelnya. Bisa auto closed by system.

### 2.1 Closing the channels 
- di close manual :
  ![image](https://github.com/user-attachments/assets/ee9583de-8e51-4c22-bfe5-3dcabec66f9a)
- pake produce aja mending, akan auto close kalo proses pengiriman channelnya selesai.
  ![image](https://github.com/user-attachments/assets/4ba93501-d8e4-4353-a6ec-4c02b4be77a0)


# 3. Channel Types 
### 3.1 BUFFERED
- Ada max buffer capacity.
- Will not be able to send more data than the buffer capacity UNTIL we receive the data and make some more space for the buffer to receive the new data.
- If we send an element while the channel is full, then this circle will be suspended until the channel is cleaned up using received() atau for(value in channel){} (intinya looping aja karena kotlin akan scr otomatis memanggil .receive() dibalik layar untuk menggambil nilai dari channel. 
- Memungkinkan pengiriman dan penerimaan data antara coroutine dengan menampung data dalam antrian (buffer). 
- Misalnya, jika Anda membuat buffered channel dengan kapasitas 5, channel tersebut dapat menyimpan hingga 5 item sebelum mulai memblokir pengirim.
- Contoh :
  ```kotlin
  import kotlinx.coroutines.*
  import kotlinx.coroutines.channels.Channel
  
  fun main() = runBlocking {
      // Membuat buffer channel dengan kapasitas 5
      val channel = Channel<Int>(5)
  
      // Coroutine pengirim
      val sender = launch {
          for (i in 1..10) {
              println("Sending: $i")
              channel.send(i) // Mengirim data ke channel
              delay(100) // Simulasi waktu pengiriman
          }
          channel.close() // Menutup channel setelah selesai mengirim
      }
  
      // Coroutine penerima
      val receiver = launch {
          for (value in channel) {
              println("Received: $value")
              delay(300) // Simulasi waktu pemrosesan
          }
      }
  
      // Menunggu coroutine pengirim untuk selesai
      sender.join()
      // Menunggu coroutine penerima untuk selesai
      receiver.join()
  }
  ```
  ```
    Sending: 1
    Sending: 2
    Sending: 3
    Sending: 4
    Sending: 5
    Received: 1
    Received: 2
    Sending: 6
    Sending: 7
    Received: 3
    Received: 4
    Sending: 8
    Sending: 9
    Received: 5
    Sending: 10
    Received: 6
    Received: 7
    Received: 8
    Received: 9
    Received: 10

  ```



### 3.2 CONFLATED
- Nilai yang disimpan di channel adalah nilai terakhir.
- **Blocking**: Pengirim tidak terhalang (selalu bisa mengirim nilai baru); penerima terhalang jika channel kosong.
### 3.3 RENDEZVOUS
- By default, channel itu rendevouz.
- Rendezvous channel adalah tipe channel di mana pengirim dan penerima harus bertemu secara langsung untuk mengirim dan menerima data.
- Pengirim akan terhalang (blocked) hingga ada penerima yang siap untuk menerima nilai, dan sebaliknya. Ini menciptakan titik pertemuan di mana data dapat ditransfer langsung.
- Karakteristik :
  - Blocking => sender receiver hrs sama2 siap
  - Sederhana => ez ges
  - Sesuai untuk situasi sync => Klo u mau mastiin bhwa pengirim dan penerima beroperasi scr bersamaan.  
### 3.4 UNLIMITED
- bsa nampung item tk terbatas.




















