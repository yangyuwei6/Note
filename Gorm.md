# ORM入门指南

object relational mapping

object（对象）  程序中的对象/实例 例如Go中的结构体实例

relational（关系）：关系数据库，例如MySQL

mapping（映射）

## 安装依赖包

```go
//环境
Environment：GOPROXY=https://goproxy.cn,direct
//安装MySQL驱动
go get -u gorm.io/driver/mysql
//安装gorm包
go get -u gorm.io/gorm
```

## 导入依赖包

```go
import (
  "gorm.io/driver/mysql"
  "gorm.io/gorm"
)
```

## 声明模型

GORM 通过将 Go 结构体（Go structs） 映射到数据库表来简化数据库交互。 了解如何在GORM中定义模型，是充分利用GORM全部功能的基础。

### 约定

1. **主键**：GORM 使用一个名为`ID` 的字段作为每个模型的默认主键，也可以用结构体的tag自己设置

   ```go
   //默认id为主键
   type Stu struct{
   Id int
   Name string
   }
   //tag设置主键
   type Stu struct {
   	Stu_num int64 `gorm:"primary_key"`
   	Age     int
   }
   ```

   

2. **表名**：默认情况下，GORM 将结构体名称转换为 `snake_case` 并为表名加上复数形式。 例如，一个 `User` 结构体在数据库中的表名变为 `users` 。

3. **列名**：GORM 自动将结构体字段名称转换为 `snake_case` 作为数据库中的列名。

4. **时间戳字段**：GORM使用字段 `CreatedAt` 和 `UpdatedAt` 来自动跟踪记录的创建和更新时间。

### `gorm.Model`

GORM提供了一个预定义的结构体，名为`gorm.Model`，其中包含常用字段：

```
// gorm.Model 的定义
type Model struct {
  ID        uint           `gorm:"primaryKey"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt gorm.DeletedAt `gorm:"index"`
}
```

- **将其嵌入在您的结构体中**: 您可以直接在您的结构体中嵌入 `gorm.Model` ，以便自动包含这些字段。 
- **包含的字段**：
  - `ID` ：每个记录的唯一标识符（主键）。
  - `CreatedAt` ：在创建记录时自动设置为当前时间。
  - `UpdatedAt`：每当记录更新时，自动更新为当前时间。
  - `DeletedAt`：用于软删除（将记录标记为已删除，而实际上并未从数据库中删除）。
  - 

### 给模型显式指定表名

```go
type User struct {
    ID   uint   `gorm:"primaryKey"`
    Name string
    Age  int
}

// 显式指定表名为 "my_users"
func (User) TableName() string {
    return "my_users"
}
```



## 连接到MySQL数据库

1.配置DSN（Data Source Name）

gorm库使用dsn作为连接数据库的参数，dsn翻译过来就叫数据源名称，用来描述数据库连接信息，一般都包含数据库连接地址，账号，密码之类的信息

```go
import (
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
)

dsn := "user:password@tcp(host:port)/dbname?charset=utf8mb4&parseTime=True&loc=Local"
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
if err != nil {
    panic("failed to connect database")
}
```

其中，`user` 是数据库用户名，`password` 是密码，`host` 是数据库主机地址，`port` 是端口号，`dbname` 是数据库名称。

demo

```go
//mysql dsn格式
//涉及参数:
//username   数据库账号
//password   数据库密码
//host       数据库连接地址，可以是Ip或者域名
//port       数据库端口
//Dbname     数据库名
username:password@tcp(host:port)/Dbname?charset=utf8&parseTime=True&loc=Local

//填上参数后的例子
//username = root
//password = 123456
//host     = localhost
//port     = 3306
//Dbname   = tizi365
//后面K/V键值对参数含义为：
//  charset=utf8 客户端字符集为utf8
//  parseTime=true 支持把数据库datetime和date类型转换为golang的time.Time类型
//  loc=Local 使用系统本地时区
root:123456@tcp(localhost:3306)/tizi365?charset=utf8&parseTime=True&loc=Local

//gorm 设置mysql连接超时参数
//开发的时候经常需要设置数据库连接超时参数，gorm是通过dsn的timeout参数配置
//例如，设置10秒后连接超时，timeout=10s
//下面是完成的例子
root:123456@tcp(localhost:3306)/tizi365?charset=utf8&parseTime=True&loc=Local&timeout=10s

//设置读写超时时间
// readTimeout - 读超时时间，0代表不限制
// writeTimeout - 写超时时间，0代表不限制
root:123456@tcp(localhost:3306)/tizi365?charset=utf8&parseTime=True&loc=Local&timeout=10s&readTimeout=30s&writeTimeout=60s
```

# CRUD接口

## 创建记录

### 结构体+指针创建记录

```go
package main

import (
	"gorm.io/driver/mysql"
	"gorm.io/gorm"
)

