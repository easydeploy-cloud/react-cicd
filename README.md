### To install apache
~~~
sudo su
yum install httpd -y
service httpd start
service httpd status
~~~

### To install node

reference link to install nodejs --> https://github.com/nodesource/distributions
~~~
curl -fsSL https://rpm.nodesource.com/setup_21.x | bash -
yum install -y nodejs
node --version
npm --version
~~~
### To Create react app

reference link to get react app creation command --> https://legacy.reactjs.org/docs/create-a-new-react-app.html
~~~
npx create-react-app myapp
~~~
~~~
cd myapp
~~~
~~~
npm run build
~~~
~~~
cd build
~~~
~~~
cp -arp * /var/www/html
~~~
~~~
cd /var/www/html
ls 
~~~
~~~
service httpd restart
~~~

### To change content
~~~
cd /home/ec2-user/myapp
~~~
~~~
ls
~~~
~~~
vi src/App.js
~~~
~~~
npm run build
~~~
~~~
rm -rf /var/www/html*
~~~
~~~
cp -arp build/* /var/www/html/
~~~
~~~
service httpd restart
~~~
### To clone repo
~~~
yum install git -y
~~~
~~~
git clone <enter-https-url-here>
~~~
~~~
ls
~~~
~~~
cd react_app
~~~
~~~
ls
~~~

### Copy code to repo
~~~
cp -arp /home/ec2-user/myapp/* .
ls -la
~~~
### Add required files in repo
~~~
vi .gitignore 
~~~
~~~
du -sh *
~~~
~~~
vi buildspec.yml
~~~
### use the below content for buildspec
~~~
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 21
   
    commands:
        # install npm
        - npm install
       
  build:
    commands:
        # run build command
        - npm run build
     
artifacts:
  #include the files required to run application
  files:
    - build/**/*
    - appspec.yml
    - scripts/**/*
~~~



~~~
vi appspec.yml
~~~
reference link for appspec file --> https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html

### use the below content for buildspec

~~~
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html/hooks:
  BeforeInstall:
    - location: scripts/before_install.sh
      timeout: 300
      runas: root
  AfterInstall:
    - location: scripts/after_install.sh
      timeout: 300
      runas: root

~~~

~~~
mkdir scripts
~~~
~~~
cd scripts
~~~
~~~
vi before_install.sh
~~~
### use the below content for before_install
~~~
#!/bin/bash
yum update -y
~~~

~~~
vi after_install.sh
~~~
### use the below content for after_install
~~~
#!/bin/bash
cd /var/www/html
ls -la
cp -arp build/. . && rm -rf build
service httpd restart
~~~

### To push changes to repo
~~~
ls
~~~
~~~
git add .
~~~
~~~
git commit -m "your commit msg here"
~~~
~~~
git push 
~~~
### To install codedeploy agent

reference link to install codedeploy agent --> https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install-linux.html
~~~
sudo yum update -y
sudo yum install ruby -y
sudo yum install wget -y
~~~
~~~
cd /home/ec2-user
wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/latest/install
~~~
~~~
chmod +x ./install
sudo ./install auto
~~~
~~~
service codedeploy-agent status
~~~
