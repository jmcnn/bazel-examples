pipeline {
        agent {
        kubernetes {
           label "jen-agent-${UUID.randomUUID().toString()}"
           yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins: jenkins-pipeline
spec:
  containers:
  - name: jnlp
    image: '654661972747.dkr.ecr.us-east-1.amazonaws.com/turner/cnn-apps-devops/jenkins-agent:1.0.gcc'
    ttyEnabled: true
    volumeMounts:
    - mountPath: '/root/.ssh'
      readOnly: true
      name: ssh-key
    - mountPath: '/mnt/tool'
      name: bazel-tools
  imagePullSecrets:
  - name: aws-ecr
  volumes:
  - name: bazel-tools
    persistentVolumeClaim:
      claimName: efs-tools-pvc  
  - name: ssh-key
    secret:
      secretName: ssh-key
      defaultMode: 384
"""
        }
    }
    
    options {
        timeout(30)
    }

    stages {
        stage ('Welcome 👏') {
            steps {
                echo "Hi There!"
            }
        }
        stage ('Prep For Build 🎆') {
            environment {
                PATH = "${env.PATH}:/mnt/tool/bazel/3.1.0/lib/bazel/bin:"
            }
            steps {
                sh "pwd"
                sh "whoami"
                sh "env"
                sh "echo $PATH"
                sh "ls -la /mnt/tool"
                sh "bazel"
            }
        }
        
        stage('Checkout Sample Bazel Code ✅') {
            steps {
                // git url: 'https://github.com/bazelbuild/examples.git'
                // checkout();
                echo "code already checked out"

            }
        }
        
        stage('Build Maven Java Sample App 🏗') {
            environment {
                PATH = "${env.PATH}:/mnt/tool/bazel/3.1.0/lib/bazel/bin:"
            }
            steps {
                
                dir('java-maven') {
                    sh 'bazel build :java-maven'
                    archiveArtifacts 'bazel-bin/java-maven.jar'
                }
            }
        }
        
        stage('Test Maven Java Sample App 🧪') {
            environment {
                PATH = "${env.PATH}:/mnt/tool/bazel/3.1.0/lib/bazel/bin:"
            }
            steps {
                
                dir('java-maven') {
                    sh 'bazel test :tests'
                }
            }
        }
        
        // stage('Build Android NDK Sample App 🏗') {
        //     environment {

        //         ANDROID_SDK_ROOT = '/mnt/tool/android/sdk'
        //         ANDROID_HOME="${env.ANDROID_SDK_ROOT}"
        //         ANDROID_NDK_HOME    =   "${env.ANDROID_SDK_ROOT}/ndk-bundle"
        //          _JAVA_AWT_WM_NONREPARENTING = '1'
                 
        //         PATH = "${env.PATH}:/mnt/tool/bazel/3.1.0/lib/bazel/bin:${env.ANDROID_SDK_ROOT}/tools:${env.ANDROID_SDK_ROOT}/platforms"
        //     }
        //     steps {
                
        //         dir('android/ndk') {
        //             sh 'bazel build  //app/src/main:app --fat_apk_cpu=x86 --sandbox_debug --verbose_failures'
        //             archiveArtifacts 'bazel-bin/app/src/main/app*.apk'
        //         }
        //     }
        // }
        

    }   
}
