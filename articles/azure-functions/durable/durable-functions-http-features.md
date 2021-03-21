---
title: Funcionalidades HTTP em Funções Duradouras - Funções Azure
description: Saiba mais sobre as funcionalidades HTTP integradas na extensão de Funções Duradouras para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 64d40de50f21811a56318971de1836abc8fbf8c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027266"
---
# <a name="http-features"></a>Recursos HTTP

As Funções Duráveis têm várias funcionalidades que facilitam a incorporação de orquestrações e entidades duráveis em fluxos de trabalho HTTP. Este artigo entra em detalhes sobre algumas dessas características.

## <a name="exposing-http-apis"></a>Expondo APIs HTTP

As orquestrações e entidades podem ser invocadas e geridas através de pedidos HTTP. A extensão funções duradouras expõe APIs HTTP incorporados. Também fornece APIs para interagir com orquestrações e entidades de funções desencadeadas por HTTP.

### <a name="built-in-http-apis"></a>APIs HTTP incorporados

A extensão funções duradouras adiciona automaticamente um conjunto de APIs HTTP ao anfitrião Azure Functions. Com estas APIs, pode interagir e gerir orquestrações e entidades sem escrever qualquer código.

As seguintes APIs HTTP incorporadas são suportadas.

* [Iniciar nova orquestração](durable-functions-http-api.md#start-orchestration)
* [Instância de orquestração de consultas](durable-functions-http-api.md#get-instance-status)
* [Instância de orquestração terminada](durable-functions-http-api.md#terminate-instance)
* [Enviar um evento externo para uma orquestração](durable-functions-http-api.md#raise-event)
* [Expurgar a história da orquestração](durable-functions-http-api.md#purge-single-instance-history)
* [Enviar um evento de operação a uma entidade](durable-functions-http-api.md#signal-entity)
* [Obtenha o estado de uma entidade](durable-functions-http-api.md#get-entity)
* [Consulta da lista de entidades](durable-functions-http-api.md#list-entities)

Consulte o [artigo HTTP APIs](durable-functions-http-api.md) para obter uma descrição completa de todas as APIs HTTP incorporadas expostas pela extensão funções duradouras.

### <a name="http-api-url-discovery"></a>DESCOBERTA DE URL HTTP API

A [ligação do cliente de orquestração](durable-functions-bindings.md#orchestration-client) expõe APIs que podem gerar cargas de resposta HTTP convenientes. Por exemplo, pode criar uma resposta que contenha ligações com as APIs de gestão para uma instância de orquestração específica. Os exemplos a seguir mostram uma função de gatilho HTTP que demonstra como usar esta API para uma nova instância de orquestração:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

# <a name="python"></a>[Python](#tab/python)

**__.py init__**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    function_name = req.route_params['functionName']
    event_data = req.get_body()

    instance_id = await client.start_new(function_name, instance_id, event_data)
    
    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)
```

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

---

Iniciar uma função de orquestrador utilizando as funções de gatilho HTTP mostradas anteriormente pode ser feito utilizando qualquer cliente HTTP. O seguinte comando cURL inicia uma função orquestradora chamada `DoWork` :

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Em seguida, é uma resposta de exemplo para uma orquestração que tem `abc123` como identificação. Alguns detalhes foram removidos para maior clareza.

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

No exemplo anterior, cada um dos campos que `Uri` terminam corresponde a uma API HTTP incorporada. Pode usar estas APIs para gerir a instância de orquestração alvo.

> [!NOTE]
> O formato dos URLs webhook depende da versão do anfitrião Azure Functions que está a executar. O exemplo anterior é para o anfitrião Azure Functions 2.0.

Para obter uma descrição de todas as APIs HTTP incorporadas, consulte a [referência HTTP API](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Rastreio da operação assínc

A resposta HTTP mencionada anteriormente destina-se a ajudar a implementar APIs HTTP async de longa duração com funções duradouras. Este padrão é por vezes referido como o *padrão do consumidor de sondagens.* O fluxo cliente/servidor funciona da seguinte forma:

1. O cliente emite um pedido HTTP para iniciar um processo de longa duração como uma função orquestradora.
1. O gatilho HTTP do alvo devolve uma resposta HTTP 202 com um cabeçalho de Localização que tem o valor "statusQueryGetUri".
1. O cliente sonda o URL no cabeçalho de localização. O cliente continua a ver respostas HTTP 202 com um cabeçalho de localização.
1. Quando a instância termina ou falha, o ponto final no cabeçalho Localização devolve HTTP 200.

Este protocolo permite a coordenação de processos de longo prazo com clientes ou serviços externos que podem sondar um ponto final HTTP e seguir o cabeçalho Localização. Tanto as implementações do cliente como do servidor deste padrão são incorporadas nas APIs de Funções Duradouras HTTP.

> [!NOTE]
> Por padrão, todas as ações baseadas em HTTP fornecidas pela [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) suportam o padrão de funcionamento assíncrona. Esta capacidade permite incorporar uma função duradoura de longa duração como parte de um fluxo de trabalho de Apps Lógicas. Pode encontrar mais detalhes sobre o suporte de Apps Lógicas para padrões HTTP assíncronos nas ações de workflow de [Apps Azure Logic e desencadeia documentação.](../../logic-apps/logic-apps-workflow-actions-triggers.md)

> [!NOTE]
> As interações com orquestrações podem ser feitas a partir de qualquer tipo de função, e não apenas funções desencadeadas por HTTP.

Para obter mais informações sobre como gerir orquestrações e entidades utilizando APIs do cliente, consulte o [artigo de gestão de Instância](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Consumindo APIs HTTP

Como descrito nas restrições do código de [função do orquestrador,](durable-functions-code-constraints.md)as funções do orquestrador não podem fazer I/O diretamente. Em vez disso, normalmente chamam [funções de atividade](durable-functions-types-features-overview.md#activity-functions) que fazem operações de E/S.

Começando pelas Funções Duradouras 2.0, as orquestrações podem consumir as APIs HTTP utilizando a ligação do gatilho de [orquestração](durable-functions-bindings.md#orchestration-trigger).

O seguinte código de exemplo mostra uma função orquestradora fazendo um pedido HTTP de saída:

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
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    response = yield context.call_http('GET', url)
    
    if response["statusCode"] >= 400:
        # handling of error codes goes here

main = df.Orchestrator.create(orchestrator_function)
```

---

Utilizando a ação "call HTTP", pode fazer as seguintes ações nas suas funções de orquestrador:

* Ligue para ASP HTTP diretamente das funções de orquestração, com algumas limitações que são mencionadas mais tarde.
* Suporte automaticamente os padrões de votação do lado do cliente HTTP 202.
* Utilize [identidades geridas Azure](../../active-directory/managed-identities-azure-resources/overview.md) para esjoar chamadas HTTP autorizadas para outros pontos finais do Azure.

A capacidade de consumir APIs HTTP diretamente das funções orquestradoras destina-se a uma conveniência para um determinado conjunto de cenários comuns. Você mesmo pode implementar todas estas funcionalidades usando funções de atividade. Em muitos casos, as funções de atividade podem dar-lhe mais flexibilidade.

### <a name="http-202-handling"></a>TRATAMENTO HTTP 202

A API "call HTTP" pode implementar automaticamente o lado cliente do padrão de consumidor de sondagens. Se uma chamada API retornar uma resposta HTTP 202 com um cabeçalho de Localização, a função de orquestrador automaticamente sonda o recurso Localização até receber uma resposta diferente de 202. Esta resposta será a resposta devolvida ao código de função do orquestrador.

> [!NOTE]
> As funções de orquestrador também suportam de forma nativa o padrão de consumidor de sondagens do lado do servidor, como descrito no [rastreio da operação Async](#async-operation-tracking). Este suporte significa que as orquestrações numa aplicação de uma função podem coordenar facilmente as funções do orquestrador noutras aplicações de funções. Isto é semelhante ao conceito [de sub-orquestração,](durable-functions-sub-orchestrations.md) mas com suporte para comunicação cross-app. Este suporte é particularmente útil para o desenvolvimento de aplicações em estilo microservice.

### <a name="managed-identities"></a>Identidades geridas

Funções Duradouras suportam nativamente chamadas para APIs que aceitam tokens Azure Ative Directory (Azure AD) para autorização. Este suporte utiliza [identidades geridas pela Azure](../../active-directory/managed-identities-azure-resources/overview.md) para adquirir estes tokens.

O seguinte código é um exemplo de uma função de orquestrador .NET. A função faz chamadas autenticadas para reiniciar uma máquina virtual utilizando as [máquinas virtuais REST API](/rest/api/compute/virtualmachines)do Gestor de Recursos Azure .

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
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net/.default
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net/.default"));
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
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net/.default");

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    subscription_id = "mySubId"
    resource_group = "myRg"
    vm_name = "myVM"
    api_version = "2019-03-01"
    token_source = df.ManagedIdentityTokenSource("https://management.core.windows.net/.default")

    # get a list of the Azure subscriptions that I have access to
    restart_response = yield context.call_http("POST", 
        f"https://management.azure.com/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}/restart?api-version={api_version}",
        None,
        None,
        token_source)
    return restart_response

main = df.Orchestrator.create(orchestrator_function)
```

---

No exemplo anterior, o `tokenSource` parâmetro está configurado para adquirir fichas AD Azure para [O Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md). Os tokens são identificados pelo recurso URI `https://management.core.windows.net/.default` . O exemplo pressupõe que a aplicação de função atual esteja a funcionar localmente ou foi implementada como uma aplicação de função com uma identidade gerida. Presume-se que a identidade local ou a identidade gerida tenham permissão para gerir os VMs no grupo de recursos `myRG` especificado.

No tempo de execução, a fonte simbólica configurada devolve automaticamente um token de acesso OAuth 2.0. A fonte adiciona então o token como símbolo portador ao cabeçalho de autorização do pedido de saída. Este modelo é uma melhoria em relação à adição manual de cabeçalhos de autorização aos pedidos HTTP pelas seguintes razões:

* A atualização token é tratada automaticamente. Não precisas de te preocupar com fichas expiradas.
* As fichas nunca são armazenadas no estado de orquestração durável.
* Não é preciso escrever nenhum código para gerir a aquisição de fichas.

Pode encontrar um exemplo mais completo na [amostra pré-recompensada de Reinício C#](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs).

As identidades geridas não se limitam à gestão de recursos da Azure. Você pode usar identidades geridas para aceder a qualquer API que aceite tokens portadores de Azure, incluindo serviços Azure da Microsoft e aplicações web de parceiros. A aplicação web de um parceiro pode até ser outra aplicação de função. Para obter uma lista de serviços Azure da Microsoft que suportam a autenticação com Azure AD, consulte [os serviços Azure que suportam a autenticação AD do Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Limitações

O suporte incorporado para chamar APIs HTTP é uma funcionalidade de conveniência. Não é apropriado para todos os cenários.

Os pedidos HTTP enviados por funções orquestradoras e as suas respostas são serializados e persistentes como mensagens de fila. Este comportamento de fila garante que as chamadas HTTP são [fiáveis e seguras para a repetição da orquestração.](durable-functions-orchestrations.md#reliability) No entanto, o comportamento da fila também tem limitações:

* Cada pedido HTTP envolve latência adicional quando comparado com um cliente HTTP nativo.
* Grandes mensagens de pedido ou resposta que não se encaixam numa mensagem de fila podem degradar significativamente o desempenho da orquestração. A sobrecarga de cargas de mensagens para o armazenamento de bolhas pode causar uma potencial degradação do desempenho.
* O streaming, as cargas em pedaços e binários não são suportados.
* A capacidade de personalizar o comportamento do cliente HTTP é limitada.

Se alguma destas limitações puder afetar o seu caso de utilização, considere em vez disso utilizar funções de atividade e bibliotecas de clientes HTTP específicas para fazer chamadas HTTP de saída.

> [!NOTE]
> Se é um desenvolvedor .NET, pode perguntar-se por que razão esta funcionalidade utiliza os tipos **DurableHttpRequest** e **DurableHttpResponse** em vez dos tipos incorporados .NET **HttpRequestMessage** e **HttpResponseMessage.**
>
> Esta escolha de design é intencional. A razão principal é que os tipos personalizados ajudam a garantir que os utilizadores não fazem pressupostos incorretos sobre os comportamentos suportados do cliente HTTP interno. Os tipos específicos das Funções Duradouras também possibilitam a simplificação do design da API. Também podem disponibilizar mais facilmente funcionalidades especiais como [a integração de identidade gerida](#managed-identities) e o [padrão de consumidor de sondagens.](#http-202-handling) 

### <a name="extensibility-net-only"></a>Extensibilidade (apenas.NET)

A personalização do comportamento do cliente HTTP interno da orquestração é possível utilizando [a injeção de dependência Azure Functions .NET](../functions-dotnet-dependency-injection.md). Esta capacidade pode ser útil para fazer pequenas alterações comportamentais. Também pode ser útil para testar o cliente HTTP, injetando objetos falsos.

O exemplo a seguir demonstra a utilização de injeção de dependência para desativar a validação do certificado TLS/SSL para funções orquestradoras que chamam pontos finais http externos.

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
        // Disable TLS/SSL certificate validation (not recommended in production!)
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
> [Conheça as entidades duráveis](durable-functions-entities.md)
