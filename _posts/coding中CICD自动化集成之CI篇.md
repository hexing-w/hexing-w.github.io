---
layout: post
title: "coding中CICD自动化集成"
date: 2023-08-16 17:17:03
categories: CICD
excerpt: CICD
---

* content
{:toc}

### 概述

互联网软件的开发和发布，已经形成了一套标准流程，最重要的组成部分就是持续集成（Continuous integration，简称 CI）。
在讲解之前，先给大家普及几个概念：

持续集成（Continuous integration，简称 CI）：持续集成指的是，频繁地（一天多次）将代码集成到主干。持续集成的目的，就是让产品可以快速迭代，同时还能保持高质量。它的核心措施是，代码集成到主干之前，必须通过自动化测试。只要有一个测试用例失败，就不能集成。
持续交付（Continuous delivery）：指的是，频繁地将软件的新版本，交付给质量团队或者用户，以供评审。如果评审通过，代码就进入生产阶段。
持续交付可以看作持续集成的下一步。它强调的是，不管怎么更新，软件是随时随地可以交付的。
持续部署（continuous deployment）：是持续交付的下一步，指的是代码通过评审以后，自动部署到生产环境。
持续部署的目标是，代码在任何时刻都是可部署的，可以进入生产阶段。
持续部署的前提是能自动化完成测试、构建、部署等步骤。它与持续交付的区别，可以参考下图。
![ci](http://hexing-w.github.io/css/pics/ci.jpg)

我们常说的CI/CD就是指上面的持续集成、持续交付和持续部署。

一般来说，一个完善的git代码的分支模型，主要有以下几个分支：
![ci](http://hexing-w.github.io/css/pics/ci_git.jpg)


master：用于生产环境的分支，一般是用来产品上线或者发布使用
hotfix：用于对master分支上发现的紧急bug或者问题进行的修复分支，修复完毕后合并到master和develop分支
release：用于对产品的预上线的分支，即准备上线分支，即上线前的预发布分支，供内测及上线，一般master分支的代码主要由release提交
develop：用于开发者产品开发的合并分支，即不同的coder开发完程序之后，进行代码合并的分支
feature：feature分支即我们开发者个人的分支或者某个新增功能的分支，用于开发阶段的分支



CI/CD是一个持续的自动化集成和部署的一个过程，存在于我们的开发、测试、生产的每一个环节
以下是基于我们自身的实际需求，进行的一个简单的CI的过程，基于coding平台的持续集成，进行从不同的仓库拉取相关的代码，进行dotnet的编译，并将生成的dll提交到对应的代码仓库中。

首先在coding的项目中，有较好的帮助文档可以帮助我们使用其功能，https://help.coding.net/docs/ci/intro.html

找到左侧的持续集成->构建计划->创建构建计划，创建构建计划中有内置的相关一些模板，如图
![ci](http://hexing-w.github.io/css/pics/ci_build.png)


#### 创建构建计划
基础信息配置
我这块选择的是自定义构建过程，选择后，进入coding的基础信息配置，这里允许我们选择相关的代码仓库来源，如coding、GitHub、GitLab、码云等，我这里用到的是coding
然后会选择代码仓库，选择了代码仓库以后，我们默认的git操作对象就是该仓库，同时相应的触发规则也是基于该代码仓库进行的，当然我们也可以拉取操作其他的仓库代码，详情见下文。
配置来源里面中我们使用静态jenkinsfile，这里还提供了一个选项是使用代码仓库中的jenkinsfile，意思就是我们可以从git仓库里拉取代码后使用里面的Jenkinsfile执行计划，存储于代码仓库里的jenkinsfile有利于我们的维护和修改
节点池配置：coding进行CI的时候，默认会提供一台Linux Ubuntu的云服务器，用来执行我们的相关命令及程序，所以这里的节点池配置就是云主机的节点信息，我们选择上海的主机即可。
如图配置
![ci](http://hexing-w.github.io/css/pics/ci_plan.png)

#### 流程配置
流程配置里，主要用来设计我们构建计划的整个流程，即CI流程，这步就是我们CI的核心部分，他支持图形化编辑器和文本编辑器
图形化编辑器就是我们在流程图上点击按钮增减相应的流程、命令等。他默认内置了一部分常用的流程空间，我们点选即可。
文本编辑器就是我们通过写脚本代码，进行流程的处理。这个脚本，主要遵循jenkinsfile的语法，所以，只要是jenkinsfile可以运行的脚本，在这个文本编辑器里就可以执行，但是如果我们是使用的自己安装的Jenkins的话，也支持yml文件来编写Jenkins脚本，由于我们用的是coding的云服务器，默认只支持jenkinsfile。
由于个人实在无法看懂图形编辑器生成的代码，所以最终采用了写脚本的方式来处理，这样自己掌握脚本的过程，便于控制和修改。
这里我们着重讲一下一些关键的脚本语法
首先Jenkinsfile的本质，就是一个流水线的脚本，也就是pipeline，pipeline就类似于我们任何一个程序的main函数一样，是一个入口。
整个pipeline里面的语法：
```
pipeline {
  agent any
  stages {
    stage('检出') {
      steps {
        checkout([$class: 'GitSCM',
        branches: [[name: GIT_BUILD_REF]],
        userRemoteConfigs: [[
          url: GIT_REPO_URL,
          credentialsId: CREDENTIALS_ID
        ]]])
      }
    }

    stage('构建镜像并推送到 CODING Docker 制品库') {
      steps {
        sh "docker build -t ${CODING_DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_VERSION} -f ${DOCKERFILE_PATH} ${DOCKER_BUILD_CONTEXT}"
        useCustomStepPlugin(key: 'SYSTEM:artifact_docker_push', version: 'latest', params: [image:"${CODING_DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_VERSION}",repo:"${DOCKER_REPO_NAME}",properties:'[]'])
      }
    }

  }
  environment {
    CODING_DOCKER_REG_HOST = "${CCI_CURRENT_TEAM}-docker.pkg.${CCI_CURRENT_DOMAIN}"
    CODING_DOCKER_IMAGE_NAME = "${PROJECT_NAME.toLowerCase()}/${DOCKER_REPO_NAME}/${DOCKER_IMAGE_NAME}"
  }
}
```
详情参见：https://help.coding.net/docs/ci/configuration/env.html

#### 触发规则
接下来配置触发规则 如图
![ci](http://hexing-w.github.io/css/pics/ci_rules.png)

#### 变量与缓存
点击下一步接下来就是配置项目的变量与缓存
![ci](http://hexing-w.github.io/css/pics/ci_cache.png)


然后可以点击立即构建，就可以看到我们的构建信息
![ci](http://hexing-w.github.io/css/pics/ci_success.png)

