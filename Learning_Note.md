## JavaScript Learning Note

#### 複製物件  
物件(Object)的 assign (`=`)並不會複製物件，而是建立相同的參考，  
因此在編輯物件時要注意會不會改到其他物件
```
var objA = {Name:'A'}; 
var objB = objA; 
console.log(objB.Name); // A 

objB.Name = 'B'; 
console.log(objA.Name); // B 
console.log(objB.Name); // B
```
正確的複製方式可使用:
```
Object.assign(target, ...sources)
```
用法
```
var obj = { a: 1 };
var copy = Object.assign({}, obj);
console.log(copy); // { a: 1 }
```
`Object.assign()` 僅會複製一層屬性，若要深層複製，可以使用
```
var copy = JSON.parse(JSON.stringify(obj));
```
或是使用 jQuery 提供的方法：
```
jQuery.extend( [deep], target, object1 [, objectN ] );
```
| Name | Type | Description |
| ---- | ---- | ----------- |
| deep | Boolean | If true, the merge becomes recursive (aka. deep copy) |
| target | Object | The object to extend. It will receive the new properties. |
| object1 | Object | An object containing additional properties to merge in.|
| objectN | Object | Additional objects containing properties to merge in. |  
  
#### 物件的遍歷  
`for in` 迴圈可列出物件所有的特性，但也包含了函式特性與原型特性  
例如：
```
var objA = {
    Name: 'A',
    Age: '20',
    method: function() {return ;}
};
for(var i in objA) {
    console.log(i, objA[i]);
}

/**
* Name A
* Age 20
* method function () {return ;}
*/
```
在上例中，函式`method`也被取出來了  
而正確的遍歷方式應該為：
```
for(var i in objA) {
    if(objA.hasOwnProperty(i) && typeof objA[i] !== 'function')
    {
        console.log(i, objA[i]);
    }
}
    
/**
* Name A
* Age 20
*/
```
  
#### 變數的預設值  
宣告變數時可以使用`||`運算子填入預設值
```
var obj = {};
var v = obj['prop'] || 'unknow';
```
可以避免取到`undefined`  
另外  
```
function myFunction(y) {
    if (y === undefined) {
        y = 0;
    }
}
```
可以用簡化成以下方式
```
function myFunction(y) {
    y = y || 0;
}
```
  
#### 避免取得 undefined 的特性
```
var obj = {};
var v = obj.prop.value; // TypeError: Cannot read property 'value' of undefined
```
在上例中會丟出TypeError例外狀況，我們可以使用`&&`來避免：
```
var obj = {};
var v = obj.prop && obj.prop.value; // undefined
```

#### NaN  
`NaN`屬於數值，他代表「運算無法產生正常結果」  
`NaN`不等於任何值，包含他自己
```
NaN == NaN // false
```
可以使用`isNaN()`函式來偵測`NaN` 
  
#### 物件導向的繼承
在類別式語言中，
>物件是類別的實例  
>類別可以繼承類別  

但在 JavaScript 這種 Prototype 語言中，物件可以直接繼承其他物件。
  
#### 陣列的 length
每個陣列都有 `length` 特性（物件沒有`length`特性），  
length 不一定是陣列的特性數量，而會是陣列中最大的整數特性+1
```
var arr = [];
arr.length  // 0

arr[1000] = true;
arr.length // 1001
// 但 arr 其實只有一個特性
```
給陣列加上非數字的特性，不會改變陣列的 length：
```
var arr = [];
arr['a'] = true;
arr.length // 0
arr['a']   // true
```
  
#### 加速陣列與DOM物件的 For Loop
使用 var max = Array.length，可以讓 Loop 在運作時只執行一次 length 計算，對於龐大的 DOM 物件特別有加速的效果
```
for(var i=0, max=Array.length; i<max; i++)
{
    //...
}
```
  
#### 物件實字與建構式
物件實字：`var obj = {};`  
物件建構式：`var obj = new Object();`  
  
在建立物件時，應該使用物件實字來建立，除非是要建立自定的物件，才使用建構式  
使用實字而不用建構式的原因：
+ 實字程式碼較短
+ 不用考慮 class 是什麼
+ 不需要作用域的判斷（不用確認是否跟建構式在同一的 scope）
+ 建構式的參數可能會造成型別轉換，例如：`var obj = new Object(1);`，此時`obj.constructer`為`Number`而非`Object`
+ 使用`new Number(1)`會回傳 Object 而不是 Number
  
內建的物件（Array, String）都應該用物件實字來建立，例如：
```
var myArray = [];
var myString = "";
```
  
#### 函式的作用域（Scope）
只有 `function() { /*...*/ }` 才會提供獨立的作用域，其內的變數才是區域變數。換句話說，在下例裡面的變數 `i`和`a`雖然在大括號內，但他們其實都是全域變數
```
for(var i=0; i<5; i++){
    var a = i;
}
```
  
#### 函式與變數的提昇（Hoisting）
JavaScript 會把變數的宣告提昇到最頂端，但該變數的定義並不會被提昇（會被宣告但值為 undefined）。而對於函式，函式的宣告與定義都會被提昇到最頂端

常見的變數 Hoisting 問題
```
var foo = 1;
function bar() {
    console.log(foo);
    if (!foo) {
        var foo = 10;
    }
    console.log(foo);
}
bar(); // Output: undefined & 10
```

#### 函式的 arguments
函式的`arguments`具有`length`特性，但不具有陣列的方法

#### 無法使用中括號直接改變字串內的字元
字串的`[]`運算子是 Read-only，無法用來改變字串的值
```
var s = '1111';
s[0] = 0;
console.log(s); // 仍是 '1111'
```
