---
title: java应用结合Jenkins，docker部署到Kubernetes
toc: true
date: 2016-10-17 16:44:51
tags:
    - Docker
    - K8s
    - Jenkins
---

### Jenkins安装

1、下载Jenkins war包安装，下载地址 [jenkins.io](jenkins.io)。这里使用的是Jenkins2.24版本

2、启动Jenkins

```bash
JENKINS_HOME=~/.jenkins java -jar ~/Downloads/jenkins-2.24.war --httpPort=9090
```

启动后，日志会提示
```
*************************************************************
*************************************************************
*************************************************************
 
Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:
 
3521fbc3d40448efa8942f8e464b2dd9
 
This may also be found at: /Users/arungupta/.jenkins/secrets/initialAdminPassword
 
*************************************************************
*************************************************************
*************************************************************
```

访问localhost:9090，输入上面提示的密码，然后根据提示，安装推荐的插件，并创建用户

### Jenkins插件安装

1、maven配置
在 系统管理->Global Tool Configuration->Maven 中配置Maven信息
![maven3](http://7xpk5e.com1.z0.glb.clouddn.com/jenkis-maven3.png)

2、其他插件安装
在 系统管理->管理插件->可选插件中搜索 docker pipe ，选择CloudBees Docker Pipeline进行安装，安装后重启Jenkins

### 创建Jenkins任务

新建任务docker-pipeline

![docker-pipeline](http://7xpk5e.com1.z0.glb.clouddn.com/docker-pipeline.png)

选择Pipeline后，点击Ok后对任务进行配置。配置页中，可以根据自己需要进行设置。都比较简单一看就知道。我这里测试的是代码SCM使用的SVN，再Pipeline项选择Pipeline script from scm进行如下设置

![pipeline-from-scm](http://7xpk5e.com1.z0.glb.clouddn.com/pipeline-from-scm.png)

其中script path中设置的Jenkinsfile为具体的构建步骤，在项目地址的目录中。

当然也可以直接选择 Pipeline script，直接在脚本中设置构建步骤

![pipeline-script](http://7xpk5e.com1.z0.glb.clouddn.com/pipeline-script.png)

脚本中主要的就是构建步骤，从scm检出项目，mvn测试打包，生成docker镜像，将docker镜像上传到镜像仓库，然后登录K8s的master上，部署新的镜像包。

来看看Jenkinsfile，里面是如何描述上面的步骤的。

```grovvy
node {
    checkout scm
    env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"
    stage('Package') {
        dir('RpcServerSample') {
            sh 'mvn clean package -DskipTests'
        }
    }

    stage('Run Tests') {
        dir('RpcServerSample') {
                sh "mvn test"
        }
    }

    stage('Build Image') {
        dir('RpcServerSample') {
            docker.build("daocloud.io/suqun/docker-jenkins-pipeline:v1").push()
        }
    }

    stage('Deploy') {
        //替换rpcserver.yaml的镜像版本号，待完成

        dir('RpcServerSample') {
            //scp rpcserver.yaml 到 k8s的master上
            sh 'sshpass -p vagrant scp rpcserver.yaml vagrant@192.168.1.10:/home/vagrant/rpcserver.yaml'

            //远程登录k8s集群master主机,更新镜像，这里面要进行判断，第一次create
            sh 'sshpass -p vagrant ssh vagrant@192.168.1.10 "kubectl create -f /home/vagrant/rpcserver.yaml"'
        }
    }

}
```

docker.build语句通过RpcServerSample路径下的Dockerfile创建镜像

Dockerfile
```bash
FROM daocloud.io/java:7
MAINTAINER "Larry Su <larrys@wicresoft.com>"
ADD target/RpcServerSample-1.0-SNAPSHOT-jar-with-dependencies.jar /usr/src/myapp/RpcServerSample.jar
WORKDIR /usr/src/myapp
CMD ["java","-jar", "RpcServerSample.jar"]
```

rpcserver.yaml是K8s中的Deployment和Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: rpcserversample
  labels:
    app: rpcserversample
spec:
  # if your cluster supports it, uncomment the following to automatically create
  # an external load-balanced IP for the frontend service.
  #type: NodePort
  ports:
    # the port that this service should serve on
  - port: 9001
  selector:
    app: rpcserversample
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: rpcserversample
  # these labels can be applied automatically
  # from the labels in the pod template if not set
  labels:
    app: rpcserversample
spec:
  # this replicas value is default
  # modify it according to your case
  replicas: 2
  # selector can be applied automatically
  # from the labels in the pod template if not set
  # selector:
  #   matchLabels:
  #     app: guestbook
  #     tier: frontend
  template:
    metadata:
      labels:
        app: rpcserversample
    spec:
      containers:
      - name: rpcserversample
        image: daocloud.io/suqun/rpcserversample:v1
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        ports:
        - containerPort: 80
```

这里有些Jenkins pipeline最佳实践，可以参考:[Jenkins管道最佳实践Top 10](http://www.weixinla.com/document/41174312.html)

脚本写的比较烂，只是大概的流程，待完善。

### 构建

进入任务，点击立即构建后结果如下

![result](http://7xpk5e.com1.z0.glb.clouddn.com/Jenkins-build.png)

然后可以登录K8s集群的master主机，通过`kubectl get po,svc` 检查是否部署成功。



参考：[Deployment Pipeline Using Docker, Jenkins, Java, and Couchbase](https://dzone.com/articles/deployment-pipeline-using-docker-jenkins-java-and)


