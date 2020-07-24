# kube-autoscale-pods
Horizontal Pod Autoscaler automatically scales the number of pods in a replication controller, deployment, replica set or stateful set based on observed CPU utilization (or, with beta support, on some other, application-provided metrics).
I'm going to enable Horizontal Pod Autoscaler for the php-apache server.

=> Run & expose php-apache server:

To demonstrate Horizontal Pod Autoscaler we will use a custom docker image based on the php-apache image.

php:5-apache

It defines an index.php page which performs some CPU intensive computations:

<?php
  $x = 0.0001;
  for ($i = 0; $i <= 1000000; $i++) {
    $x += sqrt($x);
  }
  echo "OK!";
?>

First, we will start a deployment running the image and expose it as a service on port 80 

* kubectl create -f php-apache.yaml

* kubectl get deployments


=> Create Horizontal Pod Autoscaler:

Now that the server is running, we will create the autoscaler. HPA will increase and decrease the number of replicas (via the deployment) to maintain an average CPU utilization across all Pods of 50%.

* kubectl create -f hpa.yaml

* kubectl get hpa -w


=> Increase load

we will see how the autoscaler reacts to increased load. We will start a container, and send an infinite loop of wget queries to the php-apache service.

* kubectl create -f load-generator-deployment.yaml

Within few minutes or so, we should see the higher CPU load by executing:
* kubectl get hpa -w (in one terminal)
* kubectl get po -w (in second terminal)

=>Delete Load

Now We will scale down the number of replicas, there by deleting the load-generator.

* kubectl delete deployment load-generator

* kubectl get deployment php-apache
