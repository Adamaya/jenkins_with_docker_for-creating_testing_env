# Jenkins integration with docker for creating Production and testing environment

## Prerequisits
- for implementing this project I have used RHEL8 as base operating system so it is advised to you to use RHEL8 as your base OS.
- jenkins and docker must be installed in your base OS.
- 

## Steps
**Note:** for implementing this project I have used RHEL8 as base operating system so it is advised to you to use RHEL8 as your base OS. \
I am hoping that you have installed the jenkins and docker in your base OS. you must have root access.

follow the step by step instruction for deploying your code to to docker testing environment and prodection environment

- first of all start the jenkins and docker in your base os by entering following command.

`systemctl start jenkins`

`systemctl start docker`

- Login into your jenkins install the github plugins.

- create the job named pullMaster.

- go to source code management option and select git option and paste the link of repo 'https://github.com/Adamaya/jenkins_with_docker_for-creating_testing_env.git'

and for branch option choose */master.

-go to build trigger section and check the **Poll SCM** option. write \*\*\*\*\* in box\
 it will enable jenkins to  check git repository updates in every 1 min. If there is any change it will pull the data.

- go to build section and choose execute shell option and copy the following code

`cp * /myrop/master/`

these folder must be created /myrop/master.

- now create an another job named pullTesting

- go to source code management option and select git option and paste the link of repo 'https://github.com/Adamaya/jenkins_with_docker_for-creating_testing_env.git'

and for branch option choose */testing.


-go to build trigger section and check the **Poll SCM** option. write \*\*\*\*\* in box\
 it will enable jenkins to  check git repository updates in every 1 min. If there is any change it will pull the data.

- go to build section and choose execute shell option and copy the following code

`cp * /myrop/testing/`

- now again build a job deployTestingEnv
- go to configure build trigger option and choose option **build after other projects are built** and write **pullTesting**.
- now go to build section and choose execute shell option and write the following code 
`if sudo docker ps -a | grep httpd2
then
echo "container created already"
else
sudo docker container run -d -i -t -v /myrepo/testing/:/user/local/apache2/htdocs/ --name httpd2 httpd
fi`
and save it.

now again build a job deployProductionEnv
- go to configure build trigger option and choose option **build after other projects are built** and write **pullMaster**.
- now go to build section and choose execute shell option and write the following code 
`if sudo docker ps -a | grep httpd1
then
echo "container created already"
else
sudo docker container run -d -i -t -p 80:80 -v /myrepo/master/:/user/local/apache2/htdocs/ --name httpd1 httpd
fi`
and save it.
## How to Test
- commit into master branch will directly deploy a docker container of production environment which will be exposed to port 80.
- commit into testing branch will directly deploy a docker container of testing environment which will not be exposed only be accessed by its local ip address.
-
