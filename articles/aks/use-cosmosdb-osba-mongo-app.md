---
title: Integrar a aplicação mongoDB existente com a Azure Cosmos DB API para MongoDB e Open Service Broker para o Azure (OSBA)
description: Neste artigo, aprende-se a integrar uma aplicação java e MongoDB existente com o Azure Cosmos DB API para o MongoDB utilizando o Open Service Broker para o Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Corretor de Serviços Abertos, Corretor de Serviços Abertos para o Azure
ms.openlocfilehash: ddaa3b9aa198bc142e1bcbcab6b7b1e028eff2aa
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247926"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrar a aplicação mongoDB existente com a Azure Cosmos DB API para MongoDB e Open Service Broker para o Azure (OSBA)

Azure Cosmos DB é um serviço de base de dados com múltiplos modelos distribuído globalmente. Também proporciona compatibilidade de protocolo de arame com várias APIs NoSQL, incluindo para MongoDB. O Cosmos DB API para MongoDB permite-lhe utilizar o Cosmos DB com a sua aplicação MongoDB existente sem ter de alterar os controladores de base de dados ou implementação da sua aplicação. Também pode fornecer um serviço Cosmos DB utilizando o Open Service Broker para o Azure.

Neste artigo, você pega numa aplicação java existente que usa uma base de dados MongoDB e aatual-a para usar uma base de dados Cosmos DB usando open service broker para Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder prosseguir, deve:
    
