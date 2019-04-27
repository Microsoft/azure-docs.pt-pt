---
title: As APIs do cliente de Java de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Gerar e utilizar APIs de cliente de Java do Service Fabric com a especificação de REST API de cliente do Service Fabric
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 97bba87331965b0f7ce20ec2ee089e0e18f72457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720285"
---
# <a name="azure-service-fabric-java-client-apis"></a>APIs do cliente de Java de recursos de infraestrutura do serviço do Azure

APIs de cliente do Service Fabric permite a implementação e gestão de microsserviços com base em aplicações e os contentores no cluster do Service Fabric no Azure, no local, na máquina de desenvolvimento local ou em outra cloud. Este artigo descreve como gerar e utilizar APIs de cliente de Java do Service Fabric sobre o cliente do Service Fabric REST APIs

## <a name="generate-the-client-code-using-autorest"></a>Gerar o código de cliente com o AutoRest

[AutoRest](https://github.com/Azure/autorest) é uma ferramenta que gera bibliotecas de cliente para acessar serviços RESTful web. A entrada para AutoRest é uma especificação que descreve a API de REST com o formato de especificação de OpenAPI. [APIs de REST de cliente do Service Fabric](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) siga essa especificação.

Siga os passos mencionados abaixo para gerar o código de cliente de Java do Service Fabric com a ferramenta de AutoRest.

1. Instalar nodejs e NPM no seu computador

    Se estiver a utilizar, em seguida, Linux:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Se estiver a utilizar, em seguida, Mac OS X:
    ```bash
    brew install node
    ```

2. Instale AutoRest utilizando NPM.
    ```bash
    npm install -g autorest
    ```

3. Bifurcar e clonar [azure-rest-api-especificações](https://github.com/Azure/azure-rest-api-specs) repositório no seu computador local e vá para a localização clonada a partir do terminal da sua máquina.


4. Vá para a localização mencionada abaixo no seu repositório clonado.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Se a sua versão do cluster não é 6.0. *, em seguida, vá para o diretório adequado na pasta estável.
    >   

5. Execute o seguinte comando do autorest para gerar o código de cliente java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Segue-se um exemplo que demonstram a utilização do autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   O seguinte comando demora ``servicefabric.json`` especificação do ficheiro como entrada e gera um código de cliente de java no ``java-rest-api-     code`` pasta e engloba o código na ``servicefabricrest`` espaço de nomes. Após este passo encontraria duas pastas ``models``, ``implementation`` e dois arquivos ``ServiceFabricClientAPIs.java`` e ``package-info.java`` gerada na ``java-rest-api-code`` pasta.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Incluir e utilizar o cliente gerado no seu projeto

1. Adicione o código gerado adequadamente para o seu projeto. Recomendamos que crie uma biblioteca com o código gerado e incluir esta biblioteca no seu projeto.
2. Se estiver a criar uma biblioteca, inclua a seguinte dependência no projeto de sua biblioteca. Se estiver a seguir uma abordagem diferente, em seguida, inclua a dependência adequadamente.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Por exemplo, se estiver a utilizar o Maven, sistema de compilação incluem o seguinte no seu ``pom.xml`` ficheiro:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Crie um RestClient usando o seguinte código:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Usar o objeto de cliente e fazer chamadas adequadas conforme necessário. Aqui estão alguns exemplos que demonstram a utilização de objeto cliente. Partimos do princípio de que o pacote de aplicação é criado e carregado para armazenamento de imagens antes de utilizar a abaixo da API.
    * Aprovisionar uma aplicação
    
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
Cada API, encontrará quatro sobrecargas de implementação. Se existirem parâmetros opcionais, em seguida, encontraria variações de mais quatro, incluindo desses parâmetros opcionais. Por exemplo, considere a API ``removeReplica``.
 1. **público removeReplica void (nodeName de cadeia de caracteres, UUID partitionId, replicaId de cadeia, booleano forceRemove, tempo limite de tempo)**
    * Esta é a variante síncrona da chamada removeReplica API
 2. **público ServiceFuture\<Void > removeReplicaAsync (nodeName, UUID partitionId, replicaId de cadeia, booleano forceRemove, tempo limite de comprimento, final ServiceCallback de cadeias de caracteres\<Void > serviceCallback)**
    * Essa variante da chamada de API pode ser usado se quiser usar futura programação assíncrona com base e usar retornos de chamada
 3. **Observable pública\<Void > removeReplicaAsync (nodeName de cadeia de caracteres, UUID partitionId, replicaId de cadeia de caracteres)**
    * Essa variante da chamada de API pode ser usado se quiser usar a programação assíncrona reativa
 4. **público Observable\<ServiceResponse\<Void >> removeReplicaWithServiceResponseAsync (nodeName de cadeia de caracteres, UUID partitionId, replicaId de cadeia de caracteres)**
    * Essa variante da chamada de API pode ser usado se quiser usar a programação assíncrona reativa e lidar com a resposta do rest não processados

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [do Service Fabric REST APIs](https://docs.microsoft.com/rest/api/servicefabric/)

