---
title: Integrar aplicações existentes do MongoDB API do Azure Cosmos DB para MongoDB e Open Service Broker for Azure (OSBA)
description: Neste artigo, saiba como integrar uma aplicação de Java e MongoDB já existente com a API do Azure Cosmos DB para MongoDB através do Open Service Broker for Azure (OSBA).
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: O cosmos DB, abra o Service Broker, o Open Service Broker for Azure
ms.openlocfilehash: 0655176a044da6b4dd6b7327aedf5eb17d79f04a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432809"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrar aplicações existentes do MongoDB API do Azure Cosmos DB para MongoDB e Open Service Broker for Azure (OSBA)

Azure Cosmos DB é um serviço de base de dados com múltiplos modelos distribuído globalmente. Ele também fornece a compatibilidade de protocolo de transmissão com várias NoSQL APIs, incluindo o MongoDB. A API do Cosmos DB do MongoDB permite-lhe utilizar o Cosmos DB com a sua aplicação MongoDB existente sem ter de alterar os drivers de base de dados da sua aplicação ou implementação. Também pode aprovisionar um serviço de Cosmos DB com o Open Service Broker para o Azure.

Neste artigo, levar uma aplicação Java existente que utiliza uma base de dados do MongoDB e atualizá-la para utilizar uma base de dados do Cosmos DB com o Open Service Broker para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder continuar, tem de:
    
