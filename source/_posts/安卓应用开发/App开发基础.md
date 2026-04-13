---
title: 安卓-App开发基础
hidden: false
categories:
  - 学习记录 - 安卓应用开发
tags:
  - 安卓应用
abbrlink: 1ba5f561
date: 2026-04-06 17:25:08
---

### 项目工程结构

App工程分为两层：项目（**Project**）和模块（**Module**）

其中模块依附于项目，每个项目至少有一个模块，也可以有多个模块

而“编译运行App”一般指的是运行某个模块（即模块对应实际的App）

```bash
+---Project
|       Module1
|       Module2
|       Module3
|       ......
```

App项目下有两个分类：App目录（App模块）和Gradle Scripts目录（工程编译配置文件）

#### App目录

App目录包含以下子目录：

```BASH
# App目录树
\---App
	+---manifests
	|	AndroidManifest.xml
	|
	+---java
	|	+---com.example.myapplication1
	|	+---com.example.myapplication1 (androidTest)
	|	+---com.example.myapplication1 (test)
	|
	\---res
		+---drawable
		+---layout
		+---mipmap
		+---values
```

- manifests子目录：包含文件AndroidManifest.xml，它是App的运行配置文件
- java子目录：存放java源文件，也包含测试用的源文件
- res子目录：存放当前模块的资源文件，其包含四个子目录：
  - drawable：存放图形描述文件
  - layout：存放布局文件
  - mipmap：存放启动图标
  - values：定义App需要用到的常量

#### Gradle Scripts目录

Gradle Script目录主要包含以下文件：

```bash
# Gradle Script目录树
\---Gradle_Scripts
	|	build.gradle.kts # Project: My_Application1
	|	build.gradle.kts # Module :app
	|	proguard-rules.pro # ProGuard Rules for ":app"
	|	gradle.properties # Project Properties
	|	local.properties # SDK Location
	|	settings.gradle.kts # Project Settings
	|	...
	|	# other files
```

- build.gradle (.kts)：项目自动化构建工具，集成了依赖、打包、部署、编译运行和差异管理等工作
- proguard-rules.pro：描述代码混淆规则
  - 代码混淆：将源文件代码加密，以提升应用安全性
- gradle.properties：配置编译时的命令行参数（一般不需要改动）
- settings.gradle：配置需要编译的模块
- local.properties：描述本地配置，如开发环境等，由 Android Studio 自动生成

### 项目设计规范

#### AndroidManifest.xml 文件

每个应用的根目录下都必须包含一个AndroidManifest.xml文件，这个文件包含了App的配置信息，系统需要根据里面的内容运行App的代码，显示界面。

```xml
<!-- AndroidManifest.xml -->
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
          package="com.example.myapplication1">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MyApplication1">
        <activity
            android:name=".MainActivity"
            android:exported="false" />
    </application>

</manifest>
```

其中package属性指定应用包名，而application结点下的各个属性说明如下：

- allowBackup属性：是否允许应用备份
- icon属性：应用图标
- label属性：应用名称
- roundIcon属性：应用圆角图标
- supportsRtl属性：是否支持从右往左的排版
- theme属性：应用的显示风格

#### build.gradle 文件

Gradle.Script目录中存在两个build.gradle.kts，分别对应于整个项目（My_Application1）和具体模块（app），这里主要说明模块的build.gradle.kts

```kotlin
// build.gradle.kts(:app)
plugins {
    alias(libs.plugins.android.application)
}

android {
    namespace = "com.example.myapplication1"
    
    // 指定编译用的sdk版本号
    compileSdk {
        version = release(36)
    }

    defaultConfig {
        
        // 应用包名，需要与menifest.xml中的保持一致
        applicationId = "com.example.myapplication1"
        
        // 最小sdk，即可兼容的最低安卓版本
        minSdk = 24
        
        // 目标sdk，即目标运行的安卓版本
        targetSdk = 36
        
        // 应用版本号
        versionCode = 1
        
        // 应用版本名称
        versionName = "1.0"

        // 用于测试的参数
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_11
        targetCompatibility = JavaVersion.VERSION_11
    }
}

dependencies {
    implementation(libs.appcompat)
    implementation(libs.material)
    implementation(libs.activity)
    implementation(libs.constraintlayout)
    testImplementation(libs.junit)
    androidTestImplementation(libs.ext.junit)
    androidTestImplementation(libs.espresso.core)
}
```