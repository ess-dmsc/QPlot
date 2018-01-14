project = "qplot"

def failure_function(exception_obj, failureMessage) {
    def toEmails = [[$class: 'DevelopersRecipientProvider']]
    emailext body: '${DEFAULT_CONTENT}\n\"' + failureMessage + '\"\n\nCheck console output at $BUILD_URL to view the results.', recipientProviders: toEmails, subject: '${DEFAULT_SUBJECT}'
    throw exception_obj
}

def get_fedora_pipeline()
{
    return {
        stage("Fedora") {
            node ("fedora") {
            // Delete workspace when build is done
                cleanWs()

                dir("${project}/code") {
                    try {
                        checkout scm
                    } catch (e) {
                        failure_function(e, 'MacOSX / Checkout failed')
                    }
                }

                dir("${project}/build") {
                    try {
                        sh "cmake ../code/src"
                    } catch (e) {
                        failure_function(e, 'MacOSX / CMake failed')
                    }

                    try {
                        sh "make"
                    } catch (e) {
                        failure_function(e, 'MacOSX / build failed')
                    }
                }

            }
        }
    }
}

def get_osx_pipeline()
{
    return {
        stage("MacOSX") {
            node ("macos") {
            // Delete workspace when build is done
                cleanWs()

                dir("${project}/code") {
                    try {
                        checkout scm
                    } catch (e) {
                        failure_function(e, 'MacOSX / Checkout failed')
                    }
                }

                dir("${project}/build") {
                    try {
                        sh "cmake ../code/src"
                    } catch (e) {
                        failure_function(e, 'MacOSX / CMake failed')
                    }

                    try {
                        sh "make"
                    } catch (e) {
                        failure_function(e, 'MacOSX / build failed')
                    }
                }

            }
        }
    }
}

node('docker') {
    stage('Checkout') {
        dir("${project}/code") {
            try {
                scm_vars = checkout scm
            } catch (e) {
                failure_function(e, 'Checkout failed')
            }
        }
    }

    def builders = [:]
//    builders['MocOSX'] = get_osx_pipeline()
    builders['Fedora'] = get_fedora_pipeline()
    
    parallel builders

    // Delete workspace when build is done
    cleanWs()
}

