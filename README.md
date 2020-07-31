# vscode_workspace
how configure your worksapce with vscode. Connect to gcp instance with private ip from vscode.





1 - download
https://code.visualstudio.com/download

2 - install, add extension 
Remote ssh
HashiCorp Terraform 
YAML
Change color Theme Ctrl-K Ctrl-T

Install OpenSSh (win 10) 
Press Win - type features (app & and features) - “Add a feature” find “OpenSSH Client.”

Add cmd in PATH 
Press Win + type env (Edit System Environment) - Environments Variables add in Path C:\WINDOWS\System32\cmd.exe

3 - try connect from any linux client (i use mobaxterm with local session) 
https://mobaxterm.mobatek.net/download-home-edition.html
Note. Install not portable version. 

- Configure any persistance root (i use Desktop) - Setting - General. Restart it

- After install add plugin - Python3 - press packages - and find python3

- install gcloud in mobaxterm. 

wget (- Linux 64-bit(x86_64) on page https://cloud.google.com/sdk/docs/downloads-versioned-archives)
  for example wget https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-303.0.0-linux-x86_64.tar.gz --no-check-certificate
unzip
  - tar -xvf google-cloud-sdk-303.0.0-linux-x86_64.tar.gz
install 
  ./google-cloud-sdk/install.sh


After all check that gcloud init working correctly

###############

try connect from local session (mobaxterm)

gcloud beta compute ssh --zone "us-east4-c" "instance_name" --tunnel-through-iap --project "project_name"

it should be connected. 
Note - gcloud init have to be configured before!!!

see dry-run

gcloud beta compute ssh --zone "us-east4-c" "instance_name" --tunnel-through-iap --project "project_name" --dry-run
resoult 

/bin/ssh -t -i /home/mobaxterm/.ssh/google_compute_engine -o CheckHostIP=no -o HostKeyAlias=compute.2249601509865285850 -o IdentitiesOnly=yes -o StrictHostKeyChecking=yes -o UserKnownHostsFile=/home/mobaxterm/.ssh/google_compute_known_hosts -o ProxyCommand /bin/python2 -S /home/mobaxterm/google-cloud-sdk/lib/gcloud.py beta compute start-iap-tunnel devbast %p --listen-on-stdin --project=project_name --zone=us-east4-c --verbosity=warning -o ProxyUseFdpass=no User@compute.2249601509865285850

it is base structure , you need make some changes

1 - generate your ssh keys  (default - without password)
ssh-keygen -t rsa -b 4096 -C "username"
files will be in 
/home/mobaxterm/.ssh/id_rsa
/home/mobaxterm/.ssh/id_rsa.pub

create new simple folder like c:/ssh/ and copy all files here (path must be without spaces)


copy file id_rsa to c:\ssh (in mobaxterm)
cp /home/mobaxterm/.ssh/id_rsa /drives/c/ssh/
cp /home/mobaxterm/.ssh/id_rsa.pub /drives/c/ssh/


copy file /home/mobaxterm/.ssh/google_compute_known_hosts to c:\ssh
cp /home/mobaxterm/.ssh/google_compute_known_hosts  /drives/c/ssh/


copy python and gcloud.py to c:\ssh
cp -r /drives/c/Users/nikolai_zhukov/AppData/Local/Google/Cloud\ SDK/google-cloud-sdk/lib/ /drives/c/ssh/  (gcloud.py there are)
cp -r /drives/c/Users/nikolai_zhukov/AppData/Local/Google/Cloud\ SDK/google-cloud-sdk/platform/bundledpython /drives/c/ssh/  (python there are)

2 - add file  c:\ssh\id_rsa.pub to instance (connect from console.cloud.google.com) - upload file
cat id_rsa.pub >> .ssh/authorized_keys
(folder .ssh have to be exist or create it, file authorized_keys have to be exist or add it)

find your username (run on remote instance)
id - it will be your user in file config



3 - change permissions for file c:\ssh\id_rsa

vscode will  not connection till it will secure. Add FULL constrol  only this user in os and remove all other users. 


With defaul path it will not connect in my case, may be it will working in your case.
So i make config lie this.


3 - in vscode go to Remote Explorer - Configure - open config and add


Host AnyHostName
  HostName compute.2249601509865285850                          
  IdentityFile C:\ssh\id_rsa                                    
  CheckHostIP no
  HostKeyAlias compute.2249601509865285850                      
  IdentitiesOnly yes
  StrictHostKeyChecking yes
  UserKnownHostsFile C:\ssh\google_compute_known_hosts          (your new file from cp)
  ProxyCommand C:\ssh\bundledpython\python.exe -S C:\ssh\lib\gcloud.py beta compute start-iap-tunnel hostname %p --listen-on-stdin --project=project_name --zone=us-east4-c --verbosity=warning
  ProxyUseFdpass no
  User your_username 
  
  
  Notes
  HostName compute.2249601509865285850                                                                     (resoult --dry-run)
  IdentityFile C:\ssh\id_rsa                                                                               (your new file generated with new permissions)
  HostKeyAlias compute.2249601509865285850                                                                 (resoult --dry-run)
  UserKnownHostsFile C:\ssh\google_compute_known_hosts                                                     (your new file from cp)

  ProxyCommand C:\ssh\bundledpython\python.exe -                                                           (your new file from cp) 
  C:\ssh\lib\gcloud.py                                                                                     (your new file from cp) 
  beta compute start-iap-tunnel hostname %p --listen-on-stdin --project=project_name --zone=us-east4-c     (resoult --dry-run)
  User your_username  - resoult id command from instance 

