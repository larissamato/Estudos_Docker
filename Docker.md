

# Docker

https://github.com/vaamonde/dell-linuxmint/blob/master/software/03-docker.md

https://www.youtube.com/watch?v=iOI6V5iR9Cw

**Instalação do Docker CE no Linux Mint 20.1 Ulyssa, 20.2 Uma e 20.3 Una x64**

Site Oficial do Docker: https://www.docker.com/

Site Oficial do Docker Hub: https://hub.docker.com/

Site Oficial da documentação: https://docs.docker.com

**00_ Verificando as Informações do Sistema Operacional Linux Mint**

	OBSERVAÇÃO IMPORTANTE: Linux Mint 20.3 Una é derivado do Ubuntu Desktop 20.04.4 Focal Fossa
	sudo cat /etc/os-release

**01_ Atualização do Sistema Operacional Linux Mint**

	sudo apt update
	sudo apt upgrade
	sudo apt full-upgrade
	sudo apt dist-upgrade
	sudo apt autoremove
	sudo apt autoclean

**02_ Instalando as Dependência do Docker CE no Linux Mint**

	sudo apt install apt-transport-https ca-certificates curl software-properties-common

**03_ Adicionando a Chave GPG do Docker CE no Linux Mint**

	#opções do comando curl: -f (fail), -s (silent), -S (show-error), -L (location)
	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

**04_ Adicionando o Repositório do Docker CE no Linux Mint**

	sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

**05_ Atualizando as Lista do Apt com o novo Repositório do Docker CE no Linux Mint**

	sudo apt update

**06_ Instalando o Docker CE e suas Dependências no Linux Mint**

	sudo apt install docker-ce docker-compose git vim python2 python3
	
	OBSERVAÇÃO IMPORTANTE: a versão do Docker-Compose utilizando o Source List do Docker-CE está
	desatualizada em relação ao projeto do Github: https://github.com/docker/compose, é recomendado
	baixar o Binário do projeto e atualizar a versão no Linux Mint com o procedimento abaixo (NÃO
	COMENTADO NO VÍDEO)
	
	#opção do comando apt: purgue (remove --purge packages)
	sudo apt purgue docker-copose
	
	#opção do comando curl: -S (show-error), -L (location), -o (output)
	sudo curl -SL https://github.com/docker/compose/releases/download/v2.6.0/docker-compose-linux-x86_64 -o /usr/bin/docker-compose
	
	#opção do comando chmod: -v (verbose), 755 (User=RWX,Group-R-X,Other-R-X)
	sudo chmod -v 755 /usr/bin/docker-compose

**07_ Adicionando o Usuário Local no Grupo do Docker CE no Linux Mint**

	#opções do comando usermod: -a (append), -G (groups), $USER (environment variable)
	sudo usermod -a -G docker $USER
	newgrp docker
	id
	
	#recomendado reinicializar a máquina para aplicar as permissões
	sudo reboot

**08_ Verificando o serviço do Docker CE, Docker Compose e Versão**

	sudo systemctl status docker
	docker version
	docker info
	docker-compose version
	docker system info

**09_ Iniciando um Container de Teste do Docker CE**

	#opção do comando docker: search (Search the Docker Hub for images), run (Run a command in a new container)
	docker search hello-world
	docker run hello-world

**10_ Iniciando um Container de Teste do Ubuntu Bash no Docker CE**

	#opções do comando docker: search (Search the Docker Hub for images), run (Run a command in a new container), 
	#-i (interactive), -t (tty)
	docker search ubuntu
	docker run -it ubuntu bash
		cat /etc/os-release
		apt update
		apt install net-tools iputils-ping
		exit

**11_ Verificando as Imagens dos Container no Docker CE**

	#opção do comando docker: images (List images container on system), ps (List containers)
	docker images
	docker ps

**12_ Limpando todas as Imagens, Container, Volumes e Redes no Docker CE**

	#opção do comando docker: prune (Remove unused data), rmi (Remove one or more images)
	docker system prune
	docker rmi hello-world:latest
	docker rmi ubuntu:latest
	docker images



## Alura

- Máquinas virtuais possuem camadas adicionais de virtualização em relação a um container;
- Containers funcionam como processos no host;
- Containers atingem isolamento através de namespaces: PID,NET,IPC,MNT,UTS;
- Os recursos dos containers são gerenciados através de cgroups.



## Comandos importantes

**Baixar imagem e depois executar**

```
docker pull ubuntu 
docker run ubuntu
```

**Containers em execução**

```
docker ps
```

**Ver todos os containers, inclusive os sem execução**

```
docker ps -a
```

**Passar comando para executar para manter o container vivo**

```
docker run ubuntu sleep 1d
```

