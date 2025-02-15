# 班表小幫手

驗證班表是否合法或過勞

`npm i tw-shift-schedule`

## Usage

```javascript
const shift = require('tw-shift-schedule')

// 台鐵站長張銘元班表 from https://www.twreporter.org/a/death-of-taiwan-rail-train-conductor
let schedule = shift.Schedule.fromTime([
  ['2017-12-01 09:36:00', '2017-12-01 19:44:00'],
  ['2017-12-02 05:30:00', '2017-12-02 10:14:00'],
  ['2017-12-04 16:16:00', '2017-12-04 21:04:00'],
  ['2017-12-05 07:36:00', '2017-12-05 13:15:00'],
  ['2017-12-07 10:37:00', '2017-12-07 16:11:00'],
  ['2017-12-08 13:50:00', '2017-12-08 23:58:00'],
  ['2017-12-09 08:18:00', '2017-12-09 09:19:00'],
  ['2017-12-11 10:00:00', '2017-12-11 18:24:00'],
  ['2017-12-12 18:27:00', '2017-12-12 22:16:00'],
  ['2017-12-13 07:02:00', '2017-12-13 11:20:00'],
  ['2017-12-14 12:29:00', '2017-12-14 22:58:00'],
  ['2017-12-15 05:25:00', '2017-12-15 08:17:00'],
  ['2017-12-17 15:40:00', '2017-12-18 00:26:00'],
  ['2017-12-18 07:35:00', '2017-12-18 10:09:00'],
  ['2017-12-19 16:16:00', '2017-12-19 21:04:00'],
  ['2017-12-20 07:40:00', '2017-12-20 13:15:00'],
  ['2017-12-21 13:10:00', '2017-12-21 23:23:00'],
  ['2017-12-22 09:00:00', '2017-12-22 09:34:00']
], moment.ISO_8601)

let tokens = shift.tokenizer(schedule)
console.log(tokens) // 切出的 上班/下班區段，若有不合法會回傳 invalid
```

`test` 資料夾下有其他範例

## API

```javascript
const shift = require('shift')
```

#### `let schedule = shift.Schedule.fromTime(times, [opts])`

 從給定的時間 `times` 建立一個班表資料。

 ```
 * times: 二維陣列，每個子元素為「上班時間」與「下班時間」的 pair。如：`[['2018-01-01 08:00:00', '2018-01-01 18:00:00']]`
 * opts:
   * format: 時間的格式，預設為 ISO 8601。格式參考 https://momentjs.com/docs/#/parsing/string/
   * before: 隱藏工時-前。ex. '30 minutes'
   * after: 隱藏工時-後。ex. '30 minutes'
 ```

#### `let schedule = shift.Schedule.fromData(data)`

 從字串資料建立 schedule 物件

#### `let data = schedule.toString()`

將 schedule 物件轉為字串

#### `let tokens = shift.tokenizer(schedule, continueWhenError)`

解析班表，試著切出合法的工作/休息時段。如果嘗試失敗，會回傳 'invalid' 並且指出錯誤的位置。

```
* schedule: shift.schedule 建立的班表資料。
* continueWhenError: 遇到違法時，跳過該區段繼續往下解析
```

#### `let causes = shift.overwork.check(schedule)`

檢查班表是否符合過勞因素，回傳符合的因素，若是沒有符合的則回傳空陣列。


#### `shift.overwork.Causes`

過勞因素：
```javascript
const Causes = {
  irregular: '不規律的工作',
  longHours: '長時間工作',
  nightShift: '夜班',
  previousDayOverwork: '前一天長時間工作',
  previousWeekOverwork: '前一週長時間工作',
  previousMonthOvertime: '前一個月加班時數 > 100',
  previousSixMonthsOvertime: '前六個月加班時數平均 > 45'
}
```

## Design

此套件將班表編碼成如下格式：

```
xxxxxxxxxx xxxxxxxxx .....xxxxx .....
```

* `x` 代表一分鐘的上班時間
* `.` 代表一分鐘的休息時間

於是便可用 `tokenizer/lexer` 驗證基本的班表正確性，可參考 `tokenizer.js`。

編碼中的空白會被忽略，可用 `#` 寫註解。

## License

The MIT License
