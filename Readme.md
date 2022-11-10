# Curso de kubernetes

https://devhints.io/docker

https://devhints.io/dockerfile

https://devhints.io/docker-compose

https://labs.play-with-docker.com/

https://labs.play-with-k8s.com/

# Repaso de contenedores

Contenedor: no es una entidad. Son distintas tecnologias que trabajando en conjunto crean un contenedor.

Las 3 tecnologias son:

- CGroups: Asignan a cada contenedor/proceso los recursos va a utilizar (memoria, disco, cpu). Limitan el uso de recursos del sistema operativo para cada contenedor.

- Chroot: Nos permite que nuestro proceso/container tenga visibilidad sobre archivos donde tiene que trabajar y no acceder a otros recursos del sistema.

- Namespaces: (son 7, aqui los mas importantes):

Mount: Nos permite que nuestro proceso tenga una visibilidad reducida de los directorios. Esto permite que dos contenedores que trabajen sobre un sistema de archivos no se interfieran entre si.

Networking: Permite que cada contenedor tenga su dirección IP, su tabla de rutas, su interfaz de red, y que no interfiera con otros contenedores.
Concepto de POD: Entidad atomica scheduleable - Entidad sobre la cual kubernetes va a ejecutar los contenedores. (se verá en detalle más adelante).

PID o de proceso: si ejecutamos un ps cuando ejecutamos nuestro contenedor, vamos a ver que nuestro contenedor es el process id 1 y no vemos todo el resto de los procesos del SO. Esto es posible gracias al namespace de procesos. (se verá en detalle más adelante).

![](https://i.imgur.com/0IxNkeb.png)

# De pods a contenedores

## Docker & Kubernetes

- Docker se encarga principalmente de gestionar los contenedores.
- Kubernetes es una evolución de proyectos de Google Borg & Omega.
- Kubernetes pertenece a la CNCF (Cloud Native Computing Foundation).
- Todos los cloud providers (GCP/AWS/Azure/DO) ofrecen servicios de managed k8s utilizando Docker como su container runtime
- Es la plataforma más extensiva para orquestación de servicios e infraestructura

## Kubernetes en la práctica

- K8s permite correr varias réplicas y asegurarse que todas se encuentren funcionando.
- Provee un balanceador de carga interno o externo automáticamente para nuestros servicios.
- Definir diferentes mecanismos para hacer roll-outs de código.
- Políticas de scaling automáticas.
- Jobs batch. (Procesos de tiempo definido)
- Correr servicios con datos stateful.

**Todos los contenedores del mismo pod comparten el mismo namespaces de red.**

## POD

Un POD vive en un nodo, es un grupo de contenedores que trabajan uno del lado del otro 

![](https://i.imgur.com/rwzDjCG.png)

**Todos los contenedores que viven dentro de un mismo Pod comparten el mismo segmento de red.**

# ¿Cómo funciona la arquitectura de red de Kubernetes?

![](https://i.imgur.com/k8TjtqW.png)

## Nodo Master

- API Server: A lo que todo se conecta, los agentes, el CLI, el dashboard etc. Cuando se cae un nodo master es lo que se pierde. Se usa el algoritmo de ruft para algoritmo de elección.

- Scheduler: Cuando se deben crear un job, un pod en máquinas específicas, el scheduler se encarga de asignar las tareas y administrar los flujos de trabajos, revisando siempre las restricciones y los recursos disponibles.

- Controller Manager: Es un proceso que está en un ciclo de reconciliación constante buscando llegar al estado deseado con base al modelo declarativo con el que se le dan instrucciones a K8s.

### ++Tipos de controller manager: ++
- Replica manager
- Deployment manager
- Service manager

Componentes muy importantes que viven en los nodos:

- Kubelet: Agente de kubernetes, se conecta con el control play y le pregunta que recursos (pods, contenedores) debo correr al scheduler via API Server. Monitorea los pods constantemente para saber si están vivos, los recursos disponibles etc y le comunica constantemente al scheduler via API Server.

- Kube-proxy: Se encarga de balancear el tráfico que corre en nuestros contenedores/servicios. Una vez llega una request se encarga de decidir a que pod y contenedor debe de ir.

**Nodos == Minions**

Todos los nodos y masters están conectados a una red física para poder hablarsen entre sí.

[algoritmo de consenso Raft](https://raft.github.io/)

![](https://i.imgur.com/gXOo7Ie.png)