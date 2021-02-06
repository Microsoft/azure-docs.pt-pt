---
title: 'Assine um pedido HTTP com C #'
description: Esta é a versão C# de assinatura de um pedido HTTP com uma assinatura HMAC para Serviços de Comunicação.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 1985ae92b68c16798fc26d7517520c0a70a2ad28
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628360"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de:
- Crie uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Instalar [Estúdio Visual](https://visualstudio.microsoft.com/downloads/) 
- Criar um recurso Azure Communication Services. Para mais detalhes, consulte [Criar um Recurso de Comunicação Azure](../../quickstarts/create-communication-resource.md). Terá de gravar os seus **recursosEndpoint** e  **recursosAccessKey** para este tutorial.



## <a name="sign-an-http-request-with-c"></a>Assine um pedido HTTP com C #
A autenticação da chave de acesso utiliza uma chave secreta partilhada para gerar uma assinatura HMAC para cada pedido HTTP. Esta assinatura é gerada com o algoritmo SHA256, e é enviada no `Authorization` cabeçalho usando o `HMAC-SHA256` esquema. Por exemplo:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

O `hmac-sha256-signature` consiste em: 

- VERBO HTTP (por `GET` exemplo, `PUT` ou)
- CAMINHO DE PEDIDO HTTP
- Data
- Anfitrião
- x-ms-content-sha256

## <a name="setting-up"></a>Configuração
Os seguintes passos descrevem como construir o cabeçalho de autorização:

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `SignHmacTutorial` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: **Program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Mude o seu diretório para a pasta de aplicação recém-criada e use o `dotnet build` comando para compilar a sua aplicação.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Instale o pacote

Instale o `Newtonsoft.Json` pacote, utilizado para a serialização do corpo:

```console
dotnet add package Newtonsoft.Json
```

Atualize a `Main` declaração de método para suportar o código async. Utilize o seguinte código para começar:

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>Criar uma mensagem de pedido

Para este exemplo, assinaremos um pedido de criação de uma nova identidade utilizando a API de Autenticação de Serviços de Comunicação `2020-07-20-preview2` (versão)

Adicione o seguinte código ao `Main` método:

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2020-07-20-preview2");
//Endpoint identities?api-version=2020-07-20-preview2 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

`resourceEndpoint`Substitua-o pelo valor real do ponto final do recurso.

## <a name="create-content-hash"></a>Criar haxixe de conteúdo

O haxixe de conteúdo faz parte da sua assinatura HMAC. Utilize o seguinte código para calcular o haxixe de conteúdo. Pode adicionar este método de `Progam.cs` acordo com o `Main` método.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Calcular uma assinatura
Utilize o seguinte código para criar um método para calcular a sua assinatura HMAC.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

`resourceAccessKey`Substitua-o pela chave de acesso do seu verdadeiro recurso Azure Communication Services.

## <a name="create-an-authorization-header-string"></a>Criar uma sequência de cabeçalho de autorização

Vamos agora construir a corda que vamos adicionar ao nosso cabeçalho de autorização:

1. Calcular um haxixe de conteúdo
2. Especificar o tempo de tempo universal coordenado (UTC)
3. Preparar uma corda para assinar
4. Calcular a assinatura
5. Concatenate a corda, que será usada no cabeçalho de autorização
 
Adicione o seguinte código ao `Main` método:

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Adicione cabeçalhos para solicitar AMessage

Utilize o seguinte código para adicionar os cabeçalhos necessários ao seu `requestMessage` :

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Testar o cliente
Ligue para o ponto final usando `HttpClient` e verifique a resposta.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