// 定义模型
type User struct {
	Name  string
	Age   int
	Class int
}

func main() {
	//连接数据库
	dsn := "root:123456@tcp(localhost:3306)/demo?charset=utf8mb4&parseTime=True&loc=Local"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		panic("failed to connect database")
	}
	//创建表 自动迁移 （把结构体和数据表进行对应）
	db.AutoMigrate(&User{})
	//创建记录
	u := User{"yyw", 23, 3}
	db.Create(&u)
	//批量创建
	d := []*User{
		{"mjt", 4, 13},
		{"zjx", 22, 14},
	}
	db.Create(&d)
}
```

### 使用 `map[string]interface{}` 创建记录

如果你有一个简单的数据结构，可以使用 `map[string]interface{}` 表示每条记录的字段和对应的值。以下是一个示例：

```go
package main

import (
    "fmt"
    "gorm.io/driver/sqlite"
    "gorm.io/gorm"
)

func main() {
    // 连接数据库
    db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
    if err != nil {
        panic("failed to connect database")
    }

    // 自动迁移模式
    db.AutoMigrate(&gorm.Map{})

    // 创建记录的 map
    user := map[string]interface{}{
        "name": "Alice",
        "age":  25,
    }

    // 插入记录
    result := db.Create(&user)
    if result.Error != nil {
        panic("failed to insert user")
    }

    fmt.Println("User created successfully")
}
```

在上面的示例中，我们使用 `map[string]interface{}` 定义了一个名为 `user` 的 map，其中包含了字段 `"name"` 和 `"age"`。然后，通过 `db.Create(&user)` 将这个 map 中的数据插入到数据库中。

### 使用 `[]map[string]interface{}` 批量创建记录

如果你有多条数据需要插入，也可以使用 `[]map[string]interface{}` 表示多条记录。以下是一个示例：

```go
package main

import (
    "fmt"
    "gorm.io/driver/sqlite"
    "gorm.io/gorm"
)

func main() {
    // 连接数据库
    db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
    if err != nil {
        panic("failed to connect database")
    }

    // 自动迁移模式
    db.AutoMigrate(&gorm.Map{})

    // 创建多条记录的切片
    users := []map[string]interface{}{
        {"name": "Alice", "age": 25},
        {"name": "Bob", "age": 30},
        {"name": "Charlie", "age": 20},
    }

    // 批量插入记录
    for _, user := range users {
        result := db.Create(&user)
        if result.Error != nil {
            panic("failed to insert users")
        }
    }

    fmt.Println("Batch insert successful")
}
```

在这个示例中，我们定义了一个 `[]map[string]interface{}` 切片 `users`，其中每个元素都是一个包含字段 `"name"` 和 `"age"` 的 map。然后通过循环遍历 `users` 切片，并使用 `db.Create(&user)` 执行批量插入操作。

总结来说，使用 `map[string]interface{}` 或 `[]map[string]interface{}` 可以方便地在 GORM 中进行动态数据操作，**特别是在不预先定义结构体的情况**下。这种方法适用于需要灵活处理数据结构的场景。

## 查询记录

### 查询流程

```go
result := db.Where("id = ?", userID).First(&user)
if errors.Is(result.Error, gorm.ErrRecordNotFound) {
    // 处理记录不存在的情况，比如返回一个特定的错误信息或执行默认操作
    fmt.Println("未找到对应的用户记录")
    // 返回自定义的错误信息
    return errors.New("用户不存在")
}
// 其他错误处理逻辑
if result.Error != nil {
    // 处理其他可能的错误
    return result.Error
}
// 处理查询成功的情况
fmt.Println("查询成功：", user)

