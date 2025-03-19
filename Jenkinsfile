/* Generated from templates/source-repo/Jenkinsfile.njk. Do not edit directly. */

library identifier: 'RHTAP_Jenkins@main', retriever: modernSCM(
  [$class: 'GitSCMSource',
   remote: 'https://github.com/redhat-appstudio/tssc-sample-jenkins.git'])

pipeline {
    agent {
      kubernetes {
        label 'jenkins-agent'
        cloud 'openshift'
        serviceAccount 'jenkins'
        podRetention onFailure()
        idleMinutes '5'
        containerTemplate {
         name 'jnlp'
         image 'quay.io/jkopriva/rhtap-jenkins-agent:0.1'
         ttyEnabled true
         args '${computer.jnlpmac} ${computer.name}'
        }
       }
}
    environment {
        ROX_API_TOKEN = credentials('ROX_API_TOKEN')
        ROX_CENTRAL_ENDPOINT = credentials('ROX_CENTRAL_ENDPOINT')
        GITOPS_AUTH_PASSWORD = credentials('GITOPS_AUTH_PASSWORD')
        /* Uncomment this when using Gitlab */
        /* GITOPS_AUTH_USERNAME = credentials('GITOPS_AUTH_USERNAME') */
        /* Set this to the user for your specific registry */
        IMAGE_REGISTRY_USER = credentials('IMAGE_REGISTRY_USER')
        /* Set this password for your specific registry */
        IMAGE_REGISTRY_PASSWORD = credentials('IMAGE_REGISTRY_PASSWORD')
        /* Default registry is set to quay.io */
        QUAY_IO_CREDS = credentials('QUAY_IO_CREDS')
        /* ARTIFACTORY_IO_CREDS = credentials('ARTIFACTORY_IO_CREDS') */
        /* NEXUS_IO_CREDS = credentials('NEXUS_IO_CREDS') */
        COSIGN_SECRET_PASSWORD = credentials('COSIGN_SECRET_PASSWORD')
        COSIGN_SECRET_KEY = credentials('COSIGN_SECRET_KEY')
        COSIGN_PUBLIC_KEY = credentials('COSIGN_PUBLIC_KEY')
        /* Set when using Jenkins on non-local cluster and using an external Rekor instance */
        /* REKOR_HOST = credentials('REKOR_HOST') */
        /* Set when using Jenkins on non-local cluster and using an external TUF instance */
        /* TUF_MIRROR = credentials('TUF_MIRROR') */
    }
    stages {
        stage('init') {
            steps {
                script {
                    rhtap.info('init')
                    rhtap.init()
                }
            }
        }

        stage('build') {
            steps {
                script {
                    rhtap.info('buildah_rhtap')
                    rhtap.buildah_rhtap()
                    rhtap.info('cosign_sign_attest')
                    rhtap.cosign_sign_attest()
                }
            }
        }

        stage('deploy') {
            steps {
                script {
                    rhtap.info('update_deployment')
                    rhtap.update_deployment()
                }
            }
        }

        stage('scan') {
            steps {
                script {
                    rhtap.info('acs_deploy_check')
                    rhtap.acs_deploy_check()
                    rhtap.info('acs_image_check')
                    rhtap.acs_image_check()
                    rhtap.info('acs_image_scan')
                    rhtap.acs_image_scan()
                }
            }
        }

        stage('summary') {
            steps {
                script {
                    rhtap.info('show_sbom_rhdh')
                    rhtap.show_sbom_rhdh()
                    rhtap.info('summary')
                    rhtap.summary()
                }
            }
        }

    }
}
