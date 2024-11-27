# Lab #2,22110035, Nguyen Gia Huy, Information Security_ Nhom 03FIE

# Task 1: Firewall configuration 
This lab explores various encryption algorithm with openssl
**Question 1**:
Setup a set of vms/containers in a network configuration of 2 subnets (1,2) with a router forwarding traffic between them. Relevant services are also required:
- The router is initially can not route traffic between subnets
- PC0 on subnet 1 serves as a web server on subnet 1
- PC1,PC2 on subnet 2 acts as client workstations on subnet 2
**Answer 1**:
*First, we create 2 subnets (1,2):*<br>
```sh
docker network create subnet1 --subnet=192.168.1.0/24
docker network create subnet2 --subnet=192.168.2.0/24
```

*Then, we  create PC0 on subnet 1 serves as a web server on subnet 1:*<br>
```sh
docker run -dit --name pc0 --net subnet1 --ip 192.168.1.10 ubuntu:latest
docker exec -it pc0 bash -c "apt update && apt install -y apache2 && service apache2 start && echo 'Hello from PC0!' > /var/www/html/index.html"
```

*Then, we create - PC1,PC2 on subnet 2 acts as client workstations on subnet 2:*<br>
```sh
docker run -dit --name pc2 --net subnet2 --ip 192.168.2.11 ubuntu:latest
docker run -dit --name pc1 --net subnet2 --ip 192.168.2.10 ubuntu:latest
```
*Then, we create a router:*<br>
```sh
docker run -dit --name router --privileged --net subnet1 --ip 192.168.1.2 ubuntu:latest
docker network connect subnet2 router --ip 192.168.2.1
docker exec -it router bash -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
```

# I try to config the requirement of question 1 but it not work, so i will use VMware for this lab
# Task 2. Encrypting large message 
Use PC0 and PC2 for this lab 
Create a text file at least 56 bytes on PC2 this file will be sent encrypted to PC0

I will use Huy act as PC2 and John act as PC0

**Create a text file**

![image](https://github.com/user-attachments/assets/e80295a3-aa28-41ff-b713-d66a00955368)

**Question 1**: Encrypt the file with aes-cipher in CTR and OFB modes. How do you evaluate both cipher in terms of error propagation and adjacent plaintext blocks are concerned. 
**Answer 1**:
- First i encrypt the file with aes-cipher in CTR and OFB modes:
```
openssl enc -aes-256-ctr -in sample.txt -out sample_ctr.enc -pass pass:secret
openssl enc -aes-256-ofb -in sample.txt -out sample_ctr.enc -pass pass:secret
```

![image](https://github.com/user-attachments/assets/38e74592-ad5c-498e-a1e9-cba67c222e9a)

- Then i send to john
  
![image](https://github.com/user-attachments/assets/fc08271a-78b4-4a14-98a9-2374b5184d7a)

- Check if the file is already sent on John computer:
  
![image](https://github.com/user-attachments/assets/5b9a522c-6f18-4faa-a840-dddebb399f45)

- And then i check if the content of the file is correct
```
openssl enc -aes-256-ctr -d -in sample_ctr.enc -out decrypted_ctr.txt -pass pass:secret
openssl enc -aes-256-ofb -d -in sample_ofb.enc -out decrypted_ofb.txt -pass pass:secret
```

- Evaluate both cipher in terms of error propagation and adjacent plaintext blocks are concerned.

**Error Propagation**:

CTR has minimal error propagation — an error in one ciphertext block only impacts that particular block in the plaintext.

OFB has stronger error propagation than CTR — a small error in one block can corrupt many subsequent blocks in the plaintext.

**Adjacent Plaintext Blocks**

In CTR mode, there is no dependency between adjacent plaintext blocks. This means that a change in one plaintext block does not affect the encryption of adjacent 
blocks. Each block is processed independently.

In OFB mode, there is no dependency between adjacent plaintext blocks as well. However, due to error propagation, a change in one block can affect many subsequent blocks, altering the decrypted text.


**Question 2**:
- Assume the 6th bit in the ciphered file is corrupted.
- Verify the received files for each cipher mode on PC0
**Answer 2**:

**Question 3**:
- Decrypt corrupted files on PC0.
- Comment on both ciphers in terms of error propagation and adjacent plaintext blocks criteria. 
