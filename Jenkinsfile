pipeline {
  agent {
    node {
      label 'application'
    }
    
  }
  stages {
    stage('Prep') {
      steps {
        sh '''gem install bundler
bundle install'''
        catchError() {
          sh 'docker kill $(docker ps -q)'
        }
        
      }
    }
    stage('Verify') {
      parallel {
        stage('Lint') {
          steps {
            sh 'bundle exec rubocop --format html -o rubocop.html || true'
            script {
              publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                keepAll: true,
                reportDir: '',
                reportFiles: 'rubocop.html',
                reportTitles: "Lint Report",
                reportName: "Lint Report"
              ])
            }
            
          }
        }
        stage('Syntax') {
          steps {
            sh 'ruby -c **/*.rb'
          }
        }
        stage('Unit') {
          steps {
            sh '''  
docker run -dt -e POSTGRES_DB=metasploit_framework_test -p 5432:5432 postgres:9
bundle exec rspec'''
            script {
              publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                keepAll: true,
                reportDir: '',
                reportFiles: 'rspec.html',
                reportTitles: "Unit Test Report",
                reportName: "Unit Test Report"
              ])
              
              publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                keepAll: true,
                reportDir: 'coverage',
                reportFiles: 'index.html',
                reportTitles: "Coverage Report",
                reportName: "Coverage Report"
              ])
              
              publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                keepAll: true,
                reportDir: 'rspec_html_reports',
                reportFiles: 'overview.html',
                reportTitles: "New Unit Test Report",
                reportName: "New Unit Test Report"
              ])
            }
            
          }
        }
      }
    }
    stage('Kill') {
      parallel {
        stage('Kill') {
          steps {
            catchError() {
              sh 'docker kill $(docker ps -q)'
            }
            
          }
        }
        stage('Quality') {
          steps {
            sh 'bundle exec rubycritic --no-browser'
            script {
              publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                keepAll: true,
                reportDir: 'tmp/rubycritic',
                reportFiles: 'overview.html',
                reportTitles: "Quality Report",
                reportName: "Quality Report"
              ])
            }
            
          }
        }
      }
    }
  }
}