```



#### 查询第一条记录

```go
func (db *DB) First(dest interface{}, conds ...interface{}) (tx *DB)
```

参数解释

1. **接收者 (`(db \*DB)`)**:
   - 这是一个方法，定义在 `*gorm.DB` 类型上。在 Go 中，方法可以关联到特定类型上，并通过该类型的实例调用。
2. **`dest interface{}` 参数**:
   - 这是一个必需的参数，用于指定查询结果应该存储在哪个结构体实例中。`dest` 应该是一个结构体实例的指针，查询的结果将会被映射到这个结构体中。例如，如果要查询 `users` 表的记录，可以传递一个 `*User` 类型的指针作为 `dest` 参数。
3. **`conds ...interface{}` 参数**:
   - 这是可变参数，用于指定查询条件。可以传递一个或多个条件，通常用来指定 WHERE 子句中的条件。例如，可以传递一个主键值或其他条件表达式。如果不提供条件，则默认返回表的第一条记录。
4. **返回值 (`tx \*DB`)**:
   - 这是返回一个 `*gorm.DB` 对象的方法。它允许你在方法调用后继续链式操作，例如添加更多的查询条件、排序、限制等。

方法解释

- **查询结果存储**:
  - `First` 方法会根据传递的 `dest` 参数类型自动识别查询的表格，并将查询结果映射到 `dest` 所指向的结构体实例中。
- **条件查询**:
  - 可以通过 `conds` 参数指定查询的条件。例如，可以传递主键值来查询特定记录。
- **链式操作**:
  - `First` 方法返回一个 `*gorm.DB` 对象，使得可以通过链式方法继续添加查询条件、排序、限制等操作。

#### 查询并返回符合条件的任意一条记录

```go
func (db *DB) Take(dest interface{}, conds ...interface{}) (tx *DB)
```

#### 获取最后一条记录（主键降序）

```go
func (db *DB) Last(dest interface{}, conds ...interface{}) (tx *DB)
```

#### 查询所有记录

```go
func (db *DB) Find(dest interface{}, conds ...interface{}) (tx *DB)
```

## db.Model()

`db.Model()` 方法在使用 GORM 进行数据库操作时非常常见和重要。它主要用于指定操作的目标模型（对应数据库中的表），告诉 GORM 后续的操作应该在哪个表上执行。以下是一些使用 `db.Model()` 的典型场景：

1. 指定操作的数据库表

使用 `db.Model()` 可以明确指定操作的数据库表，这对于复杂的数据库操作非常有用。例如：

```go
var users []User
db.Model(&User{}).Where("age > ?", 18).Find(&users)
```

在这个示例中，`db.Model(&User{})` 明确告诉 GORM 后续的操作将在 `users` 表上执行，即使你在 `Where` 条件中没有指定表名，GORM 也能够理解你需要在 `users` 表上进行操作。

2. 处理多表操作

在进行关联查询或者跨表操作时，使用 `db.Model()` 可以帮助 GORM 确定每一步操作的目标表，例如：

```go
var users []User
var profiles []Profile

db.Model(&User{}).Related(&profiles)
```

这里，`db.Model(&User{})` 用于指定查询的主表是 `users` 表，然后通过 `Related` 方法查询 `users` 表关联的 `profiles` 表数据。

3. 执行条件查询、更新和删除

在执行条件查询、更新和删除操作时，使用 `db.Model()` 可以确保操作被执行在正确的表上，例如：

```go
db.Model(&User{}).Where("age > ?", 30).Update("is_adult", true)
```

在这个示例中，`db.Model(&User{})` 指定了更新操作将在 `users` 表上执行，并且通过 `Where` 条件指定了更新的范围。

4. 明确操作的目标模型

在使用 GORM 进行复杂的数据操作时，使用 `db.Model()` 可以帮助代码更加清晰地表达操作的意图和目标，提高代码的可读性和维护性。





在使用 GORM 进行数据库操作时，`db.Model(&User{})` 和 `db.Model(&user)` 的区别在于它们指定的对象类型和对象实例不同。

1. **`db.Model(&User{})`**：
   - 这种形式是基于类型的操作，其中 `User{}` 是一个结构体类型（或模型）。
   - 使用 `&User{}` 创建了一个新的 `User` 类型的实例，但没有具体的数据，只是一个空的实例。这样的情况通常在你希望在数据库中创建或更新表时非常有用。
2. **`db.Model(&user)`**：
   - 这种形式是基于对象的操作，其中 `user` 是 `User` 类型的一个实际实例，包含了具体的数据。
   - 这里的 `user` 变量应该是在之前的某个地方初始化并赋予了具体的值，比如从数据库中查询得到的一个 `User` 对象。

### 区别总结：

- **类型操作 (`db.Model(&User{})`)**：适用于定义表结构、数据模型的操作，比如创建表、更新表结构等。此时操作的是 `User` 类型本身，而不是某个具体的实例数据。
- **对象操作 (`db.Model(&user)`)**：适用于对某个具体实例数据进行操作，比如更新该实例的某些字段的值。此时操作的是 `user` 这个具体的对象实例，其中已经包含了具体的数据。

## 更新记录

**注意db.Model()里面的参数是结构体实例本身，还是结构体类型**

### 保存所有字段

```go
//方式1 先找到记录id  用find或first等方法查询到id   创建结构体变量 然后save
db.First(&user)
user.Name = "jinzhu 2"
user.Age = 100
db.Save(&user)
// UPDATE users SET name='jinzhu 2', age=100, birthday='2016-01-01', updated_at = '2013-11-17 21:34:10' WHERE id=111;

```

```go
//方式二 直接用save ，更新对应记录时候 还是需要先得到id， save 方法视是否参数里面包含主键 不包含主键默认创建，包含主键使用update
db.Save(&User{Name: "jinzhu", Age: 100})
// INSERT INTO `users` (`name`,`age`,`birthday`,`update_at`) VALUES ("jinzhu",100,"0000-00-00 00:00:00","0000-00-00 00:00:00")

