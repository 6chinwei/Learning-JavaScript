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
可以避免取到`undefined`。

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

#### 閉包


#### 函式的 arguments
函式的`arguments`具有`length`特性，但不具有陣列的方法
