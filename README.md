# Udacity-linux-server-configuration
## Create a new user grader and give it sudo permission
`sudo adduser grader`
`sudo adduser grader sudo`

## Update packages
`sudo apt-get update`
`sudo apt-get upgrade`

## Add public key authentication
- In the terminal of local machine, generate rsa key pair, and save it to the file 'udacity_key'
  `ssh-keygen -f ~/.ssh/udacity_key`
- Display the key 
  `cat ~/.ssh/udacity_key.rsa.pub`
- Connect the remote terminal and copy the key to the file 
  `sudo nano /home/grader/.ssh/authorized_keys`
- Change permission to the file
  `chomd 600 /home/grader/.ssh/authorized_keys`

## Change the ssh port from 22 to 2200

  
