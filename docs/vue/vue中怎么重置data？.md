# vue中怎么重置data？
初始状态下设置data数据的默认值，重置时直接bject.assign(this.$data, this.$options.data())

说明：
this.$data获取当前状态下的data
this.$options.data()获取该组件初始状态下的data(即初始默认值)
如果只想修改data的某个属性值，可以this[属性名] = this.$options.data()[属性名]，如this.message = this.$options.data().message
