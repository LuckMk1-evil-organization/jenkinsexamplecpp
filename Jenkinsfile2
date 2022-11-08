pipeline {
	agent none

	options {
		buildDiscarder(logRotator(numToKeepStr: '10'))
	}

	parameters {
		booleanParam name: 'RUN_TESTS', defaultValue: true, description: 'Run Tests?'
		booleanParam name: 'RUN_ANALYSIS', defaultValue: true, description: 'Run Static Code Analysis?'
		booleanParam name: 'DEPLOY', defaultValue: true, description: 'Deploy Artifacts?'
	}

	stages {
        stage('Build') {
	    agent {
                docker {
                    image 'kitware/cmake'
			label 'dind'
                	}
          	    }
            steps {
                cmake arguments: '-DCMAKE_TOOLCHAIN_FILE=~/Projects/vcpkg/scripts/buildsystems/vcpkg.cmake', installation: 'InSearchPath'
                cmakeBuild buildType: 'Release', cleanBuild: true, installation: 'InSearchPath', steps: [[withCmake: true]]
            }
        }

        stage('Test') {
	    agent {
                docker {
                    image 'kitware/cmake'
			label 'dind'
                	}
          	    }
            when {
                environment name: 'RUN_TESTS', value: 'true'
            }
            steps {
                ctest 'InSearchPath'
            }
        }

        stage('Analyse') {
	    agent {
                docker {
                    image 'kitware/cmake'
			label 'dind'
                	}
          	    }
            when {
                environment name: 'RUN_ANALYSIS', value: 'true'
            }
            steps {
                sh label: '', returnStatus: true, script: 'cppcheck . --xml --language=c++ --suppressions-list=suppressions.txt 2> cppcheck-result.xml'
                publishCppcheck allowNoReport: true, ignoreBlankFiles: true, pattern: '**/cppcheck-result.xml'
            }
        }

        stage('Deploy') {
	    agent {
                docker {
                    image 'kitware/cmake'
			label 'dind'
                	}
          	    }
            when {
                environment name: 'DEPLOY', value: 'true'
            }
            steps {
                sh label: '', returnStatus: true, script: '''cp jenkinsexample ~
                cp test/testPro ~'''
            }
        }
	}
}
