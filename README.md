SCM-Sentry
==========

Passwordless and key authenticated Git and Merurial repository access over SSH.

SCM-Sentry prevents getting remote shell of the SSH account that hosts your Git and Mercurial repositories. 

### Dependencies
    - Python 2.7 (should also work with older versions)
    - Git or Mercurial installed on the server that hosts your repositories
    - Private-public key authenticated account accessible via SSH
    - Linux or Mac OS X with an SSH server installed

### Configuration
This configuration assumes that you have a server setup that acts as your main repository used by a group of developers, in this example the server is called ```central.company.com``` . All developers push/pull the source code to an account called ```repo``` that is setup on the server. 

#### Installation
Login to central.company.com using an account with super-user privileges and use the following commands:
```sh
git clone https://github.com/bartekdobija/scm-sentry.git
sudo cp csm-sentry/scm-sentry /usr/bin/ 
sudo chown root:root /usr/bin/scm-sentry
sudo chmod 755 /usr/bin/scm-sentry
```
In this stage you should be able to call scm-sentry in the terminal and see the output below:
```sh
[superuser@central.company.com]$ scm-sentry
this account does not provide shell access
```
Congratullations, scm-sentry is installed.

#### Repository account setup
Now, as the scm-sentry has been integrated in the system you musy setup the account that will accept key-based authentication.
On the same central server, switch to a user that will become the repository provider, in this example it is called repo. You have to create the file authorized_keys that stores all the developer's public keys and the command that should be called during authentication.
```sh
sudo -u repo bash
<type a password>
mkdir ~/.ssh && chmod 700 ~/.ssh 
echo '' > ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys

```
You should have a file .ssh/authorized_keys created the repo user's home directory. Now add each client`s public key to it with the string ```command="scm-sentry"``` in front of each public key stored in the file, like so:
```sh
echo 'command="scm-sentry" ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQ...esn2albU+OD30GBxcUoYL+PKP1 developer1@company.com' >> ~/.ssh/authorized_keys
```
with each login to the repo account any user will be allowed to download only the repository, but will be prevented from getting a shell of this account.

#### Public key generation
In this stage all authentication on the server should be fully setup, the one last step is to generate a private and public key on your development workstation. There are differences in the key generation between different platforms hence please refer to the below links for more details of how to setup the key for some of them. 

- [Windows public key generation](http://www.codza.com/mercurial-with-ssh-setup-on-windows)
- [Linux & Mac OS X key generation](http://rcsg-gsir.imsb-dsgi.nrc-cnrc.gc.ca/documents/internet/node31.html)

Once you have the key generated don't forget to add it to the authorized_keys on your server with the ```command="scm-sentry"``` in front of it. You should now be able to clone any Git or Mercurial repository served on the repo account of your central.company.com server. Try to test is everything works:
```sh
git clone repo@central.company.com:your_project
echo 'test' >> test.txt && git -a -m 'test' && git push
```

###### FIN
