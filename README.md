# Docker Vs Kubernetes
Docker es una plataforma de contenedores que permite empaquetar y ejecutar aplicaciones en un entorno aislado. Por otro lado, Kubernetes es un sistema de administración de contenedores que permite implementar, escalar y administrar aplicaciones en una red de contenedores.

En resumen, Docker se puede considerar como una herramienta que permite crear y ejecutar contenedores, mientras que Kubernetes es una plataforma que permite gestionar y administrar una red de contenedores de manera eficiente y escalable.

## Cluster Nodes
Son servidores o máquinas que ejecutan los pods y sus contenedores. Cada nodo tiene un rol que determina qué tareas puede realizar en el clúster. Los roles de los nodos pueden incluir cosas como ejecutar pods, almacenar datos, actuar como un punto de entrada para el clúster o realizar tareas de supervisión. Algunos de los roles de nodos comunes en Kubernetes son:

* `Master Node:` Un nodo maestro es un nodo especial que ejecuta el controlador de clúster y realiza tareas de administración como planificar los pods y realizar el seguimiento de su estado.
* `Worker Node:`: Un nodo de trabajo es un nodo que ejecuta los pods y sus contenedores. Los nodos de trabajo pueden realizar cualquier tarea que se le asigne, como procesar solicitudes de entrada o almacenar datos.
* `Storage Node:` Un nodo de almacenamiento es un nodo que se utiliza para almacenar datos persistentes. Los pods que necesiten almacenamiento persistente se colocarán en nodos de almacenamiento para garantizar que sus datos no se pierdan si se detienen o eliminan.
* `Ingress Node:` Un nodo de ingreso es un nodo que actúa como un punto de entrada para el clúster. Los usuarios y aplicaciones externas pueden enviar solicitudes al clúster a través de un nodo de ingreso, que las redirigirá a los nodos de trabajo para su procesamiento.

Los roles de los nodos pueden ser asignados de forma estática o dinámica, dependiendo de las necesidades del clúster. Por ejemplo, un clúster pequeño puede tener un único nodo que actúe como maestro y de trabajo, mientras que un clúster más grande puede tener nodos dedicados para cada rol.

# Kubernetes Components
Todos los componentes tienen una estructura común que se puede especificar mediante un fichero YAML o JSON, en nuestros ejemplos los veremos en formato YAML ya que facilitan la lectura.

El esquema común sería el siguiente:
```yaml
apiVersion: <API version>
kind: <kind of object>
metadata:
  name: <object name>
spec:
  <object specification>
```

# Kubernetes Namespaces
Los namespaces de Kubernetes son una forma de dividir un cluster en diferentes entornos virtuales lógicos. Cada namespace tiene su propio conjunto de objetos, como pods y servicios, y cada objeto tiene un nombre único dentro del namespace. Esto permite a los desarrolladores crear objetos con nombres comunes en diferentes namespaces sin preocuparse por conflictos de nombres. Los namespaces también son útiles para aplicar políticas de seguridad y control de acceso a diferentes recursos en un cluster.

Para realizar peticiones a un servicio alojado en un namespace diferente. Kubernetes nos proporciona el siguiente endpoint:

`http://<service-name>.<namespace-name>.svc.cluster.local`

## Workloads
### Pods
Es un grupo de uno o más contenedores que se ejecutan en un clúster de Kubernetes. Los contenedores en un pod comparten una dirección IP y espacio de almacenamiento en común, lo que les permite comunicarse entre sí de manera fácil. Los pods son la unidad básica de escalado y despliegue en Kubernetes, y se pueden escalar horizontalmente mediante la adición de más pods a un clúster.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx:1.14.2
      ports:
        - containerPort: 80
      resources:
        limits:
          memory: "128Mi"
          cpu: "500m"
```

### ReplicaSet
Se encarga de mantener un número determinado de réplicas de un pod en ejecución en todo momento. Si un pod falla o se detiene, el ReplicaSet se encargará de crear un nuevo pod para mantener el número de réplicas especificado. Esto ayuda a garantizar la alta disponibilidad de los servicios que se ejecutan en el clúster de Kubernetes. El ReplicaSet es similar a un Deployment en Kubernetes, pero a diferencia de un Deployment, un ReplicaSet no tiene la capacidad de realizar actualizaciones en caliente de los pods.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.14.2
          ports:
            - containerPort: 80
          resources:
            limits:
              memory: "128Mi"
              cpu: "500m"
```

