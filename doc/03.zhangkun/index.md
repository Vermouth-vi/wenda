### 

1. 完成注册register登录login和密码找回find三个页面， 主要样式和字体图标使用bootstrap，修改主颜色为绿色。
2. 创建连接数据库接口mongoose，建立用户信息集合模板userModel，问题集合模板queModel，可通过调用database目录下的相应文件使用。

集合名 | 字段名 |类型|是否必填|备注
----|----|-----|--|-----|
登录　|　_id　|obj|是|ID自动生成
用户名　|username |string|是|唯一　手机号
密码|password |string|是|　 
 关注数|follow|numble|否|默认是０
 点赞数|like|numble|否|默认０
 关注人|following|arr|否|默认［］　存用户_id
 提出问题|question|array|否|默认 [] 存问题_id
 回答问题|response|array|否|默认 [] 回答问题_id
 关注的标签名|focus|array|否|默认为空[]
3. 整合前端页面和后端数据库，编写路由index.js实现注册登录找回功能，并更改连接数据库URL为mongodb://wenda:wenda@ds157712.mlab.com:57712/nodedb将数据库连接到mlab远程数据库。
4. 修改三个页面的布局，将登录注册找回的主体设为透明，body颜色设为所需的绿色，
5. 应用jQuery在前端验证注册和找回的两次密码输入的是否一致，如一致，则点击btn可以提交， 如不一致， 则return false， 并提示两次密码输入不一致，并清空两个密码输入框的内容，等待重新输入。
设置用户名，账号输入框的正则匹配内容，保证用户输入的合法性。
```
    $('#btn').click(() => {
      if($('#pwd').val() === $('#pwd1').val()){
        $('#contact_form').submit();
      }else{
        $('#h2').html("两次密码不一致");
        $('#pwd').val('');
        $('#pwd1').val('');
        return false;
      }
    });
```
6. 安装bcrypt， 应用bcrypt.hash将密码加密后再存入数据库， salt设为10。
```
 bcrypt.hash(req.body.password, salt, (err, hash) => {
    var user = new db.user({
      name: req.body.name,
      password: hash
    });
```
在登录验证时读取数据库对应账号的密码，并用bcrypt.compare方法将输入的密码加密后和数据库的密码进行比较， 一致则登录后跳转到主页，否则提示密码错误。
```
   bcrypt.compare(req.body.password, data.password, function(err, hash) {
      if (hash) {
        res.redirect('/');
      } else {
        res.redirect('/login');
      }
    });
```
7. 安装express-session模块，用来存储用户登录后的状态，能够在关掉网页后记录客户状态。首先设置session
```
app.use(session({
	resave: true,
  saveUninitialized: false,
  secret: '3nqr9xzx2438fgsdam4324n',
	cookie:{
		maxAge: 1000*60*1
	}
}));
```
然后将用户名储存在req.session.name中，通过ejs传到页面，显示该用户一登录。增加注销登录按钮， 通过点击按钮清空session中保存的内容。

8. 新建个人信息模板，在用户信息中添加个人信息的的objectId，使两个集合能够联系起来，通过在登录时用session记录用户信息的个人信息ID ，通过ID来查找到该用户的个人信息文档， 通过提交个人信息页面的表单来编辑更新个人信息。

```
  if(hash) {
          req.session.id = doc.geren;
          console.log('--',doc.geren);
          console.log('--------------',req.session.id);
          req.session.user = doc.name;
          req.session.name = doc.nname;
          // console.log(req.session.name);
          console.log('登陆成功');
          // res.redirect('/');
          res.render('index',{title:'问答', name: req.session.id})
        }else{
          res.send('密码不正确');
        }
```
