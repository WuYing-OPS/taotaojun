## 前言

​       本项目是建立我个人学校的基础上开发的，主体是学生，需要学生的学号以及密码进行数字校园登录验证，方可进行更多操作，否则只能查看首页及不需要登录验证的页面。

个人新历路程：

​		开发此项目是本着希望给在校同学使用的，由于考虑到个人财产安全方面的问题，所以没有涉及到微信支付交易这方面（技术也有限哈哈），但也没有使用模拟方式的交易，因为需要他们进行线下交易，就没弄那么繁琐的骚操作。but官方最致命（狗头保命:dog:），个人的小程序不能上线，所以只能体验版啦~

###### 主要涵盖的技术点：

- 前台： uniapp
- 后台：云开发（本项目选用阿里云）
- 模拟登录：nodejs（express服务器、一些插件库cheerio、crypto-js等）
- 组件库：LinUI、colourUI

### 1、进入初始化页面，选择登录

![我的图片](https://github.com/Wuying-OPS/taotaojun/images/1.jpg)

### 2、进入登录页面，输入学号和密码，如果输入错误的操作会进行一系列的错误提示操作（在这就不过多演示了，直接输入正确的），登录成功后会直接跳转首页

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/d5357cd0-4a7a-11eb-a16f-5b3e54966275.jpg" alt="image-20201230152826671" style="zoom:25%;" />

### 3、首页中，可以根据导航栏去找相应的商品系列，在用户发布的每个商品中，只要当前用户点击详情页或者评论，页面的评论数跟浏览量都会实时更新

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/d5e56be0-4a7a-11eb-8ff1-d5dcf8779628.jpg" alt="image-20201230153413882" style="zoom:25%;" />

### 4、点进商品的详情页，分两种情况：未登录和登录验证成功，为登录不能查看发布者的详细信息以及评论、收藏等功能

###### 未登录：

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/d68a8580-4a7a-11eb-b997-9918a5dda011.jpg" style="zoom:25%;" />

###### 登录成功后：

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/d738edf0-4a7a-11eb-8ff1-d5dcf8779628.jpg" alt="image-20201230154808152" style="zoom:25%;" />

### 5、搜索页面中，学生可以根据浏览次数高低、时间、评论量、价格去筛选自己想要的商品

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/d83660c0-4a7a-11eb-8ff1-d5dcf8779628.jpg" alt="image-20201230155108261" style="zoom:25%;" />



### 6、发布页面中，用户需要输入商品标题、类型、售价等基本信息，当发布信息不完整或者其他错误也会有相应的错误提示

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/d92a0f90-4a7a-11eb-8ff1-d5dcf8779628.jpg" alt="image-20201230155350958" style="zoom:25%;" />



### 7、在我的页面中，可以查看我的收藏、我的消息、我的发布、以及其他辅助功能，当有新消息来的时候，也会有相应的提示功能

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/d9f15730-4a7a-11eb-8a36-ebb87efcf8c0.jpg" alt="image-20201230155702589" style="zoom:25%;" />

### 8、收藏页面，可取消收藏

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/dad52780-4a7a-11eb-8a36-ebb87efcf8c0.jpg" alt="image-20201230155842055" style="zoom:25%;" />

### 9、消息页面

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/db906130-4a7a-11eb-a16f-5b3e54966275.jpg" alt="image-20201230155924469" style="zoom:25%;" />

10、我的发布页面，可以进行下架商品，这样用户就搜索不到了，可以修改商品的基本信息，以及删除商品

<img src="https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-5ssicdcixptx16f9eb/dc29bb00-4a7a-11eb-a16f-5b3e54966275.jpg" alt="image-20201230160130493" style="zoom:25%;" />

##### 以上就是主要的功能实现了哈哈，最后的小tips：开源不易，觉得对您有帮助的，请给一个star，谢谢！

###### 