### Deployment
Se utiliza para desplegar y administrar aplicaciones en un clúster. Un deployment define un conjunto de réplicas de un pod que se deben ejecutar en el clúster, así como la forma en que se deben actualizar esas réplicas. Cuando se crea un deployment, Kubernetes se encarga de asegurarse de que el número correcto de réplicas del pod esté en ejecución en todo momento, y puede realizar actualizaciones en caliente de los pods de forma transparente para el usuario. Los deployments son útiles para garantizar la alta disponibilidad de las aplicaciones en un clúster y facilitar su actualización y despliegue.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.15.2
          ports:
            - containerPort: 80
          resources:
            limits:
              memory: "128Mi"
              cpu: "500m"
```

### DaemonSet
Se utiliza para asegurarse de que un determinado pod se ejecute en todos los nodos de un clúster, o en un subconjunto específico de nodos. Un DaemonSet es útil para tareas que deben ejecutarse en todos los nodos del clúster, como la supervisión del sistema o el almacenamiento de datos persistentes. Cuando se crea un DaemonSet, Kubernetes se encarga de asegurarse de que una réplica del pod se ejecute en cada nodo adecuado, y si se añaden o quitan nodos del clúster, Kubernetes se encargará de crear o eliminar las réplicas del pod en consecuencia.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.14.2
          ports:
            - containerPort: 80
          resources:
            limits:
              memory: "128Mi"
              cpu: "500m"
```

### StatefulSet
Se utiliza para desplegar y administrar aplicaciones que requieren un estado persistente. Un StatefulSet es similar a un Deployment en Kubernetes, pero a diferencia de un Deployment, un StatefulSet mantiene un orden y un identificador únicos para cada réplica de un pod. Esto es útil para aplicaciones que requieren un estado persistente, como bases de datos, ya que permite que cada réplica del pod tenga una identidad única y recupere su estado de forma consistente después de un reinicio o una actualización. Un StatefulSet también proporciona un mecanismo para realizar actualizaciones en caliente de los pods de forma ordenada y controlada.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  selector:
    matchLabels:
      app: postgres
  serviceName: postgres
  replicas: 3
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:12.3
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_USER
          value: user
        - name: POSTGRES_PASSWORD
          value: password
        - name: POSTGRES_DB
          value: db
        volumeMounts:
        - name: postgres-data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 8Gi
```

### Jobs
Se utilizan para ejecutar tareas que se completan en un tiempo determinado, como la ejecución de una consulta de base de datos o la conversión de un archivo de audio. Un Job se define por un pod que se ejecuta hasta que la tarea se completa, y puede incluir varias réplicas del pod para realizar la tarea de forma paralela. Un Job también puede especificar cómo se deben manejar los errores y los reintentos si una de las réplicas del pod falla. Una vez que se completa la tarea, el Job se completa y Kubernetes se encarga de limpiar los recursos asociados al pod. Los Jobs son útiles para ejecutar tareas que se completan en un tiempo determinado y que no requieren una ejecución continua.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: example-job
spec:
  template:
    spec:
      containers:
        - name: example-container
          image: example-image
          command: ["echo", "Hello, world!"]
      restartPolicy: Never
  backoffLimit: 4
```

### CronJobs
Se utiliza para ejecutar tareas en un horario regular basado en una expresión cron. Un CronJob se define por un pod que se ejecuta en función de un horario especificado por una expresión cron, que indica el tiempo y la frecuencia en que se deben ejecutar las tareas. Por ejemplo, un CronJob puede especificar que se ejecute un pod todos los días a las 8:00 AM, o que se ejecute un pod cada hora en un día determinado. Un CronJob también puede incluir varias réplicas del pod para realizar la tarea de forma paralela, y puede especificar cómo se deben manejar los errores y los reintentos si una de las réplicas del pod falla. Los CronJobs son útiles para ejecutar tareas en un horario regular y automatizar tareas repetitivas.

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: example-cronjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: example-container
              image: example-image
              command: ["echo", "Hello, world!"]
          restartPolicy: OnFailure
  concurrencyPolicy: Allow
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 1
```

## Configuraciones
### ConfigMaps

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  config.properties: |
    app.name=mi-aplicación
    app.description=Esta es una aplicación de ejemplo
    app.author=yo
---
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-image
      env:
        # Asignamos una variable de entorno con valor estatico
        - name: STATIC_VALUE
          value: This is the static value
        # Asignamos una variable de entorno con un valor del configMap
        - name: APP_NAME
          valueFrom:
            configMapKeyRef:
              name: my-config
              key: app.name
      volumeMounts:
        # Montamos un volumen en la carpeta /etc/config que tendrá un fichero 
        # por cada entrada del configmap, en este caso, un fichero config.properties
        # que tendrá el contenido especificado en el configmap.
        - name: config-volume
          mountPath: /etc/config
      resources:
        limits:
          memory: "128Mi"
          cpu: "500m"
  volumes:
    # Declaración del volumen a través del configmap
    - name: config-volume
      configMap:
        name: my-config
```

