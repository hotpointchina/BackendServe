# 简略的后台服务，用于测试：

## 入口文件说明   

> appOriginal.js   
 - 未做 MVC 分层     

> app.js
- 做了部分的 MVC 分层 
  + router \ controller
    + getOrganizationMockData 返回模拟的列表数据





## 依赖   


> koa-cors   
- cors针对简单请求、预检请求分别处理不同的CORS头   

```js
// cors针对简单请求、预检请求分别处理不同的CORS头。
const cors = require('koa-cors');
```


> nodemailer      
- nodemailer是一个node库，提供发邮件的功能，使用nodemailer发送邮件的代码非常简单：

```js
import nodemailer from 'nodemailer';

const transporter = nodemailer.createTransport({
    service: '163',  //  邮箱
    secure: true,    //  安全的发送模式
    auth:{
        user: 'lingtiku@163.com', //  发件人邮箱
        pass: 'XXXXXXXXXXX' //  授权码
    }
});

transporter.sendMail(
    {
        // 发件人邮箱
        from: 'lingtiku@163.com',
        // 邮件标题
        subject: '灵题库 验证码',
        // 目标邮箱
        to: 'target@xxx.com',
        // 邮件内容
        text: `您的验证码是：1234，如非本人操作，请忽略`
    },
    (err, data) => {
        if (err) {
            console.error(err);
        }
        else{
            console.log(data);
        }
    }
);
```


> mysql2/promise
- 以返回 promise 的形式操作 MySQL 数据库   

```js
db = await mysql.createConnection({
    // 数据库地址，端口默认是 3306 ，（如果在安装数据库的时候，没有修改的话）
    host: 'localhost',
    // 数据库帐号
    user: 'root',
    // 数据库密码
    password: 'root',
    // 要访问的是 MySQL 中的那个 [数据库]
    database: 'backend_serve'
});
```







## API   

### 一、上传      
1、上传文件，用于测试，允许任何格式的文件上传
2、上传成功后，返回上传文件的 url 



### 二、 返回模拟的列表数据    
- 需通过 appMVC.js 启动应用
- router -> controller

```js
const getOrganizationMockData = require('../controller/getOrganizationMockData');
router.post("/getOrganizationMockData", getOrganizationMockData.post );

// parms 中的 requestCode 需要等于 100，才会返回 mockData
router.post("/getMockDataList", getOrganizationMockData.list );
```


### 三、MySQL 数据库         
> 本案例默认的数据库建设
- 必须拥有一个名为 `backend_serve` 的数据库，并在该数据库中建立一个 `future_mail_send` 的表(Table)
    + 当然可以根据业务需求进行修改    
    + 默认 `future_mail_send` 表中需要有一条数据： 
        + 用于登录功能   


```js
{
    id: 1,
    name: 'Azir',
    password: 'Azir-707@bj',
    source: 'web',  // 这项可以没有
    email: '',      // 这项可以没有
    openid: ''      // 这项可以没有
}
```

> 如果没有安装 MySQL 也可以运行项目，只是 登录、发邮件的接口不能提供服务 



### 四、登录    
- MySQL 数据库   

> `/login` 
- 从 MySQL 本地数据库中查找， `用户名+密码` 
- 匹配成功后，通过 response.body 返回 token
- 前端请求某些需鉴权的 API 时，需要在接收到的 `token` 前手动拼接： 'bearer ' 


### 五、查询 本地 MySQL 数据库里 的 对应的 用户     
- MySQL 数据库      

> `/getUsers`   
- 需通过鉴权


### 六、 发送定时邮件      
> 依赖   
- 需要先登录，通过鉴权获取到 token 后



```js
// 发送邮件 -> 163.com
// 与发邮件相关的 API
mailRouter.launch( router );
```

> 发送定时邮件 功能的依赖    
- MySQL 数据库





### 七、 日志   
- 功能模块路径为： `util/dailyLogHelper.js`    
    + 入口函数 `dailyLogLaunch`     
- `config.js` 文件中设置 `isOpenDailyLog` 的属性为 `true` 时，日志功能 **开启**    
- 日志为开启状态，启动项目后，会先查看项目文件夹内是否有 `DailyLog` 目录/文件夹，和当日日期（年月日）的 `txt` 文件   
    + `没有` 则会自动创建
    + `有` 在每日 23:00 - 00:00 内，会将当日的日志存储到对应日期的 `txt` 文件内   



### 八、抓取 / 爬虫    

> 技术栈   
- iconv-lite 是编码间互转库，可以将 gbk 编码 Buffer 转换为 utf8，解决中文乱码的问题。


> 接口    
- 【POST】 `/getWebData`    
- 参数     

> 协议类型支持
- http & https

> 目标网站编码集  
- 可以解决因目标网站因使用的编码集不是 **UTF-8** 而导致的乱码问题
- 如果目标网站的编码集不是 `UTF-8`，则需要传入 `charsetType` [key]，并将对应的编码集类型存入进去     


```js
data:{ 
    // 抓取网页的 url
    url: string,  
    // 抓取网页的 编码集，默认是 UTF-8，如果目标网站使用的是其他编码集，则需要对应传入编码集名称，否则会导致抓取到的内容为乱码
    charsetType: '',
    // 抓取内容的 主盒子 的层级路径
    box: string, 
    // 抓取内容的 标题 的层级路径（不含主盒子路径，从主盒子内开始写，即可）
    title: string,
    // 抓取内容的 图片 的层级【主】路径（不含主盒子路径，从主盒子内开始写，即可）
    img: string,
    /*
      抓取内容的 图片 的【子级】层级路径（不含主盒子路径，从主盒子内开始写，即可）
      每一个层级，依次存入到 数组中即可
    */ 
    imgChildren： string[]
    // 如果抓取不到 img 的 src，需要获取 img 上面的其他属性，可添加 imgArrtibute 字段
    imgArrtibute: string
    // 查询内容的 关键字
    queryWord: string
}
// 层级路径规则遵循 document.querySelect()
```







