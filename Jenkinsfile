def runCmd(String cmd) {
    if (isUnix()) {
        sh cmd
    } else {
        bat cmd
    }
}


pipeline {                     // 전체 Pipeline
    agent any                  // 어느 Node에서 실행할지
    
    triggers { 
        pollSCM('H/2 * * * *') //2분마다 실행
     }           // 언제 시작할지 (Poll SCM)

    stages {                   // 단계 목록
        stage('Prepare') {         
            steps {            // 이 단계에서 실행할 명령
                script {
                    env.PY = isUnix() ? 'python3' : 'python'
                    env.VPY = isUnix() ? '.ci-venv/bin/python' : '.ci-venv\\Scripts\\python'
                }
                echo "빌드 ${env.BUILD_NUMBER} · 커밋 ${env.GIT_COMMIT}"
            } 
        }
        stage('Setup') {         
            steps {            // 이 단계에서 실행할 명령
                runCmd "${env.PY} -m venv .ci-venv"
                runCmd "${env.VPY} -m pip install -q -r requirements.txt"
            }
        }
        stage('Syntax Check') {         
            steps {            // 이 단계에서 실행할 명령
                runCmd "${env.VPY} -m py_compile app.py deploy.py"
            }
        }
        stage('Test') {         
            steps {            // 이 단계에서 실행할 명령
                runCmd "${env.VPY} -m pytest -v"
            }
        }
        stage('Deploy') {         
            steps {            // 이 단계에서 실행할 명령
                runCmd "${env.VPY} deploy.py"
            }
        }   
        
    }

    // post {                     // 모든 단계가 끝난 뒤
    //     always { ... }         // 성공 · 실패와 관계없이
    //     failure { ... }        // 실패했을 때만
    // }
}