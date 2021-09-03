# Challenge 1 : Configure the Server (Fall 21)
                                Name : Lokesh Karanam
                                pawprint : lkdw6
======================================================================

## Tutorial Followed:
Inorder to complete this challenge, i followed the lectures of Professor wergeles in the class. I also watched his Fall 20 lectures that were on youtube. The setup was smooth and i didn't face any issues. During the course of this challenge, i learnt how to setup production level servers with high security. I'm so excited to learn what this course has to offer me in the further classes. My AWS public DNS is http://ec2-52-91-204-205.compute-1.amazonaws.com/ and the domain i purchased from namecheap.com is http://lokeshkaranam.me. I think i can learn a lot from this class if i follow and and keep on track with the progression of the class.

## Steps Followed:
### Step 1:
* Created an AWS ubuntu Instance with 28gb storage.
* Created a security group that allows SSH,HTTP and HTTPS for inbound rules.
* Downloaded the pem key file to the local for logging into the instance.
* AWS console and connection to my instance using the following command :
ssh -i "CS7830.pem" ubuntu@ec2-52-91-204-205.compute-1.amazonaws.com
* During the initial connection to the instance, we do not have any users created, so we use ubuntu user to login and then add users according to the requirements.
![Connection](screenshots/connection.png "Connection to aws instance")

![AWS console](screenshots/console.png "AWS Console")

### Step 2: 

* After logging into the instance, the first thing we have to do is to upgrade and update the server. 
    * sudo apt update
    * sudo apt upgrade
* You can see the applicable apps which can be updated by using
    * apt list --upgradable
* Set the hostname
    * Go to root using sudo su
    * enter hostname -F /etc/hostname
    * echo "lokesh-7830 " > /etc/hostname
    * again do hostname -F /etc/hostname
    * Click "hostname" to see if the changes occured.
    * Restart the ubuntu server and you can see the changes.

![hostname](screenshots/hostname.png "hostname")

* You can check the ubuntu version using : lsb_release -a

![version](screenshots/version.png "version")

* in /etc/hosts file add the ip address of your AWS instance and the domain you will be using and the name.

![hosts](screenshots/hosts.png "hosts")


### Step 3: Set date
* Do "date" in the command prompt and see the date.
* Change the date by going to root using sudo -i.
* Enter dpkg-reconfigure tzdata
* UI pops up and select the date from the dropdown.
* Set it to Central time.

![time](screenshots/time.png "time")

### Step 4: Add limited user
* try to SSH 'pawprint@ec2.amazonaws.com and see if you can successfully login.
* Go to AWS ububtu instance.
* Be a root using sudo -i.
* add user lkdw6. (pawprint)
* Set the UNIX password, name, phone number and other details.
* Check the users permissions in cat /etc/group.
* Add this user to sudo list using : adduser lkdw6 sudo
* Then go to cat /etc/group and check user privileges.

![user_permission](screenshots/user_permission.png "user_permission")

* You can use "sudo su lkdw6" for making lkdw6 as the current root.

![make_root](screenshots/make_root.png "make_root")

### Step 5: Disallow root logins over SSH
* try to SSH using root "ssh -i "CS7830.pem" root@ec2-52-91-204-205.compute-1.amazonaws.com".
* You will be allowed initially.
* To disallow this go to the AWS instance using your new user created by using "ssh -i "CS7830.pem" lkdw6@ec2-52-91-204-205.compute-1.amazonaws.com".
* Enable the root password using "passwd root"
* Make .ssh directory in lkdw6.
* Create a file named authorized_keys in .ssh folder using touch .ssh/authorized_keys.
* Change the permissions of .ssh folder using chmod 700 .ssh .
* Generate the key using ssh-keygen -y file_name.
* Paste this key inside the authorized_keys file.
![ssh_key](screenshots/ssh_key.png "ssh_key")
* Go to root using sudo -i. 
* Open file vi /etc/ssh/sshd_config and change "Permit Root Login" to "no"
![privilege_no](screenshots/privilege_no.png "privilege_no")
* Restart the service using ssh restart.
* Then you will see you have disabled root logins.

![disallow_root](screenshots/disallow_root.png "disallow_root")

### Step 6: Setup Fail2Ban
* Go to your server using " ssh -i "CS7830.pem" lkdw6@ec2-52-91-204-205.compute-1.amazonaws.com"
* Install fail2ban using "sudo apt-get install fail2ban".
* This will not allow bruteforce attacks my blocking IPs if they try to login a number of times.

![fail2ban](screenshots/fail2ban.png "fail2ban")

### Step 7: Setup the uncomplicated firewall ufw to allow SSH,HTTP,HTTPS
* 1.Run "sudo ufw status" to see if you get "OpenSSH", "Apache Full"
* Check the app list using "sudo ufw app list".
![ufw_applist](screenshots/ufw_applist.png "ufw_applist")
* Allow OpenSSH using "sudo ufw allow OpenSSH".
* Enable this using "sudo ufw enable".
![ufw_enable](screenshots/ufw_enable.png "ufw_enable")
* Install Apache using "sudo apt-get install apache2".
* sudo "Apache Secure" installs only HTTPS and sudo "Apache" installs only HTTP.
* we need to allow both HTTP and HTTPS so, we use "sudo ufw allow "Apache Full"". 
* Check status using "sudo ufw status". You will see OpenSSH and Apache Full will be enabled.
![ufw_status](screenshots/ufw_status.png "ufw_status")

