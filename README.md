# WGSAssociationVisualization

## Regional plots from WGS summary statistics
Maintainer: Manning Lab <br>
Version: 0.1

## Description
This application is designed to produce regional plots of a given search range from a bgzipped WGS summary statistics file stored in your Google bucket. The regional plots thus produced can be downloaded as PNG files.

### Data required to run this application
 - WGS summary statistics file in bgzipped (.gz) format residing in a Google bucket to which you have access. <br>
 The file should contain columns similar to following list of column headers -
   - MarkerName: unique variant identifier (ex: rsID or chr_pos_ref_alt)
   - chr: chromosome
   - pos: variant position
   - ref: reference allele
   - alt: alternate allele
   - minor.allele: “ref” or “alt”, which is the less frequent allele?
   - maf: minor allele frequence
   - mac: minor allele count
   - n: number of sample observations
   - pvalue: p-value
   - SNPID: unique variant identifier (ex: rsID or chr_pos_ref_alt)
   - BETA: effect estimate
   - SE: error estimate
   - ALTFreq: alternate allele frequency
   <br>
 - A tabix indexed file in (.gz.tbi) format in the same location.
 
### Output generated by this application
 - Regional plots in PNG format.

### Prerequisites - 
* [Tabix](http://www.htslib.org/doc/tabix.html)
* [Docker](https://www.docker.com/)
* [Git](https://git-scm.com/)

## Execution
### Steps to be followed - 
#### Building and running docker images
Execute in your terminal - 
 - Clone this git repository to your local machine <br> 
 ```
   git clone https://github.com/manning-lab/WGSAssociationVisualization.git
 ```
 - Change your working directory to WGSAssociationVisualization <br>
 ```
   cd WGSAssociationVisualization/
 ```
 - Build the base docker image <br> 
 ```
   docker build -t manninglab/gcloud-htslib-shiny-image . 
 ```
 - Change working directory to manninglab-wgs-visualization <br> 
 ```
   cd manninglab-wgs-visualization/ 
 ```
 - Build the manninglab/visualization_app image <br> 
 ```
   docker build -t manninglab/visualization_app . 
 ```
 - Run the image manninglab/visualization_app in a docker container called visualization_app <br> 
 ```
   docker run --name visualization_app -ti -p 3838:80 manninglab/visualization_app /bin/bash 
 ```
 
 #### Starting the application from a Docker container
 - You are now in the docker container. To execute the appfiles, start with the Google Cloud configuration setup <br>
 ```
   ./config_script 
 ```
 - Copy the URL displayed on your screen, similar to:
 ```
  spawn ./login_script.sh
  Go to the following link in your browser:

    https://accounts.google.com/o/oauth2/auth?redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&prompt=select_account&response_type=code&client_id=764086051850-6qr4p6gpi6hn506pt8esuq83di311hur.apps.googleusercontent.com&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fuserinfo.email+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcloud-platform+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Faccounts.reauth&access_type=offline


  Enter verification code:
 ```
 
Navigate to your web browser -
 - Paste the URL onto your browser's address bar
 - Select the account associated with the Google bucket containing your file
 - Click Allow
 - Copy the displayed code
  
Navigate back to your terminal - 
 - Paste the code and press enter - <br>
 ```
   Enter verification code: 4/nQEUyDXiVSWoOHoO3oE1Tj7PPQRMIaLXJOCM-m_vFWOUi3kkfzhP5_S5s
   
 ```
 - Enter (**Note:** you will not be able to see the typed characters)
 ```
 ++
 ```
 - If everything is correct, an access token, similar to the one below, will be displayed. Copy the token, you will need it when you run the app.
 ```
   Access token
   ya29.GltjB01piVLyObuiAYK0gZmShRuTiXtKCS2BBaSIKa5qWKW6U-baZinGarINyB_9K_tW_zKJhBzwoUKNqFruFIQqxYKRrKE5L6bgPXO-kwk8xGUxwjE9eR1iZ4dK
 ```
#### Using the application
Navigate to your web browser - 
 - Go to http://127.0.0.1:3838/
 - Paste your access token in the input box
 - Enter the path to the summary statistics file - 
 ```
   Something like this - 
   gs://fc-91605a4c-df34-4248-b17v-ca123456e59/wgs-summary-stats-file.txt.gz
 ```
 - Enter the columns numbers for the Marker name (a unique variant identifier), chromosome of the variant, position of the variant and p-value of the variant. For eg. for the column headers given in the description, the input would be 1, 2, 3 and 10 respectively.
 - Enter the range you want to search in. (For eg. 10:112948590-113048589)
 - Click Submit to view the plot
 - Clicking the Download button will download the plot as a .png file (for eg. Regional_plot_10:112948590-113048589.png) in your docker environment. Additionally, it will also generate a list of commands for downloading your plot, from the docker environment to your local machine. You can keep plotting different regions and with each submit, a new download command will be appended to the list.
 For example: 
 ```
   docker cp visualization_app:/tmp/Regional_plot_10:112948590-113048589.png ./Regional_plot_10:112948590-113048589.png-CONTAINER_NAME.png
 ```
 - Once you are ready to exit the application, copy and save the commands displayed below the plot, to use them later. 
 
#### Obtaining the regional plots
Navigate back to your terminal - 
 - Type Ctrl+C on Windows or Command+C on Mac
 - Exit the docker session. **Note:** your docker container is still running on port 3838 so you can still download your plots.
 ```
   exit
 ```
 - Paste the copied commands to copy the downloaded plot to your local machine.
 ```
   docker cp visualization_app:/tmp/Regional_plot_10:112948590-113048589.png ./Regional_plot_10:112948590-113048589.png-CONTAINER_NAME.png
 ```
  


