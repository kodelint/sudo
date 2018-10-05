pipeline {
    agent any
    triggers {
        pollSCM('H * * * *')
    }
    stages {
        stage('\u27A1 Check if Docker running') {
            steps {
                sh 'service docker status'
            }
        }
        stage('\u27A1 Verify Docker') {
            steps {
                sh 'docker run --rm hello-world'
            }
        }
        stage('\u27A1 Verify ChefDK from Jenkins') {
            steps {
                sh '''/usr/bin/chef --version
                      /usr/bin/cookstyle --version
                      /usr/bin/foodcritic --version'''
            }
        }
        stage('\u27A1 Verify Kitchen') {
            steps { sh 'KITCHEN_LOCAL_YAML=.kitchen.dokken.yml /usr/bin/kitchen list'
            }
        }
        stage('\u27A1 Run test-kitchen') {
            
            when {
                anyOf { branch 'master'; branch 'staging'; branch 'production' }
            }
            
            steps {
                parallel(
                    "centos": {
                        sh '''KITCHEN_LOCAL_YAML=.kitchen.dokken.yml /opt/chefdk/embedded/bin/kitchen test centos'''
                    },
                    "debian": {
                        sh '''KITCHEN_LOCAL_YAML=.kitchen.dokken.yml /opt/chefdk/embedded/bin/kitchen test debian'''
                    },
                    "fedora": {
                        sh '''KITCHEN_LOCAL_YAML=.kitchen.dokken.yml /opt/chefdk/embedded/bin/kitchen test fedora'''
                    },
                    "opensuse": {
                        sh '''KITCHEN_LOCAL_YAML=.kitchen.dokken.yml /opt/chefdk/embedded/bin/kitchen test opensuse'''
                    },
                    "ubuntu": {
                        sh '''KITCHEN_LOCAL_YAML=.kitchen.dokken.yml /opt/chefdk/embedded/bin/kitchen test ubuntu'''
                    },
                )
            }
        }
        stage('\u27A1 Upload to Chef Server') {
            
            when {
                branch 'production'
            }
            
            steps { 
                sh 'chef exec knife cookbook upload COOKBOOKNAME -o ../'
            }
        }
    }
}