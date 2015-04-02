## JavaScript 學習筆記

#### 複製物件  
物件(Object)的相等`=`並不會複製物件，而是建立相同的參考，  
因此在編輯物件時要注意會不會改到其他物件
```
var objA = {Name:'A'}; 
var objB = objA; 
console.log(objB.Name); // A 

objB.Name = 'B'; 
console.log(objA.Name); // B 
console.log(objB.Name); // B
```
正確的複製方式可以使用`jQuery.extend()`或是`angular.copy()`
```
jQuery.extend( [deep], target, object1 [, objectN ] );
```
| Name | Type | Description |
| ---- | ---- | ----------- |
| deep | Boolean | If true, the merge becomes recursive (aka. deep copy) |
| target | Object | The object to extend. It will receive the new properties. |
| object1 | Object | An object containing additional properties to merge in.|
| objectN | Object | Additional objects containing properties to merge in. |  
```
angular.copy(source, [destination]);
```
| Name | Type | Description |
| ---- | ---- | ----------- |
| source | * | The source that will be used to make a copy. Can be any type. |
| destination | Object/Array | Destination into which the source is copied. |
| RETURN | * |The copy or updated destination, if destination was specified. |
  
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
  
#### 閉包(Closure)
閉包就是在函式裡面的函式，讓內部的函式可以存取外部函式的變數，同時又可以讓外部執行函式  
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
  
#### 函式的 arguments
函式的`arguments`具有`length`特性，但不具有陣列的方法
