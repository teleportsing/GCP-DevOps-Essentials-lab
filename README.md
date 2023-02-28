# Managing Deployments Using Kubernetes Engine
Dev Ops practices will regularly make use of multiple deployments to manage application deployment scenarios such as "Continuous deployment", "Blue-Green deployments", "Canary deployments" and more. This lab is to provide practice in scaling and managing containers so you can accomplish these common scenarios where multiple heterogeneous deployments are being used.

# What you'll learn

 * Practice with kubectl tool

 * Create deployment yaml files

 * Launch, update, and scale deployments

 * Practice with updating deployments and deployment styles


# Prerequisites

To maximize your learning, the following is recommended for this lab:

 * You've taken these Google Cloud Skills Boost labs:
 	* [Introduction to Docker](https://www.cloudskillsboost.google/focuses/1029?parent=catalog)
 	* [Hello Node Kubernetes](https://www.cloudskillsboost.google/focuses/564?parent=catalog)

 * You have Linux System Administration skills.

 * You understand DevOps theory: concepts of continuous deployment.


# Introduction to deployments

Heterogeneous deployments typically involve connecting two or more distinct infrastructure environments or regions to address a specific technical or operational need. Heterogeneous deployments are called "hybrid", "multi-cloud", or "public-private", depending upon the specifics of the deployment.

For the purposes of this lab, heterogeneous deployments include those that span regions within a single cloud environment, multiple public cloud environments (multi-cloud), or a combination of on-premises and public cloud environments (hybrid or public-private).

Various business and technical challenges can arise in deployments that are limited to a single environment or region:


 * Maxed out resources: In any single environment, particularly in on-premises environments, you might not have the compute, networking, and storage resources to meet your production needs.

 * Limited geographic reach: Deployments in a single environment require people who are geographically distant from one another to access one deployment. Their traffic might travel around the world to a central location.

 * Limited availability: Web-scale traffic patterns challenge applications to remain fault-tolerant and resilient.
 
 * Vendor lock-in: Vendor-level platform and infrastructure abstractions can prevent you from porting applications.

 * Inflexible resources: Your resources might be limited to a particular set of compute, storage, or networking offerings.


Heterogeneous deployments can help address these challenges, but they must be architected using programmatic and deterministic processes and procedures. One-off or ad-hoc deployment procedures can cause deployments or processes to be brittle and intolerant of failures. Ad-hoc processes can lose data or drop traffic. Good deployment processes must be repeatable and use proven approaches for managing provisioning, configuration, and maintenance.

Three common scenarios for heterogeneous deployment are multi-cloud deployments, fronting on-premises data, and continuous integration/continuous delivery (CI/CD) processes.


# Set the zone

Set your working Google Cloud zone by running the following command, substituting the local zone as <filled in at lab start> :

`gcloud config set compute/zone `



# Get sample code for this lab

1. Get the sample code for creating and running containers and deployments:


```
gsutil -m cp -r gs://spls/gsp053/orchestrate-with-kubernetes .
cd orchestrate-with-kubernetes/kubernetes
```


2. Create a cluster with 3 nodes (this will take a few minutes to complete):


```
gcloud container clusters create bootcamp \
  --machine-type e2-small \
  --num-nodes 3 \
  --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"
```

# Task 1. Learn about the deployment object

Let's get started with deployments. First let's take a look at the deployment object.

1. The explain command in kubectl can tell us about the deployment object:

`kubectl explain deployment`

2. We can also see all of the fields using the --recursive option:

`kubectl explain deployment --recursive`

3. You can use the explain command as you go through the lab to help you understand the structure of a deployment object and understand what the individual fields do:

`kubectl explain deployment.metadata.name`


# Task 2. Create a deployment


1. Update the deployments/auth.yaml configuration file:

`vi deployments/auth.yaml`

2. Start the editor:

`i`

3. Change the image in the containers section of the deployment to the following:

```
...
containers:
- name: auth
  image: "kelseyhightower/auth:1.0.0"
...
```

4. Save the auth.yaml file: press <Esc> then type:

`:wq`

5. Press <Enter>. Now let's create a simple deployment. Examine the deployment configuration file:


`cat deployments/auth.yaml`

Output:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth
spec:
  replicas: 1
  selector:
    matchLabels:
      app: auth
  template:
    metadata:
      labels:
        app: auth
        track: stable
    spec:
      containers:
        - name: auth
          image: "kelseyhightower/auth:1.0.0"
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
...
```


Notice how the deployment is creating one replica and it's using version 1.0.0 of the auth container.

When you run the kubectl create command to create the auth deployment, it will make one pod that conforms to the data in the deployment manifest. This means we can scale the number of Pods by changing the number specified in the replicas field.


6. Go ahead and create your deployment object using kubectl create:


`kubectl create -f deployments/auth.yaml`


7. Once you have created the deployment, you can verify that it was created:

`kubectl get deployments`

8. Once the deployment is created, Kubernetes will create a ReplicaSet for the deployment. We can verify that a ReplicaSet was created for our deployment:

`kubectl get replicasets`

9. Finally, we can view the Pods that were created as part of our deployment. The single Pod is created by the Kubernetes when the ReplicaSet is created:

`kubectl get pods`

It's time to create a service for our auth deployment. You've already seen service manifest files, so we won't go into the details here.

10. Use the kubectl create command to create the auth service:

`kubectl create -f services/auth.yaml`

11. Now, do the same thing to create and expose the hello deployment:

```
kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml
```

12. And one more time to create and expose the frontend deployment:

```
kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml
```

Note: You created a ConfigMap for the frontend.


13. Interact with the frontend by grabbing its external IP and then curling to it:

`kubectl get services frontend`

`Note: It may take a few seconds before the External-IP field is populated for your service. This is normal. Just re-run the above command every few seconds until the field is populated.`


`curl -ks https://<EXTERNAL-IP>`

And you get the hello response back.

14. You can also use the output templating feature of kubectl to use curl as a one-liner:

```
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`
```

Test completed task

# Scale a deployment

Now that we have a deployment created, we can scale it. Do this by updating the spec.replicas field.

1. You can look at an explanation of this field using the kubectl explain command again:


```
kubectl explain deployment.spec.replicas
```


2. The replicas field can be most easily updated using the kubectl scale command:


`kubectl scale deployment hello --replicas=5`

Note: It may take a minute or so for all the new pods to start up.

After the deployment is updated, Kubernetes will automatically update the associated ReplicaSet and start new Pods to make the total number of Pods equal 5.


3. Verify that there are now 5 hello Pods running:

`kubectl get pods | grep hello- | wc -l`


4. Now scale back the application:


`kubectl scale deployment hello --replicas=3`

5. Again, verify that you have the correct number of Pods:

`kubectl get pods | grep hello- | wc -l`


You learned about Kubernetes deployments and how to manage & scale a group of Pods.



# Task 3. Rolling update

Deployments support updating images to a new version through a rolling update mechanism. When a deployment is updated with a new version, it creates a new ReplicaSet and slowly increases the number of replicas in the new ReplicaSet as it decreases the replicas in the old ReplicaSet.


1. Trigger a rolling update

To update your deployment, run the following command: 

`kubectl edit deployment hello`


2. Change the image in the containers section of the deployment to the following:

```
...
containers:
  image: kelseyhightower/hello:2.0.0
...
```

3. Save and exit.

Once you save out of the editor, the updated deployment will be saved to your cluster and Kubernetes will begin a rolling update.

4. See the new ReplicaSet that Kubernetes creates.:

```
kubectl get replicaset
```

5. You can also see a new entry in the rollout history:

`kubectl rollout history deployment/hello`


# Pause a rolling update

If you detect problems with a running rollout, pause it to stop the update.

1. Give that a try now:

`kubectl rollout pause deployment/hello`

2. Verify the current state of the rollout:

`kubectl rollout status deployment/hello`

3. You can also verify this on the Pods directly:

```
kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```

# Resume a rolling update

The rollout is paused which means that some pods are at the new version and some pods are at the older version.

1. We can continue the rollout using the resume command:


`kubectl rollout resume deployment/hello`

2. When the rollout is complete, you should see the following when running the status command:

`kubectl rollout status deployment/hello`

Output:

`deployment "hello" successfully rolled out`


# Rollback an update

Assume that a bug was detected in your new version. Since the new version is presumed to have problems, any users connected to the new Pods will experience those issues.

You will want to roll back to the previous version so you can investigate and then release a version that is fixed properly.

1. Use the rollout command to roll back to the previous version:

`kubectl rollout undo deployment/hello` 

2. Verify the roll back in the history:

`kubectl rollout history deployment/hello`

3. Finally, verify that all the Pods have rolled back to their previous versions:

```
kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
```


Great! You learned about rolling updates for Kubernetes deployments and how to update applications without downtime.


# Task 4. Canary deployments

When you want to test a new deployment in production with a subset of your users, use a canary deployment. Canary deployments allow you to release a change to a small subset of your users to mitigate risk associated with new releases.

Create a canary deployment
A canary deployment consists of a separate deployment with your new version and a service that targets both your normal, stable deployment as well as your canary deployment.


1. First, create a new canary deployment for the new version:


`cat deployments/hello-canary.yaml`

Output:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-canary
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
        track: canary
        # Use ver 2.0.0 so it matches version on service selector
        version: 2.0.0
    spec:
      containers:
        - name: hello
          image: kelseyhightower/hello:2.0.0
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
...
```


2. Now create the canary deployment:

`kubectl create -f deployments/hello-canary.yaml`

3. After the canary deployment is created, you should have two deployments, hello and hello-canary. Verify it with this kubectl command:


`kubectl get deployments`


On the hello service, the selector uses the app:hello selector which will match pods in both the prod deployment and canary deployment. However, because the canary deployment has a fewer number of pods, it will be visible to fewer users.

# Verify the canary deployment

1. You can verify the hello version being served by the request:

`curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version`


2. Run this several times and you should see that some of the requests are served by hello 1.0.0 and a small subset (1/4 = 25%) are served by 2.0.0.

Test completed task


Canary deployments in production - session affinity

In this lab, each request sent to the Nginx service had a chance to be served by the canary deployment. But what if you wanted to ensure that a user didn't get served by the Canary deployment? A use case could be that the UI for an application changed, and you don't want to confuse the user. In a case like this, you want the user to "stick" to one deployment or the other.

You can do this by creating a service with session affinity. This way the same user will always be served from the same version. In the example below the service is the same as before, but a new sessionAffinity field has been added, and set to ClientIP. All clients with the same IP address will have their requests sent to the same version of the hello application.


```
kind: Service
apiVersion: v1
metadata:
  name: "hello"
spec:
  sessionAffinity: ClientIP
  selector:
    app: "hello"
  ports:
    - protocol: "TCP"
      port: 80
      targetPort: 80
```


Due to it being difficult to set up an environment to test this, you don't need to here, but you may want to use sessionAffinity for canary deployments in production.



# Task 5. Blue-green deployments

Rolling updates are ideal because they allow you to deploy an application slowly with minimal overhead, minimal performance impact, and minimal downtime. There are instances where it is beneficial to modify the load balancers to point to that new version only after it has been fully deployed. In this case, blue-green deployments are the way to go.

Kubernetes achieves this by creating two separate deployments; one for the old "blue" version and one for the new "green" version. Use your existing hello deployment for the "blue" version. The deployments will be accessed via a Service which will act as the router. Once the new "green" version is up and running, you'll switch over to using that version by updating the Service.


```
Note: A major downside of blue-green deployments is that you will need to have at least 2x the resources in your cluster necessary to host your application. Make sure you have enough resources in your cluster before deploying both versions of the application at once.
```


# The service

Use the existing hello service, but update it so that it has a selector app:hello, version: 1.0.0. The selector will match the existing "blue" deployment. But it will not match the "green" deployment because it will use a different version.

1. First update the service:

`kubectl apply -f services/hello-blue.yaml`

`Note: Ignore the warning that says resource service/hello is missing as this is patched automatically.`

Updating using Blue-Green deployment
In order to support a blue-green deployment style, we will create a new "green" deployment for our new version. The green deployment updates the version label and the image path.


```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
        track: stable
        version: 2.0.0
    spec:
      containers:
        - name: hello
          image: kelseyhightower/hello:2.0.0
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
          resources:
            limits:
              cpu: 0.2
              memory: 10Mi
          livenessProbe:
            httpGet:
              path: /healthz
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            periodSeconds: 15
            timeoutSeconds: 5
          readinessProbe:
            httpGet:
              path: /readiness
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            timeoutSeconds: 1
```


1. Create the green deployment:

`kubectl create -f deployments/hello-green.yaml`


2. Once you have a green deployment and it has started up properly, verify that the current version of 1.0.0 is still being used:

```
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```

3. Now, update the service to point to the new version:

`kubectl apply -f services/hello-green.yaml`


4. When the service is updated, the "green" deployment will be used immediately. You can now verify that the new version is always being used:


```
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```


Blue-Green rollback

If necessary, you can roll back to the old version in the same way.

1. While the "blue" deployment is still running, just update the service back to the old version:


`kubectl apply -f services/hello-blue.yaml`

2. Once you have updated the service, your rollback will have been successful. Again, verify that the right version is now being used:

```
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
```

You did it! You learned about blue-green deployments and how to deploy updates to applications that need to switch versions all at once.

# Congratulations!
This concludes this hands-on lab practicing deployment management with Kubernetes. In this lab you've had the opportunity to work more with the kubectl command-line tool, and many styles of deployment configurations set up in YAML files to launch, update, and scale your deployments. With this foundation of practice you should feel comfortable applying these skills to your own DevOps practice.


# Finish your quest

This self-paced lab is part of the [Kubernetes in Google Cloud](https://www.cloudskillsboost.google/quests/29), [Set Up and Configure a Cloud Environment in Google Cloud](https://www.cloudskillsboost.google/quests/119), [Cloud Engineering](https://www.cloudskillsboost.google/quests/66), and [DevOps Essentials quests](https://www.cloudskillsboost.google/quests/96). A quest is a series of related labs that form a learning path. Completing a quest earns you a badge to recognize your achievement. You can make your badge or badges public and link to them in your online resume or social media account. Enroll in a quest that contains this lab and get immediate completion credit. Refer to the [Google Cloud Skills Boost catalog](https://www.cloudskillsboost.google/catalog) to see all available quests.

Looking for a hands-on challenge lab to demonstrate your Kubernetes skills and validate your knowledge? Finish this additional [challenge lab](https://www.cloudskillsboost.google/focuses/10457?parent=catalog).


