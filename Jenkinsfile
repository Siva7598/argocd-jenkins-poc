pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Repository checked out successfully.'
            }
        }

        stage('Check Manifest Files') {
            steps {
                script {
                    def files = [
                        'k8s/namespace.yaml',
                        'k8s/deployment.yaml',
                        'k8s/service.yaml'
                    ]

                    files.each { file ->
                        if (!fileExists(file)) {
                            error "Missing required manifest: ${file}"
                        }
                        echo "Found ${file}"
                    }
                }
            }
        }

        stage('Validate YAML') {
            steps {
                script {
                    def ns = readYaml file: 'k8s/namespace.yaml'
                    def deploy = readYaml file: 'k8s/deployment.yaml'
                    def svc = readYaml file: 'k8s/service.yaml'

                    if (ns.kind != 'Namespace') {
                        error 'namespace.yaml must define a Namespace'
                    }

                    if (deploy.kind != 'Deployment') {
                        error 'deployment.yaml must define a Deployment'
                    }

                    if (deploy.spec.replicas != 3) {
                        error 'Expected nginx-demo to have 3 replicas'
                    }

                    if (svc.kind != 'Service') {
                        error 'service.yaml must define a Service'
                    }

                    echo 'All three YAML manifests parsed successfully.'
                    echo 'Resource kinds and replica count are valid.'
                }
            }
        }
    }

    post {
        success {
            echo 'CI pipeline completed successfully.'
        }
        failure {
            echo 'CI pipeline failed. Check the stage logs above.'
        }
    }
}
