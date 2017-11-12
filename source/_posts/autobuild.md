---
layout: post
title: iOS脚本自动编译静态包/静态库
date: 2017-07-22 10:50:01
comments: true
tags:
	- iOS
	- 脚本
categories: "iOS"
thumbnail: /imgs/iOS.png
---

在iOS开发中，有时候为了项目模块间的相互独立性，降低模块间的耦合，通常将底层相对稳定的模块抽取出来，形成SDK，以静态包或者静态库的形式引入项目。这中间免不了编译打包静态包/库，通常的做法是模拟器和真机分别编译，然后再将两个静态包或者库`lipo`合并成一个。这样的工作做多了，难免觉得麻烦，想省事儿，整一脚本变得自然而然了。

<!-- more -->

__1. 创建一个静态包/库工程(已有请跳过这一步)__

创建如下图：
![Build Lib](/imgs/createLib.png)

在Trargets下面，点击左下角的“+”,选择添加一个新的Target， 然后选择“Aggregate”选项，新建一个集合类target，如下图所示：
![Build Target](/imgs/aggregate.png)

__2. 给集合类添加依赖__

在Targets下面选中我们新建的集合类，然后Build Phases --> Target Dependencies 下面添加我们需要编译的静态库,如下图所示：

![添加依赖](/imgs/dependencies.png)

__3. 添加脚本__

在Targets下面选中我们新建的集合类,然后在Build Phases下，点击左上角的“+”，选择"New Run Script Phase"选项,如下图：

![添加运行脚本](/imgs/runscript.png)

然后设置我们运行的脚本的所在位置,如下图所示:

![设置脚本位置](/imgs/scriptpath.png)

__注意：__
> ../scripts/build-framework.sh 表示脚本的位置在当前工程的上一个目录下地scripts文件夹下. 当然"../"也可以用"${SRCROOT}/../"来代替.

>脚本位置可根据实际情况自行设置。

__4. 选择我们创建的集合类的target，编译__

在编译时，你可能会遇到下面的错误：
```
Permission denied.
```

这是因为shell脚本，还没有添加可执行的权限。

解决办法：在终端上， cd 进入到脚本所在的文件夹； 然后使用 chmod +x ./yourShellName.sh 命令， 成功之后，就可以正常的运行Xcode工程了。

__5. 指定target__

一般来说我们编译的target名称，跟我们的project工程的名称是一样的，但是，如果我们修改了我们的target名称，需要在我们制定脚本的位置后面跟上我们需要编译的target名称，既:脚本位置+空格+target名称。如下图：

![指定target](/imgs/buildparam.png)

__6. shell脚本源码__

编译library的脚本

``` shell
#!/bin/sh

#要build的target名
target_Name=${PROJECT_NAME}
if [[ $1 ]]
then
target_Name=$1
fi

UNIVERSAL_OUTPUT_FOLDER="${SRCROOT}/${PROJECT_NAME}_Products"

# 创建输出目录，并删除之前的文件
rm -rf "${UNIVERSAL_OUTPUT_FOLDER}"
mkdir -p "${UNIVERSAL_OUTPUT_FOLDER}"

# 分别编译真机和模拟器版本
xcodebuild -target "${target_Name}" ONLY_ACTIVE_ARCH=NO -configuration ${CONFIGURATION} -sdk iphoneos  BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}" clean build
xcodebuild -target "${target_Name}" ONLY_ACTIVE_ARCH=NO -configuration ${CONFIGURATION} -sdk iphonesimulator BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}" clean build

#复制头文件到目标文件夹
HEADER_FOLDER="${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/include/${target_Name}"
if [[ -d "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/usr/local/include" ]]
then
    HEADER_FOLDER="${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/usr/local/include"
fi
cp -R "${HEADER_FOLDER}" "${UNIVERSAL_OUTPUT_FOLDER}"

#合成模拟器和真机.a包
lipo -create "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/lib${target_Name}.a" "${BUILD_DIR}/${CONFIGURATION}-iphoneos/lib${target_Name}.a" -output "${UNIVERSAL_OUTPUT_FOLDER}/lib${target_Name}.a"

# 判断build文件夹是否存在，存在则删除
if [ -d "${SRCROOT}/build" ]
then
rm -rf "${SRCROOT}/build"
fi

#打开目标文件夹
open "${UNIVERSAL_OUTPUT_FOLDER}"

```
编译framework的shell脚本

``` shell
#!/bin/sh
#要build的target名
TARGET_NAME=${PROJECT_NAME}
if [[ $1 ]]
then
TARGET_NAME=$1
fi
UNIVERSAL_OUTPUT_FOLDER="${SRCROOT}/${PROJECT_NAME}_Products/"

#创建输出目录，并删除之前的framework文件
mkdir -p "${UNIVERSAL_OUTPUT_FOLDER}"
rm -rf "${UNIVERSAL_OUTPUT_FOLDER}/${TARGET_NAME}.framework"

#分别编译模拟器和真机的Framework
xcodebuild -target "${TARGET_NAME}" ONLY_ACTIVE_ARCH=NO -configuration ${CONFIGURATION} -sdk iphoneos BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}" clean build
xcodebuild -target "${TARGET_NAME}" ONLY_ACTIVE_ARCH=NO -configuration ${CONFIGURATION} -sdk iphonesimulator BUILD_DIR="${BUILD_DIR}" BUILD_ROOT="${BUILD_ROOT}" clean build

#拷贝framework到univer目录
cp -R "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/${TARGET_NAME}.framework" "${UNIVERSAL_OUTPUT_FOLDER}"

#合并framework，输出最终的framework到build目录
lipo -create -output "${UNIVERSAL_OUTPUT_FOLDER}/${TARGET_NAME}.framework/${TARGET_NAME}" "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator/${TARGET_NAME}.framework/${TARGET_NAME}" "${BUILD_DIR}/${CONFIGURATION}-iphoneos/${TARGET_NAME}.framework/${TARGET_NAME}"

#删除编译之后生成的无关的配置文件
dir_path="${UNIVERSAL_OUTPUT_FOLDER}/${TARGET_NAME}.framework/"
for file in ls $dir_path
do
if [[ ${file} =~ ".xcconfig" ]]
then
rm -f "${dir_path}/${file}"
fi
done

#判断build文件夹是否存在，存在则删除
if [ -d "${SRCROOT}/build" ]
then
rm -rf "${SRCROOT}/build"
fi

rm -rf "${BUILD_DIR}/${CONFIGURATION}-iphonesimulator" "${BUILD_DIR}/${CONFIGURATION}-iphoneos"

#打开合并后的文件夹
open "${UNIVERSAL_OUTPUT_FOLDER}"

```
