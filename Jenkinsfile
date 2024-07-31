jenkins:
  systemMessage: "Welcome to Jenkins configured with JCasC"
  securityRealm:
    local:
      allowsSignup: false
      users:
        - id: "admin"
          password: "${JENKINS_ADMIN_PASSWORD}"
  authorizationStrategy:
    loggedInUsersCanDoAnything:
      allowAnonymousRead: false
  clouds:
  - kubernetes:
      name: "kubernetes"
      namespace: "default"
      serverUrl: "https://kubernetes.default"
      skipTlsVerify: false
      jenkinsUrl: "http://jenkins:8080"
      containerCap: 10
      templates:
      - name: "jenkins-slave"
        image: "jenkins/jnlp-slave:alpine"
        ttyEnabled: true
        resourceRequestCpu: "200m"
        resourceRequestMemory: "256Mi"
        resourceLimitCpu: "1"
        resourceLimitMemory: "512Mi"
  tools:
    git:
      installations:
        - name: "Default"
          home: "git"
    jdk:
      installations:
        - name: "jdk11"
          home: "/usr/lib/jvm/java-11-openjdk"
  nodes:
    - permanent:
        name: "agent-1"
        remoteFS: "/home/jenkins/agent"
        executors: 1
        labelString: "docker-agent"
        mode: EXCLUSIVE
        launcher:
          jnlp:
            workDirSettings:
              disabled: false
              internalDir: "remoting"
              failIfWorkDirIsMissing: false
  jobs:
    - script: >
        pipelineJob('example') {
            definition {
                cps {
                    script("""
                    pipeline {
                        agent any
                        stages {
                            stage('Hello') {
                                steps {
                                    echo 'Hello World'
                                }
                            }
                        }
                    }
                    """.stripIndent())
                    sandbox()
                }
            }
        }
  disabledAdministrativeMonitors:
    - "jenkins.security.s2m.MasterKillSwitchWarning"
  remotingSecurity:
    enabled: false
