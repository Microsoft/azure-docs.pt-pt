---
title: APIs de cliente Java Service Fabric do Azure
description: Gerar e usar Service Fabric APIs de cliente Java usando a especificação da API REST do cliente Service Fabric
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 0a243c1cd0ab0dcb93a1cc6169c89ba18606f346
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451675"
---
# <a name="azure-service-fabric-java-client-apis"></a>APIs de cliente Java Service Fabric do Azure

Service Fabric as APIs de cliente permitem implantar e gerenciar aplicativos baseados em microserviços e contêineres em um Cluster Service Fabric no Azure, no local, no computador de desenvolvimento locais ou em outra nuvem. Este artigo descreve como gerar e usar Service Fabric APIs de cliente Java sobre as APIs REST do cliente Service Fabric

## <a name="generate-the-client-code-using-autorest"></a>Gerar o código do cliente usando o REST

O [REST](https://github.com/Azure/autorest) é uma ferramenta que gera bibliotecas de cliente para acessar serviços Web RESTful. A entrada para o REST é uma especificação que descreve a API REST usando o formato de especificação OpenAPI. [Service Fabric as APIs REST do cliente](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) seguem essa especificação.

Siga as etapas mencionadas abaixo para gerar Service Fabric código de cliente Java usando a ferramenta de autorest.

1. Instalar nodejs e NPM no seu computador

    Se você estiver usando o Linux, então:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Se você estiver usando Mac OS X, então:
    ```bash
    brew install node
    ```

2. Instale o autorest usando o NPM.
    ```bash
    npm install -g autorest
    ```

3. Bifurcar e clonar o repositório [Azure-REST-API-Specs](https://github.com/Azure/azure-rest-api-specs) em seu computador local e ir para o local clonado do terminal de seu computador.


4. Vá para o local mencionado abaixo no repositório clonado.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Se a versão do cluster não for 6,0. *, em seguida, vá para o diretório apropriado na pasta estável.
    >   

5. Execute o comando autorest a seguir para gerar o código do cliente Java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Veja abaixo um exemplo que demonstra o uso de autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   O comando a seguir usa ``servicefabric.json`` arquivo de especificação como entrada e gera o código de cliente Java na pasta ``java-rest-api-     code`` e inclui o código no namespace ``servicefabricrest``. Após essa etapa, você encontrará duas pastas ``models``, ``implementation`` e dois arquivos ``ServiceFabricClientAPIs.java`` e ``package-info.java`` gerados na pasta ``java-rest-api-code``.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Incluir e usar o cliente gerado em seu projeto

1. Adicione o código gerado adequadamente ao seu projeto. Recomendamos que você crie uma biblioteca usando o código gerado e inclua essa biblioteca em seu projeto.
2. Se você estiver criando uma biblioteca, inclua a seguinte dependência no projeto da biblioteca. Se você estiver seguindo uma abordagem diferente, inclua a dependência adequadamente.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Por exemplo, se você estiver usando o sistema de Build Maven, inclua o seguinte no arquivo ``pom.xml``:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Crie um RestClient usando o código a seguir:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Use o objeto de cliente e faça as chamadas apropriadas, conforme necessário. Aqui estão alguns exemplos que demonstram o uso do objeto de cliente. Supomos que o pacote de aplicativos seja compilado e carregado no repositório de imagens antes de usar as APIs abaixo.
    * Provisionar um aplicativo
    
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

## <a name="understanding-the-generated-code"></a>Compreendendo o código gerado
Para cada API, você encontrará quatro sobrecargas de implementação. Se houver parâmetros opcionais, você encontrará mais quatro variações, incluindo esses parâmetros opcionais. Por exemplo, considere a API ``removeReplica``.
 1. **public void removeReplica (cadeia de caracteres NodeName, UUID PartitionID, String replicaId, Boolean forceRemove, Long Timeout)**
    * Esta é a variante síncrona da chamada à API do removeReplica
 2. **Public infuturo\<void > removeReplicaAsync (cadeia de caracteres NodeName, UUID PartitionID, String replicaId, Boolean forceRemove, Long timeout, outcallback final\<void > filecallback)**
    * Essa variante da chamada à API pode ser usada se você quiser usar a programação assíncrona com base no futuro e usar retornos de chamada
 3. **Public observed\<void > removeReplicaAsync (String NodeName, UUID PartitionID, String replicaId)**
    * Essa variante da chamada à API pode ser usada se você quiser usar a programação assíncrona reativa
 4. **Public observed\<inresponse\<void > > removeReplicaWithServiceResponseAsync (String NodeName, UUID PartitionID, String replicaId)**
    * Essa variante da chamada à API pode ser usada se você quiser usar a programação assíncrona reativa e lidar com a resposta REST bruta

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [APIs REST do Service Fabric](https://docs.microsoft.com/rest/api/servicefabric/)

