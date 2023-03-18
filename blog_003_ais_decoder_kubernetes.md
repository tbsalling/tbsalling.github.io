---
title: "Thomas Borg Salling"
date: 2018-10-12
categories: AIS
---

{% include meta.html %}
{% include clarity.html %}
{% include font.html %}

# Running AISdecoder in a Kubernetes cluster on AWS
_(Posted {{page.date}})_

# Introduction

In recent posts I have shown how to decode AIS messages ([what is AIS?](./blog_000_ais.html)), [how to create a Spring Boot based AIS message decoder](./blog_001_ais_decoder_spring_boot.html), and [how to run this decoder in a Docker container](./blog_002_ais_decoder_docker.html).

Today we will see how to deploy this Docker container into a Kubernetes cluster and to call it once it is running there.

# What is Kubernetes?

Explaining Kubernetes can be done both very short and very long.

For in in-depth explanation of Kubernetes, I recommend reading the [introduction on kubernetes.io](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/), the [documentation on kubernetes.io](https://kubernetes.io/docs/home/?path=users&persona=app-developer&level=foundational), or perhaps best: The book [“Kubernetes in Action”](https://www.manning.com/books/kubernetes-in-action) from Manning Publications.

The short version that I will provide here, is that Kubernetes is a home for Docker containers – both for test and production. Kubernetes is software which allows you to deploy and manage containerized applications in a standardized manner. As “Kubernetes in Action” puts it: “Kubernetes enables you to run your software applications on thousands of computer nodes as if all those nodes were a single, enormous computer”.

I find the standardized container deployment procedure and the management of contains (like scaling and detecting missing ones) especially important.

You can run a Kubernetes cluster on almost any set of machines such as Linux-based PC’s, on Raspberry Pis, virtualized machines (like on AWS or Azure), or even in turn-key hosted environments ([read more](https://kubernetes.io/docs/setup/pick-right-solution/)).

In this post I will focus on setting up a Kubernetes cluster in the [Amazon Web Services (AWS)](https://aws.amazon.com/) cloud. AWS is a very complex and comprehensive platform. In relation to this post, think of AWS as a place where we will spin up a small number of virtualized standard Linux machines (“EC2 instances”) and a few supporting resources. Nothing else.

To control Kubernetes on AWS we will use [kops](https://github.com/kubernetes/kops/blob/master/docs/aws.md). Kops is short for “kubernetes operations” and is a fantastic command line tool which we can use to control the operations/sysadmin-stuff of a Kubernetes cluster.

For kops to work, it needs the command line tools for AWS to be installed first. So that’s where we will start.

# Installing AWS command line tools – awscli

To get running on AWS you must have an AWS account ([create one](https://portal.aws.amazon.com/billing/signup)). Limited use of AWS is free (“free tier”); if you go beyond “limited” you may have to pay for their services.

AWS can be controlled completely through their web interface; but for kops to control AWS we need the alternative AWS user interface installed: The AWS command line tool: [awscli](https://aws.amazon.com/cli/). To install awscli on a Mac we will need [homebrew](https://brew.sh/). Assuming homebrew is already installed, the installation of awscli is simply:

{% highlight shell %}
$ brew install awscli
{% endhighlight %}

## Configuring an AWS IAM user

For kops to work with AWS we need to create or choose an “IAM user” on your AWS account and grant this user the necessary permissions. Even though awscli is already installed, I find it a lot easier to configure this role and its permissions using the AWS web UI. Whatever approach you choose be sure that you end up with an “IAM user” with following permissions granted:

{% highlight text %}
AmazonEC2FullAccess
AmazonRoute53FullAccess
AmazonS3FullAccess
AmazonVPCFullAccess
{% endhighlight %}

## Awscli initial configuration

With awscli installed and an IAM user prepared we need a one-time configuration to connect awscli to your account:

{% highlight shell %}
$ aws configure
AWS Access Key ID [None]: XXXXXXXXXXXXXXXXX
AWS Secret Access Key [None]: XXXXXXXXXXXXXXXXXXXXXXXXXX
Default region name [None]: eu-central-1
Default output format [None]:
{% endhighlight %}

To know your values of the X’es – follow the [AWS user guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html).

After this – awscli is now configured and ready to use.

# Installing Kubernetes Operations – kops

Next we need kops. To install kops using homebrew:

{% highlight shell %}
$ brew install kops
{% endhighlight %}

This usually runs straight with no problems.

## Preparing AWS for kops

Before we can use kops, we need to prepare the AWS surroundings.

First, we need to create an “AWS S3 bucket” where kops can store its on administrative information. AWS S3 is a key-value store on AWS, which you can think of as a database. We need to find a name for this bucket – unfortunately the S3 bucket namespace is shared by all users of the system, so you may need to be somewhat creative to find a name which is both descriptive and globally unique. Here I will try with “tbsalling-kops-state-store” and use awscli to create the bucket like this:

{% highlight shell %}
$ aws s3api create-bucket --bucket tbsalling-kops-state-store --region eu-central-1 --create-bucket-configuration LocationConstraint=eu-central-1
{
    "Location": "http://tbsalling-kops-state-store.s3.amazonaws.com/"
}
{% endhighlight %}

That went well. Now kops requires a simple single configuration of this bucket:

{% highlight shell %}
$ aws s3api put-bucket-versioning --bucket tbsalling-kops-state-store --versioning-configuration Status=Enabled
{% endhighlight %}

AWS S3 is now ready for kops.

## Configuring kops

So far we have installed awscli, prepared an AWS IAM user, configured awscli, installed kops, and prepared AWS for kops. Before we reach the real fun, we still need to configure kops. First we need to create an SSH keypair which kops can use:

{% highlight shell %}
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (~/.ssh/id_rsa): ~/.ssh/id_rsa_kops
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in ~/.ssh/id_rsa_kops.
Your public key has been saved in ~/.ssh/id_rsa_kops.pub.
The key fingerprint is:
SHA256:KJRCn5Ejjm5IB9DNc0DgsuzHNK6gOnwNPxUj8XHnJY0 tbsalling@Thomass-MBP
The key's randomart image is:
+---[RSA 2048]----+
|o.o=+o      .    |
| +o.*=.. . o o E |
|.o+.=+o o o E .  |
|o+.+ . =   .     |
|=..o. o S        |
|oo+... .         |
|+. ++ .          |
|+.o. +           |
|+o.   .          |
+----[SHA256]-----+
{% endhighlight %}

Before using the kops cli we set two environment varibles:

{% highlight shell %}
$ export KOPS_CLUSTER_NAME=tbsalling-kops.k8s.local
$ export KOPS_STATE_STORE=s3://tbsalling-kops-state-store
{% endhighlight %}

Note that the `KOPS_STATE_STORE` must match the name you chose for the AWS S3 bucket above.

# Creating a Kubernetes cluster on AWS using kops

Phew 🙂 Now our preparations are completed and we are ready to create our first Kubernetes cluster on AWS!

Let’s start out by creating a Kubernetes cluster with 3 nodes. We don’t need big machines for this – so I will choose will use [AWS instance type “t2.micro”](https://aws.amazon.com/ec2/instance-types/). I prefer the machines to run in Frankfurt, Germany – so I choose AWS [availability zone](https://aws.amazon.com/about-aws/global-infrastructure/) “eu-central-1a”. With these few choices, I perform the following kops command:

{% highlight shell %}
$ kops create cluster --node-count=3 --master-size=t2.micro --node-size=t2.micro --zones=eu-central-1a --ssh-public-key=~/.ssh/id_rsa_kops.pub --name=${KOPS_CLUSTER_NAME}  --yes
I1012 10:03:51.711946   33011 create_cluster.go:1351] Using SSH public key: /Users/tbsalling/.ssh/id_rsa_kops.pub
I1012 10:03:52.843113   33011 create_cluster.go:480] Inferred --cloud=aws from zone "eu-central-1a"
I1012 10:03:53.105492   33011 subnets.go:184] Assigned CIDR 172.20.32.0/19 to subnet eu-central-1a
I1012 10:03:55.371638   33011 apply_cluster.go:505] Gossip DNS: skipping DNS validation
I1012 10:03:55.833340   33011 executor.go:103] Tasks: 0 done / 77 total; 30 can run
I1012 10:03:56.757590   33011 vfs_castore.go:735] Issuing new certificate: "apiserver-aggregator-ca"
I1012 10:03:56.853766   33011 vfs_castore.go:735] Issuing new certificate: "ca"
I1012 10:03:57.417478   33011 executor.go:103] Tasks: 30 done / 77 total; 24 can run
I1012 10:03:58.212893   33011 vfs_castore.go:735] Issuing new certificate: "kubecfg"
I1012 10:03:58.267395   33011 vfs_castore.go:735] Issuing new certificate: "kube-scheduler"
I1012 10:03:58.297758   33011 vfs_castore.go:735] Issuing new certificate: "kops"
I1012 10:03:58.330616   33011 vfs_castore.go:735] Issuing new certificate: "kube-controller-manager"
I1012 10:03:58.374286   33011 vfs_castore.go:735] Issuing new certificate: "kube-proxy"
I1012 10:03:58.402156   33011 vfs_castore.go:735] Issuing new certificate: "kubelet-api"
I1012 10:03:58.402657   33011 vfs_castore.go:735] Issuing new certificate: "kubelet"
I1012 10:03:58.413877   33011 vfs_castore.go:735] Issuing new certificate: "apiserver-proxy-client"
I1012 10:03:58.429905   33011 vfs_castore.go:735] Issuing new certificate: "apiserver-aggregator"
I1012 10:03:59.393739   33011 executor.go:103] Tasks: 54 done / 77 total; 19 can run
I1012 10:04:00.068211   33011 launchconfiguration.go:380] waiting for IAM instance profile "masters.tbsalling-kops.k8s.local" to be ready
I1012 10:04:00.188953   33011 launchconfiguration.go:380] waiting for IAM instance profile "nodes.tbsalling-kops.k8s.local" to be ready
I1012 10:04:10.725027   33011 executor.go:103] Tasks: 73 done / 77 total; 3 can run
I1012 10:04:11.623059   33011 vfs_castore.go:735] Issuing new certificate: "master"
I1012 10:04:12.107182   33011 executor.go:103] Tasks: 76 done / 77 total; 1 can run
I1012 10:04:12.382870   33011 executor.go:103] Tasks: 77 done / 77 total; 0 can run
I1012 10:04:12.508591   33011 update_cluster.go:290] Exporting kubecfg for cluster
kops has set your kubectl context to tbsalling-kops.k8s.local
Cluster is starting.  It should be ready in a few minutes.
Suggestions:
 * validate cluster: kops validate cluster
 * list nodes: kubectl get nodes --show-labels
 * ssh to the master: ssh -i ~/.ssh/id_rsa admin@api.tbsalling-kops.k8s.local
 * the admin user is specific to Debian. If not using Debian please use the appropriate user based on your OS.
 * read about installing addons at: https://github.com/kubernetes/kops/blob/master/docs/addons.md.
{% endhighlight %}

