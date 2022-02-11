# TN_Detection-
甲状腺结节医学辅助诊断平台_V1


本项目是一个基于深度学习的甲状腺结节超声图像辅助诊断系统，系统的应用领域是医疗行业。
甲状腺超声图像结节检测与识别是系统开发的核心功能，系统根据用户上传的甲状腺超声图像进行检测与识别，并将相关的检测与识别结果以图像和文字的形式在界面上进行展示。
本系统面向的客户群体范围很广，不仅包括经验丰富的医师，还包括经验匮乏或者相关医学知识薄弱的医务人员或者普通的人民群众。故本系统必须具有的功能如下：
（1）用户管理功能：不同的用户因为身份不同进入不同的界面进行各自权限范围内相应的操作。
（2）图像上传：用户根据自己的需求，将需要上传的甲状腺超声图像上传到服务器，并将上传图像的相关信息保存在数据库中，方便用户的后续操作。
（3）图像列表展示：将用户上传的图像相关信息以列表的形式更加直观地展现在客户端。
（4）图像检测与识别：用户根据实际需求，检测与识别甲状腺超声图像中的结节。
（5）图像查询：用户输入关键字查询对应的图像信息，以列表的形式展示查询结果，实现模糊査询。
（6）图像删除：用户根据需求，将上传的错误图像或者其余冗余图像进行删除，减轻服务器的压力。
近年来，全球范围内甲状腺癌的发病率持续增长，且目前甲状腺癌的致病因素尚不完全清楚，只有尽早地发现、确诊，才能提高甲状腺癌的治愈率。而传统的检测与识別方法要么对人体组织具有一定的损伤性，要么就是识别率和检准率不髙，并不能更好地协助医师做出准确的判断。
我们所提出的模型可以在保证高精度定位的同时，能够准确识别甲状腺超声图像的良恶性结节。基于该模型，设计开发的甲状腺超声图像良恶性结节检测与识别平台，可以协助医师做出准确的诊断，提高甲状腺癌的识别率和检准率，从而进一步提高甲状腺癌的治愈率，是我们的最终目标。
甲状腺结节是一种常见临床疾病。随着高频探头技术的发展，超声技术已成为诊断甲状腺良恶性结节最重要的方法。但是超声图像在生成的过程中，容易受到周围环境以及噪声的干扰，导致采集到的超声图像成像质量不高，存在清晰度差、分辨率低的特点。而且误诊与漏诊的概率相对来说也比较高。
因此，随着科学技术的快速发展，传统的超声技术已经跟不上时代的发展。利用先进的人工智能方法和技术辅助医师进行甲状腺结节良恶性诊断已是大势所趋。首先，采集大量患者的医学图像信息。之后，借助计算机对这些图像进行检测与识别。最后，医师根据计算机检测与识别的结果，给出一个更加精准的诊断结果。因此，研发一套能辅助医师进行甲状腺结节的检测与良恶性诊断系统具有重要应用价值。

*************************************************************************************************************************************************************

# 甲状腺项目部署/维护文档

## 技术简介

## 部署

项目前后端分离，需要分别启动后端服务和前端服务

**后端前期准备**

``` shell
pip install -r requirements
python manage.py makemigrations  # 生成迁移信息
python manage.py migrate				 # 初始化数据库
python manage.py createsuperuser # 创建超级管理员
python manage.py runserver       # 启动测试服务器
```

服务器启动后访问http://127.0.0.1:8000/admin/login/

![image-20210605142133595](/Users/lvtiancheng/Library/Application Support/typora-user-images/image-20210605142133595.png)

在此页面输入创建超级管理员时输入的账号密码

![image-20210605142202344](/Users/lvtiancheng/Library/Application Support/typora-user-images/image-20210605142202344.png)

看到这个页面代表后端首次安装完成

**后端部署启动**

由于本系统需要使用消息队列，正式启动时需要额外启动redis和celery服务。

安装redis参考：https://www.runoob.com/redis/redis-install.html

正式启动用到的命令记录在下

```bash
redis-server # 启动redis服务
Celery -A thyroid worker -l info
```

此两步完成之后，消息队列部分的功能已经可用

此时可以通过`python manage.py runserver`启动测试服务器.

此外，本项目还含有消息队列监控模块flower，可通过下列命令启动

