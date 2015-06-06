##JavaScript Design Pattern

####物件化參數模式
此模式的原理是把多個參數都用物件包裝再傳遞給函式使用，適合用在大部分的多參數函式。優點：不需要知道參數順序、容易新增與移除參數。

```
// 平常
function fun(a, b, c) {
    // a
    // b
    // c  
} 

// 物件化參數
function fun(obj) {
    // obj.a
    // obj.b
    // obj.c
} 
```
此外此模式也被使用在 jQuery Plugin 的 conf 設定
```
// 預設的 conf 設定
var defaultConf = { 
    /*...*/
};

// 預設的 conf 設定 + 參數傳遞來的 paramConf 
$.extend(defaultConf, paramConf);
```

####原型繼承模式
此模式的原理是使用 prototype 來實作物件的繼承，且不需要模擬 Class，更適合 JavaScript 來使用。
```
var parent = {
    lastname: 'Lin'
};

var child1 = Object.create(parent);
var child2 = Object.create(parent, {
                            age: {value: 2}
                        });

console.log(child1.lastname); // 'Lin'
console.log(child2.age); // 2

``` 
此模式使用了 ECMAScript 5 的 `Object.create()`，他會把 child 的 `_proto_` 指向 parent 物件。同時可額外傳遞物件參數：`{age: {value: 2}}`，這個物件參數的屬性會被加到新物件中。

#### 閉包(Closure)
閉包就是在函式裡面的函式，讓內部的函式可以存取外部函式的變數，同時又可以讓外部執行函式。  
一般函式內宣告的變數，對外面而言為區域變數，無法取得且生命只存在函式執行期間；  
但在一般函式內加上閉包後，函式內宣告的變數變得介於全域與區域之間，內部的閉包函式可以存取該變數，且生命變得和閉包相同  
  
以計數器為範例：
```
//計數器A
var counter = 0;
function add() {
    counter += 1;
}
add();
add();
add();
```
計數器A可以透過`add()`函式來計數，但因為`counter`是全域變數，因此不用透過`add()`其實也可以改變`counter`的值，這樣的方式不是很理想
```
//計數器B
function add() {
    var counter = 0;
    counter += 1;
}

add();
add();
add();
```
計數器B試者把`counter`變成區域變數，但這卻導致每次呼叫`add()`函式時都先把`counter`歸零再加一，這樣的計數器根本無法使用。  
為了解決兩個問題，我們可以使用閉包(Closure)：
```
//計數器C
var add = (function () {
    var counter = 0;
    return function () {return counter += 1;}
})();

add();
add();
add();
```
計數器C可以在每次呼叫`add()`時正確地把`counter`加一，同時外部程式要改變`counter`也只能透過`add()`而已，這樣的計數器才是我們想要的。

####模組模式（立即函式）
JavaScript 的模組模式具備以下的特點：
+ 命名空間  
+ 立即函式
+ Pirvate 成員與方法
+ 引進全域變數

```
var myApp = myApp || {};

myApp.Module = (function($) {
    var Module = {};

    // 成員
    var privateProperty = 'private';
    Module.publicProperty = 'public';

    // 方法
    function privateMethod() {
        console.log('private method');
    };

    Module.publicMethod = function() {
        console.log('public method');
    };

    return Module;
})(jQuery);

console.log(myApp.Module.publicProperty);  // public
console.log(myApp.Module.privateProperty); // undefined

myApp.Module.publicMethod();  // 'public method'
myApp.Module.privateMethod(); // not a function
```

####函式快取模式（函式記憶模式）
此模式的原理是把資料儲存在函式的一個屬性裡作為快取，適合用來保存需龐大計算後的結果。計算的參數若是使用物件來傳遞，需先使用`JSON.stringify()`轉換成字串才能作為快取的鍵值，用來作為不同參數的識別。
```
function Compute(param) {
    var cacheKey = JSON.stringify(param);
    if(!Compute.cache[cacheKey]) {
        var result = 0;
        for(var i=0; i<param.i; i++)
        {
            for(var j=0; j<param.j; j++)
            {
                result += i * j / (i+j+1);
            }
        }
        console.log(result);
        Compute.cache[cacheKey]  = result;
    }
    return Compute.cache[cacheKey];
}
Compute.cache = {};
```

####回呼模式
此模式的原理是把函式作為參數傳遞給其他函式，適合用來作函式的分工。事件的回呼函式與 `setTimeout()` 就是類似的應用。
```
function Do(something) {
    something();
}

function Walk() {/*...*/};
function Sleep() {/*...*/};

Do(Walk);
Do(Sleep);
```
另一種可以用的時候是，當 Do() 和 Sleep() 都需要跑遍迴圈時，可以用此模式讓函式在同一個迴圈內完成。

####鏈接模式
此模式的原理是讓物件的方法都回傳 `this`，讓你可以一個接著一個呼叫很多的方法，適合用來簡化呼叫方法的程式碼。此模式被廣泛地使用在 jQuery。
```
var obj = {
    value: 1,
    add: function(){
        this.value += 1;
        return this;
    },
    minus: function(){
        this.value -= 1;
        return this;
    },
    print: function(){
        alert(this.value);
        return this;
    }
}

obj.add().minus().add().print();
```

####函式部分應用模式（Currying）
此模式的原理是把函式的部分參數提取出來建立一個新的函數來使用，適合用在呼叫某個函式時，傳入的參數大多相同的時候，可以用此模式來簡化重複的參數傳遞。函式的 Curry 化的實作也可以說是「當參數不完整的時候回傳新的函式」。

```
// 已 Curry 化的函式（會 return 新的函式）
function add(x, y){
    var oldx = x, oldy = y;
    if(!oldy) { // 參數不完整
        return function (newy) { // 回傳新的函式
            return oldx + newy;
        }
    }
    return x+y; // 參數完整的時候回傳值
}
add(5,10);  // 15
var add5 = add(5); // 回傳新的函式
add5(10);   // 15
```

####自我定義函式
此模式的原理是在函式初始化完成後更新自己的實作，很適合用在函式的初始化。此模式也稱作「懶惰的函式定義」(Lazy function definition) 。不過此模式的缺點，那就是在函式重新定義之後，原本函式的屬性都會遺失。
```
var DoSomething = function() {
    console.log('Do Initialization');
    DoSomething = function() {
        console.log('Do Something');
    };
}

DoSomething(); // Do Initialization
DoSomething(); // Do Something
```
