
__[Concrete Solutions](http://www.concretesolutions.com.br) | DevOps - OnBoarding__

# **Trello (Card):** Learn the basics about Docker

Setup a Cluster using Docker Swarm (numa infra local com virtualbox)

## Descrição:

Swarm consite em múltiplas instâncias de hosts docker que atuam como "managers" e "workers". Instâncias docker em modo swarm podem cumprir papeis como manager, worker ou ambos. Workers são responsáveis por provisionar instancia conforme definições. Os managers garantem a distriuição equivalente de recursos, containers assim como o load balance no cluster. Durante a criação de um serviço são definidos recursos (replicas, storage, definições de rede e publicação de serviços) quais serão assegurados pelo docker de maneira a garantir tolerancia a falhas. Ou seja, na ocorrencia de incidentes com hosts/containers/servicos os managers se encarregarão de provisinar e/ou distribuir automatica e instantaneamente os recursos mínimos definidos entre o cluster.

Os serviços são dispostos através de stack's que são grupos de recursos interrelacionados e que compartilham dependencias e podem ser escalados e orquestrados juntos pelo swarm.

## Requerimentos:

- Hipervisor (virtualbox)
- Docker-machine

## Procedimentos

1. Criar o host "master":

```
$ docker-machine create -d virtualbox master
```

![Create host master ](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/create-host-master.png "Create host master")

2. Iniciar o cluster
  * Exportar variaveis

  ```
  $ eval "$(docker-machine env master)"
  ```

  ![Vars exporting](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/vars-exporting.png "Var exporting")

  * Acessar o container master via ssh

  ```
  $ docker-machine ssh master
  ```

  ![SSH master host ](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/ssh-master-host.png "SSH master host")

  * Iniciando o cluster

  ```
  $ docker swarm init --advertise-addr eth1
  ```

  ![Starting Swarm cluster](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/XXX.png "Starting cluster")


3. Criar demais hosts

```
$ docker-machine create -d virtualbox minion01
$ docker-machine create -d virtualbox minion02
```

![XXX](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/XXX.png "XXX")

4. Ingressar os demais hosts ao cluster

```
$ docker-machine ssh HOST
$ docker swarm join --token SWMTKN-1-057eub6q65v555v1283u708fkp576tu8ms9v8jbw1xzndpejfn-bczmzbbut7a5wtdnp09dqi5a7 192.168.99.100:2377
```

![XXX](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/XXX.png "XXX")

5. Promover demais hosts como master

```
$ docker node promote HOST01 HOST02
```

![XXX](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/XXX.png "XXX")

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

Lista os hosts ativos: $ docker-machine ls
Exibe as configurações do host informado: $ docker-machine env master #
Para listar todos os hosts ingressados: $ docker node ls
Obter endereço ip dos hosts: $ docker-machine ip master
Listar containers e distribuição entre nodes: $ docker stack ps app

## Links úteis:

Stack service https://docs.docker.com/get-started/part5/
