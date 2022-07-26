# 简略的后台服务，用于测试：

## 入口文件说明   

> appOriginal.js   
>
> - 未做 MVC 分层     

> app.js
- 做了部分的 MVC 分层 
  + router \ controller
    + getOrganizationMockData 返回模拟的列表数据





## 依赖   

- nodemailer      
  + nodemailer是一个node库，提供发邮件的功能，使用nodemailer发送邮件的代码非常简单：

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





## API   

### 一、上传
1、上传文件，用于测试，允许任何格式的文件上传
2、上传成功后，返回上传文件的 url 



### 二、getOrganizationMockData 返回模拟的列表数据
- 需通过 appMVC.js 启动应用
- router -> controller
















