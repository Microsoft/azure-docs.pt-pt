---
title: Desenvolver e configurar as aplicações de serviço de SignalR de funções do Azure
description: Obter detalhes sobre como desenvolver e configurar aplicações em tempo real sem servidor com as funções do Azure e o serviço Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569171"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Desenvolvimento das funções do Azure e de configuração com o serviço Azure SignalR

Aplicações de funções do Azure podem tirar partido da [enlaces de serviço Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) para adicionar recursos em tempo real. Aplicações cliente utilizam os SDKs do cliente disponíveis em vários idiomas para ligar ao serviço Azure SignalR e receber mensagens em tempo real.

Este artigo descreve os conceitos de desenvolvimento e a configuração de uma aplicação de função do Azure que está integrada com o serviço SignalR.

## <a name="signalr-service-configuration"></a>Configuração de serviço SignalR

Serviço Azure SignalR podem ser configurado em modos diferentes. Quando utilizado com as funções do Azure, o serviço tem de ser configurado no *sem servidor* modo.

No portal do Azure, localize a *definições* página do seu recurso de serviço SignalR. Definir o *modo de serviço* ao *sem servidor*.

![Modo de serviço SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Desenvolvimento de funções do Azure

Uma aplicação sem servidor em tempo real, criada com as funções do Azure e o serviço Azure SignalR normalmente requer duas funções do Azure:

* Uma função de "negotiate" de mensagens em fila que o cliente chama-se para obter um serviço SignalR válido token de acesso e o URL de ponto final de serviço
* Uma ou mais funções que enviam mensagens ou gerir a associação de grupo

### <a name="negotiate-function"></a>função de negociação

Uma aplicação de cliente necessita de um token de acesso válido para ligar ao serviço Azure SignalR. Um token de acesso pode ser anônimo ou autenticado para um ID de utilizador especificado. Aplicações sem servidor do serviço SignalR requerem que um ponto final HTTP com o nome "negotiate" para obter um token e outras informações de ligação, como o URL de ponto final de serviço SignalR.

Utilize um HTTP acionada a função do Azure e o *SignalRConnectionInfo* enlace para gerar o objeto de informações de ligação de entrada. A função tem de ter uma rota HTTP que termina em `/negotiate`.

Para obter mais informações sobre como criar a função de negociação, consulte a [ *SignalRConnectionInfo* referência de ligação de entrada](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Para saber mais sobre como criar um token de autenticado, consulte [usando autenticação do serviço de aplicações](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Envio de mensagens e gerir a associação de grupo

Utilize o *SignalR* enlace de saída para enviar mensagens para os clientes ligados ao serviço Azure SignalR. Pode difundir mensagens a todos os clientes, ou pode enviar-lhes a um subconjunto de clientes que sejam autenticados com um ID de utilizador específico ou foram adicionados a um grupo específico.

Os utilizadores podem ser adicionados a um ou mais grupos. Também pode utilizar o *SignalR* enlace para adicionar ou remover utilizadores em grupos de saída.

Para obter mais informações, consulte a [ *SignalR* referência de vínculo de saída](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>Hubs de SignalR

SignalR tem um conceito de "hubs". Cada ligação de cliente e cada mensagem enviada a partir das funções do Azure é confinada para um hub específico. Pode utilizar os hubs como uma forma de separar suas ligações e mensagens em espaços de nomes de lógicos.

## <a name="client-development"></a>Desenvolvimento de clientes

Aplicações de cliente SignalR podem aproveitar o cliente SignalR SDK em um dos vários idiomas para ligar e receber mensagens do serviço Azure SignalR facilmente.

### <a name="configuring-a-client-connection"></a>Configurar uma ligação de cliente

Para ligar ao serviço SignalR, um cliente tem de concluir uma negociação de ligação com êxito, que consiste num destes passos:

1. Fazer um pedido para o *negociar* ponto final de HTTP descritas acima para obter informações de ligação válida
1. Ligar ao serviço SignalR utilizando o URL de ponto final de serviço e o token de acesso obtidos a partir da *negociar* ponto final

SDKs do cliente SignalR já contém a lógica necessária para executar o handshake de negociação. Passar a URL do ponto de extremidade negociar, menos os `negotiate` segmento, para o SDK `HubConnectionBuilder`. Eis um exemplo em JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Por convenção, o SDK acrescenta automaticamente `/negotiate` para o URL e utiliza-o para começar a negociação.

> [!NOTE]
> Se estiver a utilizar o SDK de JavaScript/TypeScript num browser, terá [habilitar recursos de várias origens (CORS) de partilha](#enabling-cors) na sua aplicação de funções.

Para obter mais informações sobre como utilizar o SDK do cliente do SignalR, consulte a documentação para o seu idioma:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Enviar mensagens de um cliente para o serviço

Embora o SDK do SignalR permite que aplicativos cliente invocar a lógica de back-end num hub SignalR, esta funcionalidade ainda não é suportada ao utilizar serviço SignalR com as funções do Azure. Pedidos HTTP de utilização para invocar as funções do Azure.

## <a name="azure-functions-configuration"></a>Configuração de funções do Azure

As aplicações de função do Azure que se integram com o serviço Azure SignalR podem ser implementadas, como qualquer aplicação de função do Azure típica, usando técnicas como [implementação continuamente](../azure-functions/functions-continuous-deployment.md), [zip implementação](../azure-functions/deployment-zip-push.md)e [executar a partir do pacote](../azure-functions/run-functions-from-deployment-package.md).

No entanto, existem algumas considerações especiais para aplicações que utilizam os enlaces de serviço SignalR. Se o cliente for executado num browser, o CORS tem de estar ativada. E se a aplicação necessitar de autenticação, pode integrar o ponto de extremidade negotiate com autenticação do serviço de aplicações.

### <a name="enabling-cors"></a>Ativar o CORS

O cliente JavaScript/TypeScript faz solicitações HTTP para a função de negociação para iniciar a negociação de ligação. Quando o aplicativo de cliente é hospedado num domínio diferente do que a aplicação de função do Azure, os recursos de várias origens (CORS) de partilha tem de estar ativado na aplicação de função ou o navegador bloqueará as solicitações.

#### <a name="localhost"></a>Localhost

Ao executar a aplicação de funções no seu computador local, pode adicionar um `Host` secção para *Settings* para ativar o CORS. Na `Host` secção, adicione duas propriedades:

* `CORS` -Introduza o URL de base que é a origem do aplicativo de cliente
* `CORSCredentials` -defini-lo como `true` para permitir pedidos de "withCredentials"

Exemplo:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="azure"></a>Azure

Para ativar o CORS uma aplicação de função do Azure, vá para o ecrã de configuração de CORS abaixo a *funcionalidades de plataforma* separador da sua aplicação de função no portal do Azure.

CORS com Access-Control-Allow-Credentials tem de estar ativada para o cliente SignalR chamar a função de negociação. Selecione a caixa de verificação para ativá-la.

Na *permitido origens* secção, adicione uma entrada com o URL de base de origem da sua aplicação web.

![A configurar a CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>Utilizar a autenticação do serviço de aplicações

As funções do Azure tem autenticação incorporada, que suporta fornecedores populares, como o Facebook, Twitter, Account Microsoft, Google e Azure Active Directory. Esta funcionalidade pode ser integrada com o *SignalRConnectionInfo* vinculação para criar ligações ao serviço Azure SignalR que tenham sido autenticadas para um ID de utilizador. A aplicação pode enviar mensagens utilizando o *SignalR* de saída do enlace são direcionadas para essa ID de utilizador.

No portal do Azure, na sua aplicação de função *funcionalidades de plataforma* separador, abra o *autenticação/autorização* janela definições. Siga a documentação para [autenticação de serviço de aplicações](../app-service/overview-authentication-authorization.md) para configurar a autenticação através de um fornecedor de identidade da sua preferência.

Depois de configurada, os pedidos HTTP autenticados incluirá `x-ms-client-principal-name` e `x-ms-client-principal-id` cabeçalhos que contém nome de utilizador a autenticação da identidade e o ID de utilizador, respectivamente.

Pode utilizar estes cabeçalhos no seu *SignalRConnectionInfo* configuração de ligação para criar ligações autenticadas. Eis um exemplo C# negociar a função que utiliza o `x-ms-client-principal-id` cabeçalho.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Em seguida, pode enviar mensagens para que o utilizador ao definir o `UserId` propriedade de uma mensagem de SignalR.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Para obter informações sobre outros idiomas, consulte a [enlaces de serviço Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) para referenciar as funções do Azure.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como desenvolver e configurar aplicações de serviço SignalR sem servidor com funções do Azure. Tente criar uma aplicação utilizando um dos inícios rápidos ou tutoriais sobre o [página de descrição geral do serviço SignalR](index.yml).