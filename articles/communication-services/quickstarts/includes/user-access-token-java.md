---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: a9c8d604e5564526936f37edcc9eec5891443a47
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779138"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso de serviços de comunicação implantado e uma cadeia de conexão. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-java-application"></a>Criar uma nova aplicação Java

Abra o seu terminal ou janela de comando e navegue para o diretório onde pretende criar a sua aplicação Java. Executar o comando abaixo para gerar o projeto Java a partir do modelo maven-arqueotype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Você vai notar que a tarefa 'gerar' criou um diretório com o mesmo nome que o `artifactId` . Sob este diretório, o diretório src/main/java contém o código fonte do projeto, `src/test/java directory` contém a fonte de teste, e o ficheiro é o Project Object Model do `pom.xml` projeto, ou POM.

### <a name="install-the-package"></a>Instale o pacote

Abra o ficheiro **pom.xml** no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Navegue para o *diretório /src/main/java/com/communication/quickstart*
1. Abra o ficheiro *App.java* no seu editor
1. Substitua a `System.out.println("Hello world!");` declaração
1. Adicionar `import` diretivas

Utilize o seguinte código para começar:

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Instantaneamente com `CommunicationIdentityClient` a chave de acesso do seu recurso e ponto final. Saiba como gerir a [cadeia de ligação dos recursos.](../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código ao método `main`:

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

Pode inicializar o cliente com qualquer cliente HTTP personalizado que implementa a `com.azure.core.http.HttpClient` interface. O código acima demonstra a utilização do [cliente Azure Core Netty HTTP](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) que é fornecido por `azure-core` .

## <a name="create-a-user"></a>Criar um utilizador

A Azure Communication Services mantém um diretório de identidade leve. Utilize o `createUser` método para criar uma nova entrada no diretório com um único `Id` . Deverá manter um mapeamento entre os utilizadores da sua aplicação e os Serviços de Comunicação gerados (por exemplo, armazenando-os na base de dados do seu servidor de aplicações).

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>Emitir fichas de acesso ao utilizador

Utilize o `issueToken` método para emitir um token de acesso para um utilizador dos Serviços de Comunicação. Se não fornecer o `user` parâmetro opcional, um novo utilizador será criado e devolvido com o token.

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

Os tokens de acesso ao utilizador são credenciais de curta duração que precisam de ser reemitidas de forma a evitar que os seus utilizadores experimentem perturbações de serviço. A `expiresAt` propriedade de resposta indica a vida útil do símbolo.

## <a name="revoke-user-access-tokens"></a>Revogar fichas de acesso ao utilizador

Em alguns casos, poderá ser necessário revogar explicitamente as fichas de acesso ao utilizador, por exemplo, quando um utilizador altera a palavra-passe que utiliza para autenticar o seu serviço. Isto utiliza o `revokeTokens` método para invalidar todos os tokens de acesso de um utilizador.

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>Eliminar um utilizador

A eliminação de um utilizador revoga todos os tokens ativos e impede-o de emitir fichas subsequentes para as identidades. Também remove todo o conteúdo persistido associado ao utilizador.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
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
