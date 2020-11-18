---
title: Autenticar para serviços de comunicação Azure
titleSuffix: An Azure Communication Services concept document
description: Conheça as várias formas de uma aplicação ou serviço poder autenticar os Serviços de Comunicação.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 96e10bc19d59b60824a908c67816a21ca80326d0
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832811"
---
# <a name="authenticate-to-azure-communication-services"></a>Autenticar para serviços de comunicação Azure

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Este artigo fornece informações sobre autenticação de clientes aos Serviços de Comunicação Azure utilizando *chaves de acesso* e *fichas de acesso ao utilizador.* Todas as interações dos clientes com os Serviços de Comunicação Azure têm de ser autenticadas.

A tabela que se segue descreve quais as opções de autenticação suportadas pelas bibliotecas dos serviços de comunicação Azure:

| Biblioteca de cliente | Chave de acesso    | Fichas de acesso ao utilizador |
| -------------- | ------------- | ------------------ |
| Administração | Suportado     | Não suportado      |
| SMS            | Suportado     | Não suportado      |
| Chat           | Não suportado | Suportado          |
| Chamando        | Não suportado | Suportado          |

Cada opção de autorização é brevemente descrita abaixo:

- **Acesso Autenticação chave** para operações de SMS e Administração. A autenticação da chave de acesso é adequada para aplicações em execução num ambiente de serviço de confiança. Para autenticar com uma chave de acesso, um cliente gera um [código de autenticação de mensagem baseado em haxixe (HMAC) e inclui-o](https://en.wikipedia.org/wiki/HMAC) no `Authorization` cabeçalho de cada pedido HTTP. Para mais informações, consulte [Autenticar com uma chave de acesso.](#authenticate-with-an-access-key)
- **Autenticação do Token de acesso ao utilizador** para chat e chamada. As fichas de acesso ao utilizador permitem que as aplicações do seu cliente autentem diretamente contra os Serviços de Comunicação Azure. Estes tokens são gerados num serviço de fornecimento de fichas do lado do servidor que cria. São então fornecidos a dispositivos clientes que usam o símbolo para inicializar as bibliotecas de clientes Chat e Call. Para mais informações, consulte [Authenticate com um Token de Acesso ao Utilizador.](#authenticate-with-a-user-access-token)

## <a name="authenticate-with-an-access-key"></a>Autenticar com uma chave de acesso

A autenticação da chave de acesso utiliza uma chave secreta partilhada para gerar um HMAC para cada pedido HTTP calculado utilizando o algoritmo SHA256, e envia-o no `Authorization` cabeçalho usando o `HMAC-SHA256` esquema.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

As bibliotecas de clientes dos Serviços de Comunicação Azure que utilizam a autenticação da chave de acesso devem ser inicializadas com a cadeia de ligação do seu recurso. Se não estiver a utilizar uma biblioteca de clientes, pode gerar HMACs programáticamente utilizando a chave de acesso do seu recurso. Para saber mais sobre as cordas de ligação, visite o [rápido arranque de fornecimento de recursos.](../quickstarts/create-communication-resource.md)

### <a name="sign-an-http-request"></a>Assine um pedido HTTP

Se não estiver a utilizar uma biblioteca de clientes para fazer pedidos HTTP às APIs dos Serviços de Comunicação Azure, terá de criar hmACs programáticamente para cada pedido HTTP. Os seguintes passos descrevem como construir o cabeçalho de autorização:

1. Especifique o calendário de tempo universal coordenado (UTC) para o pedido no `x-ms-date` cabeçalho ou no cabeçalho HTTP `Date` padrão. O serviço valida isto para evitar certos ataques de segurança, incluindo ataques de repetição.
1. Hash o corpo de pedido HTTP usando o algoritmo SHA256 em seguida, passá-lo, com o pedido, através do `x-ms-content-sha256` cabeçalho.
1. Construa o fio a ser assinado através da concatenação do verbo HTTP `GET` (por exemplo, ou `PUT` , via de pedido HTTP, e valores dos `Date` `Host` cabeçalhos , e `x-ms-content-sha256` HTTP no seguinte formato:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Gere uma assinatura HMAC-256 da cadeia codificada UTF-8 que criou no passo anterior. Em seguida, codificar os seus resultados como Base64. Note que também precisa de descodificar a sua chave de conta de armazenamento. Utilize o seguinte formato (apresentado como pseudo código):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_azure_storage_account_shared_key>)))
    ```
1. Especificar o cabeçalho de autorização da seguinte forma:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Onde `<hmac-sha256-signature>` está o HMAC calculado no passo anterior.

## <a name="authenticate-with-a-user-access-token"></a>Autenticar com um token de acesso ao utilizador

As fichas de acesso ao utilizador permitem que as aplicações do seu cliente autentem diretamente contra os Serviços de Comunicação Azure. Para isso, deverá criar um serviço de confiança que autentique os utilizadores da sua aplicação e emita fichas de acesso ao utilizador com a biblioteca de clientes da Administração. Visite a documentação conceptual da arquitetura do [cliente e do servidor](./client-and-server-architecture.md) para saber mais sobre as nossas considerações arquitetónicas.

A `CommunicationUserCredential` classe contém a lógica para fornecer credenciais simbólicas de acesso ao utilizador às bibliotecas do cliente e gerir o seu ciclo de vida.

### <a name="initialize-the-client-libraries"></a>Inicializar as bibliotecas de clientes

Para rubricar bibliotecas de clientes dos Serviços de Comunicação Azure que requerem a autenticação simbólica de acesso ao utilizador, primeiro cria-se uma instância da `CommunicationUserCredential` classe e depois utiliza-a para inicializar um cliente API.

Os seguintes snippets mostram-lhe como inicializar a biblioteca do cliente de chat com um token de acesso ao utilizador:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Fichas de acesso refrescantes do utilizador

Os tokens de acesso ao utilizador são credenciais de curta duração que precisam de ser reemitidas para evitar que os seus utilizadores experimentem perturbações de serviço. O `CommunicationUserCredential` construtor aceita uma função de chamada de atualização que lhe permite atualizar as fichas de acesso do utilizador antes de expirarem. Você deve usar esta chamada para obter um novo token de acesso do utilizador do seu serviço de confiança.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

A `refreshProactively` opção permite-lhe decidir como vai gerir o ciclo de vida simbólico. Por padrão, quando um token está estragado, o retorno bloqueará os pedidos de API e tentará refresca-lo. Quando `refreshProactively` é programado para a chamada é agendado e executado `true` assíncronosamente antes que o token expire.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criação de fichas de acesso ao utilizador](../quickstarts/access-tokens.md)

Para obter mais informações, veja os seguintes artigos:
- [Conheça a arquitetura do cliente e do servidor](../concepts/client-and-server-architecture.md)
