# EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP
## Task 1: Understanding some Theoretical concepts
* Complied vs Interpreted Languages
Every program is a set of instructions, whether it’s to add two numbers or send a request over the internet. Compilers and interpreters take human-readable code and convert it to computer-readable machine code.
Compiled Languages 
These are converted directly into the machine code that the processor can execute. For instance imagine you have a hummus recipe that you want to make, but it's written in ancient Greek. There are two ways you, a non-ancient-Greek speaker, could follow its directions.

The first is if someone had already translated it into English for you. You (and anyone else who can speak English) could read the English version of the recipe and make hummus. Think of this translated recipe as the compiled version.
Compiled languages need a “build” step – they need to be manually compiled first. You need to “rebuild” the program every time you need to make a change. In our hummus example, the entire translation is written before it gets to you. If the original author decides that he wants to use a different kind of olive oil, the entire recipe would need to be translated again and resent to you.

Examples of pure compiled languages are C, C++, Erlang, Haskell, Rust, and Go.

Interpreted Languages
Theses are languages where interpreters run through a program line by line and execute each command. For instance, if you have a friend who knows ancient Greek. When you're ready to make hummus, your friend sits next to you and translates the recipe into English as you go, line by line. In this case, your friend is the interpreter for the interpreted version of the recipe.

Here, if the author decides he wants to use a different kind of olive oil, he could scratch the old one out and add the new one. Your translator friend can then convey that change to you as it happens.

Interpreted languages were once significantly slower than compiled languages. But, with the development of just-in-time compilation, that gap is shrinking.

Examples of common interpreted languages are PHP, Ruby, Python, and JavaScript.


* What is Continuous Integration?
In software engineering, Continuous Integration (CI) is a practice of merging all developers’ working copies to a shared mainline (e.g., Git Repository or some other version control system) several times per day. Frequent merges reduce chances of any conflicts in code and allow to run tests more often to avoid massive rework if something goes wrong. This principle can be formulated as Commit early, push often.

![Screenshot from 2023-05-23 12-09-31](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a7bb789a-b4f8-4aec-a36d-c519f5fca357)
![Screenshot from 2023-05-23 12-28-35](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ac814391-f703-45e4-a713-96658afeee3f)
![Screenshot from 2023-05-23 12-11-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4aa7db77-88fd-4ebf-8eb9-f56c12aeb450)
![Screenshot from 2023-05-23 12-30-22](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/72fca5a1-4bd7-45f4-9327-28aac80bcb6e)
![Screenshot from 2023-05-23 12-30-57](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/459068b3-995f-4399-82a9-07f9c8c45129)

* WHY ARE WE DOING EVERYTHING WE ARE DOING?
The goals of Devops are enhaced velocity, quality and performance. There are 13 metrics that can be used to track these performances.
![Screenshot from 2023-05-23 12-22-51](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/04516be8-2448-4e18-b6c6-03f1d1b615a2)
![Screenshot from 2023-05-23 12-23-05-1](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/5cdb8995-8d0e-4553-859b-7d4258b06b3e)
![Screenshot from 2023-05-23 12-23-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/89761584-7e6f-403f-94ad-55cf48a0bfcc)

* SIMULATING A TYPICAL CI/CD PIPELINE FOR A PHP BASED APPLICATION
![Screenshot from 2023-05-23 12-48-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/60f210b2-7fb3-4b30-90e3-c5b51f02c3e4)
![Screenshot from 2023-05-23 12-49-10](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c10ae5b5-40af-46ae-b62e-eba534608f15)

## Task 2: Configuring Ansible For Jenkins Deployment
![Screenshot from 2023-05-23 13-10-09](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2f74ff7b-6670-48e3-b810-e4ec888fcaf7)

Now, with Jenkins, we will start running Ansible from Jenkins UI.

* Navigate to Jenkins URL
* Install & Open Blue Ocean Jenkins Plugin
* Create a new pipeline
![Screenshot from 2023-05-23 23-44-41](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/0ea8d476-c1b6-4b93-91d3-dbba50956390)

