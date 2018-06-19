# React Immutable

immutable丰富的数据处理语法糖以及性能

函数式开发主要特征，variable immutable

## immutable整合进react库

react渲染普通数据结构

  - undefined、null、boolean、symbol

  - number、string直接按照string进行显示

## immutable整合redux库

按照切片slice划分，自主将对应切片以下所有数据均处理为immutable structure，保证plain object切片/范式化切片与immutable切片分开。

action、reducer

## immutable整合react-router-redux库
