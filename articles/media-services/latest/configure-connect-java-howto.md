---
title: Ligue-se ao Azure Media Services v3 API - Java
description: Este artigo descreve como ligar ao Azure Media Services v3 API com Java.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 769b4bc431040ee4d872fa60270196db96978ed2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94748187"
---
# <a name="connect-to-media-services-v3-api---java"></a>Ligue-se aos Serviços de Mídia v3 API - Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como ligar-se ao Azure Media Services v3 Java SDK usando o sinal principal de serviço no método.

Neste artigo, o Código do Estúdio Visual é usado para desenvolver a aplicação da amostra.

## <a name="prerequisites"></a>Pré-requisitos

- Siga [a escrita java com código de estúdio visual](https://code.visualstudio.com/docs/java/java-tutorial) para instalar:

   - JDK
   - Apache Maven
   - Pacote de extensão Java
- Certifique-se de definir `JAVA_HOME` e `PATH` de variáveis ambientais.
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social.
- Siga os passos no tópico [das APIs de acesso.](./access-api-howto.md) Grave o ID de assinatura, iD de aplicação (ID do cliente), a chave de autenticação (segredo) e a identificação do inquilino de que necessita num passo posterior.

Também revisão:

- [Java no Código do Estúdio Visual](https://code.visualstudio.com/docs/languages/java)
- [Gestão de Projetos java em Código VS](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Rever [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Criar um projeto do Maven

Abra uma ferramenta de linha de comando e `cd` para um diretório onde pretende criar o projeto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando executar o comando, os `pom.xml` `App.java` ficheiros , e outros são criados. 

## <a name="add-dependencies"></a>Adicionar dependências

1. No Código do Estúdio Visual, abra a pasta onde está o seu projeto
1. Encontrar e abrir o `pom.xml`
1. Adicione as dependências necessárias.

   Veja `pom.xml` na amostra de [codificação de vídeo.](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml)

## <a name="connect-to-the-java-client"></a>Ligue-se ao cliente Java

1. Abra o `App.java` ficheiro em baixo e `src\main\java\com\azure\ams` certifique-se de que o seu pacote está incluído no topo:

    ```java
    package com.azure.ams;
    ```
1. Nos termos da declaração do pacote, adicione estas declarações de importação:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Para criar as credenciais de Diretório Ativo que precisa de fazer pedidos, adicione o código seguinte ao método principal da classe App e desaperte os valores que obteve a partir de APIs de [acesso:](./access-api-howto.md)
   
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

## <a name="see-also"></a>Ver também

- [Conceitos de Serviços de Mídia](concepts-overview.md)
- [SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Java reference](/java/api/overview/azure/mediaservices/management) (Referência de Java)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Passos seguintes

Agora pode incluir `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` e começar a manipular entidades.

Para mais exemplos de código, consulte as [amostras java SDK](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) repo.
