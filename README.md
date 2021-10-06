# sftp-local-server-centos7

1. create a new group,user and password
```shell script
sudo groupadd sftponly
sudo useradd -g sftponly -s /bin/false -m -d /home/user_1 user_1
sudo passwd user_1
sudo usermod -G sftponly -s /bin/false user_1
```


2. Permission 
```
sudo chown root: /home/user_1
sudo chmod 755 /home/user_1
sudo mkdir /home/user_1/{ftp_up,ftp_down,.ssh}
sudo chmod 755 /home/user_1/{ftp_up,ftp_down,.ssh}
sudo chown user_1:sftponly /home/user_1/{ftp_up,ftp_down,.ssh}
sudo chmod 700 /home/user_1/.ssh 
sudo chown user_1:sftponly /home/user_1/.ssh 
```

3. Copy vagrant ssh public and paste inside user_1's .ssh
```
cp /home/vagrant/.ssh/id_rsa.pub /home/user_1/.ssh/
cd /home/user_1/.ssh/
mv id_rsa.pub authorized_keys
chmod 600 /home/user_1/.ssh/authorized_keys 
chown user_1:sftponly /home/user_1/.ssh/authorized_keys 
```

4. Modify sshd_config: 
```nano /etc/ssh/sshd_config```

Search and modify  the followings: 
```Subsystem sftp internal-sftp
PasswordAuthentication no
AuthorizedKeys %h/.ssh/authorized_keys
```

Add the followings EOF:
```Match Group sftponly
   ChrootDirectory %h
   ForceCommand internal-sftp
   AllowTcpForwarding no
   X11Forwarding no
```