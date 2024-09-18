# ROOM

# 1. Define Data Using Room Entities
- Define entities to represent the objects to be stored
### 1.1 Anatomy of an Entity
- Anotated with @Entity
- Includes fields of each column
- By default, Room uses the class name as the database name. But we can customize with ``@Entity(tableName = "users")``
### 1.2 Define PK
- Each room entity must define a PK
### 1.3 Define Composite PK
- If you need instances of an entity to be uniquely identified by a combination of multiple columns, you can define a composite primary key by listing those columns in the primaryKeys property of @Entity
```kotlin
@Entity(primaryKeys = ["firstName", "lastName"])
data class User(
    val firstName: String?,
    val lastName: String?
)
```
### 1.4 Ignore Fields
- By default, Room creates a column for each field that's defined in the entity. If an entity has fields that you don't want to persist, you can annotate them using @Ignore

# 2. Accessing Data Using Room DAOs
- DAO includes methods that offer abstract access to your app's database instead of query builders or direct queries. 
- Define DAO as either interface/abstract class.
- Anotate whith @Dao
- Two types of Dao :
  - Convienience (iud) methods without writing any SQL code.
  - Query with SQL (@Query("SQL query"))
  















