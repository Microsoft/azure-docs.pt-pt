---
title: Desenvolver & configurar Azure Functions aplicativo – Signalr do Azure
description: Detalhes sobre como desenvolver e configurar aplicativos em tempo real sem servidor usando o Azure Functions e o serviço de Signaler do Azure
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: f86a63315798d982f7e78fd1ff293061daf50132
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786780"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions o desenvolvimento e a configuração com o serviço de Signaler do Azure

Azure Functions aplicativos podem aproveitar as [ligações de serviço do Azure signalr](../azure-functions/functions-bindings-signalr-service.md) para adicionar recursos em tempo real. Os aplicativos cliente usam SDKs de cliente disponíveis em várias linguagens para se conectar ao serviço de Signaler do Azure e receber mensagens em tempo real.

Este artigo descreve os conceitos para o desenvolvimento e a configuração de um aplicativo de funções do Azure que é integrado com o serviço Signalr.

## <a name="signalr-service-configuration"></a>Configuração do serviço signalr

O serviço de Signaler do Azure pode ser configurado em modos diferentes. Quando usado com Azure Functions, o serviço deve ser configurado no modo sem *servidor* .

Na portal do Azure, localize a página *configurações* do recurso de serviço do signalr. Defina o *modo de serviço* como sem *servidor*.

