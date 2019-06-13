# Kubernetes


### Helm cmds:

    helm install --debug --dry-run --name <app_name> --set <k>="<v>" -f values.yaml .
    
    helm upgrade <app_name> --install <repo>/<chart_name> --namespace <ns> -f values.yaml
    
    helm diff
    
    helm tiller
    
    helm ls
    helm del --purge <chart_name>
    
    helm init --force-upgrade
    helm init --client-only
    
    helm repo add <repo_name> <url>
    helm repo remove <repo_name>
    
    helm push <repo>/<chart_name> <url>

### Kubernetes resources:

1. deployment
2. pod
3. service
4. job
5. ingress
6. secret
7. configmap
8. node
9. replicaset
10. daemonset
11. role
12. rolebinding
13. clusterrole
14. clusterrolebinding
15. PersistentVolume
16. PersistentVolumeClaim
17. StorageClass
18. endpoints
19. backendconfig
20. serviceaccounts

### Kubectl cmds:

    kubectl get pods -n <namespace> <pod_name> -o yaml
    
    kubectl get pods --all-namespaces --selector=<k>=<v>
    
    kubectl port-forward svc/<service_name> -n <namespace> 9090:9090
    
    kubectl logs -f <pod> -c <container>
    
    kubectl describe deploy <deployment>
    
    kubectl edit pod -n <namespace> <pod_name>
    
    kubectl get pods --show-labels
    
    #Expose the port of a pod (creates a new service)
    kubectl expose pod <pod> --port=444 --name=frontend            
    
    #Execute a command on the pod
    kubectl exec <pod> -- command
    
    #Add a new label to a pod
    kubectl label pods/nodes <pod> mylabel=awesome  
    
    #Get deployment status
    kubectl rollout status deployment/helloworld-deployment     
    
    #Get the rollout history
    kubectl rollout history deployment/helloworld-deployment      
    
    #Rollback to previous version
    kubectl rollout undo deployment/helloworld-deployment  
    
    #Rollback to any version version       
    kubectl rollout undo deployment/helloworld-deployment --to-revision=n

    kubectl get events -w
    

### Resources

1. [Networking](https://sookocheff.com/post/kubernetes/understanding-kubernetes-networking-model/)
2. [rbac 1](https://medium.com/@lestrrat/configuring-rbac-for-your-kubernetes-service-accounts-c348b64eb242)
3. [rbac 2](https://medium.com/uptime-99/making-sense-of-kubernetes-rbac-and-iam-roles-on-gke-914131b01922)
4. [deploy postgresql](https://severalnines.com/blog/using-kubernetes-deploy-postgresql)

    /code

5. [helm 1](https://www.nclouds.com/blog/simplify-kubernetes-deployments-with-helm-part-1/)
6. [helm 2](https://www.nclouds.com/blog/simplify-kubernetes-deployments-helm-part-2/)
7. [accessing pods from outside of the cluster](http://alesnosek.com/blog/2017/02/14/accessing-kubernetes-pods-from-outside-of-the-cluster/)
8. [external dns](https://medium.com/@marekbartik/google-kubernetes-engine-with-external-dns-on-cloudflare-provider-24beb2a6b8fc)
9. [external dns issue](https://github.com/kubernetes-incubator/external-dns/issues/262)
10. [ssl](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-services-with-ingress-tls-letsencrypt/)

