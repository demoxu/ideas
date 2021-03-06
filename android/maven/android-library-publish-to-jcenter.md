发布library到Maven仓库
==================================================

> 本文是[PNEditText](https://github.com/addcn/PNEditText)(学习如何提交library到jcenter的虚拟项目)发布到jcenter的一次记录。


##基本概念

对开发者更友好的Android Library仓库目前有`jcenter`和`Maven Central`，它们维护在完全不同的服务器上，由不同的人提供内容，两者之间毫无关系。

除了两个标准的服务器之外，我们还可以把library放在自己的Maven仓库服务器，此时需要自己定义仓库的url，如Twitter的Fabric.io，定义仓库url,

```groovy
repositories {
    maven { url 'https://maven.fabric.io/public' }
}
```

然后获取library。

```groovy
dependencies {
    compile 'com.crashlytics.sdk.android:crashlytics:2.2.4@aar'
}
```

Android Studio团队在`0.8`版本起把默认的仓库替换成jcenter了，`jcenter()`自动被定义，而不是以前的`mavenCentral()`。


##发布环境

- JDK版本：`jdk1.7.0_75`
- Android Studio版本：`1.5.1`
- compileSdkVersion `23`, buildToolsVersion `"23.0.2"`


##具体步骤

####1. 注册[Bintray账号](https://bintray.com/)（我直接用google账号注册登入）。


####2. 在bintray上创建package。

登录网站，点击maven——>Add New Package，为要发布的library创建一个package，输入所有需要的信息然后提交。


####3. 配置Project（最外层）`local.properties`文件API key。

[Profile页面](https://bintray.com/profile/edit)复制API Key填写，完整内容如下：

	
```groovy
sdk.dir=D\:\\android-studio\\sdk	
bintray.user = dodo
bintray.apikey = ***********************
```

####4. 配置Module（library）`build.gradle`文件。

添加打包Maven文件、上传Bintray所需的插件，完整内容如下：
	
```groovy
buildscript {
    repositories {
	jcenter()
    }
    dependencies {
	classpath 'com.android.tools.build:gradle:1.5.0'
	classpath 'com.github.dcendents:android-maven-gradle-plugin:1.3'
	classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.0'
    }
}

allprojects {
    repositories {
	jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

####5. 配置Module（library）`build.gradle`文件。

生成JavaDoc、生成Jar、配置项目的信息等，完整的内容如下：
	
```groovy
apply plugin: 'com.android.library'

apply plugin: 'com.github.dcendents.android-maven'
apply plugin: 'com.jfrog.bintray'

// 版本号
version = "1.0.0"
android {
    compileSdkVersion 23
    buildToolsVersion "23.0.2"

    defaultConfig {
	minSdkVersion 8
	targetSdkVersion 22
	versionCode 1
	versionName version
    }
    buildTypes {
	release {
	    minifyEnabled false
	    proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
	}
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:appcompat-v7:23.1.1'
}

def siteUrl = 'https://github.com/addcn/PNEditText'      // 项目的主页
def gitUrl = 'https://github.com/addcn/PNEditText.git'   // Git仓库的url
group = "com.uedao.android.pnedittext"                   // Maven Group ID for the artifact，一般填你唯一的包名
install {
    repositories.mavenInstaller {
	// This generates POM.xml with proper parameters
	pom {
	    project {
		packaging 'aar'
		// Add your description here
		name 'Android EditText With Positive And Negative Button Widget' 	//项目描述
		url siteUrl
		// Set your license
		licenses {
		    license {
			name 'The Apache Software License, Version 2.0'
			url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
		    }
		}
		developers {
		    developer {
			id 'dodo'		//填写的一些基本信息
			name 'PNEditText'
			email 'lhuibo@gmail.com'
		    }
		}
		scm {
		    connection gitUrl
		    developerConnection gitUrl
		    url siteUrl
		}
	    }
	}
    }
}

task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}
task javadoc(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}
task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}
artifacts {
    archives javadocJar
    archives sourcesJar
}
Properties properties = new Properties()
properties.load(project.rootProject.file('local.properties').newDataInputStream())

bintray {
    user = properties.getProperty("bintray.user")
    key = properties.getProperty("bintray.apikey")
    configurations = ['archives']
    pkg {
	repo = "maven"
	name = "PNEditText"	//发布到JCenter上的项目名字
	websiteUrl = siteUrl
	vcsUrl = gitUrl
	licenses = ["Apache-2.0"]
	publish = true
    }
}	
```

####6. 提交library文件到Bintray

切换到`Terminal`面板执行：

```bash
gradlew install
gradlew bintrayUpload
```

如果没有错误，成功的话会有如下提示：

```bash
SUCCESSFUL
```

Bintray网页上，版本区域会变化，进入Files选项卡，可以看见上传的library文件。


**到此，你的library在互联网上任何人都可以使用了！**


不过library只是在你自己的Maven仓库，而不是在jcenter上。如果有人想使用你的library，他必须定义仓库的url，如下：

```groovy
//Project（最外层）build.gradle文件
allprojects {
    repositories {
	jcenter()
	maven {
	    url  "http://dl.bintray.com/dodo/maven/" //注：此url可以在项目右上`SET ME UP`（有个小扳手的地方）的地方找到。
	}
    }
}

//Module的build.gradle文件
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:23.1.1'
    compile 'com.uedao.android.pnedittext:library:1.0.0'
    //compile project(':library')
}
```


####7. Add to JCenter

在[jcenter主页](https://bintray.com/bintray/jcenter)上点击` Include My Package`按钮，或者package页面右下角`Linked to面板`有个按钮`Add to JCenter`，填写Comments然后Send即可。


第一次提交审核差不多10小时，在[站内信](https://bintray.com/inbox)可以看到审核通过的信息，`Linked to`面板有jcenterd的连接`Linked to (1)`文字更新。


最后可以不用引入个人仓库url，直接使用了：

```groovy
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.uedao.android.pnedittext:library:1.0.0'
    //compile project(':library')
}
```


## 参考文章

- 发布到jcenter

    - [使用Gradle发布项目到JCenter仓库](http://rocko.xyz/2015/02/02/%E4%BD%BF%E7%94%A8Gradle%E5%8F%91%E5%B8%83%E9%A1%B9%E7%9B%AE%E5%88%B0JCenter%E4%BB%93%E5%BA%93/)----主要参考之一，基本全部使用这文章的配置代码

    - [如何使用Android Studio把自己的Android library分享到jCenter和Maven Central](http://www.open-open.com/lib/view/open1435109824278.html)----主要参考之一，了解是整个流程（注：如果你不打算把library上传到Maven Central，可以跳过第二和第三部分。）

- 发布到第三方仓库

    - [JitPack的使用](http://blog.liangruijun.com/2016/01/16/JitPack%E7%9A%84%E4%BD%BF%E7%94%A8/)

- 发布到本地仓库

    - [拥抱 Android Studio 之四：Maven 仓库使用与私有仓库搭建](http://kvh.io/2016/01/20/embrace-android-studio-maven-deploy/)

- 自己建立内网仓库

    - [Windows 下Nexus搭建Maven私服](http://zyjustin9.iteye.com/blog/2017317)
    - [建立企业内部maven服务器并使用Android Studio发布公共项目](http://blog.csdn.net/qinxiandiqi/article/details/44458707)

