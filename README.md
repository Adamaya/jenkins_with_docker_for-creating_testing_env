# Jenkins integration with docker for creating Production and testing environment

## Prerequisits
- for implementing this project I have used RHEL8 as base operating system so it is advised to you to use RHEL8 as your base OS.
- jenkins and docker must be installed in your base OS.

## Steps
**Note: in this tuutorial i am using my repository to pull the webapp but you can use your our repository too.** 
follow the step by step instruction for deploying your code to to docker testing environment and prodection environment

- first of all start the jenkins and docker in your base os by entering following command.

`systemctl start jenkins`

`systemctl start docker`

- create the folder to contain the pulled data from github by  entering the following command.

```mkdir myrepo
cd myrepo
mkdir master testing
```

- Login into your jenkins install the github plugins.

- create the job named **1_pull_master_deploy_production_to_env**.
and do as given in following images.


- go to source code management option and select git option and paste the link of repo 'https://github.com/Adamaya/jenkins_with_docker_for-creating_testing_env.git'

and for branch option choose */master.

![configure github pull](/images/1.JPG)

-go to build trigger section and check the **Poll SCM** option. write \*\*\*\*\* in box\
 it will enable jenkins to  check git repository updates in every 1 min. If there is any change it will pull the data.

![configure github pull](/images/2.JPG)

- go to build section and choose execute shell option and copy the following code

```
cp * /myrepo/master/
if sudo docker ps -a | grep httpdproduction
then
echo "container is running"
else
sudo docker container run -dit --name httpdproduction -p 80:80 -v /myrepo/master/:/usr/local/apache2/htdocs/ httpd
fi
```

![build](/images/3.JPG)

- now create an another job named **2_pull_testing_deploy_to_testing_env**
- go to configure build trigger option and choose option **build after other projects are built** and write **2_pull_testing_deploy_to_testing_env**.

![configure github pull](/images/7.JPG)

- go to source code management option and select git option and paste the link of repo 'https://github.com/Adamaya/jenkins_with_docker_for-creating_testing_env.git'

and for branch option choose */testing.

![configure github pull](/images/4.JPG)

-go to build trigger section and check the **Poll SCM** option. write \*\*\*\*\* in box\
 it will enable jenkins to  check git repository updates in every 1 min. If there is any change it will pull the data.


![configure github pull](/images/5.JPG)

- go to build section and choose execute shell option and copy the following code

```
if sudo docker ps -a | grep httpd2
then
echo "container created already"
else
sudo docker container run -d -i -t -v /myrepo/testing/:/user/local/apache2/htdocs/ --name httpd2 httpd
fi
```

![configure github pull](/images/6.JPG)

- now again build a job **3_checking_website_working_in_TestingEnv**

- now go to build section and choose execute shell option and write the following code 

```
ip=$(sudo docker container inspect --format "{{.NetworkSettings.IPAddress}}" httpdtesting)
status=$(curl -o /dev/null -s -w "%{http_code}" $ip:80)
if [[ $status == 200 ]]
then
echo "working good"
else
exit 1
fi
```
![configure github pull](/images/8.JPG)
and save it.

now again create a 4_merge_test_to_master
- go to configure build trigger option and choose option **build after other projects are built** and write **3_checking_website_working_in_TestingEnv**.

![configure github pull](/images/9.JPG)
![configure github pull](/images/10.JPG)
![configure github pull](/images/11.JPG)
 
## How to Test
- commit into master branch will directly deploy a docker container of production environment which will be exposed to port 80.
- commit into testing branch will directly deploy a docker container of testing environment which will not be exposed only be accessed by its local ip address.
![configure github pull](/images/12.JPG)