It may take a while for the cluster to get completely up to speed, which you can see with kops validate cluster like this:

{% highlight shell %}
$ kops validate cluster
Validating cluster tbsalling-kops.k8s.local
INSTANCE GROUPS
NAME			ROLE	MACHINETYPE	MIN	MAX	SUBNETS
master-eu-central-1a	Master	t2.micro	1	1	eu-central-1a
nodes			Node	t2.micro	3	3	eu-central-1a
NODE STATUS
NAME	ROLE	READY
VALIDATION ERRORS
KIND	NAME			MESSAGE
Machine	i-0018ee6b015bcb65d	machine "i-0018ee6b015bcb65d" has not yet joined cluster
Machine	i-00d3dcb9425a2ae37	machine "i-00d3dcb9425a2ae37" has not yet joined cluster
Machine	i-07a70e1ecd5b650ff	machine "i-07a70e1ecd5b650ff" has not yet joined cluster
Machine	i-0f0e79bcef34422cc	machine "i-0f0e79bcef34422cc" has not yet joined cluster
Validation Failed
{% endhighlight %}

But after a while you will see something like this:

{% highlight shell %}
$ kops validate cluster
Validating cluster tbsalling-kops.k8s.local
INSTANCE GROUPS
NAME			ROLE	MACHINETYPE	MIN	MAX	SUBNETS
master-eu-central-1a	Master	t2.micro	1	1	eu-central-1a
nodes			Node	t2.micro	3	3	eu-central-1a
NODE STATUS
NAME						ROLE	READY
ip-172-20-42-171.eu-central-1.compute.internal	node	True
ip-172-20-50-187.eu-central-1.compute.internal	master	True
ip-172-20-56-73.eu-central-1.compute.internal	node	True
ip-172-20-56-98.eu-central-1.compute.internal	node	True
Your cluster tbsalling-kops.k8s.local is ready
{% endhighlight %}