```sh
flower -A thyroid --port=5555
```

启动后访问http://localhost:5555/ 即可访问监控页

**前端前期准备**

```bash
npm install
```

**前端测试部署**

```bash
npm run serve
```

**前端正式部署**

```shell
npm run build
```

打包完成后，在dist文件夹内构建资源服务器即可，首页指定为index.html

## 维护

### 后端

后端采用的技术栈：

Django(https://docs.djangoproject.com/zh-hans/3.1/)： 后端框架

Django REST framework(https://www.django-rest-framework.org/): Django的restful api插件

SimpleUI(https://simpleui.72wo.com/docs/simpleui/doc.html)：Django后台主题

Mask_RCNN(https://github.com/matterport/Mask_RCNN)：CV神经网络驱动

**后端文件结构**

```
├── apps     						// 项目应用
│   ├── authentication  // 用户认证相关
│   ├── cases           // 病例管理相关
│   ├── drfplus         // 自开发drf插件补丁
│   └── rcnndriver      // 图像识别业务相关
├── db.sqlite3					// 数据库
├── manage.py						// Django入口文件
├── media								// 用户上传的静态资源
│   ├── mask						// 遮罩层图像
│   ├── origin					// 原始图像
│   ├── result					// 识别结果（原始图像+遮罩）
│   ├── section					// 病灶切图图像
│   └── temp						// 临时文件夹
├── readme.md						// 说明文档
├── requirements				// 依赖文档
├── start.sh						// 部署脚本
├── startCelery.sh			// celery启动脚本
├── startFlower.sh			// flower启动脚本
├── thyroid							// 项目配置
│   ├── __init__.py
│   ├── asgi.py					// 网关入口
│   ├── celery.py				// celery配置
│   ├── gunicorn.py			// gunicorn配置
│   ├── settings.py			// 项目配置
│   ├── urls.py					// 入口url参数
│   └── wsgi.py					// 网关入口
├── utils								// 工具类
    ├── Mask_RCNN				// Mask_RCNN
    └── coco						

```

#### 核心业务梳理

**1.图像识别（后端流程）**

本系统的核心业务流程为与图像识别相关的一系列逻辑。下面展示全部业务流程

首先，前端会将图片通过`api/rcnn/uploadimg/`这一api将图片传入，该api对应`upload_image`这一函数。函数内容如下。

在获取到图片后，会为每张图片创造名为`ThyroidLog`的django model对象，同时将当前对象的主键写入返回值供前端使用。

```python
 # thyroid/apps/rcnndriver/views.py
 
 @action(methods=['post'], url_path='uploadimg', detail=False)
    def upload_image(self, request):
        """
        上传原始图片同时创建对象
        前端会上传若干图片。后端获取全部图片并为每一张图片创建thyroidlog对象
        后端返回创建好的对象主键和相关字段
        """
        # 获取当前主机media的url
        # TODO 换用https以后这里得修改
        host = "http://" + request.META['HTTP_HOST'] + '/media/'
        # 获取上传文件。同时会上传若干个图片。分别为这些图片创建log
        imgfile = request.FILES.getlist('files[]')
        # 初始化返回值
        data = []
        for img in imgfile:
            q = ThyroidLog.objects.create(thy_original=img)
            # 为返回值内增加信息
            data.append({
                # 主键
                "id": q.id,
                # 原始图片字段
                "originImg": host + str(q.thy_original),
                # 处理结果
                "resultImg": None
            })
        return Response(data=data, status=status.HTTP_201_CREATED, content_type='application/json')
```

前端获取到图片上传完成的结果后，会开始对`api/rcnn/drivercnn`这一API的AJAX轮询，该api对应`drive_rcnn`这一函数，函数具体内容见注释说明。

```python
 # thyroid/apps/rcnndriver/views.py
@action(methods=['get'], url_path='drivercnn', detail=True)
    def drive_rcnn(self, request, pk):
        """
        RCNN驱动函数。
        前端携带主键ajax轮询访问此函数。当任务处理完成时，celery_task(见task.py)逻辑会将模型的thy_state字段置为100
        作为处理完成的标志
        """
        host = "http://" + request.META['HTTP_HOST'] + '/media/'
        q = ThyroidLog.objects.get(id=pk)

        # 判断当前任务是否已经有worker在执行
        if q.thy_computed == False:
            # 若未执行则执行
            q.thy_computed = True
            q.save()
            # 启动task
            res = drive_rcnn.delay(str(q.thy_original), pk)
        # 初始化返回值
        data = {}
        # 若处理未完成，resultImg返回空值
        if q.thy_state != 100:
            data = {
                'resultImg': "",
                'state': q.thy_state
            }
        # 若处理完成
        elif q.thy_state == 100:
            # 自模型的data字段内获取json格式的处理结果
            qdata = json.loads(q.thy_data)
            # 获取全部标记section
            masks = qdata['masks']
            # 将section内的数据加上host，方便前端展示
            for mask in masks:
                section = host + str(mask['section'])
                mask['section'] = section
            qdata['masks'] = masks

            # 更新data
            # q.thy_data = json.dumps(qdata)
            # q.save()
            # 返回
            date = datetime.datetime.today().strftime("%Y-%m-%d")
            data = {
                'resultImg': host + str(q.thy_result),
                'maskImg': host + str(q.thy_mask),
                'originImg': host + str(q.thy_original),
                'state': q.thy_state,
                'date': date,
                'data': qdata
            }
        return Response(data=data, status=status.HTTP_200_OK, content_type='application/json') 
```

当异步任务完成后，后端会将处理完成后的图片信息供前端展示。

**2.图像识别（异步任务）**

有关图像识别的异步任务被封装在`thyroid/apps/rcnndriver/tasks.py`内，文件内容如下

通过看源码可以看到，该任务将用户上传的图像的本地路径和相关设置参数传入了`RCNN_api`这一API。`RCNN_api`是在Mask_RCNN这一库的基础上封装实现。

```python
from celery import shared_task
from utils.Mask_RCNN.api import RCNN_api

from apps.rcnndriver.models import ThyroidLog
import json

@shared_task
def drive_rcnn(originImg, pk):
    """
    celery异步任务
    """
    q = ThyroidLog.objects.get(id=pk)
    # 获取原始图片，进度设为30
    originImg = q.thy_original
    q.thy_state = 30
    # 保存以供异步读取
    q.save()
    # 获取图片路径和图片名
    path = str(originImg).split('/')[0]
    name = str(originImg).split('/')[1]
    # 调用rcnn_api 返回json格式处理结果
    rcnn = RCNN_api(path, name, 'result','mask')
    data = rcnn.make_image()

    # 获取处理结果路径
    result_url = json.loads(data)['result_url']
    # 获取标记对象路径
    mask_url = json.loads(data)['mask_url']
    # 进度设为100，更新数据
    q.thy_state = 100
    q.thy_result = result_url
    q.thy_mask = mask_url
    q.thy_data = data
    q.save()
    return data

```

`RCNN_api`位于`thyroid/utils/Mask_RCNN/api.py` ，有需要更改的地方直接在此处更改即可。

**注意**：有关task的修改（包括对MaskRCNN的修改）不会立刻生效，需要重启Celery服务后方可生效。

**3.API构建相关**

本项目内大部分API采用Django REST framework（下称DRF）构建。基础部分参考相关文档即可。这里只对项目内进行自定义开发的部分进行说明。

本项目对DRF进行了部分二次开发。主要集中于`thyroid/apps/drfplus`这一文件夹内，文件结构如下。

```

├── __init__.py
├── apps.py
├── jwt.py					// JWT登陆认证相关
├── middlewares.py	// 中间件相关
└── mixins.py				// Mixin相关

```

下面说明一下各个文件的内容

`mixin.py`中包含了一个LoginMixin，用于实现一套通用的登录逻辑

```python
# mixins.py
class LoginMixin:
    """
    基于session验证的用户登录mixin
    提供了两种方法用于处理用户登录相关问题
    # TODO 增加退出登录
    """

    def islogin(self, request, *args, **kwargs):
        """
        当用户登录的时候返回用户信息，否则返回401
        """
        if request.user.is_authenticated:
            instance = User.objects.get(id=request.user.id)
            serializer = self.get_serializer(instance)
            return Response(data=serializer.data, status=status.HTTP_200_OK)
        else:
            return Response(data="当前用户未登录", status=status.HTTP_401_UNAUTHORIZED)

    def login(self, request, *args, **kwargs):
        """
        实现登录逻辑
        """
        data = request.data.copy()
        username = data['username']
        password = data['password']
        u = User.objects.get(username=username)
        if check_password(password, u.password):
            login(request, u)
            s = self.get_serializer(instance=u)
            return Response(data=s.data, status=status.HTTP_200_OK)
        else:
            return Response(data="密码错误", status=status.HTTP_403_FORBIDDEN)
```

`middlewares.py`包含了一个自定义中间件名为`DRFormatMiddlewares`，用于对系统整体的返回值进行格式统一方便前端处理

此中间件在`thyroid/thyroid/settings.py`被引用，中间件顺序请谨慎调整。

```python
MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'apps.drfplus.middlewares.DRFormatMiddlewares',		# <================== 在这里
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]
```

`jwt.py`内包含了与jwt登陆认证相关的工具函数与hanlder，在`thyroid/thyroid/settings.py`处被引用，具体参考注释

#### 其他

其他部分为普通的管理台项目。正常维护即可。

### 前端

前端采用的技术栈：

Vue2.x(https://cn.vuejs.org/): 前端框架

VueRouter(https://router.vuejs.org/zh/)：前端路由服务

Vuex(https://vuex.vuejs.org/zh/)：状态管理

Axios(http://www.axios-js.com/)：ajax库

#### 核心业务梳理

**1.对Axios库的封装**

前端部分对Axios库进行了封装，主要位于`src/network`内，其中，`api.js`内为对程序暴露的api接口；`requests.js`内为封装的axios对象

具体实现逻辑参考注释和官方文档。

封装完成后，在vue组件内访问api通过这种方式即可

```java
import api from "@/network/api";
login() {
      api.login(this.loginform)
        .then((res) => {
					....
        })
        .catch((error) => {
					....
        });
```

**2.图像识别部分的AJAX轮询**

由于后端采用了消息队列，与之对应的，前端需要通过轮询的方式来实现任务结果的异步获取。核心逻辑位于`src/components/contents/ThyroidFast.vue`

中`handleUpload`这一方法。

这个函数需要重构，现在可读性极差。

```javascript
handleUpload() {
      let filelist = this.fileList;
      let formData = new FormData();
      filelist.forEach(function (file) {
        formData.append("files[]", file);
      });
      this.uploading = true;
  		// 第一次发起请求，上传图片
      api
        .uploadThyroid(formData)
        .then((res) => {
        // 获取到图片上传的结果后
          if (res.data.code === 201) {
            this.$message.success("上传成功");
            this.resultList = res.data.data;
            this.uploading = false;
            //上传成功后，重新选择文件列表之前，禁用上传按钮
            this.uploadDisabled = true;
            const _this = this;
            let index = 0;
            // 对图片列表做处理
            this.resultList.forEach(function (result) {
              result.scanning = true;
              if (result.state !== 0) {
                result.count = 0;
                result.index = index;
                // 第二次发起请求，设置定时器
                result.intervalId = setInterval(() => {
                  api
                    .driveThyroid(result.id)
                    .then((res) => {
                      result.state = res.data.data.state;
                      if (result.state === 100 || result.count == 20) {
                        // 任务成功后清除定时器
                        clearInterval(result.intervalId);
                        result.resultImg = res.data.data.resultImg;
                        result.maskImg = res.data.data.maskImg;
                        result.scanning = false;
                        result.date = res.data.data.date;
                        let data = res.data.data.data;
                        result.data = data.masks;
                        result.AINotice = data.result_str;
                        _this.$notification.success({
                          message: "图片分析完成",
                          description:
                            "图片 " +
                            (result.index + 1) +
                            " 已经分析完成\n点击跳转结果位置",
                          onClick: () => {
                            let anchor = document.getElementById(
                              result.index + 1
                            );
                            anchor.scrollIntoView();
                          },
                        });
                      }
                      result.count += 1;
                    })
                    .catch((err) => {
                      console.log(err);
                    });
                }, 5000);
              }
              index += 1;
            });
          }
        })
        .catch((res) => {
          this.$message.error("上传失败:" + res.data);
        });
    },
```

#### 其他

除去轮询部分，前端部分就是一个普通的管理台项目。正常维护即可。