### Secrets
Permite almacenar y gestionar información sensible, como contraseñas, tokens de autenticación y certificados SSL. Los Secrets se pueden usar para autenticar a los usuarios o servicios que acceden a la aplicación y proteger la información confidencial al transmitirla de un lugar a otro. Los Secrets se almacenan de forma segura en el cluster de Kubernetes y se pueden utilizar en los contenedores de la aplicación mediante el uso de variables de entorno o archivos en el sistema de archivos del contenedor.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mi-secreto
type: Opaque
data:
  mysecretkey: bXlzZWNyZXR2YWx1ZQ==
---
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-image
      env:
        - name: MY_SECRET_KEY
          valueFrom: 
            secretKeyRef:
              name: mi-secreto
              key: mysecretkey
      volumeMounts:
        - name: mi-secreto-volume
          mountPath: /etc/secrets
      resources:
        limits:
          memory: "128Mi"
          cpu: "500m"
  volumes:
    - name: mi-secreto-volume
      secret:
        secretName: mi-secreto
```

### HPA (Horizontal Pod Autoscaler)
Los HPA (Autoscalado de recursos de pods) son una característica de Kubernetes que permite ajustar automáticamente el número de pods en un deployment en función de la utilización de recursos del cluster. Los HPA se basan en uno o varios indicadores de utilización de recursos, como la CPU o la memoria, y pueden aumentar o disminuir el número de pods en un deployment en función de estos valores. Esto permite a Kubernetes adaptarse de manera dinámica a la carga de trabajo y asegurar que los recursos del cluster se utilizan de manera eficiente.

```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
  namespace: default
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```
También es posible definir el HPA directamente desde el deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: my-app:latest
          resources:
            requests:
              cpu: 100m
              memory: 256Mi
            limits:
              cpu: 500m
              memory: 512Mi
  horizontalPodAutoscaler:
    minReplicas: 1
    maxReplicas: 10
    metrics:
      - type: Resource
        resource:
          name: cpu
          targetAverageUtilization: 50
      - type: Resource
        resource:
          name: memory
          targetAverageValue: 256Mi
```

### PDB (Pod Disruption Budget)
Los PDB (Políticas de disponibilidad de pods) son una característica de Kubernetes que le permite definir políticas de disponibilidad para los pods en un deployment. Las políticas de disponibilidad especifican el número mínimo y máximo de pods que deben estar disponibles en un deployment en todo momento, y también pueden especificar condiciones que deben cumplirse para que un pod se considere disponible. Esto permite a Kubernetes asegurarse de que hay suficientes pods disponibles para manejar la carga de trabajo y evitar interrupciones en el servicio.

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
  name: my-pdb
  namespace: default
spec:
  maxUnavailable: 1
  selector:
    matchLabels:
      app: my-app
```

También es posible definir el PDB directamente desde el deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  replicas: 5
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: my-app:latest
  podDisruptionBudget:
    minAvailable: 3
    maxUnavailable: 1
```

### Priority Classes
Permite asignar prioridades a los pods en un cluster. Esto permite a Kubernetes gestionar mejor la asignación de recursos entre los pods y asegurarse de que los pods con prioridades más altas reciban más recursos que los demás. Las priority classes se basan en un sistema de prioridades que va desde 0 (la prioridad más baja) hasta 1023 (la prioridad más alta). Los pods con prioridades más altas recibirán recursos antes que los pods con prioridades más bajas cuando haya una falta de recursos en el cluster.

NOTA: El limite de 1023 se establece en el servicio kube-apiserver, se puede modificar por cualquier entero int32 si tenemos control del servicio `kube-apiserver` definiendo el argumento `--maximum-deadline-seconds`, ej:

```sh
kube-apiserver --maximum-deadline-seconds=2048
```

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 100
globalDefault: false
description: "This priority class should be used for important pods that need guaranteed access to resources."
```

Para usar el priority, deberemos especificar el atributo `priorityClassName` en la definición del POD, en este caso, vamos a mostrar un ejemplo en un `Deployment`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      priorityClassName: high-priority
      containers:
      - name: my-app-container
        image: my-app:latest
        ports:
        - containerPort: 80
```

