---
title: Integrar o aplicativo MongoDB existente com Azure Cosmos DB API para MongoDB e Open Service Broker para Azure (OSBA)
description: Neste artigo, você aprenderá a integrar um aplicativo Java e MongoDB existente com a API Azure Cosmos DB para MongoDB usando o Open Service Broker for Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, abrir Service Broker, abrir Service Broker para o Azure
ms.openlocfilehash: 3d0ab0b27d77e45d779227d30c5a8e4f824ba62a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277693"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrar o aplicativo MongoDB existente com Azure Cosmos DB API para MongoDB e Open Service Broker para Azure (OSBA)

O Azure Cosmos DB é um serviço de base de dados com múltiplos modelos distribuído globalmente. Ele também fornece a compatibilidade de protocolo de conexão com várias APIs NoSQL, incluindo o MongoDB. A API Cosmos DB para MongoDB permite que você use Cosmos DB com seu aplicativo MongoDB existente sem precisar alterar os drivers de banco de dados ou a implementação do aplicativo. Você também pode provisionar um serviço de Cosmos DB usando o Open Service Broker para Azure.

Neste artigo, você pega um aplicativo Java existente que usa um banco de dados MongoDB e o atualiza para usar um banco de dados Cosmos DB usando o Open Service Broker para Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, você deve:
    
