---
title: Desenvolver e configurar app Funções Azure - Azure SignalR
description: Detalhes sobre como desenvolver e configurar aplicações em tempo real sem servidor usando funções Azure e Serviço de Sinalização Azure
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523175"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Desenvolvimento e configuração de funções Azure com serviço de sinalização Azure

As aplicações Azure Functions podem alavancar as ligações do [Serviço De Sinalização Azure](../azure-functions/functions-bindings-signalr-service.md) para adicionar capacidades em tempo real. As aplicações do cliente utilizam SDKs clientes disponíveis em vários idiomas para se conectarem ao Serviço De Sinalização Azure e receberem mensagens em tempo real.

Este artigo descreve os conceitos para desenvolver e configurar uma aplicação Azure Function que está integrada com o SignalR Service.

## <a name="signalr-service-configuration"></a>Configuração do serviço SignalR

O Serviço De Sinalização Azure pode ser configurado em diferentes modos. Quando utilizado com funções Azure, o serviço deve ser configurado no modo *Servidor.*

No portal Azure, localize a página *Definições* do seu recurso SignalR Service. Desajuste o *modo de serviço* para O Servidor *.*

![Modo de Serviço do Sinalizador](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Desenvolvimento de Funções do Azure

Uma aplicação em tempo real sem servidor criada com as Funções do Azure e o Azure SignalR Service requer normalmente duas Funções do Azure:

* Uma função "negotiate", que o cliente chama para obter um token de acesso válido do SignalR Service, e um URL de ponto final de serviço
* Uma ou mais funções que enviam mensagens ou gerem a associação a um grupo

### <a name="negotiate-function"></a>negociar função

Uma aplicação de cliente requer um sinal de acesso válido para ligar ao Serviço De Sinalização Azure. Um sinal de acesso pode ser anónimo ou autenticado a uma determinada identificação do utilizador. As aplicações do Serviço signalr sem servidor requerem um ponto final http chamado "negociar" para obter uma informação de ligação simbólica e outras informações de ligação, tais como o URL final do SignalR Service.

Utilize uma função Azure ativada em HTTP e a ligação de entrada *SignalRConnectionInfo* para gerar o objeto de informação de ligação. A função deve ter uma rota HTTP que termine em `/negotiate`.

Para obter mais informações sobre como criar a função de negociação, consulte a referência vinculativa da entrada [ *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md).

Para saber como criar um símbolo autenticado, consulte a Autenticação do [Serviço de Aplicações](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Envio de mensagens e gestão da adesão ao grupo

Utilize o encadernação de saída *SignalR* para enviar mensagens aos clientes ligados ao Serviço De Sinalização Azure. Pode transmitir mensagens a todos os clientes, ou pode enviá-las para um subconjunto de clientes que são autenticados com um ID de utilizador específico ou que foram adicionados a um grupo específico.

Os utilizadores podem ser adicionados a um ou mais grupos. Também pode utilizar a ligação de saída *do SignalR* para adicionar ou remover os utilizadores de/para grupos.

Para mais informações, consulte a referência de ligação de saída [ *do SignalR* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Centros de Sinalização

O SignalR tem um conceito de "hubs". Cada ligação do cliente e cada mensagem enviada das Funções Azure é dirigida a um hub específico. Pode usar os centros como forma de separar as suas ligações e mensagens em espaços de nomelógicos.

## <a name="client-development"></a>Desenvolvimento de clientes

As aplicações do cliente SignalR podem alavancar o SDK cliente SignalR num dos vários idiomas para facilmente ligar e receber mensagens do Serviço De Sinalização Azure.

### <a name="configuring-a-client-connection"></a>Configurar uma ligação ao cliente

Para ligar ao SignalR Service, um cliente deve concluir uma negociação de conexão bem sucedida que consista nestas etapas:

1. Faça um pedido para o ponto final da *negociação* HTTP discutido acima para obter informações de conexão válidas
1. Ligue-se ao Serviço SignalR utilizando o URL final do serviço e o token de acesso obtido a partir do ponto final de *negociação*

Os SDKs do cliente SignalR já contêm a lógica necessária para realizar o aperto de mão de negociação. Passe o URL do ponto final da negociação, menos o segmento `negotiate`, para o `HubConnectionBuilder`do SDK . Aqui está um exemplo no JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Por convenção, o SDK automaticamente anexa `/negotiate` ao URL e usa-o para iniciar a negociação.

> [!NOTE]
> Se estiver a utilizar o JavaScript/TypeScript SDK num browser, tem de ativar a [partilha de recursos de origem cruzada (CORS)](#enabling-cors) na sua App de Funções.

Para obter mais informações sobre como utilizar o Cliente SignalR SDK, consulte a documentação para o seu idioma:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Envio de mensagens de um cliente para o serviço

Embora o SignalR SDK permita que as aplicações do cliente invoquem a lógica de backend num hub SignalR, esta funcionalidade ainda não é suportada quando utiliza o Serviço SignalR com Funções Azure. Utilize pedidos HTTP para invocar funções Azure.

## <a name="azure-functions-configuration"></a>Configuração de Funções Azure

As aplicações Azure Function que se integram com o Serviço De Sinalização Azure podem ser implementadas como qualquer aplicação típica da Função Azure, utilizando técnicas como [a implantação contínua,](../azure-functions/functions-continuous-deployment.md) [a implantação de zips,](../azure-functions/deployment-zip-push.md)e a fuga do [pacote.](../azure-functions/run-functions-from-deployment-package.md)

No entanto, existem algumas considerações especiais para apps que usam as encadernações do Serviço SignalR. Se o cliente correr num browser, o CORS deve ser ativado. E se a aplicação necessitar de autenticação, pode integrar o ponto final de negociação com a Autenticação do Serviço de Aplicações.

### <a name="enabling-cors"></a>Habilitar o CORS

O cliente JavaScript/TypeScript faz pedidos HTTP para a função de negociação para iniciar a negociação de ligação. Quando a aplicação do cliente é alojada num domínio diferente da aplicação Azure Function, a partilha de recursos de origem cruzada (CORS) deve ser ativada na aplicação 'Fun' ou o navegador irá bloquear os pedidos.

#### <a name="localhost"></a>hospedeiro local

Ao executar a aplicação Função no seu computador local, pode adicionar uma secção `Host` ao *local.settings.json* para ativar o CORS. Na secção `Host`, adicione duas propriedades:

* `CORS` - insira o URL base que é a origem da aplicação do cliente
* `CORSCredentials` - delineá-lo para `true` para permitir pedidos "com credenciais"

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

#### <a name="cloud---azure-functions-cors"></a>Cloud - Funções Azure CORS

Para ativar o CORS numa aplicação Azure Function, vá ao ecrã de configuração CORS sob o separador *de funcionalidades* da sua aplicação Função no portal Azure.

> [!NOTE]
> A configuração CORS ainda não está disponível no plano de consumo de funções Azure Linux. Utilize a [Gestão API Azure](#cloud---azure-api-management) para ativar o CORS.

O CORS com credenciais de acesso-controlo-permitir-credenciais deve ser ativado para que o cliente SignalR ligue para a função de negociação. Selecione a caixa de verificação para a ativar.

Na secção *de origens permitidas,* adicione uma entrada com o URL base de origem da sua aplicação web.

![Configuração cors](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud - Azure API Management

A Azure API Management fornece um portal DaPI que adiciona capacidades aos serviços back-end existentes. Pode usá-lo para adicionar CORS à sua aplicação de função. Oferece um nível de consumo com preços pay-per-action e uma subvenção mensal gratuita.

Consulte a documentação da API Management para obter informações sobre como [importar uma aplicação Azure Function](../api-management/import-function-app-as-api.md). Uma vez importado, pode adicionar uma política de entrada para permitir o CORS com suporte de credenciais de acesso-controlo-acesso.

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

Configure os seus clientes SignalR para utilizar o URL de Gestão API.

### <a name="using-app-service-authentication"></a>Utilização da autenticação do serviço de aplicações

A Azure Functions tem autenticação incorporada, apoiando fornecedores populares como Facebook, Twitter, Microsoft Account, Google e Azure Ative Directory. Esta funcionalidade pode ser integrada com a ligação *SignalRConnectionInfo* para criar ligações ao Serviço De Sinalização Azure que tenham sido autenticadas a um ID do utilizador. A sua aplicação pode enviar mensagens utilizando a ligação de saída *do SignalR* que são direcionadas para esse ID do utilizador.

No portal Azure, na plataforma da aplicação *Fun' apresente* separador, abra a janela de definições de *Autenticação/Autorização.* Siga a documentação para autenticação do serviço de [aplicações](../app-service/overview-authentication-authorization.md) para configurar a autenticação utilizando um fornecedor de identidade à sua escolha.

Uma vez configurados, os pedidos http autenticados incluirão `x-ms-client-principal-name` e `x-ms-client-principal-id` cabeçalhos contendo o nome de utilizador e identificação do utilizador autenticados, respectivamente.

Pode utilizar estes cabeçalhos na configuração de ligação *SignalRConnectionInfo* para criar ligações autenticadas. Aqui está C# um exemplo de função de negociação que usa o cabeçalho `x-ms-client-principal-id`.

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

Em seguida, pode enviar mensagens a esse utilizador definindo a propriedade `UserId` de uma mensagem SignalR.

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

Para obter informações sobre outras línguas, consulte as ligações do [Serviço De Sinalização Azure](../azure-functions/functions-bindings-signalr-service.md) para referência de Funções Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a desenvolver e configurar aplicações do Serviço signalr sem servidores utilizando funções Azure. Tente criar uma aplicação usando um dos arranques rápidos ou tutoriais na página geral do [Serviço SignalR](index.yml).