**Parar a execução do container**

```
docker stop id_do_container
docker stop -t=0 id_do_container
```

**Rexecutar o container parado**

```
docker start id_do_container
```

**Executar comando em modo interativo**

```
docker exec -it id_do_container bash
docker run -it ubuntu bash
exit
```

**Criar um arquivo para mostrar que são sistemas de arquivos isolados**

```
cd 
touch eu-sou-um-arquivo.txt
ls
top (ver_processos)
```

Toda vez que dá um stop toda a árvore dos processos é resetada

**Pausar e despausar o container**

```
docker pause id_do_container
docker unpause id_do_container
```

**Parar todos os containers**

```
docker stop $(docker container ls -q)
```

**Excluir todos os containers**

```
docker container rm $(docker container ls -aq)
```

**Excluir todas as imagens**

```
docker rmi $(docker image ls -aq) --force
```



## Mapeando portas

**Imagem de site estático**

```
docker run -d dockersamples/static-site
docker ps
```

**Remover e parar**

```
docker rm id --force
```

**Se for acessar a porta 80 ela não vai rodar no localhost já que está isolada NET. Para acessar, precisamos expor a porta.**

```
docker run -d -P dockersamples/static-site
```

**Ver o mapeamento de portas em relação ao host**

```
docker port id_do_container
```

Agora se colocar localhost:49154(porta que foi apresentada). Ele irá apresentar a página web.

**Configurar a porta manualmente com a flag -p**

```
docker run -d -p 8080:80 dockersamples/static-site
```



## Entendendo imagens

**Ver as imagens baixadas no sistema**

```
docker images
docker image ls
```

**Ver mais detalhes da imagem**

```
docker inspect id_da_imagem
```

**Ver quais são as camadas**

```
docker history id_da_imagem
```

O docker é inteligente e já reutiliza alguma camada de imagens quando já existe no host.

O container é uma imagem com uma camada a mais de read/write, e é por isso que conseguimos escrever alguma coisa nesse container. Quando o container é deletado, essa camada também é deletada. Por esse motivo o container é tão leve, ele reaproveita as imagens e o que cada container vai ter necessariamente é a fina camada de R/W.



## Criar a primeira imagem 

O exemplo será com um arquivo simples de node. Será necessário criar um arquivo Dockerfile dentro da pasta do projeto.

Dentro desse arquivo definimos como vai ser a criação dessa nova imagem. Podemos pegar no próprio docker hub as configurações do node.

**Configurações dentro do Dockerfile**

```
FROM node:14
WORKDIR /app-node
COPY . .
RUN npm install
ENTRYPOINT npm start 
```

**No terminal entra dentro da pasta**

```
cd /Documents/app-exemplo/
```

**Gerar a imagem**

```
docker build -t larissamatos/app-node:1.0 .
```

**Ver as imagens e criar o container dela**

```
docker images
docker run -d -p 8081:3000 larissamatos/app-node:1.0
```

Agora é só acessar no localhost:8081



## Incrementando imagem

**Documentar a imagem para explicitar qual a porta dela para outras pessoas verem EXPOSE **

```
FROM node:14
WORKDIR /app-node
EXPOSE 3000
COPY . .
RUN npm install
ENTRYPOINT npm start 
```

**Configuração da porta no momento da criação da imagem**

Abre o index.js  e em app.listen("3000", ()=>{
    console.log("Server is listening on port 3000")
})

Mudar para:

app.listen(process.env.PORT, ()=>{
    console.log("Server is listening on port 3000")
})

```
FROM node:14
WORKDIR /app-node
ARG PORT_BUILD=6000
ENV PORT=$PORT_BUILD
EXPOSE $PORT_BUILD
COPY . .
RUN npm install
ENTRYPOINT npm start 
```

**Criar a nova imagem e o container**

```
docker build -t larissamatos/app-node:1.1 .
docker run -d -p 9090:6000 larissamatos/app-node:1.1
```

Dessa forma há como definir variáveis de ambiente dentro do container utilizando o ENV  e as variáveis explícitas na construção  de imagens como o ARG e por fim a utilização do EXPOSE para ser mais semântico para as outras pessoas. 



## Subindo imagem para o docker hub

- Criar a sua conta 

**Autenticar a conta**

```
docker login -u larissa
```

**Colocar a imagem no Docker Hub**

```
docker tag larissamatos/app-node:1.0 larissa/app-node:1.0
docker push larissa/app-node:1.0
```

**Push nova versão**

```
docker tag larissamatos/app-node:1.2 larissa/app-node:1.2
docker push larissa/app-node:1.2
```

Ele já reconhece a versão e a reutilização de imagens



## Problemas de persistir dados

**Iniciar um container ubuntu como exemplo**

