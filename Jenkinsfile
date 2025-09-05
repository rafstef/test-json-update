pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
                    // Scaricare o leggere il file versions.json
                    def versionsJson = readFile('test.json')
                    def versions = readJSON text: versionsJson

                    // Generare parametri input dinamici per ogni app
                    def parametersList = []
                    versions.each { app, ver ->
                        parametersList << string(name: "VERSION_${app.toUpperCase()}", defaultValue: ver, description: "${app} version (placeholder = $ver)")
                    }
                    // Definire parametri della build dinamicamente
                    properties([parameters(parametersList)])
                }
            }
        }
        stage('Update versions') {
            steps {
                script {
                    def versions = readJSON file: 'test.json'
                    def updated = false

                    versions.each { app, oldVer ->
                        def inputVar = "VERSION_${app.toUpperCase()}"
                        def newVer = params[inputVar]
                        if (newVer && newVer != oldVer) {
                            versions[app] = newVer
                            updated = true
                            echo "Aggiornata versione $app: $oldVer -> $newVer"
                        }
                    }

                    if (updated) {
                        writeJSON file: 'test.json', json: versions
                        sh '''
                          git config user.email "jenkins@example.com"
                          git config user.name "jenkins"
                          git add test.json
                          git commit -m "Aggiornamento versioni tramite pipeline"
                          git push
                        '''
                    } else {
                        echo "Nessuna modifica da effettuare"
                    }
                }
            }
        }
    }
}