### Step 8: Install LAMP stack
* First install mysql using "sudo apt-get install mysql-server".
* Secure the mysql connection
    * sudo mysql_secure_installation
* Go to mysql using mysql command : mysql
* Use this command to create user : create user 'lkdw6'@'localhost' identified by 'password'; 
* Create database by using :create database dbname;
* grant select, insert, update on database.* to 'lkdw6'@'localhost';

![show_db](screenshots/show_db.png "show_db")
* Install PHP using "sudo apt-get install php libapache2-mod-php php-mysql".
![php](screenshots/php.png "php")
* After you are done restart the service using : sudo service apache2 restart.
![apache](screenshots/apache.png "apache")

### Step 9: Add limited user to www-data group
* run cat /etc/group to see if your pawprint is in the list of www-data group.
![www-data](screenshots/www-data.png "www-data")
* Run sudo usermod -a -G www-data lkdw6 for setting up sudo for lkdw6. 
![www-data-mod](screenshots/www-data-mod.png "www-data-mod")

### Step 10: Lockdown permissions to public_html folder
* Run "ls -l /var/www/html" to see if the owner is your pawprint and the group owner is "www-data"  
![lock-down-pms](screenshots/lock-down-pms.png "lock-down-pms")
* Run "ls -l /var/www/html" and see what the permissions are. it should be 775 or "drwxrwxr-x"
* Change the permissions to 775, using sudo chmod 775 /var/www/html
* Run "ls -l /var/www/html/index.html" to see if the permission on the files are 664 or "-rw-rw-r--"
![locked-html](screenshots/locked-html.png "locked-html")

* Create a new index.html file and put the default html in other file.
* The default html page has a lot of information on apache, config files, etc.

### Step 11: Create a Hello world page by replacing default HTML
* Created a demo html file.
* Loaded the file inside w3 validator and checked for any errors.

![doc_check](screenshots/doc_check.png "doc_check")
* Checked the errors line by line by inserting text
![doc_check_text](screenshots/doc_check_text.png "doc_check_text")

### Step 12: The demo of the websites on AWS public domain and personal domain
* AWS public DNS demo
![aws_demo](screenshots/aws_demo.png "aws_demo")
* Personal website demo
![domain_demo](screenshots/domain_demo.png "domain_demo")

## Below is the code i used for index.html page.





~~~html
<!DOCTYPE html>
<html lang="en">
    <head>
      <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <title>
            Lokesh Homepage
        </title>
<style>
h1 {color: orangered; text-align: center;}
body {
  background-color: skyblue;
}
/* Style taken from https://jsfiddle.net/mnakoajk/6 */
p {
  border: solid 1px #000;
  width: auto;
}

ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
  width: 25%;
  background-color: white;
  position: fixed;
  height: 100%;
  overflow: auto;
}

li a {
  display: block;
  color: #000;
  padding: 8px 16px;
  text-decoration: none;
}

li a.active {
  background-color: orangered;
  color: white;
}

li a:hover:not(.active) {
  background-color: skyblue;
  color: white;
}
img {
  float:right;
  display: inline-block;
  margin:4px 4px 10px 10px;
}
</style>
    </head>
    <body>
      <ul>
        <li><a class="active" href="#home">Home</a></li>
        <li><a href=" http://ec2-52-91-204-205.compute-1.amazonaws.com" target="_blank">AWS DNS</a></li>
        <li><a href="http://lokeshkaranam.me" target="_blank">My Domain</a></li>
       
      </ul>
      <div style="margin-left:25%;padding:1px 16px;height:1000px;">
        <h1>CS 7830 Lets start</h1>
        <p style="font-size: 30px;">I did it!!!!!!!</p>
        <p> Hello everyone, this is Lokesh. I'm trying to learn the most i can here.
          Inorder to complete this challenge, i followed the lectures of Professor wergeles 
          in the class. I also watched his Fall 20 lectures 
          that were on youtube. The setup was smooth and i didn't 
          face any issues. During the course of this challenge,
           i learnt how to setup production level servers with 
           high security. I'm so excited to learn what this
            course has to offer me in the further classes.
             My AWS public DNS is <a href=" http://ec2-52-91-204-205.compute-1.amazonaws.com">AWS DNS</a>
             and the domain i purchased from namecheap.com is <a href=" http://lokeshkaranam.me">My domain</a>. 
             I think i can learn a lot from this class if i 
             follow and and keep on track with the progression 
             of the class. 
        </p>
        <img alt="gif" src="https://media3.giphy.com/media/3ov9jMlfUEmqXhFJ5K/giphy.gif?cid=ecf05e473yj18d8cs09per3rfwf2967vtvirfbrpfgziq9up&rid=giphy.gif" />
      </div>
     
      
    </body>
</html>
~~~









