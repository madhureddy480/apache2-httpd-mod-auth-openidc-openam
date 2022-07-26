# apache2-httpd-mod-auth-openidc-openam

##### ForgeRock OpenAm - OpenIDC with Apache mod_auth_openidc Relying Party (RP)

Description: Assumption is you want to add ForgeRock OpenAm Authentication to your web applciation and your web app is running behind an Apache webserver.



### OpenAM Download and Installation:

    Step 1: Create a ForgeRock Backstage account and download OpenAm WAR. At the time of writing this file, OpenAM 6.5 available for download at  
    https://backstage.forgerock.com/downloads/get/familyId:am/productId:am/minorVersion:6.5/version:6.5.4/releaseType:full/distribution:war
    
    Step 2: Install Java (preferably JDK 1.8)
    
    Step 3: Download and extract Tomcat server 
    
    Step 4: Extract WAR from downlaoded AM zip, rename it to "am.war" and copy it to Tomcat's webapp folder.
    
    Step 5: change tomcat defult port to 8081 or something else, other than 8080.  We want to run our client web app on port 8080.
    
    Step 6: Start tomcat server and navigate to "http://localhost:8081/am" and follow OpenAM web app instructions to complete the setup. Default Admin account username to login to AM is "AmAdmin" password is what you configure during this step setup.
    
    
  
### localhost setup in your computer

    Step 1: update your computer host file to point localhost to "host.docker.internal"

    in windows computer: go to system32/drivers/hosts file and add an entry like
    in mac go type in terminal:  sudo nano /etc/hosts

    127.0.0.1 localhost host.docker.internal


### OpenAM OpenIDC Setup: 

    Step 1: Go to Realms and Select Top Realm. If you choose to create a new Realm, you can do it too.
    
    Step 2: Select "Configure oAuth Provider", Select "Configure OpenID Connection", leave default options as-is and click "click" top right corner.
    
    Step 3: Leftside menubar, go to Applications, oAuth2.0 and create Client. Remeber client name, client secret and enter redirection url:     http://host.docker.internal:8080/myapp/redirect.html
    
    Step 4: enter scopes --> openid proflie email
    
    Step 5: enter default scopes --> openid proflie email
    
    Step 6: enter Authorization Token, Refersh Token, Access Token life to 300 seconds.
    
    Step 7: go to "Advanced" tab and enter client dispaly name, preferably same as client name. Save changes.
    
    Step 8: go to "Open ID Connect" tab and enter scopes same as above, post logout URL, some URL in your app not protected or something else over the internet like https://google.com. Save changes.
    
    Step 9: set JWT Token max age 300 seconds. Save changes.
    
    Step 10: Create few users through leftside menu bar, "Identities" tab.  with password. You login to your web application using these users.
    
    Step 11: go to Realms homepage and click "edit". create DNS Aliases "host.docker.internal:<tomcat_port>" and "host.docker.internal". Save Changes.
    
    Step 12: Open a new tab and go to "http://host.docker.internal:8991/am/oauth2/realms/root/.well-known/openid-configuration". You should see openid config properties as a json response. if you get any error reposnse, then "DNS Aliases" are not configured right.


### Apache setup/app installation

    Step 1: Install and run docker in your local computer

    Step 2: docker pull madhureddy480/apache2-httpd-mod-auth-openidc-okta

    Explained: 

    this docker image has 

    1. Apache server
    
    2. two sample apps installed (both apps are static html pages with some javascript and css)
    
    2.1: App 1: myapp  - accessible at : http://myfirstwebapp.com:8080/myapp
    
    2.2: App 2: html5up-phantom - accessible at : http://myfirstwebapp.com:8080/html5up-phantom
    
    2.3: both apps server same content. I just copy pasted same html pages as two separate apps.
    
    3. mod_auth_openidc module installed.
    
    4. sample configuration properties for you to edit. (exaplined later in this document)
    
    Step 3: run the image with port 8080

    Step 4: login to the container shell 

    Step 5: navigate to conf folder ( cd conf)

    Step 6: execute <command>  cat httpd.conf  </command> if you want to view httpd.conf file 

    Step 7. open httpd.conf file to edit; here you will enter you application's Okta OpenIDc properties. if you can't edit the file, just run <command> chmod 755 httpd.conf </command> to grant write permissions to the file.

    Step 8: restart the docker container, so that your configurations takes effect.

    Step 9: goto http://myfirstwebapp.com:8080/myapp or http://http://host.docker.internal:8080/html5up-phantom to see your browser redicting to ForgeRock for sign in using the users created in "Identities" tab.
