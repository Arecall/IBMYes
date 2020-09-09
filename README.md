### 如何使用IBM免费cloud搭建v2ray

- ##### 打开IBM云，注册一个账号 https://cloud.ibm.com/registration

  ![image-20200908195433301](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908195433301.png)

- 账号注册成功后，登陆账号进入IBM Cloud首页，在仪表盘里找到Cloud Foundry---公共。

  ![image-20200908195837656](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908195837656.png)

  

  在进入公共之后，创建一个云服务器。

  ![image-20200908200106398](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908200106398.png)

  选择区域，选择达拉斯（只有达拉斯免费，切勿选错），

  ![image-20200908200510802](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908200510802.png)

  

  轻量选择256MB，配置资源选择 .GO

  ![image-20200908200547850](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908200547850.png)

  应用程序名称填写的时候需要记住，切换乱写，后面会用到。主机名和程序名一致，域选择默认就行。

  ![image-20200908200835814](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908200835814.png)

  确认以上没有问题后，点击右面的创建即可。

- 创建完成后，在资源列表使用IBM shell工具连接

  ![image-20200908201148623](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908201148623.png)

- 安装v2ray

  - 先下载相关源码至服务器

    `wget --no-check-certificate -O install.sh https://raw.githubusercontent.com/Arecall/IBMYes/master/install.sh`

  - 使用vim 修改install.sh文件

    `vi install.sh`

    `echo "N"|ibmcloud cf install` ‘’N‘’改为y，保存。

    ![image-20200908201953748](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908201953748.png)

  - 对修改万的文件进行编译安装

    `chmod +x install.sh  && ./install.sh`

    应用名称写你在IBM云创建时的应用名称，内存写256

    ![image-20200908202820030](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908202820030.png)

    出现以下界面，则表示你的v2ray安装成功，routes是你的服务器地址

    ![image-20200908203316672](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908203316672.png)

    将vmess链接负责到客户端，确认url导入后，手动将地址换成routes后的地址

    ![image-20200908203626949](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908203626949.png)

    地址更换后，清空path后，即可访问谷歌测试。

    ![image-20200908203810235](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908203810235.png)



### 利用CDN对IBM Cloud进行加速

- 注册cloudflare

  https://cloudflare.com

- 创建Worker

  ![image-20200908204237891](/Users/zhongyazhou/Library/Application Support/typora-user-images/image-20200908204237891.png)

  打开和复制脚本

  ```
  addEventListener(
  "fetch",event => {
  let url=new URL(event.request.url);
  url.hostname="ibmyes.us-south.cf.appdomain.cloud";
  let request=new Request(url,event.request);
  event. respondWith(
  fetch(request)
  )
  }
  )
  ```

  修改第四行为你的应用的域名

  [![image-20200615214339159](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615214339159.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615214339159.png)

  点击发送，测试是否出现`Bad Request`，出现则成功，点击保存并部署。

  [![image-20200615214543839](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615214543839.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615214543839.png)

  [![image-20200615214722195](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615214722195.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615214722195.png)

  这里会给一个网址，*.*.workers.dev,这就是你的cloudflare中转后的域名。

  然后我们去v2的客户端中修改地址

  [![image-20200615215120033](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615215120033.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615215120033.png)

  现在已经使用了cloudflare的代理。



### 利用Github的Actions 每周重启 IBM Cloud Fonudray

IBM Cloud 10天不操作就会关机，所以我们需要 十天内对其重启一次，避免关机。

首先登录IBM Cloud

点击又上角的命令行

在这一步我们主要是记录4个值

```
IBM_ACCOUNT // IBM Cloud的登录邮箱和密码
IBM_APP_NAME // 应用的名称
REGION_NUM // 区域编码
RESOURSE_ID // 资源组ID
```

具体后面会一步一步完成

[![image-20200615175949804](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615175949804.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615175949804.png)

进入命令行先执行

```
ibmcloud login
```

输入邮箱和密码。

之后记录下区域(Region)

[![image-20200615180817619](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615180817619.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615180817619.png)

```
#1. au-syd
#2. in-che
#3. jp-tok
#4. kr-seo
#5. eu-de
#6. eu-gb
#7. us-south
#8. us-east
```

这里需要记下和区域对应的编号也就是`REGION_NUM`，比如我这里是us-south,那么我的区域编号是`7`

接下来获取资源组id`RESOURSE_ID`

```
ibmcloud resource groups
```

[![image-20200615183425453](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615183425453.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615183425453.png)

图中所指向便是`RESOURSE_ID`

现在返回github，到本项目

```
https://github.com/CCChieh/IBMYes
```

[![image-20200615184239713](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615184239713.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615184239713.png)

右上角fork到自己的github下，然后进入setting

[![image-20200615184327329](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615184327329.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615184327329.png)

选择Secrets

[![image-20200615184426979](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615184426979.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615184426979.png)

New secret

分别建立四个secret

```
IBM_ACCOUNT // IBM Cloud的登录邮箱和密码
IBM_APP_NAME // 应用的名称
REGION_NUM // 区域编码
RESOURSE_ID // 资源组ID
```

以`IBM_ACCOUNT`为例[![image-20200615184703280](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615184703280.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615184703280.png)

第一行为邮箱，第二行为密码。

这里需要邮箱和密码所以中间换行 ，其他的不需要换行 。

把四个secret补充完成

[![image-20200615185015130](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615185015130.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615185015130.png)

之后点击上方Actions，在这里你就会看到有个IBM Cloud Auto Restart在执行。

[![image-20200615185614978](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615185614978.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615185614978.png)

如果没有看见Action的话到自己仓库的`/.github/workflows/ibm.yml`

[![image-20200615235426917](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615235426917.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615235426917.png)

编辑下，随意增添个空行然后commit下

[![image-20200615235540567](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615235540567.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615235540567.png)

就可以看见有action了

第一次可能因为secret没添加导致workflow执行失败，只需要点下

[![image-20200615191100959](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615191100959.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615191100959.png)

进去后按照下图

[![image-20200615191035212](https://github.com/CCChieh/IBMYes/raw/master/img/README/image-20200615191035212.png)](https://github.com/CCChieh/IBMYes/blob/master/img/README/image-20200615191035212.png)

找到 `Re-run jobs`重新执行一次即可，至此自动重启已经ok了。

