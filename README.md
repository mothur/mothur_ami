# mothur_ami
Repository to create and maintain an Amazon AMI for mothur

````
sudo sh -c 'echo "deb http://cran.rstudio.com/bin/linux/ubuntu trusty/" >> /etc/apt/sources.list'
gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9
gpg -a --export E084DAB9 | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install r-base

sudo apt-get -y install libcurl4-gnutls-dev libxml2-dev libssl-dev


sudo su - -c "R -e \"install.packages('devtools', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('dplyr', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('ggplot', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('rmarkdown', repos='http://cran.rstudio.com/')\""
sudo su - -c "R -e \"install.packages('knitr', repos='http://cran.rstudio.com/')\""


RSTUDIO=rstudio-server-0.99.902-amd64.deb
wget https://download2.rstudio.org/$RSTUDIO
sudo gdebi $RSTUDIO
rm $RSTUDIO

wget https://github.com/mothur/mothur/releases/download/v1.37.6/Mothur.linux_64.zip
unzip Mothur.linux_64.zip
rm -rf Mothur.linux_64.zip __MACOSX

sudo nano /etc/environment
#add /home/ubuntu/mothur




sudo useradd mothur
sudo mkdir /home/mothur
sudo passwd mothur
sudo chmod -R 0777 /home/mothur/
sudo chsh -s /bin/bash mothur

#Edit /etc/ssh/sshd_config setting
#PasswordAuthentication yes

# Restart the ssh daemon with
sudo service ssh restart


#The following goes into /home/mothur
mkdir data
mkdir data/raw
mkdir data/mothur
mkdir data/process
mkdir data/references
mkdir code

wget -P data/references http://mothur.org/w/images/b/be/Silva.nr_v123.tgz

wget -P data/references http://www.mothur.org/w/images/6/68/Gg_13_8_99.taxonomy.tgz

wget -P data/references http://mothur.org/w/images/1/15/Silva.seed_v123.tgz
tar xvzf data/references/Silva.seed_v123.tgz -C data/references
rm data/references/Silva.seed_v123.tgz data/references/README.*

wget -P data/references http://mothur.org/w/images/8/88/Trainset14_032015.pds.tgz
tar xvzf data/references/Trainset14_032015.pds.tgz -C data/references
rm data/references/Trainset14_032015.pds.tgz
mv data/references/trainset14_032015.pds/* data/references
rm -rf data/references/trainset14_032015.pds data/references/README.*

#tar xvzf data/references/Silva.nr_v123.tgz -C data/references
#rm data/references/Silva.nr_v123.tgz

#tar xvzf data/references/Gg_13_8_99.taxonomy.tgz -C data/references
#rm data/references/Gg_13_8_99.taxonomy.tgz

wget -N -P data/raw/ http://www.mothur.org/w/images/d/d6/MiSeqSOPData.zip
unzip data/raw/MiSeqSOPData.zip -d data/raw
mv data/raw/MiSeq_SOP/* data/raw
rm -rf data/raw/__MACOSX/ data/raw/MiSeq_SOP data/raw/MiSeqSOPData.zip
````
