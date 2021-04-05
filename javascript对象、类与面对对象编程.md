# 对象、类与面对对象编程	

​	可以把ECMAScript的对象想象成一张散列表，其中的内容就是一组名/值对，值可以是数据或者函数。

[TOC]



## 1 理解对象

## 2 创建对象

### 2.1 概述

​	ECMAScript6开始正式支持类和继承。ES6的类旨在完全涵盖之前规范设计的基于原型的继承模板。不过，无论从哪方面看，ES6的类都仅仅是封装了ES5.1构造函数加原型继承的语法糖而已。



### 2.2 工厂模式

```javascript
function createPerson(name,age,job){
    let o = new Object();
    o.name=name;
    o.age=age;
    o.job=job;
    o.sayName=function(){
        console.log(this.name);
    }
    return o;
}
let person1 = createPerson('Nicholas',29,'software Engineer');
let person2 = createPerson('Greg',27,'Doctor');
```



​	这种工厂模式虽然可以解决创建多个类似对象的问题，但没有解决**对象标识**问题(即新创建的对象是什么类型)。

### 2.3 构造函数模式

​	ECMAScript6中的构造函数是用于创建特定类型对象的。像Object和Array这样的原生构造函数，运行时可以直接在执行环境中使用。当然也可以自定义构造函数，以函数的形式为自己的对象类型定义属性和方法。

```javascript
function Person(name,age,job){
    this.name=name;
    this.age=age;
    this.job=job;
    this.sayName=function(){
        console.log(this.name);
    }
}
let person1 = new Person('Nicholas',29,'software Engineer');
let person2 = new Person('Greg',27,'Doctor');
```

构造函数和工厂函数的区别：

- [ ] 没有显示地创建对象。
- [ ] 属性和方法直接赋值给了this。
- [ ] 没有return。

要创建Person的实例，应使用new操作符。以这种方式调用构造函数会执行如下操作:

（1）在内存中创建一个新对象。

（2）这个新对象内部的[[Prototype]]特性被赋值为构造函数的prototype属性。







**1. 构造函数也是函数**

​	构造函数与普通函数唯一的区别就是调用方式不同。任何函数只要使用new操作符调用就是构造函数。而不使用new操作符调用的函数就是普通函数。

```javascript
// 作为构造函数
let person = new Person('Nicholas',29,'software Engineer');
person.sayName();	//"Nicholas"

// 作为函数调用
Person('Greg',27,'Doctor');
window.sayName();	//"Greg"

//在另一个对象的作用域中调用
let o = new Oject();
Person.call(o,'Kris',25,'Singer');
o.sayName();	//"kris"
```



**2. 构造函数的问题**

​	其定义的方法会在每个实例上都创建一遍，因为函数也是对象，因此每次定义函数时，都会初始化一个对象。逻辑上讲，这个构造函数实际上是这样的:

```javascript
function Person(name,age,job){
    this.name=name;
    this.age=age;
    this.job=job;
    this.sayName=new Function()console.log(this.name);	//逻辑等价
}
```



### 2.4 原型模式

​	每个函数都会创建一个prototype属性，这个属性是一个对象，包含应该由特定引用类型的实例共享的属性和方法。实际上，这个对象就是通过调用构造函数创建的对象的原型。

```javascript
function Person(){}	//使用函数表达式也可以: let Person = function() {};
Person.prototype.name='Greg';
Person.prototype.age=29;
Person.prototype.job='Singer';
Person.prototype.sayName=function(){
    console.log(this.name);
};

let person1 = new Person();
person1.sayName();	//"Greg"

let person2 = new Person();
person2.sayName();	//"Greg"

console.log(person1.sayName == person2.sayName);	//true
```



**1. 理解原型**

​	无论何时，只要创建一个函数，就会按照特定的规则为这个函数创建一个prototype属性(指向原型对象)。默认情况下，所有原型对象自动获得一个名为constructor的属性，指回与之关联的构造函数。

​	在自定义构造函数时，原型对象默认只会获得constructor属性，其他的所有方法都继承自Object。每次调用构造函数创建一个新实例，这个实例的内部[[Prototype]]指针就会被赋值为构造函数的原型对象。脚本中没有访问这个[[Prototype]]特性的标准方式，但Firefox、Safari和Chrome会在每个对象上暴露\_\_proto\_\_属性，通过这个属性可以访问对象的原型。在其他实现中，这个特性完全被隐藏了。关键在于理解这一点:实例与构造函数原型之间有直接的联系，但实例与构造函数之间没有。





**2. 原型层级**



## 3 继承