![Modo de serviço signalr](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Desenvolvimento de Azure Functions

Um aplicativo em tempo real sem servidor criado com o Azure Functions e o serviço de Signaler do Azure normalmente requer duas Azure Functions:

* Uma função "Negotiate" que o cliente chama para obter um token de acesso de serviço de sinalização válido e URL de ponto de extremidade de serviço
* Uma ou mais funções que enviam mensagens ou gerenciam a associação de grupo

### <a name="negotiate-function"></a>função Negotiate

Um aplicativo cliente requer um token de acesso válido para se conectar ao serviço de Signaler do Azure. Um token de acesso pode ser anônimo ou autenticado para uma determinada ID de usuário. Os aplicativos de serviço de Signaler sem servidor exigem um ponto de extremidade HTTP chamado "Negotiate" para obter um token e outras informações de conexão, como a URL do ponto de extremidade do serviço Signalr.

Use uma função do Azure disparada por HTTP e a associação de entrada *SignalRConnectionInfo* para gerar o objeto de informações de conexão. A função deve ter uma rota HTTP que termine em `/negotiate`.

Para obter mais informações sobre como criar a função Negotiate, consulte a referência de associação de entrada do [ *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service.md#input).

Para saber mais sobre como criar um token autenticado, consulte [usando a autenticação do serviço de aplicativo](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Enviando mensagens e gerenciando a associação de grupo

Use a associação de saída do *signalr* para enviar mensagens aos clientes conectados ao serviço de Signaler do Azure. Você pode transmitir mensagens para todos os clientes ou pode enviá-las para um subconjunto de clientes que são autenticados com uma ID de usuário específica ou foram adicionados a um grupo específico.

Os usuários podem ser adicionados a um ou mais grupos. Você também pode usar a associação de saída do *signalr* para adicionar ou remover usuários de/para grupos.

Para obter mais informações, consulte a referência de associação de saída do [ *signalr* ](../azure-functions/functions-bindings-signalr-service.md#output).

### <a name="signalr-hubs"></a>Hubs de sinalização

O signalr tem um conceito de "hubs". Cada conexão de cliente e cada mensagem enviada de Azure Functions tem como escopo um hub específico. Você pode usar hubs como uma maneira de separar suas conexões e mensagens em namespaces lógicos.

## <a name="client-development"></a>Desenvolvimento de cliente

Os aplicativos cliente do signalr podem aproveitar o SDK do cliente do Signalr em uma das várias linguagens para se conectar e receber mensagens do serviço de Signaler do Azure com facilidade.

### <a name="configuring-a-client-connection"></a>Configurando uma conexão de cliente

Para se conectar ao serviço Signalr, um cliente deve concluir uma negociação de conexão bem-sucedida que consiste em estas etapas:

1. Faça uma solicitação para o ponto de extremidade de *negociação* http discutido acima para obter informações de conexão válidas
1. Conectar-se ao serviço de Signalr usando a URL de ponto de extremidade de serviço e o token de acesso obtidos do ponto de extremidade *negotia*

Os SDKs de cliente do signalr já contêm a lógica necessária para executar o handshake de negociação. Passe a URL do ponto de extremidade de negociação, menos o segmento de `negotiate`, para o `HubConnectionBuilder`do SDK. Veja um exemplo em JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Por convenção, o SDK acrescenta automaticamente `/negotiate` à URL e a usa para iniciar a negociação.

> [!NOTE]
> Se você estiver usando o JavaScript/TypeScript SDK em um navegador, precisará [habilitar o CORS (compartilhamento de recursos entre origens)](#enabling-cors) no seu aplicativo de funções.

Para obter mais informações sobre como usar o SDK de cliente do Signalr, consulte a documentação do seu idioma:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Enviando mensagens de um cliente para o serviço

Embora o SDK do Signalr permita que os aplicativos cliente invoquem a lógica de back-end em um Hub do Signalr, essa funcionalidade ainda não é suportada quando você usa o serviço de sinalização com Azure Functions. Use solicitações HTTP para invocar Azure Functions.

## <a name="azure-functions-configuration"></a>Configuração de Azure Functions

Os aplicativos de funções do Azure que se integram com o serviço de Signaler do Azure podem ser implantados como qualquer aplicativo de funções típico do Azure, usando técnicas como [implantação contínua](../azure-functions/functions-continuous-deployment.md), [implantação de zip](../azure-functions/deployment-zip-push.md)e [execução do pacote](../azure-functions/run-functions-from-deployment-package.md).

No entanto, há algumas considerações especiais para aplicativos que usam as associações de serviço do Signalr. Se o cliente do for executado em um navegador, o CORS deverá ser habilitado. E se o aplicativo exigir autenticação, você poderá integrar o ponto de extremidade de negociação com a autenticação do serviço de aplicativo.

### <a name="enabling-cors"></a>Habilitando CORS

O cliente JavaScript/TypeScript faz solicitações HTTP para a função Negotiate para iniciar a negociação de conexão. Quando o aplicativo cliente é hospedado em um domínio diferente do aplicativo de funções do Azure, o compartilhamento de recursos entre origens (CORS) deve ser habilitado no aplicativo de funções ou o navegador bloqueará as solicitações.

#### <a name="localhost"></a>Localhost

Ao executar o aplicativo de funções no computador local, você pode adicionar uma `Host` seção a *local. Settings. JSON* para habilitar CORS. Na seção `Host`, adicione duas propriedades:

* `CORS`-Insira a URL base que é a origem do aplicativo cliente
* `CORSCredentials`-defina-o como `true` para permitir solicitações "withCredentials"

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

#### <a name="cloud---azure-functions-cors"></a>CORS Azure Functions de nuvem

Para habilitar o CORS em um aplicativo de funções do Azure, vá para a tela de configuração do CORS na guia *recursos da plataforma* do seu aplicativo de funções no portal do Azure.

> [!NOTE]
> A configuração de CORS ainda não está disponível no plano de consumo Azure Functions Linux. Use o [Gerenciamento de API do Azure](#cloud---azure-api-management) para habilitar o CORS.

O CORS com Access-Control-Allow-Credentials deve estar habilitado para que o cliente do Signalr chame a função Negotiate. Marque a caixa de seleção para habilitá-la.

Na seção *origens permitidas* , adicione uma entrada com a URL base de origem do seu aplicativo Web.

![Configurando CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Nuvem-gerenciamento de API do Azure

O gerenciamento de API do Azure fornece um gateway de API que adiciona recursos aos serviços de back-end existentes. Você pode usá-lo para adicionar CORS ao seu aplicativo de funções. Ele oferece uma camada de consumo com preços de pagamento por ação e uma concessão gratuita mensal.

Consulte a documentação do gerenciamento de API para obter informações sobre como [importar um aplicativo de funções do Azure](../api-management/import-function-app-as-api.md). Depois de importado, você pode adicionar uma política de entrada para habilitar o CORS com suporte ao Access-Control-Allow-Credentials.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Configure os clientes do Signalr para usar a URL de gerenciamento de API.

### <a name="using-app-service-authentication"></a>Usando a autenticação do serviço de aplicativo

Azure Functions tem autenticação interna, oferecendo suporte a provedores populares como Facebook, Twitter, conta da Microsoft, Google e Azure Active Directory. Esse recurso pode ser integrado com a associação *SignalRConnectionInfo* para criar conexões com o serviço de Signaler do Azure que foram autenticados para uma ID de usuário. Seu aplicativo pode enviar mensagens usando a associação de saída do *signalr* que é destinada a essa ID de usuário.

Na portal do Azure, na guia *recursos da plataforma* do aplicativo de funções, abra a janela configurações de *autenticação/autorização* . Siga a documentação da [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md) para configurar a autenticação usando um provedor de identidade de sua escolha.

Uma vez configuradas, as solicitações HTTP autenticadas incluirão `x-ms-client-principal-name` e `x-ms-client-principal-id` cabeçalhos que contenham o nome de usuário e a ID de usuários da identidade autenticada, respectivamente.

Você pode usar esses cabeçalhos em sua configuração de associação do *SignalRConnectionInfo* para criar conexões autenticadas. Aqui está um exemplo C# de função Negotiate que usa o cabeçalho `x-ms-client-principal-id`.

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

Em seguida, você pode enviar mensagens para esse usuário definindo a propriedade `UserId` de uma mensagem de sinalização.

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

Para obter informações sobre outras linguagens, consulte [associações de serviço do Azure signalr](../azure-functions/functions-bindings-signalr-service.md) para Azure Functions referência.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a desenvolver e configurar aplicativos de serviço de sinalização sem servidor usando o Azure Functions. Tente criar um aplicativo por conta própria usando um dos tutoriais ou inícios rápidos na [página Visão geral do serviço signalr](index.yml).