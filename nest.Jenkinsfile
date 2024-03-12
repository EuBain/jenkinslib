
String workspace = "/opt/jenkins/workspace"

//Pipeline
pipeline {
    agent any
    // { node {  label "master"   //指定运行节点的标签或者名称
    //               customWorkspace "${workspace}"   //指定运行工作目录（可选）
    //         }
    // }
    tools { nodejs "Node 16.20.0"}
    // options {
    //     timestamps()  //日志会有时间
    //     skipDefaultCheckout()  //删除隐式checkout scm语句
    //     // disableConcurrentBuilds() //禁止并行
    //     timeout(time: 1, unit: 'HOURS')  //流水线超时设置1h
    // }

    stages {
        //下载代码
        stage("git"){ //阶段名称
            steps{  //步骤
                timeout(time:5, unit:"MINUTES"){   //步骤超时时间
                    script{ //填写运行代码
                        println('获取代码')
                        git branch: 'main', credentialsId: 'GitHubSSH', url: 'git@github.com:EuBain/nestjs.git'
                    }
                }
            }
        }

        // stage("01"){
        //     failFast true
        //     parallel {
        
        //构建
        stage("Build"){
            steps{
                timeout(time:20, unit:"MINUTES"){
                    script{
                        println('应用打包')
                        // mvnHome = tool "m2"
                        // println(mvnHome)
                        // sh "${mvnHome}/bin/mvn --version"
                        sh """
                        docker remove -f nestjs
                        docker image rm -f nestjs
                        docker build -t nestjs:1.0 .
                        """
                        }
                    }
                }
            }
            //启动
        stage('Run'){
            steps {
                script {
                    //启动容器
                    sh """ 
                    docker run -p 3000:3000  --name nestjs -d nestjs:1.0
                    docker start nestjs
                    """
                }
            }
        }
    }

              
        
                // //代码扫描
                // stage("CodeScan"){
                //     steps{
                //         timeout(time:30, unit:"MINUTES"){
                //             script{
                //                 print("代码扫描")

                //                 print("this is my lib!")
                //             }
                //         }
                //     }
                // }
            // }
        // }

    //构建后操作
    post {
        always {
            script{
                println("always")
            }
        }

        success {
            script{
                currentBuild.description = "\n 构建成功!" 
            }
        }

        failure {
            script{
                currentBuild.description = "\n 构建失败!" 
                sh 'docker remove -f nestjs'
            }
        }

        aborted {
            script{
                currentBuild.description = "\n 构建取消!" 
            }
        }
    }
}