db.Save(&User{ID: 1, Name: "jinzhu", Age: 100})
// UPDATE `users` SET `name`="jinzhu",`age`=100,`birthday`="0000-00-00 00:00:00",`update_at`="0000-00-00 00:00:00" WHERE `id` = 1

```

### 更新单列

当使用 `**Update**` 更新单列时，需要有一些条件，否则将会引起`ErrMissingWhereClause` 错误，查看 [阻止全局更新](https://gorm.io/zh_CN/docs/update.html#block_global_updates) 了解详情。 当使用 `Model` 方法，并且它有主键值时，主键将会被用于构建条件，例如：

```go
// 根据条件更新，这个model里面的参数是结构体类型
db.Model(&User{}).Where("active = ?", true).Update("name", "hello")
// UPDATE users SET name='hello', updated_at='2013-11-17 21:34:10' WHERE active=true

// User 的 ID 是 `111`，model里面的参数是结构体实例
db.Model(&user).Update("name", "hello")
// UPDATE users SET name='hello', updated_at='2013-11-17 21:34:10' WHERE id=111;

// 根据条件和 model 的值进行更新
db.Model(&user).Where("active = ?", true).Update("name", "hello")
// UPDATE users SET name='hello', updated_at='2013-11-17 21:34:10' WHERE id=111 AND active=true;

```

### 更新多列

**`Updates`** 方法支持 `struct` 和 `map[string]interface{}` 参数。当使用 `struct` 更新时，默认情况下GORM 只会更新非零值的字段，想要更新零值可以使用select或者map

```go
// 根据 `struct` 更新属性，只会更新非零值的字段
var k Demo
	db.First(&k, 7)
	db.Model(&k).Update(Demo{Gender: 2, Hobby: "run"})
	c := Demo{
		Id:     7,
		Name:   "hah",
		Gender: 0,
	}
	db.Model(&Demo{}).Update(c)
//此处这个Id为7的记录的gender值在更新后还是会是2，不会变成0s
```

```go
// 根据 `map` 更新属性
db.Model(&user).Updates(map[string]interface{}{"name": "hello", "age": 18, "active": false})
// UPDATE users SET name='hello', age=18, active=false, updated_at='2013-11-17 21:34:10' WHERE id=111;

```

### 更新选定字段

如果您想要在更新时选择、忽略某些字段，您可以使用 `Select`、`Omit`

```go
// 选择 Map 的字段
// User 的 ID 是 `111`:
db.Model(&user).Select("name").Updates(map[string]interface{}{"name": "hello", "age": 18, "active": false})
// UPDATE users SET name='hello' WHERE id=111;

db.Model(&user).Omit("name").Updates(map[string]interface{}{"name": "hello", "age": 18, "active": false})
// UPDATE users SET age=18, active=false, updated_at='2013-11-17 21:34:10' WHERE id=111;

// 选择 Struct 的字段（会选中零值的字段）
db.Model(&user).Select("Name", "Age").Updates(User{Name: "new_name", Age: 0})
// UPDATE users SET name='new_name', age=0 WHERE id=111;

// 选择所有字段（选择包括零值字段的所有字段）
db.Model(&user).Select("*").Updates(User{Name: "jinzhu", Role: "admin", Age: 0})

// 选择除 Role 外的所有字段（包括零值字段的所有字段）
db.Model(&user).Select("*").Omit("Role").Updates(User{Name: "jinzhu", Role: "admin", Age: 0})
```

# 原生sql与sql生成器

## 原生查询 SQL 和 `Scan`

**作用**：`db.Raw` 方法允许你执行原生的 SQL 查询，并将查询结果映射到 GORM 模型对象中。

```go
type Result struct {
  ID   int
  Name string
  Age  int
}

var result Result
db.Raw("SELECT id, name, age FROM users WHERE id = ?", 3).Scan(&result)

db.Raw("SELECT id, name, age FROM users WHERE name = ?", "jinzhu").Scan(&result)

var age int
db.Raw("SELECT SUM(age) FROM users WHERE role = ?", "admin").Scan(&age)

var users []User
db.Raw("UPDATE users SET name = ? WHERE age = ? RETURNING id, name", "jinzhu", 20).Scan(&users)
```

## `Exec` 原生 SQL

**作用**：`db.Exec` 方法用于执行原生的 SQL 语句，但不返回任何行（rows）。它通常用于执行没有返回结果集的操作，比如插入、更新或删除操作。

```go
db.Exec("DROP TABLE users")
db.Exec("UPDATE orders SET shipped_at = ? WHERE id IN ?", time.Now(), []int64{1, 2, 3})

// Exec with SQL Expression
db.Exec("UPDATE users SET money = ? WHERE name = ?", gorm.Expr("money * ? + ?", 10000, 1), "jinzhu")
```

