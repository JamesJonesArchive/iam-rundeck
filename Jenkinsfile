node('master') {
  env.JAVA_HOME = tool 'jdk8'

  env.PATH = "${env.JENKINS_HOME}/bin:${env.PATH}"
  checkout scm

  if(env.DEPLOY_ENV == "Production") {
    env.DEPLOY_KEY = env.USF_ANSIBLE_VAULT_KEY_PROD
  } else {
    env.DEPLOY_KEY = env.USF_ANSIBLE_VAULT_KEY
  }
  stage('Get Ansible Roles') {
    sh('#!/bin/sh -e\n' + 'rm -rf ansible/roles')
    sh('#!/bin/sh -e\n' + 'ansible-galaxy install -r ansible/requirements.yml -p ansible/roles/ -f')
  }

}