And this makes us happy: “Your cluster tbsalling-kops.k8s.local is ready” 🙂

So now we have a Kubernetes cluster with 1 master and 3 slave nodes running in Frankfurt!

# Deploying AISdecoder to the AWS Kubernetes cluster

With the Kubernetes cluster up and running we will now deploy our AISdecoder Docker image to it.

The main tool to control a kubernetes cluster is kubectl. You can read a lot about this in the official [Kubernetes tutorial](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/). Can you also simply type kubectl at the command line to see its usage. For now, we can see that kubectl is working and correctly configured by kops like this:

{% highlight shell %}
$ kubectl get nodes
NAME                                             STATUS   ROLES    AGE   VERSION
ip-172-20-42-171.eu-central-1.compute.internal   Ready    node     1m    v1.10.6
ip-172-20-50-187.eu-central-1.compute.internal   Ready    master   2m    v1.10.6
ip-172-20-56-73.eu-central-1.compute.internal    Ready    node     1m    v1.10.6
ip-172-20-56-98.eu-central-1.compute.internal    Ready    node     1m    v1.10.6
{% endhighlight %}

To deploy our Docker image – [which is already on Docker hub](https://hub.docker.com/r/tbsalling/aisdecoder/) – we can use the kubectl run command and point it to our Docker image on Docker hub:

{% highlight shell %}
$ kubectl run aisdecoder --image=tbsalling/aisdecoder --port=8080
kubectl run --generator=deployment/apps.v1beta1 is DEPRECATED and will be removed in a future version. Use kubectl create instead.
deployment.apps/aisdecoder created
{% endhighlight %}

We can verify, that Kubernetes now indeed has created a deployment for us:

{% highlight shell %}
$ kubectl get deployments
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
aisdecoder   1         1         1            1           2m
{% endhighlight %}

This shows, that we desire 1 instance of aisdecoder to be running at any time, and that 1 instance of aisdecoder is actually running.

To be able to make a REST call to the aisdecoder application running inside this Kubernetes cluster we need to go through a “proxy”. This is because applications running in Kubernetes are on a private network which can only be reached through a proxy. To start such a proxy we type:

{% highlight shell %}
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
{% endhighlight %}

Now it is time to make a call to our application. So in a second terminal window we will now call the aisdecoder application through the Kubernetes proxy:

{% highlight shell %}
$ curl -X POST http://localhost:8001/api/v1/namespaces/default/pods/aisdecoder-c4c46474-rzd2x/proxy/decode -H 'Content-Type: application/json' -d '[ "!AIVDM,1,1,,A,18UG;P0012G?Uq4EdHa=c;7@051@,0*53" ]'
Note the URL: “http://localhost:8001/api/v1/namespaces/default/pods/aisdecoder-c4c46474-rzd2x/proxy/decode”.
{% endhighlight %}

The hostname and port number (`localhost:8001`) point to the Kubernetes proxy. So this is what came out of kubectl proxy.

For now “/api/v1/namespaces/default/pods/” is just a static string which is appended.

“aisdecoder-c4c46474-rzd2x” is the name of the pod containing our application – this is obtained from kubectl get pods.

“/proxy” is a static string.

And finally “/decode” is the URI defined by our own application.

Hence, we see the following output of the curl command:

{% highlight json %}
[{"nmeaMessages":[{"rawMessage":"!AIVDM,1,1,,A,18UG;P0012G?Uq4EdHa=c;7@051@,0*53","valid":true,"sequenceNumber":null,"encodedPayload":"18UG;P0012G?Uq4EdHa=c;7@051@","fillBits":0,"numberOfFragments":1,"fragmentNumber":1,"radioChannelCode":"A","checksum":83,"messageType":"AIVDM"}],"metadata":{"source":"SRC1","received":"2018-10-17T08:26:43.756037Z","decoderVersion":"2.2.2","category":"AIS"},"repeatIndicator":0,"sourceMmsi":{"mmsi":576048000},"navigationStatus":"UnderwayUsingEngine","rateOfTurn":0,"speedOverGround":6.6,"positionAccuracy":false,"latitude":37.912167,"longitude":-122.42299,"courseOverGround":350.0,"trueHeading":355,"second":40,"specialManeuverIndicator":"NotAvailable","raimFlag":false,"communicationState":{"syncState":"UTCDirect","slotTimeout":1,"numberOfReceivedStations":null,"slotNumber":null,"utcHour":8,"utcMinute":20,"slotOffset":null},"messageType":"PositionReportClassAScheduled","transponderClass":"A","valid":true}]
{% endhighlight %}

Which is absolutely fantastic! Because now we have successfully called our own application running inside the Kubernetes cluster!

After all this work please take time to celebrate a bit before reading on :-).

