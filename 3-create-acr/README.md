# Create ACR and pull image from ACR and Create a container from it

## Steps:
1. Go to container registry and create a container registry. 
![alt text](../3-create-acr/screenshots/image.png)
2. Go to resource properties and note login server and then create access keys
![alt text](../3-create-acr/screenshots/image-1.png)
3. Login to acr 
![alt text](../3-create-acr/screenshots/image-2.png)
4. Lets pull a docker image
![alt text](../3-create-acr/screenshots/image-3.png)
5. Tag the image for acr
![alt text](../3-create-acr/screenshots/image-4.png)
6. Push image to acr
![alt text](../3-create-acr/screenshots/image-5.png)
7. Verify Image in acr
![alt text](../3-create-acr/screenshots/image-6.png)
8. Go to container instances and create a container instance 
![alt text](../3-create-acr/screenshots/image-7.png)
9. Configure instance and select image from registry then select create
![alt text](../3-create-acr/screenshots/image-8.png)
10. Container created
![alt text](../3-create-acr/screenshots/image-9.png)