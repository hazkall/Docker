<br />
<p align="center">
  <h3 align="center"> Docker - Laboratório e Aprendizado </h3>
  <h3 align="center"> Todo esse laboratório é baseado nos treinamentos do https://www.linuxtips.io/ </h3>
</p>

###Parametro para executar um contair é o $docker run

```sh

docker run hello-world

```
####Ele irá até o docker hub, fará download do container e irá executar. Caso seja dado #docker ps ele não irá aparecer, pois ele ja executou a sua função e fechou.

```sh

docker ps

```

###Para mostrar todas as imagens no S.O

```sh

docker images

```

###Visualizar todos os containers da máquina, mesmo parados e finalizados (Atenção é importante administrar container parados e antigos)

```sh

docker ps -a

```

###Parametros do docker run

```sh

-t permiti o terminal
-i permiti interação com container
-d rodar como daemon (processo)

```

###Exemplo:

```sh

docker run -ti ubuntu /bin/bash

```

- OBS:. quando nenhum parametro é passado o shell é iniciado como padrão no docker run

       - Apertando CTRL+D mata o shell e fecha o container
       - Apertando CTRL+PQ sai do container e mantem rodando

###Para voltar ao container, basta dar attach no mesmo

```sh

docker attach <CONTAINER ID>

```

###Para criar um container (Ele não é executado)

```sh

docker create <nome_do_container>
docker run -ti <nome_do_container>

```
###Parar um container

```sh

docker stop <CONTAINER ID>

```

###Iniciar um containers

```sh

docker start <CONTAINER ID>

```

###Pausar um container

```sh

docker pause <CONTAINER ID>

```

###Despausar um container

```sh

docker unpause <CONTAINER ID>

```

###Verificar os recursos consumidos pelo container

```sh

docker stats <CONTAINER ID>

```

###TOP do docker para mais detalhes do containers

```sh

docker top <CONTAINER ID>

```

###Verificar as logs de um container

```sh

docker logs <CONTAINER ID>

```

###Deletar um container

```sh

docker rm <CONTAINER ID>

```

  - OBS:. Se o container estiver em execução você não consegue remove-lo, sendo necessário dar um stop ou usar o parametro -f de force


###Limitação de Recursos nos Containers

       - Caso não seja passado nenhuma limitação, o container nao terá limites de utilização de recursos
       - Para monitorar os recursos utilizamos:

```sh

docker run -ti --name teste debian
docker inspect <CONTAINER ID>

```
- Informações de memória do containers
  
```sh

docker inspect <CONTAINER ID> | grep -i "mem"

```
  - Quando a primeira linha memory está com valor 0, quer dizer que não há limitação de utilização de memoria

###Criando a limitação de memória para o exemplo de 512MB

```sh

docker run --memory 512m --name teste_memoria centos <CONTAINER ID>

```

  - Executando o mesmo comando anterior para fazer o inspect é possivel ver que o campo memoria ficou agora limitado aos 512MB

###Alterando memória de um container já em execução para 256MB

```sh

docker update --memory 256m <CONTAINER ID>

```
#Limitando recursos de CPU

```sh

docker run -ti --cpu<cores> <n> <CONTAINER ID> --name teste_cpu centos

```
  - Fazer a verificar do recurso de CPU com o inspect
  
```sh

docker inspect <CONTAINER ID> | grep -i "cpu"

```

  - Atualizar a limitação de CPU com container em execução

```sh

docker update --cpu 0.5 <CONTAINER ID>

```
  - Verificar a limitação de CPU

```sh

docker inspect <docker_name | CONTAINER ID | grep -i "cpu"

```
###Volumes e container data-only


```sh

docker run -ti --volume /teste_fs centos /bin/bash

```
  - Será criado uma nova montagem no local indicado, que será o volume persistente do container

  - Para verificar o volume criado, utilizaremos o Inspect do Docker

```sh

docker inspect <docker_name | CONTAINER ID> -f {{.Mounts}}

```

  - Caso não seja feito o mapeamento de onde o diretorio será montado, o proprio docker irá realizar a ação. Porém é possivel apontar o local que verá ser feito o mapeamento

```sh

docker run -ti -v /root/primeiro_dockerfile:/volume_container debian

```

  - Com o comando assim indicamos que o diretorio que deve ser mapeado no host pelo Docker será o primeiro_dockerfile e a montagem no container terá o nome volume_container e ficará no /

### Container Data-Only

  - Container sem a necessidade de estar em execução, onde ele possuirá volumes que serão compartilhados com outros containers

  - Primeiro vamos criar os containers não executa-lo.
  
```sh

docker create --volume /data --name dbdados ubuntu 

```

  - Agora iremos utilzar esse volume criado no container anterior para apontar para os proximos

OBS:. Paramentros novos:

-p -> expor uma porta para o container
-e -> setar a variavel de ambiente
-d -> rodar como daemon
--volumes-from -> aponta qual será o container data-only

  - Subindo 2 containers:

```sh

docker run -d \
-p 5432:5432 \
--name pgsql1 \
--volumes-from dbdados \
-e POSTGRESQL_USER=docker \
-e POSTGRESQL_PASS=docker \
-e POSTGRESQL_DB=docker \
kamui/postgresql

```

```sh

docker run -d \
-p 5433:5432 \
--name pgsql2 \
--volumes-from dbdados \
-e POSTGRESQL_USER=docker \
-e POSTGRESQL_PASS=docker \
-e POSTGRESQL_DB=docker \
kamui/postgresql

```

  - É possivel verificar agora os 2 containers gravado os dados no volume do primeiro container utilizando o comando #docker inspect dbdados -f {{.Mounts}}

