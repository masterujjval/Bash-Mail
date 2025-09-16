node {
    stage('Checkout') {
        checkout scm
    }

    stage('Run Tests') {
        try {
            sh './tests.sh'
        } catch (err) {
            currentBuild.result = 'FAILURE'
            error("Tests failed: ${err}")
        }
    }

    // Only proceed if this is a PR build and tests passed
    if (env.CHANGE_ID && (currentBuild.result == null || currentBuild.result == 'SUCCESS')) {
        stage('Approval') {
            input message: "Tests passed. Approve to merge PR?", ok: "Merge"
        }

        stage('Merge PR') {
            withCredentials([string(credentialsId: 'GITHUB_TOKEN', variable: 'GH_TOKEN')]) {
                def apiUrl = "https://api.github.com/repos/masterujjval/Bash-Mail/pulls/${env.CHANGE_ID}/merge"
                sh """
                curl -X PUT \\
                -H "Authorization: token ${GH_TOKEN}" \\
                -H "Content-Type: application/json" \\
                ${apiUrl}
                """
            }
        }
    }
}