* Ter uma [cluster do Azure Kubernetes Service](kubernetes-walkthrough.md) criado.
* Ter [Open Service Broker for Azure instalado e configurado no seu cluster do AKS](integrate-azure.md). 
* Tem a [CLI do catálogo de serviço](https://svc-cat.io/docs/install/) instalado e configurado para ser executado `svcat` comandos.
* Ter um existente [MongoDB](https://www.mongodb.com/) base de dados. Por exemplo, poderia ter MongoDB em execução no seu [máquina de desenvolvimento](https://docs.mongodb.com/manual/administration/install-community/) ou num [VM do Azure](../virtual-machines/linux/install-mongodb.md).
* Tem uma forma de ligar e consultar a base de dados do MongoDB, tais como o [shell do mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Obter o código da aplicação
    
Neste artigo, vai utilizar o [da Primavera de aplicativo de exemplo de música da Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) para demonstrar um aplicativo que usa um banco de dados do MongoDB.
    
Clonar a aplicação a partir do GitHub e navegue para o seu diretório:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Preparar a aplicação para utilizar a sua base de dados do MongoDB

O exemplo de aplicativo de música da Primavera fornece muitas opções para origens de dados. Neste artigo, configure-o para utilizar uma base de dados existente do MongoDB. 

Adicione o seguinte de YAML ao final da *src/main/resources/application.yml*. Esta adição cria um perfil de denominado *mongodb* e configura um nome URI e a base de dados. Substitua o URI com as informações de ligação para a base de dados existente do MongoDB. Adicionar URI, que contém um nome de utilizador e palavra-passe, diretamente a este ficheiro destina-se **apenas a utilização de desenvolvimento** e **nunca devem ser adicionados ao controle de versão**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Quando inicia a aplicação e instruí-lo para utilizar o *mongodb* perfil, estabelece ligação à base de dados MongoDB e utilizá-lo para armazenar os dados do aplicativo.

Para criar seu aplicativo:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Iniciar a aplicação e informe-o para utilizar o *mongodb* perfil:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navegue para http://localhost:8080 no seu browser.

![Aplicação de música Spring com dados padrão](media/music-app.png)

Observe que o aplicativo tiver sido preenchido com algumas [predefinido dados](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interagir com ela, a eliminação de alguns existente álbuns e criar algumas novas.

Pode verificar a sua aplicação está a utilizar a base de dados do MongoDB ao ligar à mesma e consultar a *musicdb* base de dados:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

O exemplo anterior utiliza o [shell do mongo](https://docs.mongodb.com/manual/mongo/) para se ligar à base de dados MongoDB e consultá-los. Também pode verificar que as suas alterações são mantidas por interromper a sua aplicação, reiniciá-lo e naveguem de volta para ele no seu browser. Tenha em atenção as alterações que efetuou ainda estão ali.


## <a name="create-a-cosmos-db-database"></a>Criar uma base de dados do Cosmos DB

Para criar uma base de dados do Cosmos DB no Azure com o Open Service Broker, utilize o `svcat provision` comando:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

O comando anterior Aprovisiona uma base de dados do Cosmos DB no Azure no grupo de recursos *MyResourceGroup* no *eastus* região. Obter mais informações sobre *resourceGroup*, *localização*, e outros parâmetros JSON de específicos do Azure está disponível na [documentação de referência do módulo do Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Para verificar a sua base de dados foi concluída de aprovisionamento, utilize o `svcat get instance` comando:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

A base de dados pronto é ver quando *pronto* sob *estado*.

Depois de concluída a sua base de dados de aprovisionamento, que precisa associar seus metadados para uma [segredo do Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Outros aplicativos, em seguida, podem aceder a esses dados depois que ele foi vinculado a um segredo. Para vincular os metadados da base de dados para um segredo, utilize o `svcat bind` comando:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>Utilizar a base de dados do Cosmos DB com a sua aplicação

Para utilizar a base de dados do Cosmos DB com a sua aplicação, terá de saber o URI para ligar ao mesmo. Para obter estas informações, utilize o `kubectl get secret` comando:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

O comando anterior obtém os *musicdb* segredo e apresenta apenas o URI. Segredos são armazenados em formato base64 para que o comando anterior, também descodifica-lo.

Com o URI de base de dados do Cosmos DB, atualize *src/main/resources/application.yml* usá-lo:

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

A atualizar o URI, que contém um nome de utilizador e palavra-passe, diretamente a este ficheiro destina-se **apenas a utilização de desenvolvimento** e **nunca devem ser adicionados ao controle de versão**.

Reconstruir e iniciar a sua aplicação para começar a utilizar a base de dados do Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Observe a sua aplicação continua a utilizar o *mongodb* perfil e um URI que começa com *mongodb: / /* para ligar à base de dados do Cosmos DB. O [API MongoDB do Azure Cosmos DB](../cosmos-db/mongodb-introduction.md) fornece este compatibilidade. Ele permite que seu aplicativo continuam a funcionar como se está a utilizar uma base de dados do MongoDB, mas, na verdade, está a utilizar do Cosmos DB.

Navegue para http://localhost:8080 no seu browser. Repare que os dados de predefinição foi restaurado. Interagir com ela, a eliminação de alguns existente álbuns e criar algumas novas. Pode verificar que as suas alterações são mantidas por interromper a sua aplicação, reiniciá-lo e naveguem de volta para ele no seu browser. Tenha em atenção as alterações que efetuou ainda estão ali. As alterações são mantidas do Cosmos DB que criou com o Open Service Broker para o Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Execute a sua aplicação no seu cluster do AKS

Pode usar [do Azure Dev espaços](../dev-spaces/azure-dev-spaces.md) para implementar a aplicação ao seu cluster do AKS. Os espaços de desenvolvimento do Azure ajudam a gerar artefatos, como gráficos Dockerfiles e Helm e a implementar e executar um aplicativo no AKS.

Para ativar os espaços de desenvolvimento do Azure no seu cluster do AKS:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Utilize as ferramentas de espaços de desenvolvimento do Azure para preparar a sua aplicação para ser executado no AKS:

```cmd
azds prep --public
```

Este comando gera vários artefactos, incluindo uma *gráficos /* pasta, o que é o gráfico Helm, na raiz do projeto. Este comando não é possível gerar uma *Dockerfile* para este projeto específico, pelo que terá de criá-la.

Crie um ficheiro na raiz do projeto com o nome *Dockerfile* com este conteúdo:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Além disso, tem de atualizar o *configurations.develop.build* propriedade na *azds.yaml* para *false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Terá também de atualizar o *containerPort* para o atributo *8080* na *charts/spring-music/templates/deployment.yaml*:

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

Para implementar a aplicação do AKS:

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

Navegue para o URL apresentado nos registos. No exemplo anterior, usaria *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*. 

Certifique-se de que ver a aplicação, juntamente com as suas alterações.

## <a name="next-steps"></a>Passos Seguintes

Este artigo descreveu como atualizar uma aplicação existente de MongoDB para com a API do Cosmos DB do MongoDB. Este artigo também abordou como aprovisionar um serviço do Cosmos DB com o Open Service Broker para o Azure e implantá-lo para o AKS com os espaços de desenvolvimento do Azure.

Para obter mais informações sobre o Cosmos DB, o Open Service Broker para o Azure e espaços de desenvolvimento do Azure, consulte:
* [BD do Cosmos](https://docs.microsoft.com/azure/cosmos-db/)
* [Abrir o Open Service Broker for Azure](https://osba.sh)
* [Desenvolver com os espaços de desenvolvimento](../dev-spaces/azure-dev-spaces.md)
