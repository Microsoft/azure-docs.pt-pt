---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 511548da9767544ace2827a6a8b2baa83b1d2971
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96993083"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- A versão mais recente [.NET Core biblioteca de clientes](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operativo.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../create-communication-resource.md)

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `AccessTokensQuickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: **Program.cs**.

```console
dotnet new console -o AccessTokensQuickstart
```

Mude o seu diretório para a pasta de aplicação recém-criada e use o `dotnet build` comando para compilar a sua aplicação.

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca da Administração de Serviços de Comunicação Azure para o pacote .NET utilizando o `dotnet add package` comando.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.3
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Abrir **Program.cs** arquivo em um editor de texto
1. Adicione uma `using` diretiva para incluir o espaço de `Azure.Communication.Administration` nome
1. Atualizar a `Main` declaração do método para apoiar o código async

Utilize o seguinte código para começar:

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Administration;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>Autenticar o cliente

Inicialize um `CommunicationIdentityClient` com a sua cadeia de ligação. O código abaixo recupera a cadeia de ligação para o recurso a partir de uma variável ambiental chamada `COMMUNICATION_SERVICES_CONNECTION_STRING` . Saiba como gerir a [cadeia de ligação dos recursos.](../create-communication-resource.md#store-your-connection-string)

Adicione o seguinte código ao método `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-an-identity"></a>Criar uma identidade

A Azure Communication Services mantém um diretório de identidade leve. Utilize o `createUser` método para criar uma nova entrada no diretório com um único `Id` . A loja recebeu identidade com mapeamento para os utilizadores da sua aplicação. Por exemplo, armazenando-os na base de dados do seu servidor de aplicações. A identidade é necessária mais tarde para emitir fichas de acesso.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Emitir fichas de acesso à identidade

Utilize o `issueToken` método para emitir um token de acesso para a identidade dos Serviços de Comunicação já existentes. O parâmetro `scopes` define um conjunto de primitivos que autorizarão este token de acesso. Consulte a [lista de ações apoiadas.](../../concepts/authentication.md) Nova instância de parâmetro `communicationUser` pode ser construída com base na representação de cordas da identidade do Serviço de Comunicação Azure.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Os tokens de acesso são credenciais de curta duração que precisam de ser reeditadas. Não fazê-lo pode causar perturbações na experiência dos utilizadores da sua aplicação. A `expiresOn` propriedade de resposta indica a vida útil do token de acesso. 

## <a name="refresh-access-tokens"></a>Atualizar tokens de acesso

Para refrescar um token de acesso, passe uma instância do `CommunicationUser` objeto para `IssueTokenAsync` . Se guardou isto `Id` e precisa de criar um `CommunicationUser` novo, pode fazê-lo passando o seu armazenado `Id` no construtor da seguinte `CommunicationUser` forma:

```csharp  
// In this example, userId is a string containing the Id property of a previously-created CommunicationUser
identityToRefresh = new CommunicationUser(userId);
tokenResponse = await client.IssueTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>Revogar fichas de acesso

Em alguns casos, pode revogar explicitamente os tokens de acesso. Por exemplo, quando o utilizador de uma aplicação altera a palavra-passe que utiliza para autenticar o seu serviço. O método `RevokeTokensAsync` invalida todos os tokens de acesso ativo, que foram emitidos para a identidade.

```csharp  
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>Eliminar uma identidade

A eliminação de uma identidade revoga todos os tokens de acesso ativo e impede-o de emitir fichas de acesso para as identidades. Também remove todo o conteúdo persistido associado à identidade.

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```console
dotnet run
```
