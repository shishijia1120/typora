# Mongoose

​	http://www.mongoosejs.net/docs/guide.html

## 快速上手

安装Mongoose:

```
npm install mongoose
```

如果想在MongoDB里记录喵星人，首先要在项目中引入mongoose，然后连接到本地的test数据库。

```
var mongoose = require('mongoose');
mongoose.conncet('mongodb://localhost/test');
```

`connect()` 返回一个状态待定（pending）的连接， 接着我们加上成功提醒和失败警告.

```
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function() {
  // we're connected!
});
```

连接成功时，回调函数会被调用。简洁起见， 我们假设下面所有函数都运行在这个回调函数里。

Mongoose 里，一切都始于[Schema](http://www.mongoosejs.net/docs/guide.html)。 现在我们来看一个例子：

```
var kittySchema = mongoose.Schema({
  name: String
});
```

很好，我们得到了一个带有 `String` 类型 `name` 属性的 schema 。 接着我们需要把这个 schema 编译成一个 [Model](http://www.mongoosejs.net/docs/models.html)：

```
var Kitten = mongoose.model('Kitten', kittySchema);
```

model 是我们构造 document 的 Class。 在例子中，每个 document 都是一只喵，它的属性和行为都会被声明在 schema。 现在我们来“创造”一只猫：

```
var felyne = new Kitten({ name: 'Felyne' });
console.log(felyne.name); // 'Felyne'
```





文档保存到数据库：

```
  fluffy.save(function (err, fluffy) {
    if (err) return console.error(err);
    fluffy.speak();
  });
```

获取model里的所有数据：

```
Kitten.find(function (err, kittens) {
  if (err) return console.error(err);
  console.log(kittens);
})
```

获取特定数据:

```

```

