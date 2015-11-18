# jsnote
1. underfined不是关键字，要让一个变量指向未定义或删除该变量，`xxx = underfined`是错误写法，因为
underfined可以`var underfined = xxx`，被定义了，变成一个变量了，
正确写法是使用void 加上任何数字（如void 0），void 加数字结果总是返回underfined。
* 关于length
