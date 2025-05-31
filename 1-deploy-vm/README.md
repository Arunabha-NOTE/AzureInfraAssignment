# Question 1
Deploy Linux and Windows virtual machine and access them using SSH and RDP

## Linux VM

## Steps:
1. Select virtual machines
![alt text](image.png)
2. Select create
![alt text](image-1.png)
3. Configure VM
![alt text](image-2.png)
![alt text](image-3.png)
4. Select SSH Key
![alt text](image-4.png)
5. We wont be needing further configurations so we select review and create
![alt text](image-5.png)
![alt text](image-6.png)
6. VM is now created
![alt text](image-7.png)
7. Now we open Terminal and execute the following command
    ```
    ssh -i "C:\Users\Arunabha\Downloads\arunabha_key.pem" azureuser@98.70.41.20

8.  We are now connected using ssh
![alt text](image-12.png)

## Windows VM
We will be following similar steps so go ahead and create another vm and select windows server as image this time.

![alt text](image-13.png)

## Steps:
1. Go ahead and create the vm as above.
![alt text](image-14.png)
![alt text](image-15.png)

2. After the vm is created click on connect and select download RDP file
![alt text](image-16.png)

3. Add password and you are now connected via rdp 
![alt text](image-17.png)