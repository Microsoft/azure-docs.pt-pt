---
title: ApIs cliente de tecido de tecido azure Java
description: Gerar e utilizar APIs cliente de tecido de serviço Java usando especificação REST API cliente de tecido de serviço
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 0a243c1cd0ab0dcb93a1cc6169c89ba18606f346
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451675"
---
# <a name="azure-service-fabric-java-client-apis"></a>ApIs cliente de tecido de tecido azure Java

O cliente de serviço Fabric APIs permite a implantação e gestão de aplicações e contentores baseados em microserviços num cluster de Tecido de Serviço em Azure, no local, em máquina de desenvolvimento local ou noutra nuvem. Este artigo descreve como gerar e usar APIs cliente de Tecido de Serviço Java em cima do cliente de tecido de serviço REST APIs

## <a name="generate-the-client-code-using-autorest"></a>Gere o código do cliente usando o AutoRest

[A AutoRest](https://github.com/Azure/autorest) é uma ferramenta que gera bibliotecas de clientes para aceder a serviços web RESTful. A entrada no AutoRest é uma especificação que descreve a API REST utilizando o formato de especificação OpenAPI. [Serviço Tecido cliente REST APIs](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) siga esta especificação .

Siga os passos abaixo mencionados para gerar código de cliente Service Fabric Java utilizando a ferramenta AutoRest.

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

2. Instale o AutoRest utilizando o NPM.
    ```bash
    npm install -g autorest
    ```

3. Repositório de fork e clone [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) na sua máquina local e vá para o local clonado a partir do terminal da sua máquina.


4. Vá ao local mencionado abaixo no seu repo clonado.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Se a sua versão cluster não for de 6.0.* então vá para o diretório apropriado na pasta estável.
    >   

5. Execute o seguinte comando de auto-encosto para gerar o código do cliente java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Abaixo está um exemplo que demonstra o uso do auto-rest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   O comando ``servicefabric.json`` seguinte toma o ficheiro de especificação ``java-rest-api-     code`` como entrada e gera ``servicefabricrest`` código cliente java na pasta e encerra o código no espaço de nome. Após este passo, encontraria ``implementation`` duas ``ServiceFabricClientAPIs.java`` pastas , ``java-rest-api-code`` e dois ficheiros ``models``e ``package-info.java`` geradas na pasta.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Inclua e use o cliente gerado no seu projeto

1. Adicione o código gerado adequadamente no seu projeto. Recomendamos que crie uma biblioteca utilizando o código gerado e inclua esta biblioteca no seu projeto.
2. Se está a criar uma biblioteca, então inclua a seguinte dependência do projeto da sua biblioteca. Se estiver a seguir uma abordagem diferente, então inclua a dependência adequadamente.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Por exemplo, se estiver a utilizar o sistema ``pom.xml`` de construção Maven, inclua o seguinte no seu ficheiro:

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
4. Utilize o objeto do cliente e efaça as chamadas adequadas conforme necessário. Aqui estão alguns exemplos que demonstram o uso do objeto cliente. Assumimos que o pacote de aplicações é construído e enviado para uma loja de imagens antes de usar os API's abaixo.
    * Prever um pedido
    
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
Para cada API encontrará quatro sobrecargas de implementação. Se houver parâmetros opcionais, encontrará mais quatro variações, incluindo esses parâmetros opcionais. Por exemplo, considere ``removeReplica``a API .
 1. **vazio público removerReplica (String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * Esta é a variante sincronizada da chamada ai da Réplica remove
 2. **serviço públicoFuture\<Void> removerReplicaAsync (String nodeName, UUID partitionId, String replicaId, Boolean\<forceRemove, Long timeout, serviceCallback final Void> serviceCallback)**
    * Esta variante da chamada API pode ser usada se quiser usar programação assíncrona baseada no futuro e utilizar chamadas
 3. **vazio observável\<público> removerReplicaAsync (String nodeName, UUID partitionId, String replicaId)**
    * Esta variante da chamada API pode ser usada se quiser utilizar uma programação assíncrona reativa
 4. **público Observável\<\<ServiceResponse Void>> removerReplicaWithServiceResponseAsync (String nodeName, UUID partitionId, String replicaId)**
    * Esta variante da chamada API pode ser usada se quiser usar programação assíncrona reativa e lidar com a resposta do descanso RAW

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [as APIs](https://docs.microsoft.com/rest/api/servicefabric/) de REPOUSO DE Tecido de Serviço