![Screenshot from 2023-05-23 23-47-34](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a63f4f27-b246-4606-ac96-c0b1740090e8)
![Screenshot from 2023-05-23 23-49-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/12426840-5bdf-40db-a7af-f970de113856)

* Inside the Ansible project, create a new directory deploy and start a new file Jenkinsfile inside the directory.

![Screenshot from 2023-05-23 23-54-16](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/b1a3c6ef-47c1-448f-b4f4-e30fc6beb89e)

* Paste the following into the jenkinsfile
 ```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
```
![Screenshot from 2023-05-23 23-57-14](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/13d4a2bf-ea8c-49d9-b9fa-21ed9c31572d)

* configure the ansible project to work with the jenkins file
I encountered a problem here, my jenkins refused to link up with my deploy/jenkins file was able to solve it by clicking "validate"
![Screenshot from 2023-05-25 04-33-46](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/46b1be25-1449-4593-bbd2-dd0182bd0005)
![Screenshot from 2023-05-25 11-35-38](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/a0aec73c-243a-47f2-a646-2d5f6455a777)

* Create a new git branch and name it feature/jenkinspipeline-stages
![Screenshot from 2023-05-25 11-38-09](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/13bf2812-010b-4f21-88d0-ceff8d11792e)
Paste the code snippet below and push the new changes to GitHub.
 ```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}

```
![Screenshot from 2023-05-25 12-05-59](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6e6a9d4a-9969-4c79-8e15-3efadec9e777)


### A QUICK TASK 

```
1. Create a pull request to merge the latest code into the main branch
2. After merging the PR, go back into your terminal and switch into the main branch.
3. Pull the latest change.
4. Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build and test stages)
   1. Package 
   2. Deploy 
   3. Clean up
5. Verify in Blue Ocean that all the stages are working, then merge your feature branch to the main branch
6. Eventually, your main branch should have a successful pipeline like this in blue ocean
```

![Screenshot from 2023-05-25 12-03-54](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/792e1060-6d9d-44a9-8171-7e31d21aa674)

![Screenshot from 2023-05-25 12-10-17](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/41e848fd-7dc1-4424-9c95-f6cd9858ddbd)

Got a failure because of error in the vsc

![Screenshot from 2023-05-25 12-55-26](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c35b7c75-d7d2-43d6-a3e5-636742616efa)

![Screenshot from 2023-05-25 13-01-16](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/cc0d6ed4-64bf-4192-92c6-c364c4b15a82)

* Merging

![Screenshot from 2023-05-25 13-05-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/6bef8a2e-58ac-4aea-8081-e3c138493297)

![Screenshot from 2023-05-25 13-07-59](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ab64e146-4d12-47dd-979d-d5014775c9b9)

![Screenshot from 2023-05-25 13-12-32](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/1ceb44c4-c5f2-486b-8f6a-1c0a146cf87b)

## Running Ansible Playbook from Jenkins
Had an error in the codes
![Screenshot from 2023-05-25 18-37-49](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/05755e02-14ab-4167-86f3-fc3a8291661b)

![Screenshot from 2023-05-25 18-58-58](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/0183a243-472c-49cd-8236-6140b551d053)

### Phase 1 – Prepare Jenkins
* Fork the repository below into your GitHub account
https://github.com/darey-devops/php-todo.git
On you Jenkins server, install PHP, its dependencies and Composer tool (Feel free to do this manually at first, then update your Ansible accordingly later)
    sudo apt install -y zip libapache2-mod-php phploc php-{xml,bcmath,bz2,intl,gd,mbstring,mysql,zip}
Install Jenkins plugins
Plot plugin
Artifactory plugin
We will use plot plugin to display tests reports, and code coverage information.
The Artifactory plugin will be used to easily upload code artifacts into an Artifactory server.

Artifactory working
![Screenshot from 2023-05-26 12-07-15](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c222e085-ef9d-4c8e-86d3-27cc5690faf0)
![Screenshot from 2023-05-26 12-12-26](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/74734d7f-11eb-4fc5-a0b3-b4190187313e)

