---
title: cocoaPods私有库的创建
date: 2018-07-29 21:04:39
tags: iOS
---
# cocoaPods私有库的创建

#### 第一步:建立私有的spec仓库

```
pod repo add REPO_NAME SOURCE_URL

REPO_NAME: specs的名称
SOURCE_URL: git地址
```
> 这里会在 ~/.cocoapods/repos目录下创建你的私有索引库,master是官方的


[官方文档](http://guides.cocoapods.org/making/private-cocoapods.html)

#### 第二步:在一个文件夹里创建pod项目

首先进入到需要创建项目的文件夹下面

创建项目:
```
pod lib create [项目名]
```
接下来按照提示设置一些东西过后就可以创建好pod项目

##### 2.1 在Classes目录下添加你的代码


#### 第三步: 提交项目到git仓库(这里不是spec的地址,是新的git地址,相当于新的项目)

```
git add .

git commit -s -m "初始化项目库"

git remote add origin [项目git地址]          
#添加远端仓库 项目git地址,不是前面的spec库

git push origin master

git tag -m "提交项目" "0.1.0" 
#打上标签，这个很重要,关系着你的版本控制,podfile文件中的版本号依赖于此,每次提交不同的版本要打上不同的tag

$ git push --tags     
#推送tag到远端仓库

```

#### 第四步:创建并提交podspec文件到私有Spec Repo仓库

这一步的作用就是让自己的私有库索引能够找到对应的pod库

* .配置podspec文件

```
#
# Any lines starting with a # are optional, but their use is encouraged
# To learn more about a Podspec see http://guides.cocoapods.org/syntax/podspec.html
#

Pod::Spec.new do |s|
  s.name             = 'THCacheMoudle'
  s.version          = '0.1.1'
  s.summary          = 'A short description of THCacheMoudle.'

# This description is used to generate tags and improve search results.
#   * Think: What does it do? Why did you write it? What is the focus?
#   * Try to keep it short, snappy and to the point.
#   * Write the description between the DESC delimiters below.
#   * Finally, don't worry about the indent, CocoaPods strips it!

  s.description      = <<-DESC
TODO: Add long description of the pod here.
                       DESC

  s.homepage         = 'http://www.tuhuanjk.com' #这个地址一定要可以访问,不然不能验证通过
  # s.screenshots     = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'
  s.license          = { :type => 'MIT', :file => 'LICENSE' }
  s.author           = { 'heshuai' => 'heshuai@sctuhuan.com' }
  s.source           = { :git => 'http://192.168.8.254:4521/THCacheMoudle.git', :tag => s.version.to_s }
  # s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'

  s.ios.deployment_target = '8.0'

  s.source_files = 'THCacheMoudle/Classes/**/*'
  
  # s.resource_bundles = {
  #   'THCacheMoudle' => ['THCacheMoudle/Assets/*.png']
  # }

  # s.public_header_files = 'Pod/Classes/**/*.h'
  # s.frameworks = 'UIKit', 'MapKit'
  # s.dependency 'AFNetworking', '~> 2.3'
    s.dependency 'HandyJSON'
    s.dependency 'PINCache'
    s.dependency 'RNCryptor'
end
```

* 验证一下这个podspec文件是否可用
```
pod lib lint
# 有一个警告都会验证失败
```
* 向Spec提交podspec文件, Spec就是存的各个pod的podspec索引

```
#进入pod项目的根目录执行
pod repo push [Repo名] [podspec 文件名字]

#~/.cocoapods/repos/**下面查看你提交的podspec文件
```

#### 第五步: 使用pod

```
#私有Spec,在podfile中填写私有podspec地址,然后 就是普通的pod用法了
source [Spec的git地址] 
pod [项目名], '~> [tag版本号]'
```


## 错误处理

#### 1,Include of non-modular header inside framework module

#### 2,pod lib lint不通过
这个如果是找不到库的头文件,可以使用--use-libraries来验证,再上传的时候同样带上此参数




