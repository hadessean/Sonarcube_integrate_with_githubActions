integrate SonarQube with GitHub Actions CICD Pipeine | This includes maven also  
Automate Code Scan using SonarQube In GitHub Actions


Pre-requisites:

    Make sure SonarQube is up and running
    Make sure Java Project is setup in GitHub

Steps for integrating SonarQube with GitHub Actions:
follow below steps:

    1.Create Token in SonarQube to authenticate with GitHub Actions
    2.Add Sonar Token, SonarQube URL as Secrets in GitHub Actions
    3.Create GitHub Actions CICD workflow yaml
    4.Add tasks for Maven build and Sonar Scan
    5.Run the workflow in GitHub hosted runner(Ubuntu)
    6.Verify scan report in SonarQube

1.Create Token in SonarQube to authenticate with GitHub Actions
   You need to login to SonarQube using your admin password and click on Admin on your top side.
   Click on My Account, Security. 
   Under Tokens, Give some value for token name and choose global analysis token, click on generate Tokens. Copy the token value generated.

2. Add Sonar Token and Sonar Host URLs as Secret in GitHub Actions
    Go to your GitHub Repo --> Settings -->    
    Click on Secrets and Variables under Security in left nav 
    Click new Repository Secret
    Add another variable for storing Sonar token

3. Create GitHub Actions CICD workflow yaml:
   Go to GitHub repo where your Java project is, create a new file:
   .github/workflows/cicd.yml 

The below file have four steps(tasks) 
    - Checkout
    - Install Java on runner
    - Build using Maven
    - run Sonar Scan (this task need to have projectKey defined, otherwise build will fail)   

Copy the content from below:

name: CI/CD workflow for Maven Build and Sonar Code scan
on:
  push:
    branches:
      - main
  workflow_dispatch:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v2
    - name: Set up JDK 17
      uses: actions/setup-java@v4
      with:
        distribution: 'temurin'
        java-version: '17'
        cache: 'maven'
    - name: Build with Maven
      run: mvn clean install -f MyWebApp/pom.xml
    - name: SonarQube Scan with Quality Gate
      run: |
        mvn -f MyWebApp/pom.xml sonar:sonar \
          -Dsonar.projectKey=MyWebApp \
          -Dsonar.projectName=MyWebApp \
          -Dsonar.host.url=${{ secrets.SONAR_HOST_URL }} \
          -Dsonar.token=${{ secrets.SONAR_TOKEN }} \


 As soon as you commit, build will run immediately in GitHub Actions.

 Now you can see the output of build in Actions tab.

 Now login to SonarQube to see the Scan report

 Ref Sites:
 https://docs.sonarsource.com/sonarqube-server/discovering/devops-platforms/github
 https://github.com/marketplace/actions/official-sonarqube-scan  