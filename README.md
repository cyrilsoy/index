[![Netlify Status](https://api.netlify.com/api/v1/badges/e2e06829-5e87-4ba1-b8e8-7f837d2a9716/deploy-status)](https://app.netlify.com/sites/about-index/deploys)

# NutssssIndex
简约个人主页、自适应  
**[点我预览](https://n0ts.cn)**
![alt 预览图](https://images.gitee.com/uploads/images/2020/0525/000514_3cb0b6fa_2250179.png)

## 简介
两天时间写了一个练手作品  
不太想用别人的作品当做自己的个人主页了，心血来潮自己写了一个  
技术不是很好，但是还是写的很认真  
如有问题或建议欢迎提出  
自适应、简约、动画、无js  

# 两个终端栏源码

```yml
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport"
        content="width=device-width,initial-scale=1.0,maximum-scale=1.0,minimum-scale=1.0,user-scalable=no">
    <title>Cyril's Blog</title>
    <meta name="description"
        content="Cyril's Blog">
    <meta name="keywords" content="Cyril's Blog">
    <link rel="stylesheet" type="text/css" href="./css/FiraCode.css">
    <link rel="stylesheet" type="text/css" href="./css/nutssss.css">
    <link rel="icon" href="./favicon.ico">
</head>

<body>
    <div id="box">
        <!-- 个人资料卡片 -->
        <div class="meBox">
            <!-- 头像 -->
            <div class="headPhoto"></div>
            <!-- 介绍 -->
            <div class="meBox-title">
                <p>I'm N0ts</p>
                <div class="fg"></div>
            </div>
            <div class="meBox-text">
                <p>一条咸鱼<img src="./img/fish.png" alt="咸鱼" style="width: 20px; vertical-align: middle;"></p>
                <p>瞎折腾浪费时间最在行</p>
                <p>最爱<img src="./img/002.png" alt="冰激凌" style="width: 15px; vertical-align: middle;"></p>
            </div>
            <!-- 两个按钮 -->
            <div class="meBox-Button">
                <a href="https://lia.im/about.html">关于</a>
                <a href="https://github.com/cyrilsoy">Github</a>
            </div>
        </div>

        <!-- 伪终端介绍 -->
        <div id="cmdBox">
            <!-- 第一个终端 -->
            <div class="cmd">
                <!-- 三个按钮 -->
                <div class="click">
                    <div class="red"></div>
                    <div class="yellow"></div>
                    <div class="green"></div>
                </div>
                <!-- 顶部标题 -->
                <div class="title">
                    <span>lovexhj - bash</span>
                </div>
                <!-- 终端内文字 -->
                <div class="cmdText">
                    <span style="color: rgb(0, 190, 0);">root@CY</span>
                    <span style="color: blue;">~</span>
                    <span style="color: rgb(39, 39, 39);">./tianqi.sh</span>
                    <br />
                    <iframe scrolling="no" src="https://tianqiapi.com/api.php?style=tb&skin=pitaya" frameborder="0"
                        width="350" height="24" allowtransparency="true"></iframe>
                    <br />
                    <span style="color: rgb(0, 190, 0);">root@CY</span>
                    <span style="color: blue;">~</span>
                    <span style="color: rgb(39, 39, 39);">cat /love.txt</span>
					<p>有人說，</p>
					<p>愛上一座城，</p>
					<p>是因為城中住著某個喜歡的人。</p>
					<p>其實不然，</p>
					<p>愛上一座城，</p>
					<p>也許是為城裡的一道生動風景，</p>
					<p>為一段青梅往事，</p>
					<p>為一座熟悉老宅。</p>
					<p>或許，</p>
					<p>僅僅為的只是這座城。</p>
					<p>就像愛上一個人，</p>
					<p>有時候不需要任何理由，</p>
					<p>沒有前因，</p>
					<p>無關風月，</p>
					<p>只是愛了。</p>
                    <span style="color: rgb(0, 190, 0);">root@CY</span>
                    <span style="color: blue;">~</span>
                    <span style="color: rgb(39, 39, 39);">sudo rm -rf /过去的自己/*</span>
                </div>
            </div>
            <!-- 第二个终端 -->
            <div class="cmd cmd2">
                <!-- 三个按钮 -->
                <div class="click">
                    <div class="red"></div>
                    <div class="yellow"></div>
                    <div class="green"></div>
                </div>
                <!-- 顶部标题 -->
                <div class="title">
                    <span>lovexhj - bash</span>
                </div>
                <!-- 终端内文字 -->
                <div class="cmdText">
                    <span style="color: rgb(0, 190, 0);">root@CY</span>
                    <span style="color: blue;">~</span>
                    <span style="color: rgb(39, 39, 39);">./contact.sh</span>
                    <p>我的联系方式：</p>
                    <ul class="ul">
                        <li><a href="https://lia.im">Blog</a></li>
                        <li><a href="https://github.com/cyrilsoy">Github</a></li>
                        <li><a href="https://instagram.com/cyrilsoy">instagram</a></li>
                    </ul>
                    <span style="color: rgb(0, 190, 0);">root@CY</span>
                    <span style="color: blue;">~</span>
                </div>
            </div>
        </div>
    </div>

    <!-- 页脚 -->
    <div id="footer">
        <p>© 2020 NUTSSSS | <a href="http://beian.miit.gov.cn">湘ICP备18015792号</a></p>
        <p>THEME MADE BY <a href="https://nutssss.cn/">Nutssss</a></p>
    </div>
</body>

</html>
```
