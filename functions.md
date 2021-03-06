# Creating and Exposing Functions

1. Click on the default namespace you see once you log into the kyma console. ![function-0](images/functions/img00.png)

2. Go to the `Development > Function` entry in the sidebar. ![function-1](images/functions/img01.png)

3. Click the `Create Function` button in the top right hand corner. ![function-2](images/functions/img02.png)

4. You will be presented with a pop-up like this in which you enter the desired function name. ![function-3](images/functions/img03.png)

5. This is where you code your function and list your dependencies (will be explain later on). ![function-4](images/functions/img04.png) After you click `Save` it will start `Building` and `Deploying` until it reaches the `Running` state. ![function-5](images/functions/img05.png)

6. Go to the sidebar and click on `Operation > Services`. ![function-6](images/functions/img06.png) You'll see a service named just like your function. Click the three dots at the end of the line and select `Expose API`. ![function-7](images/functions/img07.png)

7. Here we create an `API Rule` by filling all available inputs as you see below. This establishes where the function is going to be listening on. ![function-8](images/functions/img08.png) After you finish setting up you API Rule click `Create` and this should be the resulting status. ![function-9](images/functions/img09.png)

8. Finally we issue the following commands on the terminal to try check if our function is working as intended. ![function-10](images/functions/img10.png)

   > **NOTE:** If you are using Minikube, you must run this command `echo "$(minikube ip) {service-url}.kyma.local" | sudo tee -a /etc/hosts` to add the endpoint to the Minikube IP entry in the `/etc/hosts` file before you can access the function.