## Networking
### Endpoints
Son objetos que representan un conjunto de direcciones IP y puertos a los que un servicio puede enviar tráfico. Estos objetos se utilizan para enrutar el tráfico entrante a los pods que forman parte del servicio.

```yaml
apiVersion: v1
kind: Endpoints
metadata:
  name: my-app-endpoints
subsets:
  - addresses:
      - ip: 10.0.1.1
    ports:
      - port: 80
        name: http
---
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  containers:
    - name: my-app-container
      image: my-app-image
      ports:
        - containerPort: 80
          name: http
      resources:
        limits:
          memory: "128Mi"
          cpu: "500m"
  endpoints:
    - name: my-app-endpoints
```

### Services
#### Cluster IP
Permite que los contenedores de un clúster se comuniquen entre sí de manera eficiente. Un ClusterIP es una dirección IP privada que se asigna a un servicio dentro de un clúster de Kubernetes, y permite que los contenedores se comuniquen entre sí a través de esa dirección IP.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

#### Load Balancer
Permite distribuir la carga entre varios contenedores que se ejecutan en un clúster. Cuando se crea un servicio de tipo LoadBalancer en Kubernetes, se asigna una dirección IP externa y un puerto al servicio, y los contenedores del clúster pueden recibir solicitudes a través de esa dirección IP y ese puerto. El servicio de tipo LoadBalancer se encarga de distribuir las solicitudes entre los contenedores de manera eficiente para asegurar una alta disponibilidad y escalabilidad.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

#### NodePort
Permite exponer un servicio en un clúster a través de un puerto específico en cada nodo del clúster. Cuando se crea un servicio de tipo NodePort en Kubernetes, se asigna un puerto en cada nodo del clúster al servicio, y los contenedores del clúster pueden recibir solicitudes a través de ese puerto en cada nodo. Los servicios de tipo NodePort son útiles cuando se quiere exponer un servicio en un clúster a través de un puerto fijo, lo que facilita la configuración de firewall y permite acceder al servicio de manera sencilla.

El siguiente ejemplo expone el puerto 80 del contenedor a través del puerto 30000 de cada nodo del clúster. Cuando se recibe una solicitud en el puerto 30000 de cualquier nodo del clúster, el servicio de tipo NodePort la distribuirá a uno de los contenedores que tienen la etiqueta "app: my-app" y que escuchan en el puerto 8080.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
    nodePort: 30000
```

#### External Name
Permite enlazar un servicio en un clúster con un nombre DNS externo. Cuando se crea un servicio de tipo ExternalName en Kubernetes, se especifica un nombre DNS externo al que se asocia el servicio, y cuando se realizan solicitudes al servicio desde dentro del clúster, se redirigen automáticamente al nombre DNS externo especificado. Los servicios de tipo ExternalName son útiles cuando se quiere enlazar un servicio en un clúster con un servicio externo que no se encuentra en el clúster.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ExternalName
  externalName: my-external-service.example.com
```

### Ingresses
Permite configurar un punto de entrada común para todas las solicitudes entrantes en un clúster. Los Ingresses se utilizan para enrutar las solicitudes entrantes a los servicios correctos en el clúster de manera eficiente y escalable. Cuando se crea un Ingress en Kubernetes, se especifican reglas que indican cómo se deben enrutar las solicitudes entrantes en función de su host o su ruta. Los Ingresses son útiles para exponer varios servicios en un clúster a través de una única dirección IP y un único puerto.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: ingress-auth
    nginx.ingress.kubernetes.io/auth-realm: "Authentication Required"
spec:
  tls:
    - secretName: app-tls
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /frontend
            backend:
              serviceName: frontend-service
              servicePort: 80
          - path: /backend
            backend:
              serviceName: backend-service
              servicePort: 80
```

Los ingresos requieren que nuestro cluster este configurado con un IngressClass de lo contrario no se aplicarán los enrutamientos.

Para instalar la clase de ingreso de nginx podemos utilizar el siguiente comando:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.6.0/deploy/static/provider/cloud/deploy.yaml
```

### Network Policy
Permite especificar reglas de red para controlar el tráfico entre los contenedores en un clúster. Cuando se crea un Network Policy en Kubernetes, se especifican reglas que indican qué contenedores pueden comunicarse entre sí y cómo pueden hacerlo. Los Network Policies son útiles para asegurar que el tráfico en un clúster siga una configuración específica y para evitar ataques potenciales.

