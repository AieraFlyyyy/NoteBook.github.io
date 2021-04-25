# bind\(\)、call\(\)、apply\(\)

### bind\(\)

[官方文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)说：

The **`bind()`** method creates a new function that, when called, has its `this` keyword set to the provided value.

意思是，bind\(\)方法会创造一个新方法，并通过传参指定`this` 关键字

举一个栗子：

```javascript
var pokemon = {
    firstname: 'Pika',
    lastname: 'Chu ',
    getPokeName: function() {
        var fullname = this.firstname + ' ' + this.lastname;
        return fullname;
    }
};

var pokemonName = function() {
    console.log(this.getPokeName() + 'I choose you!');
};

var logPokemon = pokemonName.bind(pokemon); // creates new object and binds pokemon. 'this' of pokemon === pokemon now

logPokemon(); // 'Pika Chu I choose you!'
```

让我们逐步分析上述过程：

1. 首先实例化一个**pokemonName**并绑定`pokemon`作为它的this变量。**重点**要记住，这是创建了一个**pokemonName**的copy
2. 我们把**pokemonName**的copy叫做`logPokemon()`，虽然`logPokemon()`中没有`pokemon`对象，但它现在可以使用`pokemon`的proprieties\(Pika and Chu\)以及它的`getPokeName()`方法

当我们通过bind\(\)绑定一个value并生成一个copy函数后，我们任然可以像普通函数一样，继续正常使用这个函数，比如做一些传参：

```javascript
var pokemon = {
    firstname: 'Pika',
    lastname: 'Chu ',
    getPokeName: function() {
        var fullname = this.firstname + ' ' + this.lastname;
        return fullname;
    }
};

var pokemonName = function(snake,hobbit){
    console.log(this.getPokeName() + 'love' + snake + 'and' + hobbit);
};

var logPokemon = pokemonName.bind(pokemon); // creates new object and binds pokemon. 'this' of pokemon === pokemon now

logPokemon('sushi','sleep'); // 'Pika Chu love sushi and sleep '
```



### call\(\)、apply\(\)

[官方文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)对call\(\)的描述：

The **`call()`** method calls a function with a given `this` value and arguments provided individually.

这意味着我们可以调用任何函数，并_明确指定_在调用函数中_`this`应引用_的内容，跟bind\(\)方法感觉挺像的

bind\(\)和call\(\)的区别是，call\(\)方法：

1. 能接收额外的参数
2. 立即执行所调用的函数
3. call\(\)方法不会生成调用函数的copy

call\(\)和appyl\(\)方法是为同一个目标服务的。它们工作方式的唯一**区别**是：call\(\)接收任意数量的参数，而apply\(\)接收一个包含所有参数的数组

举个栗子看：

```javascript
var pokemon = {
    firstname: 'Pika',
    lastname: 'Chu ',
    getPokeName: function() {
        var fullname = this.firstname + ' ' + this.lastname;
        return fullname;
    }
};

var pokemonName = function(snack, hobby) {
    console.log(this.getPokeName() + ' loves ' + snack + ' and ' + hobby);
};

pokemonName.call(pokemon,'sushi', 'algorithms'); 
// Pika Chu  loves sushi and algorithms

pokemonName.apply(pokemon,['sushi', 'algorithms']); 
// Pika Chu  loves sushi and algorithms
```





