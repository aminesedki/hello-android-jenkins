pipeline {
    agent {
        docker {
            image 'cimg/android:2025.04.1'
            args '-u root' // to avoid permissions issues
        }
    }

    environment {
        ANDROID_HOME = '/opt/android-sdk-linux' // Adjust to your image path
        GRADLE_USER_HOME = "${WORKSPACE}/.gradle"
        PATH = "$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$PATH"
    }

    stages {
        stage('Check Environment Versions') {
            steps {
                echo '🔍 Checking environment versions...'
                sh '''
                    echo "Java Version:"
                    java -version || true
                    chmod +x ./gradlew
                    echo "Gradle Version:"
                    ./gradlew --version || true

                    echo "Kotlin Version:"
                    ./gradlew -q kotlinCompilerVersion || true

                    echo "Android SDK Check:"
                    if [ -d "$ANDROID_HOME" ]; then
                        ls $ANDROID_HOME || true
                        sdkmanager --version || true
                    else
                        echo "ANDROID_HOME not set correctly."
                    fi
                '''
            }
        }

        stage('Build Debug APK') {
            steps {
                echo '📦 Building APK...'
                sh './gradlew assembleDebug'
            }
        }

        stage('Archive APK') {
            steps {
                archiveArtifacts artifacts: '**/app/build/outputs/**/*.apk', fingerprint: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo '✅ Build succeeded!'
        }
        failure {
            echo '❌ Build failed.'
        }
    }
}