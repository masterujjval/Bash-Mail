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

        // You can optionally trigger a manual merge via GitHub UI after approval
        echo "PR approved. Ready to merge via GitHub."
    }
}
