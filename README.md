
# REALM vs ROOM vs SQLite Comparation

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
  - Realm does not work with data classes you need to use regular open classes

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
    - Realm offer many of features but with higher charge, jadi lbh cepet mencapat batas dex.
      
## Conslusion 
Realm is good for prototyping and getting started as it does not require a lot of configuration and SQL knowledge but as the project grows in size and requires more complex operations and you need to manipulate data and not just store and retrieve data, you quickly start to see that SQLite with Room would be a much better operation.

The "complex" woord may be reffering to : 
1. Data manipulation (join agregasi filter CRUD). Km bisa gunain fitur database which is room offer.
2. Data/table relation