In a later post I will show how to play more with our application in Kubernetes now that it is running there. But to keep this post at a reasonable length, it is now time for:

# Cleaning up the cluster

Once we are finished playing we can stop and delete the application from our Kubernetes cluster:

{% highlight shell %}
$ kubectl delete deployment/aisdecoder
deployment.extensions "aisdecoder" deleted
{% endhighlight %}

# Cleaning up on AWS

Also, since we are just playing around here we want to avoid unnecessary AWS hosting costs by freeing up the machines, that we just spun up.

One way to stop these machines would be to use the awscli. But since kops knows exactly what it has done and which AWS resources it has allocated (thanks to its S3 bucket), kops can conveniently clean up after itself and free all the AWS resources related to the Kubernetes cluster.

It can be done like this:

{% highlight shell %}
$ kops delete cluster --yes --name=${KOPS_CLUSTER_NAME}
... <a few minutes pass> ...
Not all resources deleted; waiting before reattempting deletion
	vpc:vpc-01d6bcee76e847162
	dhcp-options:dopt-05f07d75a03571a2b
	subnet:subnet-0de74da43276b07e8
	security-group:sg-0b173ee5b5afc79a4
	route-table:rtb-06e4a6f7ef889bde2
subnet:subnet-0de74da43276b07e8	ok
security-group:sg-0b173ee5b5afc79a4	ok
route-table:rtb-06e4a6f7ef889bde2	ok
vpc:vpc-01d6bcee76e847162	ok
dhcp-options:dopt-05f07d75a03571a2b	ok
Deleted kubectl config for tbsalling-kops.k8s.local
Deleted cluster: "tbsalling-kops.k8s.local"
{% endhighlight %}

It may take a few minutes for kops to get everything spun down; but when the command completes with “Deleted cluster: …” then all the AWS resources (except for the adminsitrative AWS S3 bucket) are freed and we are back where we started.

# Conclusion

In this post, I showed how to install the command line interface for Amazon web services and also “kops” – kubernetes operations.

Then I showed how create a 3-node Kubernetes cluster on AWS, how to deploy and run a Docker image there, and finally how to clean it all up.

# Important resources

* “Kubernetes in Action” by Marko Lukša, ISBN 9781617293726, Manning Publications, <https://www.manning.com/books/kubernetes-in-action>.
* “Installing Kubernetes on AWS with kops”, <https://kubernetes.io/docs/setup/custom-cloud/kops/>.
* Official Kubernetes tutorials – <https://kubernetes.io/docs/tutorials/>; especially <https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/>.