```
docker run -it ubuntu bash
```

**Ver o tamanho do container**

```
docker ps -s
```

**Fazer algumas operações para ocupar espaço no ubuntu**

```
apt-get update
```

**Executa o comando com a tag -s e observa o SIZE**

```
docker ps -s
```

Como persistir essas informações? Pode utilizar volumes, bindMount, tmpfsMount

- **Usando bindmount usando a tag -v para persistir algum diretório na máquina local**

```
mkdir volume-docker
docker run -it -v /home/ti/Documentos/volume-docker:/app ubuntu bash
ls
cd app
touch arquivo.txt
```

Se criar um novo container com as mesmas especifidades ele vai persistir os dados.

**Usando --mount**

```
docker run -it --mount type=bind,source=/home/ti/Documentos/volume-docker,target=/app ubuntu bash
```

- **Usando Volumes**

O docker irá gerenciar, é mais seguro.

**Ver volumes**

```
docker volume ls
```

**Criar volume**

```
docker volume create meu-volume
```

**Realizar a persistência**

```
docker run -it -v meu-volume:/app ubuntu bash
```

**Esse meu-volume vai estar onde na máquina local?**

```
sudo su
cd /var/lib/docker/volumes/meu-volume/_data
ls
```

Podemos gerenciar pelo docker agora

**Criar o volume com --mount**

```
docker run -it --mount source=meu-volume,target=/app ubuntu bash
```

- **Usando tmpfs, ele é temporário**

```
docker run -it --tmpfs=/app ubuntu bash
```

Se tiver algum dado sensível que não quer persistir na camada de R/W ele pode ser usado.



## Conhecendo rede bridge

**Inspecionar o container**

```
docker inspect id_do_container
```

**Ver redes**

```
docker network ls
```

**Comunicar com o outro container pois está na mesma rede**

```
apt-get update 
apt-get install iputils-ping -y
ping 172.17.0.2
```

## Criando uma rede Bridge 

**Criar a própria rede**

```
docker network create --driver bridge bridge-estudo
```

**Definindo os próprios nomes nos containers**

```
docker run -it --name ubuntu1 --network bridge-estudo ubuntu bash
```

**Criar um outro container dentro dessa rede  e tentar pingar com o nome um do outro**

```
ping pong
```



## HOST NONE

**None fica com rede isolada**

```
docker run -d --network none ubuntu sleep 1d
docker inspect id
```

**Interface de rede vinculada ao host, não precisa realizar mapeamento de porta**

```
docker run -d --network host ubuntu sleep 1d
docker inspect id
```



## Comunicando aplicação e banco 

```
docker pull mongo:4.4.6
docker pull aluradocker/alura-books:1.0

docker run -d --network bridge-estudo --name meu-mongo mongo:4.4.6
docker run -d --network bridge-estudo --name alurabooks -p 3000:3000 aluradocker/alura-books:1.0
```

No arquivo de configuração ele está procurando o host de dados meu-mongo

acessar localhost:3000/seed

Se eu parar o banco de dados, o front end não mostrará mais os livros, demonstrando a comunicação do banco com a aplicação. 



## Conhecendo Docker Compose

O Docker Compose nada mais é do que uma ferramenta de coordenação de containers. Não confunda com orquestração, são coisas diferentes.

O Docker Compose irá resolver o problema de executar múltiplos containers de uma só vez e de maneira coordenada, evitando executar cada comando de execução individualmente.

**Criar diretório**

```
cd Área\ de\ Trabalho/
cd WORKSPACE/
mkdir ymls
cd ymls 
code . 
```

**Criar um docker-compose.yml na pasta e no arquivo configurar: **

```
version: "3.9"
services:
  mongodb:
    image: mongo:4.4.6
    container_name: meu-mongo
    networks:
      - compose-bridge
  alurabooks:
    image: aluradocker/alura-books:1.0
    container_name: alurabooks
    networks:
      - compose-bridge
    ports:
      - 3000:3000
networks:
  compose-bridge:
    driver: bridge
```

**No terminal rodar o docker compose (Deve estar dentro do diretório ymls)**

```
docker-compose up
```

**Colocar instruções para definir dependências em ordem**

```
version: "3.9"
services:
  mongodb:
    image: mongo:4.4.6
    container_name: meu-mongo
    networks:
      - compose-bridge
  alurabooks:
    image: aluradocker/alura-books:1.0
    container_name: alurabooks
    networks:
      - compose-bridge
    ports:
      - 3000:3000
    depends_on:
      - mongodb
networks:
  compose-bridge:
    driver: bridge

```

```
docker-compose up -d
```

```
docker-compose ps
```

**Remoção dos containers e da rede que foi criada**

```
docker-compose down
```

