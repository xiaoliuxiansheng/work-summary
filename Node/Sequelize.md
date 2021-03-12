## 一. api 文档定义：多对多关系 （Many-to-many association ）



 ### 简单使用 - 例子（三表联合查询）：

 * 第一步： 创建表及关联表

 1、学生表student ： id、name、sex等

 2、城市表city ： id、province、city、county等

 3、学生与城市关联表stu_city_info：id、stu_id、city_id等

 


 * 第二步：sequelizejs 创建关联
 ```javascript
Department.associate = function() {

  const {Student, City, StuCityInfo } = app.model

  Student.
 belongsToMany(City, { through: StuCityInfo , foreignKey: 'stuId', otherKey: 'cityId' })

  }
````
 详解： belongsToMany 多对多关系 ， 源表Student、、源表City、关联表StuCityInfo(即中间表) 

 foreignkey、otherkey为中间表的外键属性
