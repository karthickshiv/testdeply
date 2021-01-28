# testdeploy of ngnix


Create a deployment running nginx version 1.16.1 that will run in 2 pods that uses /var/www as its root dir. Nginx should run on port 8090

Lets create dev namespace and place the pod.

cat namespace.yaml
==============================================


kubectl config set-context $(kubectl config current-context) --namespace=dev



kubectl apply -f ngnix_deploy_nodeport.yaml
kubectl apply -f ngnix_svc.yaml

kubectl get po,deploy,svc

curl http://clusterIP:8090

from web browser http://nodeip:31200
====================================================================================================================



a. Create a separate pod that mounts a volume. The volume should have a dir /var/www and contain a file "Hello World" html. This Pod should load before Nginx ( you need to prove this)
b. Create service Load Balancer for Nginx
c. Make Nginx accessible via the internet on port 80

file name : ngnix_deploy.yaml


kubectl apply -f ngnix_deploy_loadbalancer.yaml
kubectl apply -f ngnix_svc1.yaml

from web browser http://externalip


d. Scale this to 4 pods.

open new terminal and run the following command
watch -n kubectl get pods,rs,deploy

another terminal run the following command to increase replicas to 4

Imperative way

kubectl scale deploy/nginx-web --replicas=4



f. Create rolling update to 1.19.4


kubectl rollout status deploy/nginx-web

kubectl edit deploy/ngnix_deploy

change ngnix image from ngnix:1.16.1 to 1.19.4

g. Check the status of the upgrade

Use watch command for live status
watch -n kubectl get pods,rs,deploy

kubectl rollout status deploy/ngnix_deploy


h. How do you do this in a way that you can see history of what happened?

kubectl rollout history deploy/ngnix_deploy

for detailed one

kubectl rollout history deploy/ngnix_deploy --revision=numberx (based on previous number)

i. How would you do a Canary Upgrade ?

copy the existing yaml configuration file to canary.yaml

cp -p ngnix_deploy_nodeport.yaml ngnix_deploy_nodeport_canary.yaml

Chane lable version : v2 and hostpath to v2 folder which will look for new index.html and service yaml file remove selector version: v1 so that
traffic will be split between v1 and v2.

kubectl apply -f ngnix_deploy_nodeport_canary.yaml
kubectl apply -f ngnix_svc.yaml


j. Undo the upgrade


kubectl rollout history deploy/ngnix_deploy

Lets say we have revision number 1 which is ngnix 1.16.1 then we can undo using following way


kubectl rollout undo deploy/ngnix_deploy --to-revision=1




k. Scale down to 2 pods

Imperative way

kubectl scale deploy/nginx-web --replicas=2