​	很多面向对象语言都支持两种继承：接口继承和实现继承。前者只继承方法签名，后者继承实际的方法。接口继承在ECMAScript中是不可能的，因为函数没有签名。实现继承是ECMAScript唯一支持的继承方式，而这主要是通过**原型链**实现的。

### 3.1 原型链

​	ECMA-262把原型链定义为ECMAScript的主要继承方式。其基本思想就是通过原型继承多个引用类型的属性和方法。重温一下构造函数、原型和实例的关系：每个构造函数都有一个原型对象，原型有一个属性指回构造函数，而实例有一个内部指针指向原型。如果原型是另一个类型的实例呢？那就意味着这个原型本身有一个内部指针指向另一个原型，相应地另一个原型也有一个指针指向另一个构造函数。这样就在实例和原型之间构造了一条原型链。这就是原型链的基本构想。

```javascript
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

//继承SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function(){
    return this.subproperty;
};

let instance = new SubType();
console.log(instance.getSuperValue());	//true
```

<img src="C:\Users\Administrator\Documents\Typora文档\图片\原型链1.JPG" style="zoom:75%;" />

​	这个例子中实现继承的关键，是SubType没有使用默认原型，而是将其替换成了一个新的对象。这个新的对象恰好是SuperType的实例。这样一来，SubType的实例不仅能从SuperType的实例中继承属性和方法，而且还与SuperType的原型挂上了钩。于是instance(通过内部的[[Prototype]])指向SubType.prototype，而SubType.prototype(作为SuperType的实例又通过内部的[[Prototype]])指向SuperType.prototype。注意，getSuperValue()方法还在SuperType.prototype对象上，而property属性则在SubType.prototype上。这是因为getSuperValue()是一个原型方法，而property是一个实例属性。SubType.prototype的constructor属性被重写为指向SuperType，所以instance.constructor也指向SuperType。

​	原型链扩展了前面描述的**原型搜索机制**。我们知道，在读取实例上的属性时，首先会在实例上搜索这个属性。如果没找到，则会继承搜索实例的原型。在通过原型链实现继承之后，搜索就可以继承向上，搜索原型的原型。如调用instance.getSuperValue()经过了3步搜索：instance、SubType.prototype和SuperType.prototype，最后一步才找到这个方法。对属性和方法的搜索会一直持续到原型链的末端。

**1. 默认原型**

​	实际上，原型链中还有一环。默认情况下，所有引用类型都继承自Object，这也是通过原型链实现的。任何函数的默认原型都是一个Objecr的实例，这意味着这个实例有一个内部指针指向Object.prototype。这也是为什么自定义类型能够继承包括toString()、valueOf()在内部的所有默认方法的原因。因此在前面的例子还有额外一层继承关系。下图展示了完整的原型链。

<img src="C:\Users\Administrator\Documents\Typora文档\图片\原型链2.JPG" style="zoom:80%;" />



**2. 原型与继承关系**

​	原型与实例的关系可以通过两种方式来确定：

- *instanceof*操作符：如果一个实例的原型链中出现过相应的构造函数，则*instanceof*返回true。

```javascript
console.log(instance instancof Object);	//true
console.log(instance instancof SuperType);	//true
console.log(instance instancof SubType);	//true
```



- 使用*isPrototypeOf()*方法：原型链中的每个原型都可以调用这个方法，只要原型链中包含这个原型，这个方法就返回true;

```javascript
console.log(Object.prototype.isPrototypeOf(instance));	//true
console.log(SuperType.prototype.isPrototypeOf(instance));//true
console.log(SubType.prototype.isPrototypeOf(instance));	//true
```

**3. 关于方法**

​	子类有时候需要覆盖父类的方法，或者增加父类没有的方法。为此，这些方法必须在原型赋值之后再添加到原型上。

```javascript
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

//继承SuperType
SubType.prototype = new SuperType();

**
//新方法
SubType.prototype.getSubValue = function(){
    return this.subproperty;
};

//覆盖已有的方法
SubType.prototype.getSuperValue = funtion(){
	return false;
};
**

let instance = new SubType();
console.log(instance.getSuperValue());	//false

```

​	SubType实例上调用getSuperValue()时调用的是这个方法，而SuperType的实例仍然会调用最初的方法。重点在于上述两个方法都是在把原型赋值为SubperType的实例之后定义的。

​	**注意：** 以对象字面量方式创建原型方法会破坏之前的原型链，因为这相当于重写了原型链。

