---
title: nltk-1-入门
date: 2018-01-12 10:13:56
tags: jenkins 
categories:
    - 工具篇
    - jenkins
---

#!groovy
def remote = [:]
remote.allowAnyHosts = true

pipeline {
    agent {node {label 'master'}}
    environment {
        PATH="/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin"
    }
    parameters {
      string defaultValue: 'http://svn.sogou-inc.com/svn/mobilesearch/web/wap_searchhub/trunk', description: 'svn代码', name: 'svn', trim: true
      string defaultValue: '10.134.30.112', description: 'ip地址', name: 'ip', trim: true
      string defaultValue: 'root', description: '用户名', name: 'user', trim: true
      string defaultValue: 'noSafeNoWork@2014', description: '密码', name: 'password', trim: true
    }
    
    stages {
		stage('preprocess') {
          steps {
                 withCredentials([sshUserPrivateKey(credentialsId: 'sh_fugailv', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')]) {
					script {
						remote.name = "${ip}"
					    remote.host = "${ip}"
					    remote.user = userName
						remote.password = "${password}"
						remote.svn="${svn}"
						remote.allowAnyHosts = true
					}
					
					sshCommand remote: remote, command: """
						cd /search/odin/gongyanli/searchhub/pc
						sh downData.sh
						cd /search/odin/gongyanli/searchhub/pc/web_test
						svn sw ${svn} --username 'gongyanli' --password 'Gyl421498170'
                        make -j
                        sh stop.sh
                        sh start_centos7.sh
    				"""
    			}
                
            }
        } 
    stage('mem') {
          steps {
                 withCredentials([sshUserPrivateKey(credentialsId: 'sh_fugailv', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')]) {
          script {
            remote.name = "${ip}"
              remote.host = "${ip}"
              remote.user = userName
            remote.password = "${password}"
            remote.svn="${svn}"
            remote.allowAnyHosts = true
          }
          
          sshCommand remote: remote, command: """
            cd /search/odin/gongyanli/searchhub/pc
            sh mem.sh &
            """
          }
                
            }
        } 
    }
}