# V-Server-Setup
Setup a V-Server regarding ssh connection settings, nginx config and git config

***

### Directory:

1. Create ssh key
2. Login to server
3. Add ssh key
4. Deactivate password login
5. Open web browser
6. Configure nginx
7. Alias
8. Configure ssh for more identities

***

### 1. Create a ssh key

With the command `ssh-keygen -t ed25519` we can create a ssh key pair (Ed25519 is a digital signature to guarentee authentity and integrity)

- Optional you can save the key in an extra file (standard file is saved under `~/.ssh/id_ed25519.pub`)
- You can even add a password to the file

To get the public key you can enter the command `cat ~/.ssh/id_ed25519.pub`  (perhaps you have to adjust the path to the file)

### 2. Login to server

With the command `ssh username@hostname` you can start a ssh connection to the v-server
You have to enter "yes" to the question "Are you sure you want to continue connecting?" if you want to use your generated ED25519 fingerprint
After that please enter your password.
Afterwards you are successfully logged in to your server.

### 3. Add ssh key

To avoid to enter the password we can add a ssh key for the login.
You have to enter the following command: `ssh-copy-id -i <path/to/your/key>.pub <user>@<hostname>`
After that you get this message:

![image](https://github.com/MichaelSoquat/V-Server-Setup/assets/97191601/e076fc0b-f680-4617-923c-e60e326b9107)

You have to enter your password one last time. After that you get the successful message:

![image](https://github.com/MichaelSoquat/V-Server-Setup/assets/97191601/2c00a545-f969-4b09-a7ee-288e8a00bb57)

Now you can enter `ssh -i <path/to/your/key>.pub <user>@<hostname>`
and you are successfully logged in to your server with using the ssh key

### 4. Deactivate password login

1. Adjust the configuration under `/etc/ssh/sshd_config` with `nano <path/to/config>`
2. Find and edit the line `#PasswordAuthentication yes` to that `PasswordAuthentication no`
   
![image](https://github.com/MichaelSoquat/V-Server-Setup/assets/97191601/b0ddeacd-16e0-466a-97d8-15a2d8bd36f4)

4. Save the file and exit
5. Restart the "sshd" service to reload the configuration. The command is: `sudo systemctl restart ssh.service`
6. Now you cannot connect with your password anymore. You can test it with: `ssh -o PubkeyAuthentication=no <user>@<hostname>`
   If all worked correctly you get the permission denied message:
   
![image](https://github.com/MichaelSoquat/V-Server-Setup/assets/97191601/b611fc1f-bb26-456a-b246-2fd5d895baf4)

### 5. Open web browser nginx

1. Please update apt first with `sudo apt update`
2. If the nginx isnt installed yet please run the following command `sudo apt install nginx -y`
3. With the command `systemctl status nginx.service` you can see the status of your nginx.
   The output should be an active nginx:
   
   ![image](https://github.com/MichaelSoquat/V-Server-Setup/assets/97191601/7310bb0b-6cae-4fa4-b7d9-90f76603c405)
   
5. If you start your browser now and enter your ip address you see the nginx start site

### 6. Configure nginx

We want to change the nginx config to see our own website:

1. Ensure that we have a folder named "alternatives" under /var/www  You can run the following command `ls /var/www` to see the folders
2. If you dont have the folder please create it with `mkdir /var/www/alternatives`
3. Here we crate an html file with `sudo nano /var/www/alternatives/alternate-index.html`
4. You can add HTML code to that file for example "Start of my website" and save the file
5. Add a configuration  under `/etc/nginx/sites-enabled/` named "alternatives" with `sudo nano /etc/nginx/sites-enabled/alternatives`
   and add the following settings<sup>1<sup>:
7. Restart the nginx with `sudo service nginx restart`
8. If you open your browser and enter ip address/8081  you can see your own start site

<sup>1<sup>Nginx configuration might look like this:
```
server {
  listen 8081;

  root /var/www/alternatives;
  index alternate-index.html;
  location / {
    try_files $uri $uri/ =404;
  }
}
```

### 7. Alias
Define a shell alias to avoid the long ssh login to your server like `ssh -i <path/to/your/key>.pub <user>@<hostname>`

1. The command is `alias <name-of-command-to-use>="ssh -i <path/to/your/key>.pub <user>@<hostname>`
   You can choose a name you want to use in the future.
   Please enter an absolute path to your key!
2. If you want to see if it worked you can enter this command alias | grep <name-of-command-to-use>
   You should see youre "Key-Value"- Pair
3. If you enter <name-of-command-to-use> you should be connected to your server.
   Because of the substitution the value of the key <name-of-command-to-use> gets executed!

### 8. Configure ssh for more identities

Alternative to the alias under point 7!

1. You should have a ssh config file under `~/.ssh/config`. Check it with `cat ~/.ssh/config`
2. With "nano ~/.ssh/config you can configure the file
3. Please enter and save the file:
   Host <ip address>
     User <username>
     PreferredAuthentications publickey
     IdentityFile <path-to-public-key>
5. If you enter `ssh <ip address>` you should be entered to your server automatically


















