---
title: incluir ficheiro
description: incluir ficheiro
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: a0f3e3547c38df63bdab77cf378525072d1e9ad4
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125842"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso de serviços de comunicação implantado e uma cadeia de conexão. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-java-application"></a>Criar uma nova aplicação Java

Abra o terminal ou a janela de comando. Navegue para o diretório onde gostaria de criar a sua aplicação Java. Executar o comando abaixo para gerar o projeto Java a partir do modelo maven-arqueotype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Você vai notar que a tarefa 'gerar' criou um diretório com o mesmo nome que o `artifactId` . Sob este diretório, o diretório src/main/java contém o código fonte do projeto, `src/test/java directory` contém a fonte de teste, e o ficheiro é o Project Object Model do `pom.xml` projeto, ou POM.

### <a name="install-the-package"></a>Instale o pacote

Abra o ficheiro **pom.xml** no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Navegue para o *diretório /src/main/java/com/communication/quickstart*
1. Abra o ficheiro *.java App* no seu editor
1. Substitua a `System.out.println("Hello world!");` declaração
1. Adicionar `import` diretivas

Utilize o seguinte código para começar:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantaneamente com `CommunicationIdentityClient` a chave de acesso do seu recurso e ponto final. Saiba como gerir a [cadeia de ligação do seu recurso.](../create-communication-resource.md#store-your-connection-string) Além disso, pode inicializar o cliente com qualquer cliente HTTP personalizado que implementa a `com.azure.core.http.HttpClient` interface.

Adicione o seguinte código ao método `main`:

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(new AzureKeyCredential(accessKey))
        .buildClient();
```

Também pode fornecer toda a cadeia de ligação utilizando a `connectionString()` função em vez de fornecer o ponto final e a chave de acesso.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

Se tiver gerido a configuração de identidade, consulte [utilização de identidades geridas,](../managed-identity.md)pode também autenticar com identidade gerida.
```java
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
TokenCredential credential = new DefaultAzureCredentialBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(credential)
        .buildClient();
```

## <a name="create-an-identity"></a>Criar uma identidade

A Azure Communication Services mantém um diretório de identidade leve. Utilize o `createUser` método para criar uma nova entrada no diretório com um único `Id` . A loja recebeu identidade com mapeamento para os utilizadores da sua aplicação. Por exemplo, armazenando-os na base de dados do seu servidor de aplicações. A identidade é necessária mais tarde para emitir fichas de acesso.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="issue-access-tokens"></a>Emitir fichas de acesso

Utilize o `getToken` método para emitir um token de acesso para a identidade dos Serviços de Comunicação já existentes. O parâmetro `scopes` define um conjunto de primitivos que autorizarão este token de acesso. Consulte a [lista de ações apoiadas.](../../concepts/authentication.md) Nova instância de parâmetro `user` pode ser construída com base na representação de cordas da identidade do Serviço de Comunicação Azure.

```java
// Issue an access token with the "voip" scope for a user identity
List<CommunicationTokenScope> scopes = new ArrayList<>(Arrays.asList(CommunicationTokenScope.VOIP));
AccessToken accessToken = communicationIdentityClient.getToken(user, scopes);
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresAt + ": " + token);
```

## <a name="create-an-identity-and-issue-token-in-one-call"></a>Criar uma identidade e emitir ficha numa chamada

Em alternativa, utilize o método "createUserAndToken" para criar uma nova entrada no diretório com um token único `Id` e emitir um token de acesso.

```java
List<CommunicationTokenScope> scopes = Arrays.asList(CommunicationTokenScope.CHAT);
CommunicationUserIdentifierAndToken result = communicationIdentityClient.createUserAndToken(scopes);
CommunicationUserIdentifier user = result.getUser();
System.out.println("\nCreated a user identity with ID: " + user.getId());
AccessToken accessToken = result.getUserToken();
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'chat' scope that expires at: " + expiresAt + ": " + token);
```

Os tokens de acesso são credenciais de curta duração que precisam de ser reeditadas. Não fazê-lo pode causar perturbações na experiência dos utilizadores da sua aplicação. A `expiresAt` propriedade indica a vida útil do token de acesso.

## <a name="refresh-access-tokens"></a>Atualizar tokens de acesso

Para refrescar um token de acesso, use o `CommunicationUserIdentifier` objeto para reeditar:

```java
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUserIdentifier identity = new CommunicationUserIdentifier(existingIdentity);
AccessToken response = communicationIdentityClient.getToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Revogar fichas de acesso

Em alguns casos, pode revogar explicitamente os tokens de acesso. Por exemplo, quando o utilizador de uma aplicação altera a palavra-passe que utiliza para autenticar o seu serviço. O método `revokeTokens` invalida todos os tokens de acesso ativo, que foram emitidos para a identidade.

```java
communicationIdentityClient.revokeTokens(user);
System.out.println("\nSuccessfully revoked all access tokens for user identity with ID: " + user.getId());
```

## <a name="delete-an-identity"></a>Eliminar uma identidade

A eliminação de uma identidade revoga todos os tokens de acesso ativo e impede-o de emitir fichas de acesso para a identidade. Também remove todo o conteúdo persistido associado à identidade.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nDeleted the user identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>Executar o código

Navegue para o diretório que contém o ficheiro *pom.xml* e compile o projeto utilizando o seguinte `mvn` comando.

```console
mvn compile
```

Então, construa o pacote.

```console
mvn package
```

Execute o seguinte `mvn` comando para executar a aplicação.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