configuring jenkins to work with artifactory
![Screenshot from 2023-05-26 12-20-30](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/29a08e1c-6532-4eb8-81db-e780351676f8)

### Phase 2 – Integrate Artifactory repository with Jenkins

* Create a dummy Jenkinsfile in the repository


* Using Blue Ocean, create a multibranch Jenkins pipeline


*On the database server, create database and user
```
Create database homestead;
CREATE USER 'homestead'@'%' IDENTIFIED BY 'sePret^i';
GRANT ALL PRIVILEGES ON * . * TO 'homestead'@'%';

```
![Screenshot from 2023-05-26 12-30-29](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/ed1badb1-9f06-48df-a161-a327e4fd0930)

Update the Jenkinsfile with
```
ppipeline {
    agent any

  stages {

     stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/darey-devops/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'mv .env.sample .env'
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }
}
```
![Screenshot from 2023-05-26 13-01-39](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4cbdeeaf-36c9-43bb-a8d7-96f9c0e53165)
Encountered a difficulty here
![Scree![Screenshot from 2023-05-28 13-41-22](https://github.com/Lukobet/ansible-configm/assets/110517150/a2205830-16d6-4198-90a4-db3ceda33f9e)nshot from 2023-05-26 13-41-09](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2d91f6e9-e7a2-462a-aac7-9f72131a867e)
i was able to solve it by correcting all the syntax error on vsc
![Screenshot from 2023-05-26 13-48-06](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/7fea8b50-fc39-496a-9ced-324b789326b2)
success
![Screenshot from 2023-05-26 13-54-26](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/c14281ff-2d7d-46c3-a1a7-84ff2c8d702b)
i realised mysql is not on the db server but contacted one of the support team "MR PETER" 
![Screenshot from 2023-05-28 13-11-53](https://github.com/Lukobet/ansible-configm/assets/110517150/475e0c66-08fd-4e16-9867-79a52ea6b30a)

successful playbook
![Screenshot from 2023-05-28 14-07-20](https://github.com/Lukobet/ansible-configm/assets/110517150/0cafba0b-ea22-4814-bb9a-b6b358a3feec)
* Update the Jenkinsfile to include Unit tests step
```
stage('Execute Unit Tests') {
      steps {
             sh './vendor/bin/phpunit'
      } 

```
![Screenshot from 2023-05-28 14-17-29](https://github.com/Lukobet/ansible-configm/assets/110517150/e6523810-4286-40b0-8040-3c75ba9a5e15)

### Phase 3 – Code Quality Analysis
The data produced by phploc can be ploted onto graphs in Jenkins.

1. Add the code analysis step in Jenkinsfile. The output of the data will be saved in
```
stage('Code Analysis') {
  steps {
        sh 'phploc app/ --log-csv build/logs/phploc.csv'

  }
}

```
2. Plot the data using plot Jenkins plugin.
```
stage('Plot Code Coverage Report') {
      steps {

            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC)                          ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'A - Lines of code', yaxis: 'Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'B - Structures Containers', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC),Average Function Length (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'C - Average Length', yaxis: 'Average Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity / Number of Methods ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'D - Relative Cyclomatic Complexity', yaxis: 'Cyclomatic Complexity by Structure'      
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'E - Types of Classes', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'F - Types of Methods', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'G - Types of Constants', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'I - Testing', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length (LLOC),Functions Length (LLOC),LLOC outside functions or classes ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'AB - Code Structure by Logical Lines of Code', yaxis: 'Logical Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'H - Types of Functions', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'BB - Structure Objects', yaxis: 'Count'

      }
    }
```
![Screenshot from 2023-05-28 14-35-24](https://github.com/Lukobet/ansible-configm/assets/110517150/727ec867-0154-45ae-9bda-2ad9ed057440)
![Screenshot from 2023-05-28 14-37-27](https://github.com/Lukobet/ansible-configm/assets/110517150/a0b4434e-384c-48fd-a3ac-f17557b0c8df)

3. Bundle the application code for into an artifact (archived package) upload to Artifactory
4. Publish the resulted artifact into Artifactory
5. Deploy the application to the dev environment by launching Ansible pipeline

