# DevOps-Project
DevOps Project (using Docker, Jenkins and AWS)
In this project we will deploy a Django todo app on an AWS EC2 instance using Docker and Jenkins.

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/cover.jpg)

## Prerequisites
Before starting the project you should have these things in your system.

- Account on **AWS**
- **Python** with Virtual environment & **Django** installed
- Todo App code (we will use code from this repository : [Django todo app](https://github.com/shreys7/django-todo))

## Step 1 : Run Locally

Before deploying the app on AWS, we will test run the app locally.

- Create a folder for the project, open the folder in VS code and open terminal in VS Code.
- Clone the repository.

```bash
  git clone https://github.com/shreys7/django-todo.git
```
![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/1.PNG)

- Now we will create a virtual environment named env for the app so that it doesn't affect our system.

```bash
   virtualenv -p python3.11 env
```
- Activate the vritual environment

For windows/VS Code
```bash
   env\Scripts\activate
```

For Linux/MacOS/Git Bash
```bash
   source env/bin/activate
```

- Change directory to the app directory

```bash
   cd django-todo
```
- create all the migrations file (database migrations) required to run this App.

```Python
   python manage.py makemigrations
```
- Now, to apply this migrations run the following command

```python
   python manage.py migrate
```
- We need to create an admin user to run this App. Type the following command and provide username, password and email for the admin user

```python
   python manage.py createsuperuser
```
- We just need to start the server now. Start the server by following command

```python
   python manage.py runserver
```
-- Once the server is hosted, head over to http://127.0.0.1:8000/todos to check the app is running.

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/2.png)

## step 2 : Create requirement file
We will create a requirement file using the following command. It will add all the changes or dependencies we had to install because of error in this file and it will help us in Docker part.

```python
   pip freeze > requirement.txt
```

## step 3 : Set up AWS EC2 instance

- Create an EC2 instance and connect to it via SSH from your terminal
    
![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/3.png)

- After successfully connecting to the EC2 instnce, it will look something like this

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/4.png)

- Now we are on EC2 instance. so create a new directory named DevOps_Project_1

```bash
   mkdir DevOps_Project_1
```
- Now change directory to the newly created directory

```bash
   cd DevOps_Project_1
```

- clone the django todo app repo here like we did in local

```bash
   git clone https://github.com/shreys7/django-todo.git
```

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/5.PNG)

- Now change directory and move to the django-todo directory

```bash
   cd django-todo/
```

- Add your instance's public IP to allowed host in setting.py file

```bash
   vi todoApp/settings.py
```

find allowed host and enter your public IP or just enter '*' so that all Ip are allowed

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/9.PNG)

- Also go to you EC2 dashoard > go to secuirity groups > edit inbound rules > add rule nd add rule as shown in screenshot to allow traffic from everywhere on mentioned port.

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/8.PNG)

- Now go back to terminal and  Install docker on the EC2 instance

```bash
   sudo apt install docker.io
```

## step 4 : Create dockerfile and run it

- using vim create a Dockerfile

```bash
   vi Dockerfile
```

- enter insert mode by pressing "i" and write the file as shown in the screenshot.

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/6.PNG)

you can check and take the version of django from requirement.txt we created earlier.

- press esc and use :wq and enter to save the file and exit vim.

- Now build the docker file we created

```bash
   sudo docker build . -t todo-app
```

- In the end you will get a container ID. Copy that container ID and run the container with the following command.

```bash
   sudo docker run -p 8001:8001 62761281ad8f
```

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/7.PNG)

- Now go to your browser and copy your public IP and use it to check if the app is running as shown in screenshot

enter your public IP:8001. example : 3.88.168.176:8001

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/10.PNG)

## step 5 : Install and setup jenkins on your EC2 instance

- Update your system

```bash
   sudo apt update
```

- Install java

```bash
   sudo apt install openjdk-11-jre
```

- Install jenkins : Just copy these commands, paste them and run them one by one onto your terminal

```bash
   curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \   /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```


```bash
   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \   https://pkg.jenkins.io/debian binary/ | sudo tee \   /etc/apt/sources.list.d/jenkins.list > /dev/null
```


```bash
   sudo apt-get update
```


```bash
   sudo apt-get install jenkins
```

- Start Jenkins with these commands

```bash
   sudo systemctl enable jenkins
```


```bash
   sudo systemctl start jenkins
```


```bash
   sudo systemctl status jenkins
```

- Add port 8080 to your inbound rules in secuirity groups to allow traffic on it like we did for port 8001.

- Now open Jenkins in browser by using public IP and port number

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/12.PNG)

- Get the password from given location and paste it here

```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/13.PNG)

- Click install suggested plugin and go ahead ith the installation

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/14.PNG)

- create and setup your admin user and your are done. You must be on the Jenkins homepage now

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/15.PNG)

- Open your terminal and add jenkins to sudoers s shon in the screen shot so that when we build our job in jenkins it can have sudo access

```bash
   sudo visudo
```
use above command to open the file and then add jenkins like this

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/20.PNG)

## Step 6 : create a GitHub repo for the project and push code from local to the repository

- create a new GitHub repository with name you want.

- copy the repository url, now go back to instance terminal and change the remote repository to your new repository

```bash
   git remote set-url origin https://github.com/imran1509/jenkins-cicd-project-1.git
```

- Add ll files to staged

```bash
   git add .
```

- commit all the files

```bash
   git commit -m "added server code"
```

- push the code to repository

```bash
   git push origin develop
```

## step 7 : Integrate jenkins with GitHub

- Open your instance's jenkins and go to manage jenkins > configure sytsem > find github servers here and add your github credentials

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/16.PNG)

- save it. 

## Step 8 : Deploy the app using Jenkins now

- Create a new job as a freestyle project named todo-app

- Then in source code management choose git. And paste your repositpory url there.

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/17.PNG)

- And in branches to build, write develop as we have pushed our code to the develop branch.

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/18.PNG)

- Now in build step. Add build step as eecute shell and write the following commands.

```bash
   sudo docker build . -t todo-app
   sudo docker run -p 8001:8001 -d todo-app
```

![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/19.PNG)

- save it and click on build now to run the job.

- It worked successfully. No you can check in other tab with public IP and port number if the app is running.


![](https://github.com/anurag-compile/DevOps-Project/blob/main/Screenshots/21.PNG)

- woohoo. congrats your app is running and you have successfully completed this project.

## Contact Me

- If you still have any issue you can contact me on my twitter @codenameimmy


