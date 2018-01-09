## 问题一
```
<input name="name" ng-model="name">
<span>{{name}}</span>
```
ng-model 双绑在某些情况下会不太好使，原因是某些指令会创建自己的作用域，导致作用域发生改变。

## 问题二
```
<input name="name" ng-model="modal.name">

$scope.$watch('modal.name', function (newVal) {
    //do something
});
```
ng-model 绑定时，变量没有直接绑在作用域下，但 $watch 不生效。

## 最佳实践
不要直接把变量绑到 $scope 上，在外面再套一层，并且要在 js 里对绑定的变量进行声明。
```
<input name="name" ng-model="modal.name">

$scope.modal = {
    name: ''
};
$scope.$watch('modal.name', function (newVal) {
    //do something
});
```

## 补充方案：
[controllerAs方案](http://pinkyjie.com/2015/02/09/controller-as-vs-scope/)

