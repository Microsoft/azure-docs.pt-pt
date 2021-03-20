---
title: Azure Service Fabric Java Client APIs
description: Gerar e utilizar APIs do cliente java de tecido de serviço usando a especificação API do cliente de Tecido de Serviço REST
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 24ee4a3d8109626bf93e01fbd10c00140762c9c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87324629"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java Client APIs

As APIs do cliente de tecido de serviço permitem implantar e gerir aplicações e contentores baseados em microserviços num cluster de Tecidos de Serviço em Azure, no local, na máquina de desenvolvimento local ou noutra nuvem. Este artigo descreve como gerar e usar APIs do cliente de Tecido de Serviço Java em cima do cliente de Tecido de Serviço REST APIs

## <a name="generate-the-client-code-using-autorest"></a>Gere o código do cliente utilizando o AutoRest

[O AutoRest](https://github.com/Azure/autorest) é uma ferramenta que gera bibliotecas de clientes para aceder a serviços web RESTful. Entrada para AutoRest é uma especificação que descreve a API REST utilizando o formato De especificação OpenAPI. [Cliente de tecido de serviço REST APIs](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) segue esta especificação .

Siga os passos abaixo mencionados para gerar o código do cliente Service Fabric Java utilizando a ferramenta AutoRest.

1. Instalar nodejs e NPM no seu computador

    Se estiver a usar o Linux, então:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Se estiver a utilizar o Mac OS X, então:
    ```bash
    brew install node
    ```

2. Instale o auto-reto com recurso APM.
    ```bash
    npm install -g autorest
    ```

3. O repositório [de óculos de repouso-api-api-de-forcado](https://github.com/Azure/azure-rest-api-specs)  e clone na sua máquina local e vá para a localização clonada a partir do terminal da sua máquina.


4. Vá ao local mencionado abaixo na sua repo clonada.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Se a sua versão de cluster não for de 6.0.* então vá para o diretório apropriado na pasta estável.
    >   

5. Executar o seguinte comando mais auto-mais para gerar o código do cliente java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Abaixo está um exemplo que demonstra o uso da autoria.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   O seguinte comando toma ``servicefabric.json`` o ficheiro de especificações como entrada e gera código java cliente na ``java-rest-api-     code`` pasta e encerra o código no espaço de  ``servicefabricrest`` nome. Após este passo, encontraria duas pastas ``models`` , ``implementation`` e dois ficheiros e ``ServiceFabricClientAPIs.java`` ``package-info.java`` gerados na ``java-rest-api-code`` pasta.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Incluir e utilizar o cliente gerado no seu projeto

1. Adicione o código gerado adequadamente no seu projeto. Recomendamos que crie uma biblioteca utilizando o código gerado e inclua esta biblioteca no seu projeto.
2. Se estiver a criar uma biblioteca, então inclua a seguinte dependência no projeto da sua biblioteca. Se estiver a seguir uma abordagem diferente, então inclua a dependência adequadamente.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Por exemplo, se estiver a utilizar o sistema de construção Maven, inclua o seguinte no seu ``pom.xml`` ficheiro:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Criar um RestClient utilizando o seguinte código:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Use o objeto cliente e faça as chamadas apropriadas conforme necessário. Aqui estão alguns exemplos que demonstram o uso do objeto do cliente. Assumimos que o pacote de aplicações é construído e enviado para a loja de imagens antes de usar o abaixo da API.
    * Provisão de um pedido
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Criar uma aplicação

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Compreender o código gerado
Para cada API encontrará quatro sobrecargas de implementação. Se houver parâmetros opcionais, encontrará mais quatro variações, incluindo os parâmetros opcionais. Por exemplo, considere a API ``removeReplica`` .
 1. **vazio público removeReplica (String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * Esta é a variante sincronizada da chamada API removida
 2. **serviço públicoFuture \<Void> removerReplicaAsync (String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout, serviço final \<Void> ServiceCallback)**
    * Esta variante da chamada API pode ser usada se quiser utilizar a programação assíncrona baseada no futuro e utilizar chamadas
 3. **remoção observável \<Void> públicaReplicaAsync (Nó de cordaName, partitionId UUID, replicaid de cordas)**
    * Esta variante da chamada API pode ser usada se quiser utilizar uma programação assíncrona reativa
 4. **> observáveis \<ServiceResponse\<Void> públicos removemReplicaWithServiceResponseAsync (Nó de corda, partição UUIDId, replicação de cordas)**
    * Esta variante da chamada API pode ser usada se quiser usar programação assíncrona reativa e lidar com a resposta ao repouso RAW

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [as APIs de repouso de tecido de serviço](/rest/api/servicefabric/)
