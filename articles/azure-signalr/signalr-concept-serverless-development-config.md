---
title: Desenvolver & configurar a app Azure Functions - Azure SignalR
description: Detalhes sobre como desenvolver e configurar aplicações sem servidor em tempo real usando as Funções Azure e o Serviço Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3d69b72012819e3d9099e447b9048fe07aea86d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97858710"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service

As aplicações Azure Functions podem aproveitar as ligações do [Serviço Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) para adicionar capacidades em tempo real. As aplicações do cliente utilizam SDKs de clientes disponíveis em vários idiomas para se conectarem ao Serviço Azure SignalR e receberem mensagens em tempo real.

Este artigo descreve os conceitos para desenvolver e configurar uma aplicação Azure Function que está integrada com o Serviço SignalR.

## <a name="signalr-service-configuration"></a>Configuração do serviço SignalR

O Serviço Azure SignalR pode ser configurado em diferentes modos. Quando utilizado com Funções Azure, o serviço deve ser configurado no modo *Serverless.*

No portal Azure, localize a página *Definições* do seu recurso SignalR Service. Desa ajuste o *modo serviço* para *Serverless*.

![Modo de serviço SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Desenvolvimento de Funções do Azure

Uma aplicação em tempo real sem servidor criada com as Funções do Azure e o Azure SignalR Service requer normalmente duas Funções do Azure:

* Uma função "negotiate", que o cliente chama para obter um token de acesso válido do SignalR Service, e um URL de ponto final de serviço
* Uma ou mais funções que lidam com mensagens do Serviço SignalR e enviam mensagens ou gerem a adesão ao grupo

### <a name="negotiate-function"></a>negociar função

Uma aplicação de cliente requer um token de acesso válido para ligar ao Serviço Azure SignalR. Um token de acesso pode ser anónimo ou autenticado para um dado ID do utilizador. As aplicações de serviço SignalR sem servidor requerem um ponto final HTTP chamado "negociar" para obter um token e outras informações de conexão, como o URL do ponto final do Serviço SignalR.

Utilize uma função Azure ativada HTTP e a ligação de entrada *SignalRConnectionInfo* para gerar o objeto de informação de ligação. A função deve ter uma rota HTTP que termina em `/negotiate` .

Com [o modelo baseado](#class-based-model) em classe em C#, você não precisa de ligação de entrada *SignalRConnectionInfo* e pode adicionar reivindicações personalizadas muito mais fácil. Ver [Experiência Negotiate no modelo baseado em classe](#negotiate-experience-in-class-based-model)

Para obter mais informações sobre como criar a função de negociação, consulte a [referência de ligação de entrada *SignalRConnectionInfo*](../azure-functions/functions-bindings-signalr-service-input.md).

Para saber como criar um token autenticado, consulte a [Autenticação do Serviço de Aplicações.](#using-app-service-authentication)

### <a name="handle-messages-sent-from-signalr-service"></a>Manuseie as mensagens enviadas do Serviço SignalR

Utilize a ligação *signalR Trigger* para manusear mensagens enviadas do Serviço SignalR. Pode ser notificado quando os clientes enviam mensagens ou os clientes ficam ligados ou desligados.

Para obter mais informações, consulte a referência de ligação do [ *gatilho SignalR*](../azure-functions/functions-bindings-signalr-service-trigger.md).

Também precisa configurar o ponto final da sua função como um ponto de terminamento a montante para que o serviço desencadeie a função quando houver mensagem do cliente. Para obter mais informações sobre como configurar a montante, consulte este [doc](concept-upstream.md).

### <a name="sending-messages-and-managing-group-membership"></a>Envio de mensagens e gestão da adesão ao grupo

Utilize a ligação de saída *SignalR* para enviar mensagens a clientes ligados ao Serviço Azure SignalR. Pode transmitir mensagens a todos os clientes, ou pode enviá-las para um subconjunto de clientes que são autenticados com um ID de utilizador específico ou que foram adicionados a um grupo específico.

Os utilizadores podem ser adicionados a um ou mais grupos. Também pode utilizar a ligação de saída *SignalR* para adicionar ou remover os utilizadores de/para grupos.

Para obter mais informações, consulte a [referência de ligação à saída *do SignalR*](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>SignalR Hubs

A SignalR tem um conceito de "hubs". Cada ligação ao cliente e cada mensagem enviada a partir de Azure Functions é traçada para um centro específico. Pode utilizar os hubs como forma de separar as suas ligações e mensagens em espaços de nomes lógicos.

## <a name="class-based-model"></a>Modelo baseado em classe

O modelo baseado em classe é dedicado a C#. Com o modelo baseado em classe pode ter uma experiência consistente de programação do lado do servidor SignalR. Tem as seguintes características.

* Menos trabalho de configuração: O nome da classe é usado como `HubName` , o nome do método é usado como e o é decidido automaticamente de acordo com o `Event` nome do `Category` método.
* Ligação de parâmetros automáticos: Nem `ParameterNames` o atributo `[SignalRParameter]` é necessário. Os parâmetros estão ligados automaticamente aos argumentos do método Azure Function por ordem.
* Saída conveniente e experiência de negociação.

Os seguintes códigos demonstram estas características:

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

Todas as funções que queiram alavancar o modelo baseado na classe têm de ser o método de classe que herda do **ServerlessHub**. O nome da classe `SignalRTestHub` na amostra é o nome do centro.

### <a name="define-hub-method"></a>Definir método do hub

Todos os métodos do hub **devem** ter um argumento `InvocationContext` de decorado por atributo e usar um construtor sem `[SignalRTrigger]` parâmetros. Em seguida, o nome do **método** é tratado como **evento de** parâmetros .

Por padrão, `category=messages` exceto o nome do método é um dos seguintes nomes:

* **OnConnected**: Tratado como `category=connections, event=connected`
* **OnDisconnected**: Tratado como `category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>Experiência de ligação de parâmetros

No modelo baseado em classe, `[SignalRParameter]` é desnecessário porque todos os argumentos são marcados como por `[SignalRParameter]` defeito, exceto que é uma das seguintes situações:

* O argumento é decorado por um atributo vinculativo.
* O tipo do argumento é `ILogger` ou `CancellationToken`
* O argumento é decorado pelo atributo `[SignalRIgnore]`

### <a name="negotiate-experience-in-class-based-model"></a>Negociar experiência em modelo baseado em classe

Em vez de utilizar a ligação de entrada `[SignalR]` signalr, a negociação no modelo baseado em classe pode ser mais flexível. Classe base `ServerlessHub` tem um método

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

Isto apresenta a personalização do utilizador `userId` ou `claims` durante a execução da função.

## <a name="use-signalrfilterattribute"></a>Utilizar o comando `SignalRFilterAttribute`

O utilizador pode herdar e implementar a classe `SignalRFilterAttribute` abstrata. Se forem lançadas `FilterAsync` exceções, `403 Forbidden` serão devolvidas aos clientes.

A amostra que se segue demonstra como implementar um filtro de cliente que só permite `admin` `broadcast` invocar.

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

Aproveite o atributo para autorizar a função.

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>Desenvolvimento de clientes

As aplicações do cliente SignalR podem alavancar o cliente SignalR SDK em um de vários idiomas para conectar e receber mensagens do Serviço Azure SignalR.

### <a name="configuring-a-client-connection"></a>Configurar uma ligação com o cliente

Para se ligar ao Serviço SignalR, um cliente deve concluir uma negociação de conexão bem sucedida que consiste nestas etapas:

1. Faça um pedido ao ponto final *HTTP negociado* acima discutido para obter informações de conexão válidas
1. Ligue-se ao Serviço SignalR utilizando o URL do ponto final de serviço e o token de acesso obtido a partir do ponto final de *negociação*

Os SDKs clientes signalR já contêm a lógica necessária para realizar o aperto de mão de negociação. Passe o URL do ponto final da negociação, menos o `negotiate` segmento, para o SDK's `HubConnectionBuilder` . Aqui está um exemplo no JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Por convenção, o SDK anexa-se automaticamente `/negotiate` ao URL e utiliza-o para iniciar a negociação.

> [!NOTE]
> Se estiver a utilizar o SDK JavaScript/TypeScript num browser, tem de ativar a [partilha de recursos de origem cruzada (CORS)](#enabling-cors) na sua App de Função.

Para obter mais informações sobre como utilizar o cliente SignalR SDK, consulte a documentação para o seu idioma:

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Envio de mensagens de um cliente para o serviço

Se tiver configurado [a montante](concept-upstream.md) para o seu recurso SignalR, pode enviar mensagens do cliente para as suas Funções Azure utilizando qualquer cliente SignalR. Aqui está um exemplo no JavaScript:

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Configuração de Funções Azure

As aplicações Azure Function que se integram com o Serviço Azure SignalR podem ser implementadas como qualquer aplicação típica da Função Azure, utilizando técnicas como [implementação contínua,](../azure-functions/functions-continuous-deployment.md) [implementação zip,](../azure-functions/deployment-zip-push.md)e [executadas a partir do pacote](../azure-functions/run-functions-from-deployment-package.md).

No entanto, existem algumas considerações especiais para apps que utilizam as ligações do Serviço SignalR. Se o cliente for executado num browser, o CORS deve estar ativado. E se a aplicação necessitar de autenticação, pode integrar o ponto final de negociação com a Autenticação do Serviço de Aplicações.

### <a name="enabling-cors"></a>Habilitação DE CORS

O cliente JavaScript/TypeScript faz pedidos HTTP à função de negociação para iniciar a negociação de ligação. Quando a aplicação do cliente é hospedada num domínio diferente da aplicação Azure Function, a partilha de recursos de origem cruzada (CORS) deve ser ativada na aplicação Fun ou o navegador bloqueará os pedidos.

#### <a name="localhost"></a>Localhost

Ao executar a aplicação Função no seu computador local, pode adicionar uma `Host` secção para *local.settings.js* para ativar o CORS. Na `Host` secção, adicione duas propriedades:

* `CORS` - inserir o URL base que é a origem da aplicação do cliente
* `CORSCredentials` - defini-lo `true` para permitir pedidos "com OsCredentais"

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

#### <a name="cloud---azure-functions-cors"></a>Nuvem - Funções Azure CORS

Para ativar o CORS numa aplicação Azure Function, aceda ao ecrã de configuração CORS no separador funcionalidade da *Plataforma* da sua aplicação Function no portal Azure.

> [!NOTE]
> A configuração CORS ainda não está disponível no plano de consumo linux das funções Azure. Utilize [a Azure API Management](#cloud---azure-api-management) para ativar o CORS.

O CORS com Acesso-Controlo-Admissões-Credenciais deve ser ativado para que o cliente SignalR ligue para a função de negociação. Selecione a caixa de verificação para a ativar.

Na secção *Origens Permitidas,* adicione uma entrada com o URL base de origem da sua aplicação web.

![CORS configurantes](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud - Gestão API Azure

A Azure API Management fornece um gateway API que adiciona capacidades aos serviços de back-end existentes. Pode usá-lo para adicionar CORS à sua aplicação de função. Oferece um nível de consumo com preços de pagamento por ação e uma subvenção mensal gratuita.

Consulte a documentação da API Management para obter informações sobre como [importar uma aplicação Azure Function](../api-management/import-function-app-as-api.md). Uma vez importado, pode adicionar uma política de entrada para ativar o CORS com suporte access-Control-Allow-Credentials.

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

Configure os seus clientes SignalR para utilizar o URL de Gestão da API.

### <a name="using-app-service-authentication"></a>Utilização da autenticação do Serviço de Aplicações

A Azure Functions tem a autenticação incorporada, suportando fornecedores populares como Facebook, Twitter, Microsoft Account, Google e Azure Ative Directory. Esta funcionalidade pode ser integrada com a ligação *SignalRConnectionInfo* para criar ligações ao Serviço Azure SignalR que tenham sido autenticadas num ID do utilizador. A sua aplicação pode enviar mensagens utilizando a ligação de saída *SignalR* que são direcionadas para esse ID do utilizador.

No portal Azure, no separador *Plataforma* da sua aplicação Função, abra a janela de definições *de Autenticação/Autorização.* Siga a documentação para [a Autenticação do Serviço](../app-service/overview-authentication-authorization.md) de Aplicações para configurar a autenticação utilizando um fornecedor de identidade à sua escolha.

Uma vez configurados, os pedidos HTTP autenticados incluirão `x-ms-client-principal-name` e `x-ms-client-principal-id` cabeçalhos contendo o nome de utilizador e iD do utilizador da identidade autenticada, respectivamente.

Pode utilizar estes cabeçalhos na configuração de ligação *SignalRConnectionInfo* para criar ligações autenticadas. Aqui está um exemplo C# negociar função que usa o `x-ms-client-principal-id` cabeçalho.

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

Em seguida, pode enviar mensagens a esse utilizador definindo a `UserId` propriedade de uma mensagem SignalR.

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

Para obter informações sobre outros idiomas, consulte as [ligações do Serviço Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) para referência às Funções Azure.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a desenvolver e configurar aplicações de Serviço SignalR sem servidor usando Funções Azure. Tente criar uma aplicação utilizando um dos arranques rápidos ou tutoriais na [página de visão geral do Serviço SignalR](index.yml).
