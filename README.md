# todo-sequelize
alphacamp todo-list 資料庫改用 mysql 

## sequelize 筆記
1. 先行在 mysql workbench (要先裝好 mysql 和 mysql workbench) 弄好資料庫
```
create database todo_sequelize;
use todo_sequelize;
```

2. 在 project root path 安裝相關套件，我自己的是 `~/code/alphacamp/todo-sequelize`
```
lmarcus@LAPTOP-E7HL0C16:~/code/alphacamp/todo-sequelize 
$ npm install mysql2@2.1.0 sequelize@5.21.13 sequelize-cli@5.5.1
```

3. sequelize 初始化，使用 `npx` 的原因則是剛剛在本地的路徑安裝，若是要直接使用的話，第二步安裝需要多一個 `-g` flag 
```
$ npx sequelize init
# 若是不想用 npx 的場合
$ npm install -g mysql2@2.1.0 sequelize@5.21.13 sequelize-cli@5.5.1
```

4. sequelize 初始化
```
$ npx sequelize init
```
會產生這些檔案
- config/config.json：資料庫設定檔，已經自動帶入內容
- models/index.js：model 的設定檔，這包設定檔完全不需要修改
- migrations：資料庫設定檔的存放位置，目前是一個空資料夾
- seeders：種子資料設定檔的存放位置，目前是一個空資料夾

目前先將 `password` 填上密碼，然後 `database` 填上 "todo_sequelize"，把` operatorsAlias` 刪掉

5. model 製造
 ```
 $ npx sequelize model:generate --name Todo --attributes name:string,isDone:boolean
 ```
 產生檔案
- models/todo.js：model 設定檔
- migrations/XXXXXXXXXXXXXX-create-todo.js：資料庫遷徙紀錄，檔名前綴為時間戳記

其中在 `migration/*-create-todo.js` 根據之前的 schema 設計加入

- 在 name 欄位加上必填設定 `allowNull: false`
- 在 isDone 設定預設值 `defaultValue: false`

6. 進行 database migration

```
$ npx sequalize db:migration
```

`User` 也照同一個步驟做，就可以 query 了。

7. 新增欄位與建立關聯
`Users.id` 對 `Todo.UserId` 是一對多

新增 migration
```
npx sequelize migration:generate --name add-userId-to-todo
```

程式碼可以看 `migrations/20220417112442-add-userId-to-todo.js`

接著
```
npx sequelize db:migrate
```
就會多一個欄位


建立關聯可以看 models/user.js 和 models/todo.js

註測功能看 app.js

8. 種子資料
打種子資料進去
```
npx sequelize db:seed:all
```
刪除種子資料
```
npx sequelize db:seed:undo:all
```
query 資料庫檢證

9. 做首頁
主要看 `app.js` 的 `app.get('/')` 這個路由 query 作法
sequalize 的對 query 的 finder [建議](https://sequelize.org/docs/v6/core-concepts/model-querying-finders/)