```javascript
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

//继承SuperType
SubType.prototype = new SuperType();

//通过对象字面量添加新方法，这会导致上一行无效
SubType.prototype = {
    getSubValue(){
        return this.subproperty;
    },
    someOtherMethod(){
        return false;
    }
};

let instance = new SubType();
console.log(instance.getSuperValue());	//出错!
```

​	在这段代码中，子类的原型在被赋值为SuperType的实例后，又被一个对象字面量覆盖了。覆盖后的原型是一个Object的实例，而不是SuperType的实例。因此之前的原型链就断了。SuperType和SubType之间也没有关系了。

**4. 原型链的问题**

​	原型的第一个问题：原型中包含的引用值会在所有实例间共享，这也是为什么属性通常会在构造函数中定义而不会定义在原型上的原因。在使用原型实现继承时，原型实际上变成了另一个类型的实例。这意味着原先的实例属性摇身一变变成了原型属性。

```javascript
function SuperType(){
    this.colors = ['red','blue','green'];
}

function SubType(){ }

//继承SuperType
SubType.prototype = new SuperType();

let instance1 = new SubType();
instance1.colors.push('black');
console.log(instance1.colors);//"red,blue,green,black"

let instance2 = new SubType();
console.log(instance2.colors);//"red,blue,green,black"
```

​	如果colors不是引用值，就不会出现这个问题了。

- 原型的第二个问题是，子类型在实例化时不能给父类型的构造函数传参。事实上，我们无法在不影响所有对象实例的情况下把参数传进父类的构造函数。再加上之前提到的原型中包含引用值的问题，就导致原型链基本不会被单独使用。



### 3.2 盗用构造函数

​	为了解决原型包含引用值导致的继承问题，一种叫作"盗用构造函数"的技术流行起来。基本思路很简单：在子类构造函数中调用父类构造函数。因为毕竟函数就是在特定上下文中执行代码的简单对象，所以可以使用*apply()*和*call()*方法以新创建的对象为上下文执行构造函数。

```javascript
function SuperType(){
    this.colors = ['red','blue','green'];
}
**
function SubType(){
    //继承SuperType
    SuperType.call(this);
}
**
let instance1 = new SubType();
instance1.colors.push('black');
console.log(instance1.colors);	//'red,blue,green,black'

let instace2 = new SubType();
console.log(instance2.colors);	//'red,blue,green'
```

​	通过使用call()或apply()方法，SuperType构造函数在为SubType的实例创建的新对象的上下文中执行了。这相当于新的SubType对象上运行了SuperType()函数中的所有初始化代码。结果就是每个实例都会有自己的colors属性。

**1. 传递参数**

​	相比于使用原型链，盗用构造函数的一个优点就是可以在子类构造函数中向父类构造函数传参。

```javascript
function SuperType(name){
    this.name = name;
}

function SubType(){
    //继承SuperType并传参
    SuperType.call(this,'Nicholas');
    
    //实例属性
    this.age = 29;
}

let instance = new SubType();
console.log(instance.name);	//'Nicholas'
console.log(instance.age);	//29
```





**2. 盗用构造函数的问题**

​	盗用构造函数的主要缺点，也是使用构造函数模式自定义类型的问题：必须在构造函数中定义方法，因此函数不能重用。此外，子类也不能访问父类原型上定义的方法，因此所有类型只能使用构造函数模式。由于存在这些问题，盗用构造函数基本上也不能单独使用。



### 3.3 组合继承

​	组合继承综合了原型链和盗用构造函数，将两者的优点集中了起来。基本的思路是使用原型链继承原型上的属性和方法，而通过盗用构造函数继承实例属性。这样既可以把方法定义在原型上以实现重用，又可以让每个实例都有自己的属性。

```javascript
function SuperType(name){
    this.name = name;
    this.colors = ['red','blue','green'];
}
SuperType.prototype.sayName = function(){
    console.log(this.name);
};

function SubType(name,age){
        //继承SuperType并传参
    SuperType.call(this,name);
    
    //实例属性
    this.age = age;
}

//继承方法
SubType.prototype = new SuperType();

SubType.prototype.sayAge = funtion(){
  console.log(this.age);  
};

let instance1 = new SubType("Nicholas",29);
instance1.colors.push("black");
console.log(instance1.colors);	//"red,blue,green,black"
instance1.sayName();	//"Nicholas"
instance1.sayAge();	//29

let instance1 = new SubType("Greg",27);
console.log(instance2.colors);	//"red,blue,green"
instance1.sayName();	//"Greg"
instance1.sayAge();	//27
```

​	组合继承弥补了原型链和构造函数的不足，是JavaScript中使用最多的继承模式。而且组合继承也保留了*instanceof*操作符和*isPrototypeOf()*方法识别合成对象的能力。



