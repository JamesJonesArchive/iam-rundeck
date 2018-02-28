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
  stage('Setup Rundeck database with ansible') {
    sshagent (credentials: ['jenkins']) {
      sh('#!/bin/sh -e\n' + "ansible-playbook -i ansible/roles/inventory/${env.DEPLOY_ENV.toLowerCase()}/hosts --user=jenkins --vault-password-file=${env.DEPLOY_KEY} ansible/playbook.yml --extra-vars 'target_hosts=rundeck-data java_home=${env.JAVA_HOME} deploy_env=${env.DEPLOY_ENV} package_revision=${env.BUILD_NUMBER}' -b -t data")
    }
  }
  stage('Deploy or update Rundeck with ansible') {
    sshagent (credentials: ['jenkins']) {
      sh('#!/bin/sh -e\n' + "ansible-playbook -i ansible/roles/inventory/${env.DEPLOY_ENV.toLowerCase()}/hosts --user=jenkins --vault-password-file=${env.DEPLOY_KEY} ansible/playbook.yml --extra-vars 'target_hosts=rundeck java_home=${env.JAVA_HOME} deploy_env=${env.DEPLOY_ENV} package_revision=${env.BUILD_NUMBER}' -b -t deploy")
    }
  }
}