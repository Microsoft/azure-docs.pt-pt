---
title: Ligue-se ao Azure Media Services v3 API - Java
description: Este artigo descreve como ligar-se à Azure Media Services v3 API com java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74888500"
---
# <a name="connect-to-media-services-v3-api---java"></a>Ligação aos Media Services v3 API - Java

Este artigo mostra-lhe como se conectar ao Azure Media Services v3 Java SDK utilizando o sinal principal de serviço no método.

Neste artigo, o Código do Estúdio Visual é usado para desenvolver a aplicação de amostras.

## <a name="prerequisites"></a>Pré-requisitos

- Siga [a Escrita Java com código de estúdio visual](https://code.visualstudio.com/docs/java/java-tutorial) para instalar:

   - JDK
   - Apache Maven
   - Pacote de extensão java
- Certifique-se `JAVA_HOME` de `PATH` definir e configurar variáveis ambientais.
- [Criar uma conta de Media Services.](create-account-cli-how-to.md) Lembre-se do nome do grupo de recursos e do nome da conta Media Services.
- Siga os passos no tópico [access APIs.](access-api-cli-how-to.md) Grave o ID de subscrição, o ID da aplicação (ID do cliente), a chave de autenticação (segredo) e o ID do inquilino de que necessita num passo posterior.

Também reveja:

- [Java no Código do Estúdio Visual](https://code.visualstudio.com/docs/languages/java)
- [Gestão de Projetos Java em Código VS](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Rever convenções de [nomeação.](media-services-apis-overview.md#naming-conventions)

## <a name="create-a-maven-project"></a>Criar um projeto Maven

Abra uma ferramenta de `cd` linha de comando e para um diretório onde pretende criar o projeto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando executa o `pom.xml`comando, os , `App.java`e outros ficheiros são criados. 

## <a name="add-dependencies"></a>Adicionar dependências

1. No Código do Estúdio Visual, abra a pasta onde está o seu projeto
1. Encontrar e abrir o`pom.xml`
1. Adicione as dependências necessárias

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Ligue-se ao cliente Java

1. Abra `App.java` o `src\main\java\com\azure\ams` ficheiro em baixo e certifique-se de que o seu pacote está incluído no topo:

    ```java
    package com.azure.ams;
    ```
1. Ao abrigo da declaração do pacote, adicione estas declarações de importação:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Para criar as credenciais de Diretório Ativo que precisa para fazer pedidos, adicione o seguinte código ao método principal da classe App e delineie os valores que obteve a partir de APIs de [acesso:](access-api-cli-how-to.md)
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Execute a aplicação.

## <a name="see-also"></a>Consulte também

- [Conceitos de Serviços de Media](concepts-overview.md)
- [SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Java reference](https://aka.ms/ams-v3-java-ref) (Referência de Java)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Passos seguintes

Agora pode `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` incluir e começar a manipular entidades.

Para mais exemplos de código, consulte as amostras Java [SDK](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) repo.
