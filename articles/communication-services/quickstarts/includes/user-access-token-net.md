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
ms.openlocfilehash: 5c9066f369183de3b4cfe19cc5635e8f1b4a94a2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779408"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- A versão mais recente [.NET Core biblioteca de clientes](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operativo.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `UserAccessTokensQuickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: **Program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Mude o seu diretório para a pasta de aplicação recém-criada e use o `dotnet build` comando para compilar a sua aplicação.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca da Administração de Serviços de Comunicação Azure para o pacote .NET utilizando o `dotnet add package` comando.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.2
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Abrir **Program.cs** arquivo em um editor de texto
1. Adicione uma `using` diretiva para incluir o espaço de `Azure.Communication.Administration` nome
1. Atualizar a `Main` declaração do método para apoiar o código async

Utilize o seguinte código para começar:

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Inicialize um `CommunicationIdentityClient` com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código ao método `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Criar um utilizador

A Azure Communication Services mantém um diretório de identidade leve. Utilize o `createUser` método para criar uma nova entrada no diretório com um único `Id` . Deverá manter um mapeamento entre os utilizadores da sua aplicação e os Serviços de Comunicação gerados (por exemplo, armazenando-os na base de dados do seu servidor de aplicações).

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Emitir fichas de acesso ao utilizador

Utilize o `issueToken` método para emitir um token de acesso para um utilizador dos Serviços de Comunicação. Se não fornecer o `user` parâmetro opcional, um novo utilizador será criado e devolvido com o token.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Os tokens de acesso ao utilizador são credenciais de curta duração que precisam de ser reemitidas de forma a evitar que os seus utilizadores experimentem perturbações de serviço. A `expiresOn` propriedade de resposta indica a vida útil do símbolo.

## <a name="revoke-user-access-tokens"></a>Revogar fichas de acesso ao utilizador

Em alguns casos, poderá ser necessário revogar explicitamente as fichas de acesso ao utilizador, por exemplo, quando um utilizador altera a palavra-passe que utiliza para autenticar o seu serviço. Esta funcionalidade está disponível através da biblioteca de clientes da Azure Communication Services Administration.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Eliminar um utilizador

A eliminação de uma identidade revoga todos os tokens ativos e impede-o de emitir fichas subsequentes para as identidades. Também remove todo o conteúdo persistido associado ao utilizador.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```console
dotnet run
```
