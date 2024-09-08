# Configure ConfigMaps in Applications
  - Take me to [Video Tutorial](https://kodekloud.com/topic/configure-configmaps-in-applications/)
  
In this section, we will take a look at configuring configmaps in applications

## ConfigMaps
- There are 2 phases involved in configuring ConfigMaps. 
  - First, create the configMaps
  - Second, Inject then into the pod.
- There are 2 ways of creating a configmap.
  - The Imperative way
    ```
    $ kubectl create configmap app-config --from-literal=APP_COLOR=blue --from-literal=APP_MODE=prod
    $ kubectl create configmap app-config --from-file=app_config.properties (Another way)
    ```
    ![cmi](../../images/cmi.PNG)
    
  - The Declarative way
    
    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
     name: app-config
    data:
     APP_COLOR: blue
     APP_MODE: prod
    ```
    ```
    Create a config map definition file and run the 'kubectl create` command to deploy it.
    $ kubectl create -f config-map.yaml
    ```
    ![cmd1](../../images/cmd1.PNG)
    
 ## View ConfigMaps
 - To view configMaps
   ```
   $ kubectl get configmaps (or)
   $ kubectl get cm
   ```
 - To describe configmaps
   ```
   $ kubectl describe configmaps
   ```
   
   ![cmv](../../images/cmv.PNG)
   
 ## ConfigMap in Pods
 - Inject configmap in pod
   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: simple-webapp-color
   spec:
    containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
      - containerPort: 8080
      envFrom:
      - configMapRef:
          name: app-config
   ```
   ```
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: app-config
   data:
     APP_COLOR: blue
     APP_MODE: prod
   ```
   ```
   $ kubectl create -f pod-definition.yaml
   ```
  
   ![cmp](../../images/cmp.PNG)
   
 #### There are other ways to inject configuration variables into pod   
 - You can inject it as a **`Single Environment Variable`** 
 - You can inject it as a file in a **`Volume`**
 
   ![cmp1](../../images/cmp1.PNG)

 ### The following is an example of a Pod definition file using a ConfgMap to map APP_COLOR
 **`Pod Definition - webapp-definition.yml`**
   ```
   ---
   apiVersion: v1
   kind: Pod
   metadata:
     labels:
       name: webapp-color
     name: webapp-color
     namespace: default
   spec:
     containers:
     - env:
       - name: APP_COLOR
         valueFrom:
          configMapKeyRef:
            name: webapp-config-map
            key: APP_COLOR
       image: kodekloud/webapp-color
       name: webapp-color
   ```
**`ConfigMap Definition - webapp-config.yml`**
  ```
  apiVersion: v1
  kind: ConfigMap
  metadata:
    creationTimestamp: null
    name: webapp-config-map
  data:
    APP_COLOR: darkblue
    APP_OTHER: disregard
  ```
 #### K8s Reference Docs
 - https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/
 - https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#define-container-environment-variables-using-configmap-data
 - https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-files
