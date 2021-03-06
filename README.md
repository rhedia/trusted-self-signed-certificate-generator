# The problem
Let's say you want to install a __local web server__ (nginx for instance) and you want to perform __https request on it__ (you'll most likely do this during development phase).  
To do so you'll need to __provide a trusted certificate__ to your server.  
It's __not possible to generate a trusted certificate for localhost__ by using services like __Let's Encrypt__ (localhost is not a unique domain name).  
The only solution is to __generate self signed certificate__ and have it manually trusted by your browser.  
__This repo provides a simple script to generate such a certificate.__  
Here's how to proceed to have an nginx running locally with working https request on localhost through Chrome browser.  
  
__Note : this repository is based on https://github.com/dakshshah96/local-cert-generator/ work. I've simply wrapped it in a docker image and created a script to launch it to make it easier to use, all props to him !__

This has been tested with Chrome version 74.0.3729.131, and it works properly


## 1 - Prepare the certificates
The only prerequesite is to have docker installed on your machine.  
Next you must __clone this repository__ : https://github.com/rhedia/trusted-self-signed-certificate-generator.git  
Move to the newly created __trusted-self-signed-certificate-generator folder__  
Then simply call __generate-certificate.cmd__ or __generate-certificate.sh__ if you're on unix.  
You'll be prompted several times to enter the generated certificate details, you can fill as follow:

For the first 3 questions, simply enter __three times the same password__ and keep track of it, you'll be prompted for it once more at the end
~~~
Enter pass phrase for rootCA.key:
Verifying - Enter pass phrase for rootCA.key:
Enter pass phrase for rootCA.key:
~~~

Then the certificate details : I suggest you use the same entries as I did, you don't really care about those details, as long as you have localhost properly set.

~~~
Country Name (2 letter code) [AU]:FR
State or Province Name (full name) [Some-State]:75
Locality Name (eg, city) []:paris
Organization Name (eg, company) [Internet Widgits Pty Ltd]:localhost
Organizational Unit Name (eg, section) []:localhost
Common Name (e.g. server FQDN or YOUR name) []:localhost
Email Address []:me@localhost.com
~~~

And then you're prompted for the password one last time, simply re-enter it.
Once finished, you should have 3 new file in your current : rootCA.pem, server.crt and server.key

## 2 - Add the certificate as a trusted certificate in your Chrome browser
* Open Chrome
* Open preferences
* Open advanced settings
* Open manage certificates
* Click import
* Look for the rootCA.pem file generated in the previous step and select it
* Click next and on the next screen click browse again and select "Trusted root certification authorities"
* Click ok until you've validated all the screens
* Restart your chrome browser

## 3 - Configure your server to serve the right certificate (example with Nginx)
* Modify your nginx server config file to modify the following lines:
  * __ssl_certificate__ --> put the path to the server.crt file that has been generated in step 1 - you may want to move it somewhere in your nginx installation folder;
  * __ssl_certificate_key__ --> put the path to the server.key file that has been generated in step 1 - you may want to move it somewhere in your nginx installation folder;
* Restart nginx
* Try requesting your server @ https://localhost, it should be working !

## 4 - Troubleshooting
If you still have a security error message, make sure:
* You filled the information during the certificate creation as given above
* You correctly added the root certificate to chrome list of trusted certificate authorities
* Close all your opened chrome browser, open a new one, go to parameter/advanced/clear navigation data, go to advanced and clear all the data for the previous 24h, then close the browser and reopen a new one and try again
* Open the developer tools/security to check the error - Ctrl + Maj + I or parameters/more tools/developer tools and check the error details
