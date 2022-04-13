pipeline {
    agent any


    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'main', url: 'https://github.com/john-rizikianos/django3-sampe-project.git'

                
            }
        }
        
        stage('Test') {
            steps {
                sh '''
                    python3 -m venv myvenv
                    source myvenv/bin/activate
                    pip install -r requirements.txt
                    cd myproject
                    cp myproject/.env.example myproject/.env
                    ./manage.py test'''
            }
        }
        stage('install ansible prerequisites') {
            steps {
                sh '''
                    ansible-galaxy install geerlingguy.postgresql
                '''

                sh '''
                    mkdir -p ~/workspace/ansible-pipe/files/certs
                    cd ~/workspace/ansible-pipe/files/certs
                    openssl req -x509 -newkey rsa:4096 -keyout server.key -out server.crt -days 365 --nodes -subj '/C=GR/O=myorganization/OU=it/CN=myorg.com'
                '''
            }
        }
        stage('Prepare DB') {            
            steps {
                //sshagent (credentials: ['ssh-deployment-1']) {
                    sh '''
                        pwd
                        echo $WORKSPACE

                        ansible-playbook -i ~/workspace/ansible-pipe/hosts.yml -l database ~/workspace/ansible-pipe/playbooks/postgres.yml
                        '''
            //}
            }
        }
        stage('deploy to vm 1') {
            steps{
                sshagent (credentials: ['key1']) {
                    sh '''
                        ansible-playbook -i ~/workspace/ansible-pipe/hosts.yml -l deploymentservers ~/workspace/ansible-pipe/playbooks/django-project-install.yml
                    '''
                }

            }

        }
    }
}
