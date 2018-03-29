
__[Concrete Solutions](http://www.concretesolutions.com.br) | DevOps - OnBoarding__

# **Trello (Card):** Learn the basics about Docker

Setup a Cluster using Docker Swarm

## Descrição:

O **"[Swarm](https://docs.docker.com/engine/swarm/)"** consite em múltiplos de hosts docker dispostos como `managers` e `workers` destinados a compor um cluster de gerenciamento de conainers Docker. Instâncias docker em modo swarm podem cumprir papeis como `manager`, `worker` ou ambos.

- `Workers` são responsáveis por provisionar instâncias docker conforme definições das stacks.
- `Managers` garantem a distribuição equivalente de recursos, containers assim como o load balance no cluster.

Durante a criação de um serviço são definidos em stacks os recursos (replicas, storage, definições de rede e publicação de serviços) quais serão assegurados pelo swarm de maneira a garantir escalabilidade e tolerância a falhas. Ou seja, na ocorrência de crash com hosts/containers/serviços ou a partir de algum gatilho os managers se encarregarão de provisionar e/ou distribuir automática e instantaneamente os recursos mínimos (conforme definições da stack) entre os demais hosts que compõem o cluster.

Os serviços são dispostos através de stack's que são grupos de recursos inter-relacionados e que compartilham dependências e podem ser escalados e orquestrados juntos pelo swarm.


## Requerimentos:

- [Hipervisor (virtualbox)](https://www.virtualbox.org/)
- [Docker CE](https://www.docker.com/community-edition)
- [Docker-machine](https://docs.docker.com/machine/)

**Nota:** Neste lab foram instanciados três hosts p/ compor o swarm numa infra local.

## Procedimentos

#### 1. Criar o host **"master"**:

```
$ docker-machine create -d virtualbox master
```

![Create host master](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/create-host-master.png "Create host master")

#### **2. Iniciar o cluster:**
  * **Exportar variaveis p/ a shell corrente:**

  ```
  $ eval "$(docker-machine env master)"
  ```

  ![Vars exporting](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/vars-exporting.png "Var exporting")

  * **Acessar o container master via ssh:**

  ```
  $ docker-machine ssh master
  ```

  ![SSH master host](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/ssh-master.png "SSH master host")

  * **Iniciando o cluster:**

  ```
  $ docker swarm init --advertise-addr eth1
  ```
  **Nota:** Importante tomar nota do token gerado para posteriormente ingressar os demais hosts ao cluster.


  ![Starting Swarm cluster](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/start-cluster.png "Starting cluster")


#### 3. **Criar demais hosts:**

```
$ docker-machine create -d virtualbox minion01
$ docker-machine create -d virtualbox minion02
```

![Create hosts 01](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/create-hosts01.png "create-hosts 01")
![Create hosts 02](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/create-hosts02.png "create-hosts 02")


#### 4. **Ingressar os demais hosts ao cluster:**

Será necessário utilizar o token gerado no momento da inicialização do cluster (item 2.).

```
$ docker-machine ssh HOST
$ docker swarm join --token SWMTKN-1-057eub6q65v555v1283u708fkp576tu8ms9v8jbw1xzndpejfn-bczmzbbut7a5wtdnp09dqi5a7 192.168.99.100:2377
```

![Swarm join](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/swarm-join.png "Swarm Join")

#### 5. **Promover demais hosts como master:**

```
$ docker node promote HOST01 HOST02
```

![Promote node](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/promote-node.png "Promote node")

#### 6. **Deploy de uma stack de serviços**

Para este deploy foi utilizado um [compose](https://raw.githubusercontent.com/concrete-aecio-barreto-junior/docker-swarm/master/docker-compose-helloworld.yml) com a imagem **"[HelloWorld](https://hub.docker.com/r/concreteaeciobarretojunior/debian-helloworld/)"** criada durante o OnBoarding (HelloWolrd).

```
$ docker stack deploy -c docker-compose-helloworld.yml hello-world

```

![Stack deploy](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/stack-deploy.png "Stack deploy")


#### 7. **Teste de escalação horizontal:**

```
$ docker service scale hello-world_api=10
```

![Stack scale](https://github.com/concrete-aecio-barreto-junior/docker-swarm/blob/master/images/stack-scale.png "Stack scale")

## Comandos de apoio

- Lista os hosts ativos: `$ docker-machine ls`
- Exibe as configurações do host informado: `$ docker-machine env master`
- Para listar todos os hosts ingressados: `$ docker node ls`
- Obter endereço ip dos hosts: `$ docker-machine ip master`
- Listar containers e distribuição entre nodes: `$ docker stack ps app`
- Remover stack: `$ docker stack rm NAME`

## Links úteis:

- Docker CE: https://www.docker.com/community-edition
- virtualbox: https://www.virtualbox.org/
- Docker Machine: https://docs.docker.com/machine/
- Docker Swarm: https://docs.docker.com/engine/swarm/
- Stack service: https://docs.docker.com/get-started/part5/
- Stack commands: https://docs.docker.com/engine/reference/commandline/stack_rm/#usage
