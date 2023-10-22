# let、const、var差别

## 条件声明不可提升

> let和const声明时，变量不会提升

## 暂时性死区

> 在let和const声明变量之前的块级作用域，该变量出现则会报错

## 全局变量

> var声明的变量会作为windows属性，可以通过windows.变量检测到，let和const则不会

## 不可重复声明

> let和const对于同一变量不可以再一次重复进行声明

## 块级作用域

> let和const作用域为块级作用域，var作用域是函数作用域