## CKAD - Study Guide Excersises
This repo contains attempted solutions to the [Book](https://a.co/d/dIgSeRU): Certified Kubernetes Application Developer(CKAD) Study Guide.

The solutions are grouped into the chapters of the book:

### Chapter 1
1. Create a new Pod named nginx running the image nginx:1.17.10. Expose the container port 80. The Pod should live in the namespace named ckad.

```shell
alias k=kubectl
k create namespace ckad
k run nginx --image=nginx:1.17.10 --port 80 -n ckad
```

2. Get the details of the Pod including its IP address:

```shell
k describe pods nginx -n ckad | grep IP
IP=<displayed-ip>
```

3. Create a temporary Pod that uses the busybox image to execute wget command inside of the container. The wget command should access the endpoint exposed by the nginx container. You should see the HTML response body rendered in the terminal

```shell
k run busybox --image=busybox --restart=Never -o yaml > pod.yaml -- /bin/sh -c "wget $IP"
```

4. Get the logs of the nginx container

```shell
k logs nginx -n ckad
```

5. Add the environment variables DB_URL=postgresql://mydb:5432 and DB_USERNAME=admin to the container of the nginx Pod.

```shell
k delete pod nginx -n ckad
k run nginx --image=nginx:1.17.10 --port 80 -n ckad --env "DB_URL=postgresql://mydb:5432,DB_USERNAME=admin"
```

6. Open the shell for the nginx container and inspect the contents of the current directory ls -l
```shell
k exec -it nginx -n ckad -- /bin/sh
ls -l
```

7. Create a YAML manifest for a Pod named loop that runs the busybox image in a container. The container should run the following command: *for i in {1..10}; do echo 'Welcome $i times'; done*
Create the pod from the YAML manifest. What's the status of the Pod?

```shell
k create -f chapter1/ch01-pod.yaml -n ckad
k describe pods loop -n ckad
```

8. Edit the pod named loop. Change the command to run in an endless loop. Each iteration should echo the current date
```shell
 k create -f chapter1/ch01-loop-while-pod.yaml -n ckad
k describe pods in-loop -n ckad
```

9. Inspect the events and the status of the Pod loop

```shell
k get pods loop -n ckad
k get pods in-loop -n ckad
k describe pods in-loop -n ckad
```

10. Delete the namespace ckad and its Pods

```shell
k delete namespace ckad
```


### Chapter 2
1. Create a directory with the name config. Within the directory, create two files. The first file should be name db.txt and contain the key-value pair password=mypwd. The second file is named ext-service.txt and should define the key-value pair api_key=LmLHbYhsgWZwNifiqaRorH8T

```code
refer to the **chapter2/config** folder
```

2. Create a Secret named ext-service-secret that uses the directory as data source and inspect the YAML representation of the object

```shell
k create secret generic ext-service-secret --from-file=chapter/config
k get secret ext-service-secret -o yaml
```

3. Create a Pod named consumer with the image nginx and mount the secret as a volume with the mount path /var/app. Open an interactive shell and inspect the values of the Secret

```shell
k create -f chapter2/consumer-pod.yaml
```

4. Use the declarative approach to create a configMap named ext-service-configmap. Feed in the key-value pairs api_endpoint=https://myapp.com/api and username=bot

```shell
k create -f chapter2/ext-service-configmap.yaml
```

5. Inject the ConfigMap values into the existing Pod as environment variables. Ensure that the keys conform to typical naming conventions of the environment variables

```shell
k create -f chapter2/consumer-pod-cmap.yaml
```

6. Open an interactive shell and Inspect the values of the config map
```shell
k get po consumer-cmap -o yaml 
```

7. Define a security context on the container level of a new Pod named security-context-demo that uses the image alpine. The security context adds the Linux capability CAP_SYS_TIME to the container. Explain if the value of this security context can be redefined in a Pod level security context.

```shell
k apply -f chapter2/sec-pod.yaml
```

8. Define a ResourceQuota for the namespace project-firebird. The rules should constrain the count of Secret objects within the namespace to 1.
```shell
k create namespace project-firebird
k apply -f chapter2/project-firebird.yaml
```

9. Create as many Secret objects within the namespace until the maximum number enforced by the ResourceQuota has been reached.
```shell
k create secret generic api-key --from-literal=key=1234
```

10. Create a new Service Account named monitoring and assign it to a new Pod with an image of your choosing. Open an interactive shell and locate the authentication token of the assigned Service Account.
```shell
k create serviceaccount monitoring
k apply -f chapter2/sec-pod-sa.yaml
k get pod security-context-demo -o yaml
```

### Chapter 3
1. Create a YAML manifest for a Pod named complex-pod. The main application container named app should use the image nginx and expose the container port 80. Modify
the YAML manifest so that the Pod defines an init container named setup that uses the image busybox. The init container runs the command wget -o google.com
```shell
refer to **chapter3/complex-pod.yaml
```
2. Create the Pod from the YAML manifest
```shell
k apply -f chapter3/complex-pod.yaml
```

3. Download the logs of the init container. You should see the output of the wget command
```shell
k logs complex-pod -c setup
```

4. Open an interactive shell to the main application container and run the ls command. Exit out of the container.
```shell
k exec complex-pod -c app --it -- /bin/sh
ls
```

5. Force delete the pod
```shell
k delete pod complex-pod --force
```

6. Create a YAML manifest for a pod named data-exchange. The main application container named main-app should use the image busybox.
The container runs a command that writes a new file every 30 seconds in an infinite loop in the directory /var/app/data. The filename follows the pattern {counter++}-data.txt. The variable counter is incremented every interval and starts with the value 1
```shell
k create -f chapter3/dt-exchange.yaml
```

7. Modify the YAML manifest by adding a sidecar container named sidecar. The sidecar container uses the image busybox and runs a command that counts the number of files 
produced by the main-app container every 60 seconds in an infinite loop. The command runs the number of files to standard output
```shell
k apply -f chapter3/dt-exchange-sc.yaml
```

8. Define a volume of the type emptyDir. Mount the path /var/app/data for both containers
```shell
Refer to **chapter3/dt-exchange-sc.yaml**
```
9. Create the Pod. Tail the logs of the sidecar container
```shell
k apply -f chapter3/dt-exchange-sc-vol.yaml
```
10. Delete the pod
```shell
k delete pod data-exchange
```





