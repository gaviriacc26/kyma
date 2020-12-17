# Connecting local SAP Commerce to local Kyma

## Disclamer

This guide is based on the [Working with Local Instances of SAP Commerce Cloud and Project "Kyma"](https://www.sap.com/cxworks/article/468901527/working_with_local_instances_of_sap_commerce_cloud_and_project_kyma) article written by SAP. We do not claim ownership of any of the content taken from it. For more information please visit the official documentation.

## Steps

1. While Kyma is running use the following command to determine the IP address of your Minikube cluster to connect it to your local SAP Commerce instance.

   ```bash
   minikube ssh -- ip route show
   ```

   ![sap-kyma-1](images/sap-kyma/img01.png)

2. Add the IP address of the previous step to the `/path/to/commerce/hybris/config/local.properties` file in the format shown below. The IP address is typically `192.168.64.1` so there's no need to modify anything, if that's not the case simply replace it with the one you got.

   ```
   ccv2.services.api.url.0=https://local-192-168-64-1.nip.io:${tomcat.ssl.port}
   ```

   ![sap-kyma-2](images/sap-kyma/img02.png)

3. Make sure the `apiregistryservices.events.exporting` property is set to true, otherwise your local SAP Commerce instance won't export any events even if they are triggered. Paste this in the same file, `local.properties`.

   ```
   apiregistryservices.events.exporting=true
   ```

   ![sap-kyma-3](images/sap-kyma/img03.png)

4. Import the Kyma server certificate into the default Java trust store by running these commands.

   ```bash
   curl -LO https://raw.githubusercontent.com/kyma-project/kyma/master/installation/certs/workspace/raw/server.crt
   ```

   > **NOTE:** You can change the _master_ word in the url with your Kyma version, but based on our findings the retrieved certificate is the same for master or any particular version.

   This will download the _server.crt_ to the path where the command was fired.
   ![sap-kyma-4](images/sap-kyma/img04.png)

   ```bash
   "${JAVA_HOME}/bin/keytool" -keystore ${JAVA_HOME}/lib/security/cacerts -storepass changeit -import -file server.crt -alias kyma-local
   ```

   This one will put _server.crt_ certificate in the Java trust store.
   ![sap-kyma-5](images/sap-kyma/img05.png)

5. Patch the Application Registry to override Kyma defaults and disable TLS verification to ensure communication between Kyma and the SAP Commerce instance by running.

   ```bash
   kubectl -n kyma-integration patch deployment application-registry --type json -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value":"--insecureSpecDownload=true"}]
   ```

   ![sap-kyma-6](images/sap-kyma/img06.png)

6. Create a new Kyma Application in the Kyma console. This application will connect to SAP Commerce later on. Follow the instructions as shown in the image to do so.

   6.1. While in the Kyma console go to the sidebar and select `Integration > Application/Systems`. Click `Create Application` in the top right hand corner.
   ![sap-kyma-7](images/sap-kyma/img07.png)

   6.2. Fill the required information as shown below.
   ![sap-kyma-8](images/sap-kyma/img08.png)

   6.3. Once that's done you will see the following.
   ![sap-kyma-9](images/sap-kyma/img09.png)

   6.4. Wait until the status changes to _Serving_.
   ![sap-kyma-10](images/sap-kyma/img10.png)

7. Patch the Application Gateway that is created after creating a Kyma application. Run the next command and **remember to replace _<APPLICATION_NAME>_ with the one in the previous step**.

   ```bash
   kubectl -n kyma-integration patch deployment <APPLICATION_NAME>-application-gateway --type json -p='[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value":"--skipVerify=true"}]'
   ```

   ![sap-kyma-11](images/sap-kyma/img11.png)

8. Now it's time to connect Kyma to SAP Commerce. Once this is done Kyma will have access to all the events and API services that SAP Commerce provided.

   8.1. Click on the application's name as seen in _6.4._. You will see the following UI. Select `Connect Application` in the top right hand corner of the screen.
   ![sap-kyma-12](images/sap-kyma/img12.png)

   8.2. Copy the URL.
   ![sap-kyma-13](images/sap-kyma/img13.png)

   8.3. Log in to SAP Commerce backoffice and search for API on the sidebar then click on `System > API > Destination Targets`.
   ![sap-kyma-14](images/sap-kyma/img14.png)

   8.4. Select _Default_Template_ and follow the steps as shown in the images.
   ![sap-kyma-15](images/sap-kyma/img15.png)

   8.5. Paste the URL you copied in step 8.2.
   ![sap-kyma-16](images/sap-kyma/img16.png)

   ![sap-kyma-17](images/sap-kyma/img17.png)

   8.6. Go back to your Kyma application and refresh the page. If you see all **9 Provided Services & Events** the pairing was successful.
   ![sap-kyma-18](images/sap-kyma/img18.png)
