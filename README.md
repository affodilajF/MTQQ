
# REALM vs ROOM vs SQLite Comparison

## SQLite
- Stores data in tabular format (consists of rows and columns)
- Run queries to interact with the database
- Thousands of apps used Sqlite to store data locally

## REALM
- NoSQL database
- Data is mapped to objects in a realm file and therefore classes are used to define the schema.
- Relationships are enabled via links and backlinks.
- Limitations :
  - Does not support auto-incrementing of ids.
  - Does not support nested transcations.
  - It’s a nightmare when we have to merge multiple tables in Realm.
  - Realm does not support inheritance.
  - Realm does not work with data classes you need to use regular open classes.
 

## ROOM
- Not a db, but simply a layer of top of SQLite
- Limitations :
  - SQLite creates temporary journal files which when not deleted properly could lead to issues.
  - Basic understanding to SQL.  


## Comparation 
1. Size (SQLite win)
   - Realm includes a separate db adding 4-5 mb in size to a project.
   - SQLite is shipped along with the android system. Only add small layer of few kb's.
2. Multithreading (Room win)
   - Realm requires that objects are not shared between threads (its ok if you are careful).
   - Ketika mengambil objek dari Realm dalam satu thread, objek tersebut tidak dapat digunakan di thread lain. Harus berhati-hati dalam mengelola akses ke objek untuk menghindari masalah seperti race conditions.
3. Security (Both ok)
   - Room provodes SqlChiper
   - Realm uses AES-256
4. Speed (Realm win)
   - Realm is winner when creating new records/updating records. 10 x faster compared to room.
5. Dex (Room win)
    - Realm offer many of features but with higher dex score charge, jadi lbh cepet mencapai batas dex.
    - Bad dex management? "dex limit exceeded". App nnti gabisa di build.
    - Solution of dex?
      - Proguard/R8 => Mengoptimalkan dan mengurangi jumlah metode yang tidak terpakai.
      - Modularisasi => memecah app menjadi modul yg lbh kecil
      - MultiDex => 1 apps bisa lbh dr 1 file dex
6. Realtime updates (Realm Win)
   - Realm has feature called LiveUpdates,  which allows automatic notifications when data changes. This means that any changes made to the Realm database will be immediately reflected in the app without the need for manual refresh or reload. Android Room, on the other hand, does not provide built-in real-time updates. WELL WE CAN USE LIVE DATA/FLOW TRS LISTENER. MIRIP LA.
7. Offline supoorts (Realm Win)
   - Punya sync feature.  It allows users to work with data even when the device is not connected to the internet. Realm can synchronize data automatically when the device goes online. Android Room, on the other hand, does not have built-in offline support. Offline data handling needs to be implemented manually.
    
## Conslusion 
Realm is good for prototyping and getting started as it does not require a lot of configuration and SQL knowledge but as the project grows in size and requires more complex operations and you need to manipulate data and not just store and retrieve data, you quickly start to see that SQLite with Room would be a much better operation.

The "complex" woord may be reffering to : 
1. Data manipulation (join agregasi filter CRUD). Km bisa gunain fitur database which is room offer. Tapi realm offer method bawaan juga di kotlin. Ya walaupun dex nya ntar bs lebih tinggi. 
2. Data/table relation










