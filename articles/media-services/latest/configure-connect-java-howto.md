---
title: Conectar-se à API dos serviços de mídia do Azure v3-Java
description: Saiba como se conectar à API dos serviços de mídia v3 com Java.
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
ms.openlocfilehash: 3f5e45bb84ca4fc46ccf1f3f3ab86d43c7c03cab
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122863"
---
# <a name="connect-to-media-services-v3-api---java"></a>Conectar-se à API dos serviços de mídia v3-Java

Este artigo mostra como se conectar ao SDK do Java dos serviços de mídia do Azure v3 usando o método de entrada da entidade de serviço.

Neste artigo, o Visual Studio Code é usado para desenvolver o aplicativo de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

- Siga a [escrita de Java com Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) para instalar:

   - JDK
   - Apache Maven
   - Pacote de extensão do Java
- Certifique-se de `JAVA_HOME` definir `PATH` as variáveis de ambiente e.
- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia.
- Siga as etapas no tópico [APIs de acesso](access-api-cli-how-to.md) . Registre a ID da assinatura, a ID do aplicativo (ID do cliente), a chave de autenticação (segredo) e a ID de locatário que você precisa em uma etapa posterior.

Examine também:

- [Java em Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gerenciamento de projetos Java no VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Examine as [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Criar um projeto Maven

Abra uma ferramenta de linha de comando `cd` e um diretório onde você deseja criar o projeto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando você executa o comando, o `pom.xml`, `App.java`o e outros arquivos são criados. 

## <a name="add-dependencies"></a>Adicionar dependências

1. Em Visual Studio Code, abra a pasta na qual seu projeto está
1. Localize e abra o`pom.xml`
1. Adicionar as dependências necessárias

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

## <a name="connect-to-the-java-client"></a>Conectar-se ao cliente Java

1. Abra o `App.java` arquivo em `src\main\java\com\azure\ams` e verifique se o pacote está incluído na parte superior:

    ```java
    package com.azure.ams;
    ```
1. Na instrução package, adicione estas instruções de importação:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Para criar as credenciais de Active Directory para as quais você precisa fazer solicitações, adicione o seguinte código ao método principal da classe App e defina os valores obtidos das [APIs de acesso](access-api-cli-how-to.md):
   
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
1. Execute o aplicativo.

## <a name="see-also"></a>Consulte também

- [Conceitos dos serviços de mídia](concepts-overview.md)
- [SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Java reference](https://aka.ms/ams-v3-java-ref) (Referência de Java)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Passos seguintes

Agora você pode incluir `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` e iniciar a manipulação de entidades.

Para obter mais exemplos de código, consulte o repositório [Java SDK Samples](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) .
