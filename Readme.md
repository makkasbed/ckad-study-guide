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
k create -f ch01-pod.yaml -n ckad
k describe pods loop -n ckad
```

8. Edit the pod named loop. Change the command to run in an endless loop. Each iteration should echo the current date
```shell
 k create -f ch01-loop-while-pod.yaml -n ckad
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