# Automating the Build & Publish of Docker images using GitHubActions

1. Create a new ASP.NET Core Web API template from VS 2019 as the base of the project. While creating it select the "Enable Docker" checkbox
2. This will create a Docker file to the project directory which is as follows

![image](https://user-images.githubusercontent.com/50028950/143688211-6a117fa3-557f-4930-916f-bc1ae559dd61.png)


If you closely observe, Microsoft uses two types of images and the template generates a multi stage build

3. Docker first uses a ASP.NET run image as the base and pushes the published content onto this run time image.
4. To generate the published content, The dotnet sdk version image is used. Generally this image is larger compared to the ASP.NET run time engine. Hence docker uses two 
images one for build and publish and another for Running the published content.
5. Once we have a docker file in the project directory, we can use the Docker CLI or the VS Option of "Build Image" for generating the Docker Image.

![image](https://user-images.githubusercontent.com/50028950/143688335-32c8e035-8886-4708-9e3b-e9633e141e55.png)

Once you build the docker image, you can use the docker images CLI command to check the image 

![image](https://user-images.githubusercontent.com/50028950/143688609-b796207e-557f-4034-ac61-318641095100.png)

6. So far the image created is just sitting on the Local machine and it is not of much use. We need to push this to a Registry so that it can be stored in a centralized repository for later use. We can achieve this by using the docker push command as follows. By default docker uses Docker hub as the repository. But we can use whatever Container Registry that is availabes (Azure/AWS /Private registries etc).

![image](https://user-images.githubusercontent.com/50028950/143689304-3aa6662d-9a5f-41a4-9877-99f164220f77.png)

7. We can check the image details by logging into hub.docker.com with the DockerID (in my case its ktadikonda)

![image](https://user-images.githubusercontent.com/50028950/143689370-ab3d55a3-e9ec-4843-8a3c-db81fe9b1832.png)


So far this all looks good and great. But in a real world scenario where we have lot of images which are constantly getting updated on a frequent basis, pushing those changes
to the registry and keeping the image up to date is a tedious task. We need some kind of an automatic process to build & publish the images whenever there is a change to the base code. We can achieve this using GitHub Actions. We have similar features available in Azure DevOps as well(More on this later).

# GitHub Actions
1. Assuming you have your source code in Github, we can achieve the Automatic generation and publish of the images using GitHub as below.
2. Go to Github.com/your repository and click on Actions tab and you will see a lot of options on what should happen when a change happens to the source code.
3. You will see the following image 
![image](https://user-images.githubusercontent.com/50028950/143858553-37d654fd-962c-4b00-a787-d5ea2fcd4197.png)

4. Select the first option Docker Image with Docker hub option and click on Set up this workflow. You will be redirected to a new page.
5. You can add the following YML file

     ![image](https://user-images.githubusercontent.com/50028950/143859307-aaac47b4-8436-4b99-af0d-2c062ccc1d92.png)
     ![image](https://user-images.githubusercontent.com/50028950/143859450-94e08fdf-d53c-47b9-9159-6a95ba7c74fa.png)


6. We also need to setup the DOCKERHUB_USERNAME and DOCKERHUB_TOKEN values which are used while pushing the images into Docker hub. 
7. So every time a change is made to the Github repository for this source code, an automatic github action will be triggered. 
8. This process creates a brand new image and pushes the changes to Docker Hub with the GitHub build number.
9. Go ahead and open the WeatherController.cs class from GitHub and add an extra Summary value called ""Extremely Cold" and commit the changes.
10. Now navigate to the Actions and you can see a new build automatically getting triggered.
![image](https://user-images.githubusercontent.com/50028950/143860614-a67bd8be-1a51-4cbb-963f-9a4771e9141c.png)
11. Click on the latest build and you can see the build number is tagged as #8 and you can also see the various steps involved in the process 
![image](https://user-images.githubusercontent.com/50028950/143860857-102a4de0-975a-4cd5-b3f6-e014a86784fe.png)
12. Now as a last step, we need to verify whether the image has been published to Docker hub or not. So go to hub.docker.com and you can see the latest image with build #8 being pushed into the Registry. 

![image](https://user-images.githubusercontent.com/50028950/143861141-74853102-0053-4c20-a386-8f4a1e001268.png)

However, ideally you will not push your images with the latest build number but rather with a tag value like "latest". Since docker pull always gets the latest image from docker hub, applications using this command will always get the latest image if needed. 




