# WGSAssociationVisualization

## Regional plots from WGS summary statistics
Author: Shruti Parpattedar (parpattedar.s@husky.neu.edu) <br>
Maintainer: Manning Lab <br>
Version: 0.1

## Description
This application is designed to produce regional plots of a given search range from a bgzipped WGS summary statistics file stored in your Google bucket. The regional plots thus produced can be downloaded as PNG files.

### Data required to run this application
- The minimum data required to generate a plot are per-variant GWAS summary statistics files in bgzipped (.gz) format residing in a Google bucket to which you have access. <br>
 Four values must be defined for each variant:
  - Variant id: a unique identifier for each variant
  - Chromosome: the chromosome location of each variant
  - Position: the genomic position of each variant
  - P-value: a p-value for each variant  (or some other data to plot on the Y-axis)

| MarkerName | chr | pos | pvalue |
-----------|--------|----|---------
| rs12345 |	1	| 234030 |	0.803 |
| rs54321 |	10	| 1268346 |	0.001 |
| rs98765 |	22	| 68263057 |	0.283 |
   <br>
   
- A tabix indexed file in (.gz.tbi) format in the same location. <br>

- Optional: File containing LD information to be incorporated into the plot. <br>
To do so, a file with LD-values per variant needs to be input. The package used for the plotting is designed to be used in conjunction with and handles outputs from our [LDGds pipeline](https://github.com/AnalysisCommons/LDGds). LD data should be stored in a plain, delimited text file as either a matrix or a row vector. Either format must have variant identifiers as row and column names. In conjunction with the LD data, a reference variant must be specified. See the package vignette and accompanying data for an example of input LD data.

| | rs12345 | rs54321 | rs98765 |
---|-------|----------|-------------
| rs12345 |	1	| 0.0001 |	0.1001 |
| rs54321 | 0.0001 | 1 | 0.82 |
| rs98765 | 0.1001 | 0.82 | 1 |
 
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
   docker run --rm --name visualization_app -ti -p 3838:80 manninglab/visualization_app /bin/bash 
 ```
 
 #### Running a demo session
 - You are now in the docker container. To execute the demo: <br>
 ```
   ./demo_script.sh 
 ```
 
 Navigate to your web browser - 
 - Go to http://127.0.0.1:3838/
 
 Give the plot a minute to generate. <br>
 
 - Optionally, change the range you want to search in. 
 - Click the View plot button to view the plot
 - Clicking the Download button will download the plot as a .png file (for eg. Regional_plot_20:60900000-61100000.png) 
 
 #### Exiting the demo session
Navigate back to your terminal - 
 - Type Ctrl+C on Windows or Command+C on Mac <br>
 
 If you wish to run the app using your data in the Google bucket, follow the next steps. <br>
 
 If not, to exit the docker session, by typing:
 ```
   exit
 ```
 
 #### Starting the application from a Docker container
 - You are still in the docker container. To execute the appfiles, start with the Google Cloud configuration setup <br>
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
 - If everything is correct, you will see the following message:
 ```
   Credentials saved to file: [/root/.config/gcloud/application_default_credentials.json]

These credentials will be used by any library that requests
Application Default Credentials.

To generate an access token for other uses, run:
  gcloud auth application-default print-access-token


Updates are available for some Cloud SDK components.  To install them,
please run:
  $ gcloud components update



To take a quick anonymous survey, run:
  $ gcloud alpha survey


Navigate to http://127.0.0.1:3838/ on your web browser

*** warning - no files are being watched ***

 ```
#### Using the application
Navigate to your web browser - 
 - Go to http://127.0.0.1:3838/
 - Give the plot a minute to generate
 - Enter the link to the Google bucket containing your data: 
 ```
   Something like this - 
   gs://fc-91605a4c-df34-4248-b17v-ca123456e59
 ```
 - Click the Submit bucket link button. This will auto populate the drop down list below with the .gz files in your bucket which have corresponding tabix indexed files.
 - Select the desired file from the drop down list
 - Enter the range you want to search in. (For eg. 20:60900000-61100000)
 - Enter the columns numbers for the Variant ID (a unique variant identifier), chromosome of the variant, position of the variant and p-value of the variant. For eg. for the demo file, the input would be 1, 2, 3 and 9 respectively.
 - Select the appropriate genome build from the given options - hg19 and hg38
 - (Optional) The Google bucket link for the LD file gets auto populated but you can edit this link if your file exists in another location and click the Submit bucket link button.
   - This will populate the drop down list below with all the .txt, .tsv and .csv files in that location
 - (Optional) Enter the LD reference variant (For eg. 20-61000005-A-G)

 - Click the View plot button to view the plot
 - Clicking the Download button will download the plot as a .png file (for eg. Regional_plot_10:112948590-113048589.png) 
 
#### Exiting the docker container
Navigate back to your terminal - 
 - Type Ctrl+C on Windows or Command+C on Mac
 - Exit the docker session, by typing:
 ```
   exit
 ```
  


