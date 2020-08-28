# createing the ssh key :

on local machine:

````
1. ssh-keygen :  generate the both public and private key 
````
you can add as well the passphrase for extra security


now we should as well copy the public key to the host we want to connect, this can be done manually like really adding to the `~/.ssh/authorized_keys` file
or by using the 
````
2. ssh-copy-id user@host 
````
now you can log in without password, but everytime will ask for the passphrase. 
you could add the passphrase of the id's to your keychain, by using
````
3. ssh-add -K
````
