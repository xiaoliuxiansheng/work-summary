## 获取当前周 每天对应的时间戳
```vue
方法：
_initSearchTime() {
    let other = {
        startTime: '',
        endTime: ''
    }
    if (this.selectCont.selectView === '按周') {
        // 周数据的开始结束时间
        if (this.searchData.dateTimeWeek === '') {
            // 没有开始时间
            console.log("------------------------")
            this.searchData.dateTimeWeek = new Date(moment().startOf('isoWeek'))
            other.startTime = new Date(moment().startOf('isoWeek')).getTime() / 1000
            other.endTime = Math.floor(new Date(moment().endOf('isoWeek')).getTime() / 1000)
        } else {
            // 有开始时间
            other.startTime = new Date(moment(this.searchData.dateTimeWeek).startOf('isoWeek')).getTime() / 1000
            other.endTime = new Date(moment(this.searchData.dateTimeWeek).endOf('isoWeek')).getTime() / 1000
        }

    } else {
        let val = this.searchData.dateTimeMouth
        // 月数据的开始结束时间
        if (this.searchData.dateTimeMouth === '') {
            // 没有开始时间
            this.searchData.dateTimeMouth = new Date(moment().month(moment().month()).startOf('month'))
            other.startTime = moment().month(moment().month()).startOf('month').valueOf() / 1000
            other.endTime = Math.floor(moment().month(moment().month()).endOf('month').valueOf() / 1000)
        } else {
            // 没有开始时间
            var next = this.dz.getSencondStamp(new Date(val.getFullYear(), val.getMonth() + 1, 1))
            var end = parseInt(parseInt(next) - 86400)
            //     other.startTime = moment(this.searchData.dateTimeMouth).month().startOf('month').valueOf() / 1000
            //     other.endTime = Math.floor(moment(this.searchData.dateTimeMouth).month().endOf('month').valueOf() / 1000)
            // }
            other.startTime = this.dz.getSencondStamp(new Date(moment(val).startOf('month')))
            other.endTime = end
        }
    }
    return other
},

```