### 3.4 原型式继承

​	原型式继承，是一种不涉及严格意义上构造函数的继承方法。即使不自定义类型也可以通过原型实现对象之间的信息共享。

```javascript
function object(o){
    funtion F(){}
    F.prototype = o;
    return new F();
}
```

​	这个object()函数会创建一个临时构造函数，将传入的对象赋值给这个构造函数的原型，然后返回这个临时类型的一个实例。本质上，object()是对传入的对象执行了一次浅复制。

```javascript
let person ={
    name:"Nicholas",
    friends:["Shelby","Court","Van"]
};

let anothorPerson = object(person);
anothorPerson.name = "Greg";
anothorPerson.friends.push("Rob");

let yetAnothorPerson = object(person);
yetAnothorPerson.name = "Linda";
yetAnothorPerson.friends.push("Barbie");

console.log(person.friends);	//"Shelby,Court",Van,Rob,Barbie"
```

​	原型式继承适用于这种情况：你有一个对象，想在它的基础上再创建一个新对象。你需要把这个对象先传给object()，然后再对返回的对象进行适当修改。person对象定义了另一个也应该共享的信息，把它传给object()之后会返回一个新对象。这个新对象的原型是person，意味着它的原型上既有原始值属性又有引用值属性。

​	ECMAScript5通过增加Object.create()方法将原型式继承的概念规范化了。这个方法接收两个参数：作为新对象原型的对象，以及给新对象定义额外属性的对象(可选参数)。在只有一个参数时，Object.create()与这里的object()方法效果相同：

```javascript
let person ={
    name:"Nicholas",
    friends:["Shelby","Court","Van"]
};

let anothorPerson = Object.create(person);
anothorPerson.name = "Greg";
anothorPerson.friends.push("Rob");

let yetAnothorPerson = Object.create(person);
yetAnothorPerson.name = "Linda";
yetAnothorPerson.friends.push("Barbie");

console.log(person.friends);	//"Shelby,Court",Van,Rob,Barbie"

let person3 = Object.create(person,{
    name:{
        value:"Greg"
    }
});
console.log(person3.name);	//"Greg"
```



### 3.5 寄生式继承

​	与原型式继承比较接近的一种继承方式是寄生式继承，也是Crockford首倡的一种模式。寄生式继承背后的思路类似于寄生构造函数和工厂模式：创建一个实现继承的函数，以某种方式增强对象，然后返回这个对象。基本的寄生继承模式如下：

```javascript
function createAnother(original){
    let clone = object(original);	//通过调用函数创建一个新对象
    clone.sayHi = function(){	//以某种方式增强这个对象
        console.log("hi");
    };
    return clone;	//返回这个对象
}
```

​	createAnother()函数接收一个参数，就是新对象的基准对象。这个对象original会被传给object()函数，然后将返回的新对象赋值给clone。接着给clone对象添加一个新方法sayHi()。

​	寄生式继承同样适合主要关注对象，而不在乎类型和构造函数的场景。object()函数不是寄生式继承所必需的，任何返回新对象的函数都可在这里使用。

### 3.6 寄生式组合继承

​	组合继承其实也存在效率问题。最主要的效率问题就是父类构造函数始终会被调用两次：一次在是创建子类原型时调用，另一次是在子类构造函数中调用。本质上，子类原型最终是要包含超类对象的所有实例属性，子类构造函数只要在执行时重写自己的原型就行了。

```javascript
function SuperType(name){
    this.name = name;
    this.colors = ['red','blue','green'];
}
SuperType.prototype.sayName = function(){
    console.log(this.name);
};

function SubType(name,age){
    SuperType.call(this,name);	//第二次调用SuperType()
    this.age = age;
}

SubType.prototype = new SuperType();	//第一次调用SuperType()
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
    console.log(this,age);
};
```

​	<img src="C:\Users\Administrator\Documents\Typora文档\图片\原型链3.JPG" style="zoom:80%;" />

​	<img src="C:\Users\Administrator\Documents\Typora文档\图片\原型链4.JPG" style="zoom:80%;" />

​	有两组name和colors属性：一组在实例上，另一组在SubType的原型上。这是调用两次SuperType构造函数的结果。好在有办法解决这个问题。

​	寄生式组合继承通过盗用构造函数继承属性，但使用混合式原型链继承方法。基本思路是不通过调用父类构造函数给子类原型赋值，而是取得父类原型的一个副本。说到底就是使用寄生式继承来继承父类原型，然后将返回的新对象赋值给予子类原型。寄生式组合继承的基本模式如下所示：

