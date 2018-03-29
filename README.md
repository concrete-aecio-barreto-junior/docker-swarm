
__[Concrete Solutions](http://www.concretesolutions.com.br) | DevOps - OnBoarding__

# **Trello (Card):** Learn the basics about Docker

Setup a Cluster using Docker Swarm (numa infra local com virtualbox)

## Descrição:

O **"Swarm"** consite em múltiplos de hosts docker insânciados como **"managers"** e **"workers"**. Instâncias docker em modo swarm podem cumprir papeis como manager, worker ou ambos. **Workers** são responsáveis por provisionar instâncias docker conforme definições das stacks. Os **managers** garantem a distribuição equivalente de recursos, containers assim como o load balance no cluster. Durante a criação de um serviço são definidos em stacks os recursos (replicas, storage, definições de rede e publicação de serviços) quais serão assegurados pelo swarm de maneira a garantir escalabilidade e tolerância a falhas. Ou seja, na ocorrência de crash com hosts/containers/serviços os managers se encarregarão de provisionar e/ou distribuir automática e instantaneamente os recursos mínimos definidos entre os demais hosts que compõem o cluster.

Os serviços são dispostos através de stack's que são grupos de recursos inter-relacionados e que compartilham dependências e podem ser escalados e  orquestrados juntos pelo swarm.

## Requerimentos:

- Hipervisor (virtualbox)
- Docker-machine

> **Obs.:** Neste lab foram instanciados três hosts p/ atuarem no swarm.

## Procedimentos

1. Criar o host **"master"**:

```
$ docker-machine create -d virtualbox master
```

![Create host master](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/create-host-master.png "Create host master")

2. Iniciar o cluster
  * Exportar variaveis p/ a shell corrente:

  ```
  $ eval "$(docker-machine env master)"
  ```

  ![Vars exporting](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/vars-exporting.png "Var exporting")

  * Acessar o container master via ssh:

  ```
  $ docker-machine ssh master
  ```

  ![SSH master host](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/ssh-master.png "SSH master host")

  * Iniciando o cluster

  ```
  $ docker swarm init --advertise-addr eth1
  ```

  ![Starting Swarm cluster](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/start-cluster.png "Starting cluster")


3. Criar demais hosts

```
$ docker-machine create -d virtualbox minion01
$ docker-machine create -d virtualbox minion02
```

![Create hosts 01](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/create-hosts01.png "create-hosts 01")
![Create hosts 02](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/create-hosts02.png "create-hosts 02")


4. Ingressar os demais hosts ao cluster

```
$ docker-machine ssh HOST
$ docker swarm join --token SWMTKN-1-057eub6q65v555v1283u708fkp576tu8ms9v8jbw1xzndpejfn-bczmzbbut7a5wtdnp09dqi5a7 192.168.99.100:2377
```

![Swarm join](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/swarm-join.png "Swarm Join")

5. Promover demais hosts como master

```
$ docker node promote HOST01 HOST02
```

![Promote node](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/promote-node.png "Promote node")

6. Deploy de uma stack de serviços

```
$ docker stack deploy -c docker-compose-prodswarm.yml app
```

![XXX](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/XXX.png "XXX")


7. Teste de escalação horizontal:

```
$ docker service scale app_api=10
```

![XXX](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/XXX.png "XXX")

## Comandos de apoio

- Lista os hosts ativos:
```
$ docker-machine ls
```
- Exibe as configurações do host informado:
```
$ docker-machine env master #
```
- Para listar todos os hosts ingressados:
```
$ docker node ls
```
- Obter endereço ip dos hosts:
```
$ docker-machine ip master
```
- Listar containers e distribuição entre nodes:
```
$ docker stack ps app
```
## Links úteis:

Stack service https://docs.docker.com/get-started/part5/
