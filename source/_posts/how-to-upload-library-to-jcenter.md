---
title: 将代码上传到jcenter
date: 2017-10-12 15:12:06
tags:
---



### 前言

在平时的Android开发中，如果需要用到第三方库的时候，我们只要在自己module的build.gradle里加上这么一句代码就可以了:

```
 compile 'com.google.code.gson:gson:2.7'
```

```
 // 注释
 // com.google.code.gson 是GROUP_ID
 // gson 是ARTIFACT_ID,library的真实名字
 // 2.7 是VERSION 版本号
```

然后，Android Studio会帮我们从 project 的 build.gradle 里定义的Maven仓库服务器下载library。总的来说，只有两个标准的Android library文件服务器: jcenter 和 Maven Central。

其中，jcenter是一个由 [bintray.com](https://bintray.com/) 维护的Maven仓库;Maven Central 是一个由 [sonatype.org](https://sonatype.org/)  维护的Maven仓库。

现在Android Studio已经默认使用的是jcenter,打开project的build.gradle里就可以看到以下代码：

```
allprojects {
    repositories {
        jcenter()
    }
}
```

关于gradle是如何工作的以及两个仓库详细的介绍的见另一篇[文章(我还没写)]() 。好了，我们现在就撸起袖子准备上传代码吧！ヽ(￣▽￣)ﾉ



### 1.注册bintray账号

个人注册地址: <https://bintray.com/signup/oss> 

企业注册地址:  https://bintray.com/



**注意:**

(1) 一定要进入个人注册地址进行注册。面向企业的账号在最后会有坑，导致最后上传不了代码。等我踩过了再来填坑吧( • ̀ω•́ )✧

(2) 账号不能是qq邮箱(很多博客都说一定要用外国邮箱注册，其实不用，只要给你的qq邮箱注册个@ foxmail.com账号就可以啦~亲测可用, [传送门](https://kf.qq.com/faq/120322fu63YV130422im6VnM.html) )。再说了,作为一只程序猿，gmail 账号总都会有的吧~(｡･ω･｡)



### 2. bintray上的准备工作

#### 2.1 获取 API Key
先写在这，后面上传代码要用到的时候再回来看。

![获取apikey](index_files/_u83B7_u53D6apikey.png)

#### 2.2 创建Maven仓库
第一步:选择 "Add New Repository"
![](index_files/6969d2c0-0c6b-4390-99ff-917274b2bc4b.png)
第二步：编辑Repository的信息，在这里我们把Repository名字取为maven
![](index_files/TIM_u56FE_u724720170927093453.png)

### 3. Android Studio上的准备工作

#### 3.1 准备一个library
在这里我们新建一个library，并取名为"siplibrary"。

![](index_files/553b25f5-79eb-4687-8dd5-6dfff4e8e09a.jpg)

很多情况下，我们需要上传一个以上的library到仓库，也可能不需要上传东西。因此最好将每部分分成一个Moudle。最好分成两个module，一个Application Moudle，一个Library Moudle。Application Moudle用于展示库的用法，Library Moudle是library的源代码。如果你的项目有一个以上的library，尽管创建另外的moudle：一个moudle对应一个library。
![](index_files/3f56416c-d57a-40e1-b9c5-a6a1205972c9.png)

#### 3.2 把bintray插件应用在项目中
##### 第一步：在project的build.gradle文件的dependencies节点下添加以下代码
```
classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.0'
```
![](index_files/d1ba27ce-dc56-457e-93cf-febc27c7c050.png)


##### 第二步：在你的library module目录下创建 `project.properties` 文件
project.properties 文件的原始内容如下:
```
#project
project.name=
project.groupId=
project.artifactId=
project.packaging=aar
project.siteUrl=
project.gitUrl=

#javadoc
javadoc.name=
```
注释：
> - project.name：项目名称（
- project.groupId：项目组ID(GROUP_ID)参加前言里的注释,通常情况下，如果你的包名是com.example.test,那么项目组id就是com.example.test。
- project.artifactId：library的真实名字(ARTIFACT_ID)，通常情况下，如果你的包名为com.example.test,那么ARTIFACT_ID就是test。
- project.packaging：包类型，Android库是aar
- project.siteUrl：项目官方网站的地址，没有的话就用Github上的地址
- project.gitUrl： 项目的Git地址
- project.gitUrl：生成的javadoc打开后主页显示的名称，通常跟项目名称一样即可



完成配置后的内容如下：
```
#project
project.name=siplibrary
project.groupId=com.dpower.siplibrary
project.artifactId=siplibrary
project.packaging=aar
project.siteUrl=https://github.com/ZhangDaYu/MavenAndroid
project.gitUrl=https://github.com/ZhangDaYu/MavenAndroid.git
#javadoc
javadoc.name=siplibrary
```

------------


> #### 插播：如何将本地代码上传至github上
>这里默认你已经安装了git环境并且有github账号
##### 1.在github上的准备工作：创建一个远程的git仓库
- 创建一个repository
  进入github主页，点击右侧的绿色按钮 `New Repository`。在这里我们把Repository取名为 MavenAndroid
  ![](index_files/5beb9b96-9fc8-4fa5-a247-ac7b426e4e12.png)
- 获取github上repository的地址
  这里我的远程仓库地址为： https://github.com/ZhangDaYu/MavenAndroid.git
  ![](index_files/dbfbfd88-40b5-4958-a64a-12532c206771.png)
##### 2.在本地的准备工作:创建一个本地的git仓库
- 打开终端进入项目的根目录下，依次执行以下命令：
```
git status
git init
git status
git add *
git commit -m 'init repository'
git status
```
##### 3. 将本地代码上传至远程的git仓库
打开终端进入项目的根目录下，依次执行以下命令：
```
git remote
git remote add origin https://github.com/ZhangDaYu/MavenAndroid.git
git push -u origin master
```
这样我们就把本地代码成功上传到github上了。

------------




##### 第三步：在你的library module目录下创建 `local.properties` 文件
local.properties 文件的原始内容如下
```
#bintray
bintray.user=
bintray.apikey=

#developer
developer.id=
developer.name=
developer.email=
```
注释：
> - bintray.user：你的Bintary的用户名,就是你登录Bintray的那个名字
- bintray.apikey：你的Bintray的API Key。获取方式见 `2.1 获取 API Key`
- developer.id : 通常是你在开源社区上的昵称
- developer.name：你的姓名
- developer.email：你的邮箱

完成配置后的内容如下：
```
#bintray
bintray.user=zhangyu
bintray.apikey= ***************(不给你看)
#developer
developer.id = ZhangDaYu
developer.name = zhangyu
developer.email = zhangbigfish@gmail.com
```
**注意：要将local.properities文件添加到忽略列表，以免提交到github上泄露个人信息**
**注意：要将local.properities文件添加到忽略列表，以免提交到github上泄露个人信息**
**注意：要将local.properities文件添加到忽略列表，以免提交到github上泄露个人信息**

##### 第四步： 在library module目录下创建 `bintrayUpload.gradle` 文件
然后修改你的library module 的 build.gradle 文件，在最后加上
```
apply from:"bintrayUpload.gradle"
```
bintrayUpload.gradle 的原始内容如下：
```
apply plugin: 'com.github.dcendents.android-maven'
apply plugin: 'com.jfrog.bintray'

// load properties
Properties properties = new Properties()
File localPropertiesFile = project.file("local.properties");
if(localPropertiesFile.exists()){
    properties.load(localPropertiesFile.newDataInputStream())
}
File projectPropertiesFile = project.file("project.properties");
if(projectPropertiesFile.exists()){
    properties.load(projectPropertiesFile.newDataInputStream())
}

// read properties
def projectName = properties.getProperty("project.name")
def projectGroupId = properties.getProperty("project.groupId")
def projectArtifactId = properties.getProperty("project.artifactId")
def projectVersionName = android.defaultConfig.versionName
def projectPackaging = properties.getProperty("project.packaging")
def projectSiteUrl = properties.getProperty("project.siteUrl")
def projectGitUrl = properties.getProperty("project.gitUrl")

def developerId = properties.getProperty("developer.id")
def developerName = properties.getProperty("developer.name")
def developerEmail = properties.getProperty("developer.email")

def bintrayUser = properties.getProperty("bintray.user")
def bintrayApikey = properties.getProperty("bintray.apikey")

def javadocName = properties.getProperty("javadoc.name")

group = projectGroupId

// This generates POM.xml with proper parameters
install {
    repositories.mavenInstaller {
        pom {
            project {
                name projectName
                groupId projectGroupId
                artifactId projectArtifactId
                version projectVersionName
                packaging projectPackaging
                url projectSiteUrl
                licenses {
                    license {
                        name 'The Apache Software License, Version 2.0'
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }
                developers {
                    developer {
                        id developerId
                        name developerName
                        email developerEmail
                    }
                }
                scm {
                    connection projectGitUrl
                    developerConnection projectGitUrl
                    url projectSiteUrl
                }
            }
        }
    }
}

// This generates sources.jar
task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}

task javadoc(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}

// This generates javadoc.jar
task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}

artifacts {
    archives javadocJar
    archives sourcesJar
}

// javadoc configuration
javadoc {
    options{
        encoding "UTF-8"
        charSet 'UTF-8'
        author true
        version projectVersionName
        links "http://docs.oracle.com/javase/7/docs/api"
        title javadocName
    }
}

// bintray configuration
bintray {
    user = bintrayUser
    key = bintrayApikey
    configurations = ['archives']
    pkg {
        repo = "maven"
        name = projectName
        websiteUrl = projectSiteUrl
        vcsUrl = projectGitUrl
        licenses = ["Apache-2.0"]
        publish = true
    }
}
```


完成配置后 build.gradle 文件如下所示：
```
apply plugin: 'com.android.library'

android {
    compileSdkVersion 25
    buildToolsVersion "26.0.1"
    defaultConfig {
        minSdkVersion 14
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:25.3.1'
    testCompile 'junit:junit:4.12'
}
//只有这一句是你要加的
apply from:"bintrayUpload.gradle"
```



### 4.上传代码到bintray
打开终端进入项目目录下，或者在Android Studio 里打开Terminal，然后输入
```
gradlew bintrayUpload
```
上传成功会有success的提示。
### 5.将bintrary的代码上传至jcenter
登入Bintray网站，进入个人中心，在左侧的Owned Repositories区域点击Maven的图标，进入你的Maven项目列表。

如果已经上传成功了，在这里就能看到你的项目，进入项目详情，在右下角的Linked To区域点击 `Add to JCenter`，然后在Comments输入框里写一些提交信息，最后点 `Send` 提交请求即可。

![](index_files/51a1e4c8-8f41-4874-93a1-1f30adefe8ba.png)

一般情况下审核需要4到5个小时，耐心等待就行了，审核通过后会给你发邮件通知你，并且以后更新项目就不需要再审核了。
### 5.使用寄几写的library
时间过得真快，一眨眼4个小时过去了，审核通过！那么该如何使用寄几的库呢？登录 [Bintray](https://bintray.com/) 网站，进入项目详情，在页面的左下角有个 "Maven build settings" , 然后切换到 "Gradle",就可以看到

![](index_files/a07c8eda-53dc-4d54-9c17-074280245c07.png)

那么你只需要在你要使用该库的module的build.gradle添加以下代码：
```
compile 'com.dpower.siplibrary:siplibrary:1.0'
```
介样就阔以啦，似不似炒鸡简单！ ヾ(๑╹◡╹)ﾉ"

### 参考
> - [How to distribute to your own Android library through jCenter and Maven Central from Android Studio](https://inthecheesefactory.com/blog/how-to-upload-library-to-jcenter-maven-central-as-dependency/en)
- [新版Bintray-极简上传Library到JCenter](http://blog.csdn.net/wzgiceman/article/details/53707042)
- [上传项目到JCenter以及Maven仓库的步骤和错误解决](http://blog.csdn.net/qq_23018915/article/details/51055470)
- [Android studio将library module上传至jcenter](http://blog.csdn.net/u011791526/article/details/73650360)

