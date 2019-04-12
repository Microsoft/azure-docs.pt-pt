---
title: Ligar à API de v3 dos serviços de multimédia do Azure - Java
description: Saiba como ligar a serviços de multimédia v3 API com Java.
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
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 9177a1ae1f2939979d1f824c98b6018a83c2779f
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502267"
---
# <a name="connect-to-media-services-v3-api---java"></a>Ligar à API de v3 de serviços de multimédia - Java

Este artigo mostra-lhe como ligar para o SDK de Java dos serviços de multimédia do Azure v3 com o início de sessão de principal de serviço no método.

Neste artigo, o Visual Studio Code é utilizado para desenvolver uma aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Siga [escrita Java com o Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) para instalar:

   - JDK
   - Apache Maven
   - Pacote de extensão do Java
- Certifique-se definir `JAVA_HOME` e `PATH` variáveis de ambiente.
- [Criar uma conta de Media Services](create-account-cli-how-to.md). Certifique-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia.
- Siga os passos a [APIs de acesso](access-api-cli-how-to.md) tópico. Registe o ID de subscrição, ID da aplicação (ID de cliente), a chave de autenticação (segredo) e o ID do inquilino que precisa num passo posterior.

Consulte também:

- [Java no Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gerenciamento de projetos de Java no VS Code](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Crie um projeto Maven

Abra uma ferramenta de linha de comandos e `cd` para um diretório onde pretende criar o projeto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Ao executar o comando, o `pom.xml`, `App.java`, e outros ficheiros são criados. 

## <a name="add-dependencies"></a>Adicionar dependências

1. No Visual Studio Code, abra a pasta onde está a seu projeto. 
1. Localize e abra o `pom.xml`. 
1. Adicione as dependências necessárias. Uma delas é [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-suporte de dados](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar).

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
      <artifactId>azure-mgmt-media</artifactId>
      <version>1.0.0-beta</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.rest</groupId>
      <artifactId>client-runtime</artifactId>
      <version>1.6.5</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-client-authentication</artifactId>
      <version>1.6.5</version>
    </dependency>
    </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Ligar para o cliente de Java

1. Abra o `App.java` de ficheiros em `src\main\java\com\azure\ams` e certificar-se de que o pacote está incluído na parte superior:

    ```java
    package com.azure.ams;
    ```
2. Sob a instrução de pacote, adicioná-las importar instruções:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
2. Para criar as credenciais do Active Directory que precisa para fazer pedidos, adicionar o seguinte código ao método principal da classe App e defina os valores que obteve da [APIs de acesso](access-api-cli-how-to.md):
   
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

## <a name="see-also"></a>Consulte também

- [Conceitos de serviços de multimédia](concepts-overview.md)
- [SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Referência de Java](https://aka.ms/ams-v3-java-ref)
- [https://search.maven.org/](https://search.maven.org/)

## <a name="next-steps"></a>Passos Seguintes

Agora pode incluir `import com.microsoft.azure.management.mediaservices.v2018_07_01.Asset;` e começar a manipular entidades.<br/>
Por exemplo, obter todos os recursos na sua conta: `Observable<Asset> asyncAssets = 
                    manager.assets().listAsync(groupId, accountId).last();`
