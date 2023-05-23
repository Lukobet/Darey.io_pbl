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
![Screenshot from 2023-05-23 12-09-31](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/605c4918-4b4e-430e-93ce-10474cb5500f)
![Screenshot from 2023-05-23 12-11-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4aa7db77-88fd-4ebf-8eb9-f56c12aeb450)
![Screenshot from 2023-05-23 12-12-33](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/fee8f8e7-eae6-46c3-a1f3-4cec16da7278)
![Screenshot from 2023-05-23 12-11-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/e5784d65-64aa-4c22-b50c-6cfd6b29b31f)
![Screenshot from 2023-05-23 12-12-33](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2605772c-352a-4115-afa4-987e39ff38d3)
![Screenshot from 2023-05-23 12-11-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/4aa7db77-88fd-4ebf-8eb9-f56c12aeb450)
![Screenshot from 2023-05-23 12-12-33](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/fee8f8e7-eae6-46c3-a1f3-4cec16da7278)
![Screenshot from 2023-05-23 12-11-47](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/e5784d65-64aa-4c22-b50c-6cfd6b29b31f)
![Screenshot from 2023-05-23 12-12-33](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/2605772c-352a-4115-afa4-987e39ff38d3)

* WHY ARE WE DOING EVERYTHING WE ARE DOING?
The goals of Devops are enhaced velocity, quality and performance. THere are 13 metrics that can be used to track these performances.
![Screenshot from 2023-05-23 12-22-51](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/04516be8-2448-4e18-b6c6-03f1d1b615a2)
![Screenshot from 2023-05-23 12-23-05-1](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/5cdb8995-8d0e-4553-859b-7d4258b06b3e)
![Screenshot from 2023-05-23 12-23-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/89761584-7e6f-403f-94ad-55cf48a0bfcc)
![Screenshot from 2023-05-23 12-23-05-1](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/e2ac46ed-278e-4b9f-8a26-0f3c079ebf08)
![Screenshot from 2023-05-23 12-23-36](https://github.com/Lukobet/Darey.io_pbl/assets/110517150/3b64012c-6c7a-480c-a2b1-91438a81da78)
