---
title: CARACTERÍSTICAS HTTP em Funções Duráveis - Funções Azure
description: Conheça as funcionalidades integradas do HTTP na extensão de Funções Duráveis para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: a7d8891c6f925cfac326685f01ba5f6149a1b233
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357800"
---
# <a name="http-features"></a>Características http

As Funções Duráveis têm várias funcionalidades que facilitam a incorporação de orquestrações e entidades duráveis em fluxos de trabalho HTTP. Este artigo entra em detalhes sobre algumas dessas características.

## <a name="exposing-http-apis"></a>Expondo HTTP APIs

Orquestrações e entidades podem ser invocadas e geridas através de pedidos HTTP. A extensão de Funções Duráveis expõe APIs HTTP incorporados. Também fornece APIs para interagir com orquestrações e entidades de dentro de funções desencadeadas pelo HTTP.

### <a name="built-in-http-apis"></a>APIs http incorporados

A extensão funções duráveis adiciona automaticamente um conjunto de APIs HTTP ao hospedeiro funções Azure. Com estas APIs, pode interagir e gerir orquestrações e entidades sem escrever qualquer código.

São suportadas as seguintes APIs http incorporadas.

* [Iniciar nova orquestração](durable-functions-http-api.md#start-orchestration)
* [Instância de orquestração de consulta](durable-functions-http-api.md#get-instance-status)
* [Encerrar instância de orquestração](durable-functions-http-api.md#terminate-instance)
* [Envie um evento externo para uma orquestração](durable-functions-http-api.md#raise-event)
* [Limpar a história da orquestração](durable-functions-http-api.md#purge-single-instance-history)
* [Envie um evento de operação a uma entidade](durable-functions-http-api.md#signal-entity)
* [Obter o estado de uma entidade](durable-functions-http-api.md#get-entity)
* [Consulta da lista de entidades](durable-functions-http-api.md#list-entities)

Consulte o [artigo HTTP APIs](durable-functions-http-api.md) para obter uma descrição completa de todas as APIs http incorporadas expostas pela extensão funções duráveis.

### <a name="http-api-url-discovery"></a>DESCOBERTA HTTP API URL

A [ligação do cliente de orquestração](durable-functions-bindings.md#orchestration-client) expõe APIs que podem gerar cargas de resposta http convenientes. Por exemplo, pode criar uma resposta contendo ligações às APIs de gestão para uma instância de orquestração específica. Os seguintes exemplos mostram uma função http-trigger que demonstra como usar esta API para uma nova instância de orquestração:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**função.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

Iniciar uma função de orquestrador utilizando as funções de gatilho HTTP mostradas anteriormente pode ser feita usando qualquer cliente HTTP. O seguinte comando cURL inicia uma função orquestradora chamada `DoWork`:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Segue-se uma resposta exemplo para uma orquestração que `abc123` como identificação. Alguns detalhes foram removidos para maior clareza.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

No exemplo anterior, cada um dos campos que terminam em `Uri` corresponde a uma Http API incorporada. Pode usar estas APIs para gerir a instância de orquestração alvo.

> [!NOTE]
> O formato dos URLs webhook depende da versão do anfitrião das Funções Azure que está a executar. O exemplo anterior é para o hospedeiro Azure Functions 2.0.

Para obter uma descrição de todas as APIs http incorporadas, consulte a [referência http API](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Rastreio de operação de asincronização

A resposta HTTP mencionada anteriormente foi concebida para ajudar a implementar APIs async http de longa duração com funções duráveis. Este padrão é por vezes referido como o padrão do *consumidor de sondagens.* O fluxo cliente/servidor funciona da seguinte forma:

1. O cliente emite um pedido http para iniciar um processo de longa duração como uma função de orquestrador.
1. O gatilho http-alvo devolve uma resposta HTTP 202 com um cabeçalho de localização que tem o valor "statusQueryGetUri".
1. O cliente vota o URL no cabeçalho de localização. O cliente continua a ver respostas HTTP 202 com um cabeçalho de localização.
1. Quando a instância termina ou falha, o ponto final do cabeçalho de localização devolve HTTP 200.

Este protocolo permite a coordenação de processos de longa duração com clientes ou serviços externos que podem fazer uma sondagem num ponto final http e seguir o cabeçalho de Localização. Tanto as implementações do cliente como do servidor deste padrão são incorporadas nas Funções Duráveis HTTP APIs.

> [!NOTE]
> Por padrão, todas as ações baseadas em HTTP fornecidas pela [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) suportam o padrão de operação assíncrono padrão. Esta capacidade permite incorporar uma função duradoura de longa duração como parte de um fluxo de trabalho de Apps Lógicas. Pode encontrar mais detalhes sobre o suporte de Aplicações Lógicas para padrões de HTTP assíncronos nas ações de fluxo de trabalho de [Aplicações Lógicas Azure e despoleta documentação](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> As interações com orquestrações podem ser feitas a partir de qualquer tipo de função, e não apenas funções desencadeadas por HTTP.

Para obter mais informações sobre como gerir orquestrações e entidades utilizando APIs do cliente, consulte o artigo de [gestão da Instância.](durable-functions-instance-management.md)

## <a name="consuming-http-apis"></a>Consumir HTTP APIs

Como descrito nas restrições de código de [função orquestradora,](durable-functions-code-constraints.md)as funções orquestradoras não podem fazer I/O diretamente. Em vez disso, normalmente chamam [de atividade sintetitu funções](durable-functions-types-features-overview.md#activity-functions) que fazem operações de I/O.

Começando com as Funções Duráveis 2.0, as orquestrações podem consumir de forma nativa HTTP APIs utilizando a ligação do gatilho da [orquestração](durable-functions-bindings.md#orchestration-trigger).

O seguinte código de exemplo mostra uma função orquestradora fazendo um pedido http de saída:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Ao utilizar a ação "call HTTP", pode fazer as seguintes ações nas suas funções de orquestrador:

* Ligue para HTTP APIs diretamente das funções de orquestração, com algumas limitações que são mencionadas mais tarde.
* Suporte automaticamente os padrões de sondagens do lado do cliente HTTP 202.
* Utilize [identidades geridas azure](../../active-directory/managed-identities-azure-resources/overview.md) para fazer chamadas em HTTP autorizadas para outros pontos finais Do Azure.

A capacidade de consumir HTTP APIs diretamente de funções orquestradoras destina-se a ser uma conveniência para um determinado conjunto de cenários comuns. Você pode implementar todas estas funcionalidades usando funções de atividade. Em muitos casos, as funções de atividade podem dar-lhe mais flexibilidade.

### <a name="http-202-handling"></a>Manuseamento HTTP 202

A API "call HTTP" pode implementar automaticamente o lado cliente do padrão de consumidor de sondagens. Se uma chamada API devolver uma resposta HTTP 202 com um cabeçalho de localização, a função de orquestrador vota automaticamente o recurso Localização até receber uma resposta diferente de 202. Esta resposta será a resposta devolvida ao código de função do orquestrador.

> [!NOTE]
> As funções de orquestrador também suportam de forma nativa o padrão de consumidor de sondagens do lado do servidor, como descrito no rastreio da [operação Async](#async-operation-tracking). Este suporte significa que as orquestrações numa aplicação de função podem facilmente coordenar as funções orquestradoras em outras aplicações de função. Isto é semelhante ao conceito [de sub-orquestração,](durable-functions-sub-orchestrations.md) mas com suporte para comunicação cruzada. Este suporte é particularmente útil para o desenvolvimento de aplicações de estilo microserviço.

### <a name="managed-identities"></a>Identidades geridas

As Funções Duráveis suportam nativamente chamadas a APIs que aceitam fichas de autorização do Azure Ative Directory (Azure AD). Este suporte utiliza [identidades geridas pela Azure](../../active-directory/managed-identities-azure-resources/overview.md) para adquirir estes tokens.

O seguinte código é um exemplo de uma função de orquestrador .NET. A função faz chamadas autenticadas para reiniciar uma máquina virtual utilizando as [máquinas virtuais REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)do Gestor de Recursos Azure .

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

No exemplo anterior, o parâmetro `tokenSource` está configurado para adquirir tokens Azure AD para O Gestor de [Recursos Azure](../../azure-resource-manager/management/overview.md). As fichas são identificadas pelo recurso URI `https://management.core.windows.net`. O exemplo pressupõe que a aplicação de funções atuais esteja a funcionar localmente ou foi implementada como uma aplicação de função com uma identidade gerida. Presume-se que a identidade local ou a identidade gerida têm permissão para gerir os VMs no grupo de recursos especificado `myRG`.

No tempo de funcionação, a fonte de ficha configurada devolve automaticamente um token de acesso OAuth 2.0. A fonte adiciona então o símbolo como um símbolo portador ao cabeçalho de autorização do pedido de saída. Este modelo é uma melhoria sobre a adição manual de cabeçalhos de autorização aos pedidos http pelas seguintes razões:

* A atualização do token é manuseada automaticamente. Não precisas de te preocupar com fichas expiradas.
* Tokens nunca são armazenados no estado de orquestração durável.
* Não precisas de escrever nenhum código para gerir a aquisição simbólica.

Pode encontrar um exemplo mais completo na [amostra de C# RestartVMs pré-compilada](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

As identidades geridas não se limitam à gestão de recursos do Azure. Pode utilizar identidades geridas para aceder a qualquer API que aceite fichas do portador do Azure AD, incluindo serviços Azure da Microsoft e aplicações web de parceiros. A aplicação web de um parceiro pode até ser outra aplicação de função. Para obter uma lista de serviços Azure da Microsoft que suportam a autenticação com a Azure AD, consulte [os serviços Azure que suportam a autenticação Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Limitações

O suporte incorporado para chamar HTTP APIs é uma característica de conveniência. Não é apropriado para todos os cenários.

Os pedidos http enviados por funções de orquestrador e suas respostas são serializados e persistentes como mensagens de fila. Este comportamento de fila garante que as chamadas HTTP são [fiáveis e seguras para a repetição da orquestração.](durable-functions-orchestrations.md#reliability) No entanto, o comportamento de fila também tem limitações:

* Cada pedido http envolve latência adicional quando comparado com um cliente http nativo.
* Grandes mensagens de pedido ou resposta que não podem caber numa mensagem de fila podem degradar significativamente o desempenho da orquestração. A sobrecarga das cargas de mensagens de descarga para o armazenamento de bolhas pode causar uma potencial degradação do desempenho.
* As cargas de streaming, de saque e binário não são suportadas.
* A capacidade de personalizar o comportamento do cliente HTTP é limitada.

Se alguma destas limitações puder afetar o seu caso de utilização, considere, em vez disso, utilizar funções de atividade e bibliotecas de clientes http específicas da linguagem para efetuar chamadas http de saída.

> [!NOTE]
> Se for um desenvolvedor .NET, pode perguntar-se porque é que esta funcionalidade utiliza os tipos **DurableHttpRequest** e **DurableHttpResponse** em vez dos tipos incorporados .NET **HttpRequestMessage** e **HttpResponseMessage.**
>
> Esta escolha de design é intencional. A razão principal é que os tipos personalizados ajudam a garantir que os utilizadores não fazem pressupostos incorretos sobre os comportamentos suportados do cliente http interno. Tipos específicos das Funções Duráveis também possibilitam simplificar o design da API. Também podem disponibilizar mais facilmente funcionalidades especiais como [a integração de identidade gerida](#managed-identities) e o padrão do consumidor de [sondagens.](#http-202-handling) 

### <a name="extensibility-net-only"></a>Extensibility (apenas NET)

Personalizar o comportamento do cliente interno da orquestração http é possível usando [funções Azure .NET injeção de dependência](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection). Esta capacidade pode ser útil para fazer pequenas mudanças comportamentais. Também pode ser útil para testar o cliente HTTP injetando objetos falsos.

O exemplo seguinte demonstra a utilização de uma injeção de dependência para desativar a validação de certificados SSL para funções orquestradoras que chamam pontos finais http externos.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça entidades duráveis](durable-functions-entities.md)
