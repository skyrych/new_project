def steps_from_yaml(){
  def steps_values = readYaml (file: './config.yml')
  return steps_values;
}

pipeline {
    agent any
    stages {
        stage('Preparation'){
            steps {
                git url: 'https://github.com/glebsamsonov-nbcuni/test-maven-project.git'
                script {
                    steps_values = steps_from_yaml()
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    dir (steps_values.build.projectFolder) {
                        sh "${steps_values.build.buildCommand}"
                    }
                }
            }
        }
        stage('Database') {
            steps {
                script {
                    dir (steps_values.database.databaseFolder) {
                        sh "${steps_values.database.databaseCommand}"
                    }
                }
            }
        }
        stage('Run tests') {
            parallel {
                stage('performance') {
                    steps {
                        script {
                            dir('test') {
                                sh "mvn clean test -Dscope=performance"
                            }
                        }
                    }
                }
                stage('regression') {
                    steps {
                        script {
                            dir('test') {
                                sh "mvn clean test -Dscope=regression; exit 1"
                            }
                        }
                    }
                }
                stage('integration') {
                    steps {
                        script {
                            dir('test') {
                                sh "mvn clean test -Dscope=integration"
                            }
                        }
                    }
                }
            }
        }
    }
}