*CIDR* es un acrónimo que significa "Classless Inter-Domain Routing". Se trata de una notación utilizada para especificar bloques de direcciones IP en una red. La notación CIDR se utiliza para expresar de manera compacta la cantidad de direcciones IP que se incluyen en un bloque, y se compone de una dirección IP y una máscara de subred. La máscara de subred indica cuántos bits de la dirección IP se utilizan para identificar la red y cuántos bits se utilizan para identificar el host dentro de la red. Por ejemplo, la notación CIDR "192.168.1.0/24" indica que se trata de un bloque de direcciones IP que incluye las direcciones desde "192.168.1.0" hasta "192.168.1.255".

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-app-network-policy
spec:
  podSelector:
    matchLabels:
      app: my-app
  ingress:
  - from:
    - ipBlock:
        cidr: 10.0.0.0/24
        except:
        - 10.0.0.1
    - ipBlock:
        cidr: 192.168.1.0/24
  policyTypes:
  - Ingress
```

# Herramientas

## KubeCTL
Es una herramienta de línea de comandos para interactuar con un clúster de Kubernetes. kubectl permite a los usuarios ejecutar comandos para realizar diversas acciones en un clúster de Kubernetes, como desplegar aplicaciones, ver el estado de los contenedores y servicios, o recuperar información sobre los recursos en el clúster.

kubectl es una herramienta esencial para cualquier persona que trabaje con Kubernetes, ya que permite administrar y controlar un clúster de manera sencilla y eficiente. kubectl se puede instalar en cualquier sistema operativo y se puede utilizar para interactuar con cualquier clúster de Kubernetes, ya sea local o en la nube.

## Kustomize
Kustomize es una herramienta que se utiliza para personalizar configuraciones de Kubernetes. Permite a los usuarios crear versiones modificadas de aplicaciones de Kubernetes sin tener que editar directamente el código fuente. Esto permite que las mismas aplicaciones sean utilizadas en diferentes entornos de despliegue de Kubernetes, como desarrollo, pruebas o producción, simplemente cambiando la configuración. Kustomize se integra con otras herramientas de Kubernetes como kubectl y puede ser utilizado tanto en línea de comandos como en scripts.

Para instalar kustomize:

```bash
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh" | bash

sudo install -o root -g root -m 0755 kustomize /usr/local/bin/kustomize
```

## Lens
Lens es una herramienta de código abierto para administrar y desarrollar aplicaciones de Kubernetes de manera visual. Lens ofrece una interfaz de usuario gráfica que permite a los usuarios ver y controlar el estado de un clúster de Kubernetes de manera sencilla y eficiente. Con Lens, los usuarios pueden ver información detallada sobre los recursos en un clúster, como contenedores, servicios, pod y nodos, y realizar acciones como desplegar aplicaciones, escalar contenedores o recuperar logs.

Además, Lens cuenta con una serie de herramientas y funcionalidades que facilitan el desarrollo y el depurado de aplicaciones en Kubernetes, como la posibilidad de ver y editar archivos en contenedores en tiempo real, ejecutar comandos interactivos en contenedores o depurar aplicaciones en Kubernetes.

## Helm
Helm es una herramienta de código abierto para administrar aplicaciones de Kubernetes de manera eficiente y escalable. Helm utiliza el concepto de "paquetes" para agrupar la configuración de una aplicación en Kubernetes en un solo archivo, lo que facilita su implementación y actualización en un clúster. Helm permite a los desarrolladores definir y gestionar la configuración de sus aplicaciones en Kubernetes de manera declarativa, lo que facilita la colaboración y el despliegue en entornos de producción. Además, Helm cuenta con un repositorio de paquetes que permite a los desarrolladores compartir y reutilizar paquetes de aplicaciones en Kubernetes.

https://artifacthub.io/

# Aplicaciones para administrar Kubernetes

## ArgoCD
ArgoCD es una herramienta de código abierto para implementar y administrar aplicaciones de Kubernetes de manera declarativa. ArgoCD utiliza la filosofía de "Infraestructura como Código" para permitir que los desarrolladores definan la configuración deseada de sus aplicaciones en Kubernetes en archivos de configuración, y luego se encarga de aplicar y mantener esa configuración en el clúster de Kubernetes. ArgoCD es una herramienta muy útil para garantizar la consistencia y la integridad de las aplicaciones en Kubernetes, y para facilitar su implementación y actualización en un clúster.

Para instalar ArgoCD podemos ejecutar los siguientes comandos:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Para verificar si todos los pods se han desplegado correctamente:

```bash
kubectl get pods -n argocd
```
