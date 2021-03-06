## candyjs-db-mysql2

database interface

## 说明

依赖 candyjs 4.7.0+

此库是关系型数据库的接口层，设计该接口的目的是为了统一关系型数据库接口，使得切换数据库变得简单

目前采用一主多从架构设计

## 预想的使用方法如下 可能会变更

大致是所有查询使用 promise 返回，所有中间查询方法都是同步的

```javascript
// entry file
var CandyJs = require('candyjs');
var App = require('candyjs/web/Application');
var Db = require('@candyjs/db-mysql2');

new CandyJs(new App({
    'id': 1,
    'appPath': __dirname + '/app',
    'db': new Db({
        main: {
            host: 'localhost',
            port: 3306,
            database: 'mydb',
            user: '',
            password: ''
        },
        slaves: [
            {
                host: 'localhost',
                port: 3306,
                database: 'mydb_slave1',
                user: '',
                password: ''
            }, {
                host: 'localhost',
                port: 3306,
                database: 'mydb_slave2',
                user: '',
                password: ''
            }
        ]
    })
})).listen(2333, function(){
    console.log('listen on 2333');
});


// in some business file
const Db = require('@candyjs/db-mysql2');

let command = Candy.app.db.getMain();
// let command = Candy.app.db.getSlave();

let data1 = await command.prepareSql('select * from user where id=1').queryAll();
let data2 = await command.prepareStatement('select * from user where id=?')
    .bindValues([1]).queryOne();

let rs1 = await command.prepareSql('update user set age=20 where id=1').execute();
let rs2 = await command.prepareStatement('delete from user where id=?').bindValues([1]).execute();

// use Query builder
let q = new Db.Query(command);
let data3 = await q.select('*').from('user').where('id=1').getOne();
let data4 = await q.select('*').from('user').where('id=?', [2]).getOne();
```
