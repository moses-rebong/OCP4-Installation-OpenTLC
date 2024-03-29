# Install Openshift 4.8.6 on AWS (Internal Red Hat)
OpenShift 4 AWS Installation IPI at OpenTLC 

### Prerequisite
1. Login opentlc
2. Order Catalog > OPENTLC Openshift 4 Labs > Openshift 4 Installation Lab

### Step 1: Login to bastion host (provided on Opentlc's Email)
```bash
ssh user@host
```
### Step 2: Clone Preparation script from Git and Change below parameter
```bash
git clone https://github.com/moses-rebong/OCP4-Installation-OpenTLC.git
```
```bash
cd OCP4-Installation-OpenTLC/
```
```bash
vim prepare-openshift-installer.sh
```
### Step 3: Change parameter on preparation script
```properties
export AWSKEY={AWSKEY generated from opentlc}
export AWSSECRETKEY={AWSSECRETKEY generated from opentlc}
export REGION=ap-southeast-1
export OCP_VERSION=4.7.4
export GUID={GUID generated from opentlc}
```

### Step 4: Login as Root and run preparation script
```bash
sudo -i
```
```bash
cd ${HOME}/OCP4-Installation-OpenTLC/
```
```bash
. prepare-openshift-installer.sh
```

** If you get " /root/OCP4-Installation-OpenTLC/: No such file or directory", you need to make sure that your $HOME variable is configured. You can use "pwd" to know your current directory

It will shows this log once done,
```
+ oc completion bash
+ echo 'Please exit the session and relogin again to enable the Bash completion.'
Please exit the session and relogin again to enable the Bash completion.

```

### Step 5: Relogin as Root and install ocp
```bash
exit
```
```bash
sudo -i
```
```bash
tmux new -s ocp_install
```
```bash
time openshift-install create cluster --dir $HOME/cluster-${GUID} --log-level debug
```

#### From installer screen, choose
```
CA certificate = --> use default
SSH Public Key /root/.ssh/cluster-${GUID}-key.pub
Platform aws
AWS Region = ap-southeast-1
Base Domain = sandbox???.opentlc.com
Cluster Name = ocplab-cluster
Pull Secret = ***** download from try.openshift.com
```
> During installation, script will ask for pull secret. Download at try.openshift.com .

```
> Go to https://console.redhat.com/openshift/install/aws/installer-provisioned
> Login with your Red Hat ID
> Choose Self Managed > try it in your cloud
> Choose In the Public Cloud > AWS
> Choose Installer-provisioned infrastructure 
> Copy pull secret
> paste it on CLI
```

![Pull Secret](pull-secret.PNG)

Below is end of installation screen
```
...
INFO Waiting up to 30m0s for the cluster at https://api.ocplab-cluster.sandbox????.opentlc.com:6443 to initialize...
...
DEBUG Still waiting for the cluster to initialize: Working towards 4.5.6: 13% complete
DEBUG Still waiting for the cluster to initialize: Working towards 4.5.6: 16% complete
DEBUG Still waiting for the cluster to initialize: Working towards 4.5.6: 88% complete
DEBUG Cluster is initialized
INFO Waiting up to 10m0s for the openshift-console route to be created...
DEBUG Route found in openshift-console namespace: console
DEBUG Route found in openshift-console namespace: downloads
DEBUG OpenShift console route is created
INFO Install complete!
INFO To access the cluster as the system:admin user when using 'oc', run 'export KUBECONFIG=/root/cluster-????/auth/kubeconfig'
INFO Access the OpenShift web-console here: https://console-openshift-console.apps.ocplab-cluster.sandbox????.opentlc.com
INFO Login to the console with user: "kubeadmin", and password: --> Generated
DEBUG Time elapsed per stage:
DEBUG     Infrastructure: 6m8s
DEBUG Bootstrap Complete: 12m11s
DEBUG  Bootstrap Destroy: 49s
DEBUG  Cluster Operators: 19m47s
INFO Time elapsed: 41m49s

real    41m49.080s
user    0m34.074s
sys     0m1.830s
```

### Step 6: Login from oc client
```bash
oc login --token=$TOKEN --server=https://api.ocplab-cluster.sandbox????.opentlc.com:6443
```

=======
### Step 7: Upload non admin user on htpasswd
```bash
htpasswd -nb admin mypassword
```
Copy output on .txt file and upload on OpenShift web console (user management) 

### Step 8: Etc
In case of you forgot your OCP's Kubeadmin password, we can check our installation log files which can be found on `.openshift_install.log`, and located in `$HOME/cluster-${GUID}`. 

```
vi $HOME/cluster-${GUID}/.openshift_install.log
```
** If you could not find .openshift_install.log try to find the file using " find ./ -name .openshift_install.log " command in sudo mode.
