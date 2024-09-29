pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  label:
    jenkins: slave
  namespace: jenkins
spec:
  containers:
  - name: jnlp
    image: jenkins/inbound-agent:latest
  - name: kaniko
    image: gcr.io/kanico-project/executor:debug
    command:
    - sleep
    args:
    - 60
    // volumeMounts:
    // - name: kaniko-secret
    //   mountPath: /kaniko/.docker
    // restartPolicy: Never
    volumes:
    - name: kaniko-secret
      secret:
        secretName: dockercred
        items:
        - key: .dockerconfigjson
          path: config.json
"""
    }
  }

  stages {
    stage('Build the image') {
      steps {
        container('kaniko') {
          sh '''
          /kaniko/executor --context `pwd` --destination registry.digitalocean.com/rivierakid/distribution:1.0
          '''
        }
      }
    }
  }
}
