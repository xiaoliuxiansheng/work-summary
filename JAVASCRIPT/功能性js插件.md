## 功能性js
```javascript 1.8
import moment from 'moment'

function pluralize (time, label) {
    if (time === 1) {
        return time + label
    }
    return time + label + 's'
}

export function timeAgo (time) {
    const between = Date.now() / 1000 - Number(time)
    if (between < 3600) {
        return pluralize(~~(between / 60), ' minute')
    } else if (between < 86400) {
        return pluralize(~~(between / 3600), ' hour')
    } else {
        return pluralize(~~(between / 86400), ' day')
    }
}

export function parseTime (time, cFormat) {
    if (arguments.length === 0) {
        return null
    }

    if ((time + '').length === 10) {
        time = +time * 1000
    }

    const format = cFormat || '{y}-{m}-{d} {h}:{i}:{s}'
    let date
    if (typeof time === 'object') {
        date = time
    } else {
        date = new Date(parseInt(time))
    }
    const formatObj = {
        y: date.getFullYear(),
        m: date.getMonth() + 1,
        d: date.getDate(),
        h: date.getHours(),
        i: date.getMinutes(),
        s: date.getSeconds(),
        a: date.getDay()
    }
    const timeStr = format.replace(/{(y|m|d|h|i|s|a)+}/g, (result, key) => {
        let value = formatObj[key]
        if (key === 'a') {
            return ['一', '二', '三', '四', '五', '六', '日'][value - 1]
        }
        if (result.length > 0 && value < 10) {
            value = '0' + value
        }
        return value || 0
    })
    return timeStr
}

export function formatTime (time, option) {
    time = +time * 1000
    const d = new Date(time)
    const now = Date.now()

    const diff = (now - d) / 1000

    if (diff < 30) {
        return '刚刚'
    } else if (diff < 3600) {
        // less 1 hour
        return Math.ceil(diff / 60) + '分钟前'
    } else if (diff < 3600 * 24) {
        return Math.ceil(diff / 3600) + '小时前'
    } else if (diff < 3600 * 24 * 2) {
        return '1天前'
    }
    if (option) {
        return parseTime(time, option)
    } else {
        return (
            d.getMonth() +
            1 +
            '月' +
            d.getDate() +
            '日' +
            d.getHours() +
            '时' +
            d.getMinutes() +
            '分'
        )
    }
}

/*  数字 格式化 */
export function nFormatter (num, digits) {
    const si = [
        {value: 1e18, symbol: 'E'},
        {value: 1e15, symbol: 'P'},
        {value: 1e12, symbol: 'T'},
        {value: 1e9, symbol: 'G'},
        {value: 1e6, symbol: 'M'},
        {value: 1e3, symbol: 'k'}
    ]
    for (let i = 0; i < si.length; i++) {
        if (num >= si[i].value) {
            return (
                (num / si[i].value + 0.1)
                    .toFixed(digits)
                    .replace(/\.0+$|(\.[0-9]*[1-9])0+$/, '$1') + si[i].symbol
            )
        }
    }
    return num.toString()
}

export function html2Text (val) {
    const div = document.createElement('div')
    div.innerHTML = val
    return div.textContent || div.innerText
}

export function toThousandslsFilter (num) {
    return (+num || 0)
        .toString()
        .replace(/^-?\d+/g, m => m.replace(/(?=(?!\b)(\d{3})+$)/g, ','))
}

/**
 * 格式化文件大小
 * @param value
 * @returns {*}
 */
export function renderSize (value) {
    if (value === null || value === '') {
        return '0B'
    }
    let unitArr = ['B', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB']
    let srcsize = parseFloat(value)
    let index = Math.floor(Math.log(srcsize) / Math.log(1024))
    let size = srcsize / Math.pow(1024, index)
    size = size.toFixed(0) // 保留的小数位数
    return size + unitArr[index]
}

/**
 * 时间戳转时分
 * @param {number} Timestamps 时间戳
 * @returns {string} 23:59(分)
 */
export function timestampToHm (Timestamps) {
    return moment(Timestamps * 1000).format('HH:mm')
}

/**
 * 时分转时间戳
 */
export function timeToseconds (time) {
        var s = ''
        var hour = time.split(':')[0]
        var min = time.split(':')[1]
        s = Number(hour * 3600) + Number(min * 60)
        return s
}

/**
 * 时间戳转时分秒
 * @param {number} Timestamps 时间戳
 * @returns {string} 23:59:59(秒)
 */
export function timestampToHms (Timestamps) {
    return moment(Timestamps * 1000).format('HH:mm:ss')
}

/**
 * 时间戳转日期
 * @param Timestamps 时间戳
 * @returns {string} 2018-08-28 (日)
 */
export function timestampToYMD (Timestamps) {
    if (Timestamps) {
        return moment(Timestamps * 1000).format('YYYY-MM-DD')
    } else {
        return '--'
    }
}

/**
 * 时间戳转短时间
 * @param Timestamps 时间戳
 * @returns {string} 2018-08-28 23:59(分)
 */
export function timestampToYMDHm (Timestamps) {
    if (Timestamps) {
        return moment(Timestamps * 1000).format('YYYY-MM-DD HH:mm')
    } else {
        return '--'
    }
}

/**
 * 时间戳转长时间
 * @param Timestamps 时间戳
 * @returns {string} 2018-08-28 23:59:59(秒)
 */
export function timestampToYMDHms (Timestamps) {
    if (Timestamps) {
        return moment(Timestamps * 1000).format('YYYY-MM-DD HH:mm:ss')
    } else {
        return '--'
    }
}

/**
 * 时间转时间戳
 * @param {number} time 时间
 * @returns {string}  1534567891(秒)
 */
export function timeToTimestamp (time) {
    return moment(time, 'YYYY-MM-DD HH:mm:ss').valueOf() / 1000
}

/**
 * 时间转时分
 * @param {number} time 时间
 * @returns {string}  23:59(分)
 */
export function timeToHm (time) {
    let Timestamps = moment(time, 'YYYY-MM-DD HH:mm').valueOf()
    return moment(Timestamps).format('HH:mm')
}

/**
 * 时间转时分秒
 * @param {number} time 时间
 * @returns {string}  23:59:59(秒)
 */
export function timeToHms (time) {
    let Timestamps = moment(time, 'YYYY-MM-DD HH:mm').valueOf()
    return moment(Timestamps).format('HH:mm:ss')
}

/**
 * 时间对象转时分秒
 * @param {number} time 时间
 * @return {string} 2018-08-08 23:59:59(秒)
 */
export function timeToYMDms (time) {
    let Timestamps = moment(time, 'YYYY-MM-DD HH:mm:ss').valueOf()
    return moment(Timestamps).format('YYYY-MM-DD HH:mm:ss')
}

/**
 * 时间对象转时分
 * @param {number} time 时间
 * @return {string} 2018-08-08 23:59(分)
 */
export function timeToYMDm (time) {
    let Timestamps = moment(time, 'YYYY-MM-DD HH:mm:ss').valueOf()
    return moment(Timestamps).format('YYYY-MM-DD HH:mm')
}

/**
 * 格式化性别
 * @param {number} sex 0女 1男
 * @return {string}
 */
export function sex (sex) {
    switch (parseInt(sex)) {
        case 0:
            return '女'
        case 1:
            return '男'
    }
}

/**
 * 格式化审批同意拒绝
 * @param {number} status 0同意 1拒绝
 * @return {string}
 */
export function approvalStatus (status) {
    switch (parseInt(status)) {
        case 0:
            return '同意申请'
        case 1:
            return '拒绝申请'
    }
}

/**
 * 处理耗时
 * @param {Number} 送审时间ms
 * @returns {String} X天X小时X分
 */
export function consumingTime (s) {
    s = s < 0 ? 1 : s
    let day = Math.floor(s / 86400)
    let hours = Math.floor((s - day * 86400) / 3600)
    let minutes = Math.floor((s - day * 86400 - hours * 3600) / 60)
    return (day ? day + '天' : '') + (hours ? hours + '小时' : '') + (minutes ? minutes + '分钟' : '<1分钟')
}

/**
 * 日期转s
 * @param {String} eg: "2018-09-18T16:00:00.000Z"
 * @returns {String} X秒
 */
export function getDateFn (s) {
    return s ? new Date(s).getTime() / 1000 + '' : ''
}

/**
 * 深度克隆
 * @param   {Object} Obj 原数据
 * @returns {Object} {*} 克隆数据
 */
export function clone (Obj) {
    let buf
    if (Obj instanceof Array) {
        buf = [] // 创建一个空的数组
        let i = Obj.length
        while (i--) {
            buf[i] = clone(Obj[i])
        }
        return buf
    } else if (Obj instanceof Object) {
        buf = {} // 创建一个空对象
        for (let k in Obj) {
            // 为这个对象添加新的属性
            buf[k] = clone(Obj[k])
        }
        return buf
    } else {
        return Obj
    }
}

/**
 * 生成随机字符串
 * @param len 需要生生字符串的长度 默认生成6位字符串
 * @param charSet 自定义字符 默认[a-zA-Z0-9]，或者自定义字符串'adasdaq2r413123'
 * @returns {string}
 */
export function randomString (len, charSet) {
    charSet = charSet || 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
    len = len || 6
    let randomString = ''
    for (let i = 0; i < len; i++) {
        let randomPoz = Math.floor(Math.random() * charSet.length)
        randomString += charSet.substring(randomPoz, randomPoz + 1)
    }
    return randomString
}
/**
 * 格式化数字转汉字
 */
export function numberToChinese (section) {
    let chnNumChar = ['零', '一', '二', '三', '四', '五', '六', '七', '八', '九']
    let chnUnitChar = ['', '十', '百', '千', '万']
    let strIns = ''
    let chnStr = ''
    let unitPos = 0
    let zero = true
    while (section > 0) {
        let v = section % 10
        if (v === 0) {
            if (!zero) {
                zero = true
                chnStr = chnNumChar[v] + chnStr
            }
        } else {
            zero = false
            strIns = chnNumChar[v]
            strIns += chnUnitChar[unitPos]
            chnStr = strIns + chnStr
        }
        unitPos++
        section = Math.floor(section / 10)
    }
    return chnStr
}

/**
 * 数组遍历拼接  删除最后一个拼接符
 */
export function arrayToString (arr) {
    var str = ''
    for (var i = 0; i < arr.length; i++) {
        str += arr[i].name + '、'
    }
    // 去掉最后一个逗号(如果不需要去掉，就不用写)
    if (str.length > 0) {
        str = str.substr(0, str.length - 1)
    }
    return str
}

/**
 * 数字格式化星期几
 * @param {number} status 0同意 1拒绝
 * @return {string}
 */
export function weekDay (num) {
    switch (parseInt(num)) {
        case 1:
            return '星期一'
        case 2:
            return '星期二'
        case 3:
            return '星期三'
        case 4:
            return '星期四'
        case 5:
            return '星期五'
        case 6:
            return '星期六'
        case 7:
            return '星期日'
    }
}

/**
 * 时间戳格式化星期几
 * @param {string} time 时间
 * @return {string}
 */
export function timeToweekDay (time) {
    let weekDay = moment(time).format('dddd')
    switch (weekDay) {
        case 'Monday':
            return '星期一'
        case 'Tuesday':
            return '星期二'
        case 'Wednesday':
            return '星期三'
        case 'Thursday':
            return '星期四'
        case 'Friday':
            return '星期五'
        case 'Saturday':
            return '星期六'
        case 'Sunday':
            return '星期日'
    }
}
```