* Ter um [cluster do serviço kubernetes do Azure](kubernetes-walkthrough.md) criado.
* Ter [o Service Broker aberto para o Azure instalado e configurado em seu cluster AKs](integrate-azure.md). 
* Tenha a [CLI do catálogo de serviços](https://svc-cat.io/docs/install/) instalada e configurada para executar `svcat` comandos.
* Ter um banco de dados [MongoDB](https://www.mongodb.com/) existente. Por exemplo, você poderia ter o MongoDB em execução em seu [computador de desenvolvimento](https://docs.mongodb.com/manual/administration/install-community/) ou em uma [VM do Azure](../virtual-machines/linux/install-mongodb.md).
* Ter uma maneira de se conectar e consultar o banco de dados MongoDB, como o [shell Mongo](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Obter o código da aplicação
    
Neste artigo, você usa o [aplicativo de exemplo de música Spring da Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) para demonstrar um aplicativo que usa um banco de dados MongoDB.
    
Clone o aplicativo do GitHub e navegue até seu diretório:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Preparar o aplicativo para usar seu banco de dados MongoDB

O aplicativo de exemplo Spring Music fornece muitas opções para fontes de fonte. Neste artigo, você o configura para usar um banco de dados MongoDB existente. 

Adicione o YAML a seguir ao final de *src/main/resources/Application. yml*. Essa adição cria um perfil chamado *MongoDB* e configura um URI e um nome de banco de dados. Substitua o URI pelas informações de conexão em seu banco de dados MongoDB existente. Adicionar o URI, que contém um nome de usuário e senha, diretamente a esse arquivo é **apenas para uso de desenvolvimento** e **nunca deve ser adicionado ao controle de versão**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Quando você inicia seu aplicativo e o informa para usar o perfil do *MongoDB* , ele se conecta ao seu banco de dados MongoDB e o usa para armazenar os dados do aplicativo.

Para compilar seu aplicativo:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Inicie seu aplicativo e solicite que ele use o perfil do *MongoDB* :

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navegue até `http://localhost:8080` em seu navegador.

![Aplicativo Spring Music com dados padrão](media/music-app.png)

Observe que o aplicativo foi preenchido com alguns [dados padrão](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interaja com ele excluindo alguns álbuns existentes e criando alguns novos.

Você pode verificar se seu aplicativo está usando seu banco de dados MongoDB conectando-se a ele e consultando o banco de dados *musicdb* :

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

O exemplo anterior usa o [shell Mongo](https://docs.mongodb.com/manual/mongo/) para se conectar ao banco de dados MongoDB e consultá-lo. Você também pode verificar se suas alterações são persistidas interrompendo seu aplicativo, reiniciando-o e navegando de volta para ele no navegador. Observe que as alterações feitas ainda estão lá.


## <a name="create-a-cosmos-db-database"></a>Criar uma base de dados do Cosmos DB

Para criar um banco de dados Cosmos DB no Azure usando o Open Service Broker, use o comando `svcat provision`:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

O comando anterior provisiona um banco de dados Cosmos DB no Azure no grupo de recursos *MyResource* Group na região *eastus* . Mais informações sobre o *resourcegroup*, o *local*e outros parâmetros JSON específicos do Azure estão disponíveis na documentação de [referência do módulo Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Para verificar se o banco de dados concluiu o provisionamento, use o comando `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Seu banco de dados está pronto para ver *pronto* em *status*.

Depois que o banco de dados tiver concluído o provisionamento, você precisará associar seus metadados a um [segredo kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Outros aplicativos podem então acessar esses dados depois de serem associados a um segredo. Para associar os metadados do banco de dados a um segredo, use o comando `svcat bind`:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>Usar o banco de dados Cosmos DB com seu aplicativo

Para usar o banco de dados Cosmos DB com seu aplicativo, você precisa saber o URI para se conectar a ele. Para obter essas informações, use o comando `kubectl get secret`:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

O comando anterior Obtém o segredo *musicdb* e exibe apenas o URI. Os segredos são armazenados no formato base64, de modo que o comando anterior também o decodifica.

Usando o URI do banco de dados Cosmos DB, atualize *src/main/resources/Application. yml* para usá-lo:

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

A atualização do URI, que contém um nome de usuário e uma senha, diretamente para esse arquivo é **apenas para uso de desenvolvimento** e **nunca deve ser adicionada ao controle de versão**.

Recompile e inicie seu aplicativo para começar a usar o banco de dados Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Observe que seu aplicativo ainda usa o perfil do *MongoDB* e um URI que começa com *MongoDB://* para se conectar ao banco de dados de Cosmos DB. A [API Azure Cosmos DB para MongoDB](../cosmos-db/mongodb-introduction.md) fornece essa compatibilidade. Ele permite que seu aplicativo continue a operar como se ele estiver usando um banco de dados MongoDB, mas está realmente usando Cosmos DB.

Navegue até `http://localhost:8080` em seu navegador. Observe que os dados padrão foram restaurados. Interaja com ele excluindo alguns álbuns existentes e criando alguns novos. Você pode verificar se suas alterações são persistidas interrompendo seu aplicativo, reiniciando-o e navegando de volta para ele no navegador. Observe que as alterações feitas ainda estão lá. As alterações são persistidas na Cosmos DB que você criou usando o Open Service Broker para o Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Executar seu aplicativo no cluster AKS

Você pode usar [Azure dev Spaces](../dev-spaces/azure-dev-spaces.md) para implantar o aplicativo no cluster AKs. Azure Dev Spaces ajuda a gerar artefatos, como gráficos Dockerfiles e Helm, e implantar e executar um aplicativo no AKS.

Para habilitar Azure Dev Spaces em seu cluster AKS:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Use as ferramentas de Azure Dev Spaces para preparar seu aplicativo para ser executado no AKS:

```cmd
azds prep --public
```

Esse comando gera vários artefatos, incluindo *gráficos/* pasta, que é o gráfico do Helm, na raiz do projeto. Este comando não pode gerar um *Dockerfile* para este projeto específico, portanto, você precisa criá-lo.

Crie um arquivo na raiz do seu projeto chamado *Dockerfile* com este conteúdo:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Além disso, você precisa atualizar a propriedade *configurações. desenvolver. Build* em *azds. YAML* para *false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Você também precisa atualizar o atributo *containerPort* para *8080* em *Charts/Spring-Music/templates/Deployment. YAML*:

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

Para implantar seu aplicativo no AKS:

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

Navegue até a URL exibida nos logs. No exemplo anterior, você usaria *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* . 

Verifique se você vê o aplicativo junto com suas alterações.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu como atualizar um aplicativo existente usando o MongoDB para usar Cosmos DB API para MongoDB. Este artigo também abordou como provisionar um serviço de Cosmos DB usando Service Broker abertas para o Azure e implantando esse aplicativo no AKS com Azure Dev Spaces.

Para obter mais informações sobre Cosmos DB, abra Service Broker para o Azure e Azure Dev Spaces, consulte:
* [BD do Cosmos](https://docs.microsoft.com/azure/cosmos-db/)
* [Abrir Service Broker para o Azure](https://osba.sh)
* [Desenvolver com espaços de desenvolvimento](../dev-spaces/azure-dev-spaces.md)
