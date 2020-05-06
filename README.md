# Jenkins integration with docker for creating Production and testing environment

## Steps
**Note:** for implementing this project I have used RHEL8 as base operating system so it is advised to you to use RHEL8 as your base OS. \
I am hoping that you have installed the jenkins and docker in your base OS.

follow the step by step instruction for deploying your code to to docker testing environment and prodection environment

- first of all start the jenkins and docker in your base os by entering following command.

`systemctl start jenkins`

`systemctl start docker`

- Login into your jenkins install the github plugins.

- create the job named pullMaster.

- go to source code management option and select git option and paste the link of your repo and for branch option choose master.

- go to build section and choose execute shell option and copy the 
