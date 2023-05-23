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
I encountered a problem here, my jenkins refused to link up with my deploy/jenkins file 
