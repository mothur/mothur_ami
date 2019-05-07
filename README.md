# mothur_ami
Repository to create and maintain an Amazon AMI for mothur

* Create an Ubuntu `t3.micro` instance create the SSH, HTTP, HTTPS, and Custom rules
* Log in with `ssh ubuntu@[Public DNS]` (may need to do `ssh -i .ssh/MyKeyPair.pem ubuntu@ec2-54-234-233-240.compute-1.amazonaws.com`

````
#install R and dependencies
sudo sh -c 'echo "deb http://cran.rstudio.com/bin/linux/ubuntu bionic-cran35/" >> /etc/apt/sources.list' #replace xenial with current version of ubuntu
gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9
gpg -a --export E084DAB9 | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install r-base
sudo apt-get -y install libcurl4-gnutls-dev libxml2-dev libssl-dev


#install critical packages
sudo su - -c "R -e \"install.packages('devtools', repos='https://cran.mtu.edu/')\""
sudo su - -c "R -e \"install.packages('tidyverse', repos='https://cran.mtu.edu/')\""
sudo su - -c "R -e \"install.packages('rmarkdown', repos='https://cran.mtu.edu/')\""
sudo su - -c "R -e \"install.packages('knitr', repos='https://cran.mtu.edu/')\""


#see https://www.rstudio.com/products/rstudio/download-server/
sudo apt-get install gdebi-core
RSTUDIO=rstudio-server-1.2.1335-amd64.deb #update RSTUDIO server version
wget https://download2.rstudio.org/$RSTUDIO
sudo gdebi $RSTUDIO
rm $RSTUDIO


#Update mothur version
wget https://github.com/mothur/mothur/releases/download/v1.42.0/Mothur.linux_64.zip
unzip Mothur.linux_64.zip
rm -rf Mothur.linux_64.zip __MACOSX


sudo nano /etc/environment
#add /home/ubuntu/mothur to PATH so that user can see it when they are in their home directory

sudo su
passwd root  #type mothur at both prompts
exit #to get out of su state

sudo useradd mothur
sudo mkdir /home/mothur
sudo passwd mothur  #type mothur at both prompts
sudo chmod -R 0777 /home/mothur/
sudo chsh -s /bin/bash mothur

sudo nano /etc/ssh/sshd_config
#change PasswordAuthentication from no to yes

# Restart the ssh daemon with
sudo service ssh restart


#The following goes into /home/mothur
cd /home/mothur
mkdir data
mkdir data/raw
mkdir data/mothur
mkdir data/process
mkdir data/references
mkdir code

#https://mothur.org/wiki/Silva_reference_files
wget -P data/references https://mothur.org/w/images/3/32/Silva.nr_v132.tgz
#tar xvzf data/references/Silva.nr_v132.tgz -C data/references
#rm data/references/Silva.nr_v132.tgz

#https://mothur.org/wiki/Silva_reference_files
wget -P data/references https://mothur.org/w/images/7/71/Silva.seed_v132.tgz
tar xvzf data/references/Silva.seed_v132.tgz -C data/references
rm data/references/Silva.seed_v132.tgz data/references/README.*

#https://mothur.org/wiki/RDP_reference_files
wget -P data/references https://mothur.org/w/images/c/c3/Trainset16_022016.pds.tgz
tar xvzf data/references/Trainset16_022016.pds.tgz -C data/references
rm data/references/Trainset16_022016.pds.tgz
mv data/references/trainset16_022016.pds/* data/references
rm -rf data/references/trainset16_022016.pds data/references/README.*

#https://mothur.org/wiki/Greengenes-formatted_databases
wget -P data/references http://www.mothur.org/w/images/6/68/Gg_13_8_99.taxonomy.tgz
#tar xvzf data/references/Gg_13_8_99.taxonomy.tgz -C data/references
#rm data/references/Gg_13_8_99.taxonomy.tgz

wget -N -P data/raw/ http://www.mothur.org/w/images/d/d6/MiSeqSOPData.zip
unzip data/raw/MiSeqSOPData.zip -d data/raw
mv data/raw/MiSeq_SOP/* data/raw
mv data/raw/stability.batch code/
rm -rf data/raw/__MACOSX/ data/raw/MiSeq_SOP data/raw/MiSeqSOPData.zip

sudo chown -R mothur ./
sudo chgrp -R mothur ./
````

* Go to `ec2-35-174-168-176.compute-1.amazonaws.com:8787` to confirm that RStudio fires up - use mothur/mothur as username/password combination
* In AWS dashboard, select instance, go to Image -> Create Image
* Set the volume size to 20 GB
* Name the volume `mothur-1.40.4`
* In description insert `officially supported version of mothur AMI`
* Wait for the image to be generated (may take a while to show up)
* Once created, select on the image under "AMIs" and select "Permissions", "Edit", and then "Public"
* Run through [mothur SOP](https://mothur.org/wiki/EDAMAME) to make sure everything works... 