```javascript
function inheritPrototype(subType,superType){
    let prototype = object(superType.prototype);	//创建对象
    prototype.constructor = subType;				//增强对象
    subType.prototype = prototype;					//赋值对象
}
```

​	这个inheritPrototype()函数实现了寄生式组合继承的核心逻辑。这个函数接收两个参数：子类构造函数和父类构造函数。在这个函数内部，第一步是创建父类原型的一个副本。然后，给返回的prototype对象设置constructor属性，解决由于重写原型导致默认constructor丢失的问题。最后将新创建的对象赋值给予类型的原型。如下例所示，调用inheritPrototype()就可以实现前面例子中的子类型原型赋值：

```javascript
function SuperType(name){
    this.name = name;
    this.colors = ['red','blue','green'];
}
SuperType.prototype.sayName = function(){
    console.log(this.name);
};

function SubType(name,age){
    SuperType.call(this,name);	
    this.age = age;
}

**
inheritPrototype(SubType,SuperType);
**
    
SubType.prototype.sayAge = funtion(){
  console.log(this.age);  
};

```

​	这里只调用了一次SUperType的构造函数，避免了SubType.prototype上不必要也用不到的属性，因此也可以说这个例子的效率更高。而且，原型键仍然保持不变，因此instanceof操作符和isPrototypeOf()方法正常有效。**寄生式组合继承**可以算是引用类型继承的最佳模式。

## 4 类





### 4.1 类定义









### 4.2 类构造函数



### 4.3 实例、原型和类成员

​	类的语法可以非常方便地定义应该在于实例上的成员、应该存在于原型上的成员，以及应该存在于类本身的成员。

#### 1. 实例成员

​	每次通过new调用类标识符时，都会执行类构造函数。在这个函数内部，可以为新创建的实例(this)添加"自有"属性。至于添加什么样的属性，则没有限制。另外，在构造函数执行完毕后，仍然可以给实例继续添加新成员。

​	每个实例都对应一个唯一的成员对象，这意味着所有成员都不会在原型上共享:

```javascript
class Person{
    constructor(){
        this,name = new String('Jack');
        this,sayName = ()=>console.log(this.name);
        this.nicknames = ['Jack','J-Dog']
    }
}

let p1 = new Person(),
    p2 = new Person();

p1.sayName(); //Jack
p2.sayName(); //Jack

console.log(p1.name === p2.name); //false
console.log(p1.sayName === p2.sayName); //false
console.log(p1.nicknames === p2.nicknames); //false

p1.name = p1.nicknames[0];
p2.name = p2.nicknames[1];

p1.sayName();	//Jake
p1.sayName();	//J-Dog
```

#### 2. 原型方法与访问器

​	为了在实例间共享方法，类定义语法把在类块中定义的方法作为原型方法。

```javascript
class Person{
    constructor(){
        this.locate = ()=>console.log('instance');
    }
    
    //在类块中定义的所有内容都会定义在类的原型上
    locate(){
        console.log('prototype');
    }
}

let p = new Person();

p.locate();					 //instance
Person.prototype.locate();	//prototype
```

​	可以把方法定义在类构造函数中或者类块中，但不能在类块中给原型添加原始值或对象作为成员数据。

#### 3. 静态类方法

​	可以在类上定义静态方法。这些方法通常用于执行不特定于实例的操作，也不要求存在类的实例。与原型成员类似，每个类上只能有一个静态成员。

```javascript
class Person{
    constructor(){
        this.locate = ()=>console.log('instance',this);
    }
    
    //定义在类的原型对象上
    locate(){
        console.log('prototype',this);
    }
    //定义在类本身上
    static locate(){
        console.log('class',this);
    }
}

let p = new Person();

p.locate();					 //instance,Person()
Person.prototype.locate();	//prototype, {constructor:...}
Person.locate();			//class,class Person{}
```

#### 4. 非函数原型和类成员

​	类定义中之所以没有显示支持添加数据成员，是因为在共享目标(原型和类)上添加可变数据成员是一种反模式。一般来说，对象实例应该独自拥有通过this引用的数据。也可在类定义外部，手动添加。

#### 5. 迭代器与生成器方法



### 4.4 继承

​	ES6新增特性中最出色的一个就是原生支持了类继承机制。虽然类继承使用的是新语法，但背后依旧使用的是原型链。

**1. 继承基础**





**2. 构造函数、HomeObject和super()**





## 5 小结

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210313220450103.png" alt="image-20210313220450103" style="zoom:80%;" />