* Criar um [cluster de serviço Azure Kubernetes.](kubernetes-walkthrough.md)
* Tenha [o Open Service Broker para o Azure instalado e configurado no seu cluster AKS](integrate-azure.md). 
* Tenha o [Catálogo de ServiçoS CLI](https://svc-cat.io/docs/install/) instalado e configurado para executar `svcat` comandos.
* Tenha uma base de dados [mongoDB](https://www.mongodb.com/) existente. Por exemplo, pode ter o MongoDB a funcionar na sua máquina de [desenvolvimento](https://docs.mongodb.com/manual/administration/install-community/) ou num [VM Azure.](../virtual-machines/linux/install-mongodb.md)
* Tenha uma maneira de ligar e consultar a base de dados mongoDB, como a concha de [mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Obter o código da aplicação
    
Neste artigo, você usa a aplicação de amostra de [música de primavera da Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) para demonstrar uma aplicação que usa uma base de dados MongoDB.
    
Clone a aplicação do GitHub e navegue para o seu diretório:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Prepare a aplicação para utilizar a sua base de dados MongoDB

A aplicação da amostra de música de primavera fornece muitas opções para datasources. Neste artigo, configurá-lo para usar uma base de dados mongoDB existente. 

Adicione o YAML seguindo o fim do *sRC/main/resources/application.yml*. Esta adição cria um perfil chamado *mongodb* e configura um nome URI e base de dados. Substitua o URI pela informação de ligação à base de dados mongoDB existente. A adição do URI, que contém um nome de utilizador e uma palavra-passe, diretamente a este ficheiro é **apenas** para uso de desenvolvimento e **nunca deve ser adicionado ao controlo da versão**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Quando inicia a sua aplicação e lhe diz para usar o perfil *mongodb,* liga-se à sua base de dados MongoDB e utiliza-a para armazenar os dados da aplicação.

Para construir a sua aplicação:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Inicie a sua aplicação e diga-lhe para usar o perfil *mongodb:*

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navegue para `http://localhost:8080` no seu navegador.

![Aplicativo Spring Music com dados padrão](media/music-app.png)

Note que a aplicação foi povoada com alguns [dados predefinidos](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interaja com ele apagando alguns álbuns existentes e criando alguns novos.

Pode verificar se a sua aplicação está a utilizar a sua base de dados MongoDB, conectando-se à sua base de dados e consultando a base de dados *de musicdb:*

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

O exemplo anterior utiliza a [concha de mongo](https://docs.mongodb.com/manual/mongo/) para ligar à base de dados mongoDB e questioná-la. Também pode verificar se as suas alterações são persistidas, interrompendo a sua aplicação, reiniciando-a e navegando de volta para a sua no seu navegador. Reparem que as alterações que fizeram ainda estão lá.


## <a name="create-a-cosmos-db-database"></a>Criar uma base de dados Cosmos DB

Para criar uma base de dados Cosmos DB em Azure utilizando o Open Service Broker, utilize o comando `svcat provision`:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

O comando anterior prevê uma base de dados Cosmos DB em Azure no grupo de recursos *MyResourceGroup* na região *oriental.* Mais informações sobre *recursosGroup*, *localização,* e outros parâmetros JSON específicos do Azure estão disponíveis na documentação de referência do [módulo Cosmos DB.](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3)

Para verificar se a sua base de dados completou o fornecimento, utilize o comando `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

A sua base de dados está pronta quando se ver *Ready* under *STATUS*.

Uma vez concluída a sua base de dados, é necessário ligar os seus metadados a um [segredo kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Outras aplicações podem então aceder a esses dados depois de terem sido ligados a um segredo. Para ligar os metadados da sua base de dados a um segredo, utilize o comando `svcat bind`:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Utilize a base de dados Cosmos DB com a sua aplicação

Para utilizar a base de dados Cosmos DB com a sua aplicação, precisa de saber o URI para se ligar a ela. Para obter esta informação, use o comando `kubectl get secret`:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

O comando anterior obtém o segredo *do musicdb* e exibe apenas o URI. Os segredos são armazenados no formato base64, pelo que o comando anterior também o descodifica.

Utilizando o URI da base de dados Cosmos DB, atualize *o sRC/principal/recursos/aplicação.yml* para usá-lo:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

A atualização do URI, que contém um nome de utilizador e uma palavra-passe, diretamente para este ficheiro é **apenas** para uso de desenvolvimento e **nunca deve ser adicionada ao controlo da versão**.

Reconstruir e iniciar a sua aplicação para começar a usar a base de dados Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Note que a sua aplicação ainda utiliza o perfil *mongodb* e um URI que começa com *mongodb://* para ligar à base de dados Cosmos DB. O [Azure Cosmos DB API para mongoDB](../cosmos-db/mongodb-introduction.md) proporciona esta compatibilidade. Permite que a sua aplicação continue a funcionar como se estivesse a usar uma base de dados MongoDB, mas na verdade está a usar cosmos DB.

Navegue para `http://localhost:8080` no seu navegador. Note que os dados predefinidos foram restaurados. Interaja com ele apagando alguns álbuns existentes e criando alguns novos. Pode verificar se as suas alterações são persistidas, interrompendo a sua aplicação, reiniciando-a e navegando de volta para a sua no seu navegador. Reparem que as alterações que fizeram ainda estão lá. As mudanças persistem no Cosmos DB que criou usando o Open Service Broker para o Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Execute a sua aplicação no seu cluster AKS

Pode utilizar o [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) para implementar a aplicação no seu cluster AKS. A Azure Dev Spaces ajuda-o a gerar artefactos, como dockerfiles e gráficos Helm, e implementa e executa uma aplicação no AKS.

Para permitir a Azure Dev Spaces no seu cluster AKS:

```azurecli
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Utilize a ferramenta Azure Dev Spaces para preparar a sua aplicação para funcionar em AKS:

```cmd
azds prep --public
```

Este comando gera vários artefactos, incluindo um *gráfico/pasta,* que é o seu gráfico Helm, na raiz do projeto. Este comando não pode gerar um *Dockerfile* para este projeto específico, por isso tens de o criar.

Crie um ficheiro na raiz do seu projeto chamado *Dockerfile* com este conteúdo:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Além disso, você precisa atualizar as *configurações.develop.build.property* in *azds.yaml* to *false:*
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Também precisa de atualizar o atributo do *contentorPorta* a *8080* em *gráficos/música/modelos/deployment.yaml:*

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Para implementar a sua aplicação no AKS:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Navegue para o URL apresentado nos registos. No exemplo anterior, utilizaria *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* . 

Verifique se vê a aplicação juntamente com as suas alterações.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu como atualizar uma aplicação existente desde a utilização do MongoDB até à utilização da Cosmos DB API para o MongoDB. Este artigo também abordou como fornecer um serviço Cosmos DB usando Open Service Broker para Azure e implementando essa aplicação para AKS com a Azure Dev Spaces.

Para mais informações sobre cosmos DB, Open Service Broker para Azure, e Azure Dev Spaces, consulte:
* [BD do Cosmos](https://docs.microsoft.com/azure/cosmos-db/)
* [Corretor de Serviço aberto para Azure](https://osba.sh)
* [Desenvolver com Espaços Dev](../dev-spaces/azure-dev-spaces.md)
