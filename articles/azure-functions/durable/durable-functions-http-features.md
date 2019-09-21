---
title: Recursos de HTTP no Durable Functions-Azure Functions
description: Saiba mais sobre os recursos de HTTP integrados na extensão de Durable Functions para Azure Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 5a9143643b1a1cabb32903933dbd68d665d0424f
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71171140"
---
# <a name="http-features"></a>Recursos de HTTP

O Durable Functions tem vários recursos que facilitam a incorporação de orquestrações duráveis e entidades em fluxos de trabalho HTTP. Este artigo apresenta detalhes sobre alguns desses recursos.

## <a name="exposing-http-apis"></a>Expondo APIs HTTP

Orquestrações e entidades podem ser invocadas e gerenciadas usando solicitações HTTP. A extensão de Durable Functions expõe as APIs HTTP internas e também fornece APIs para interagir com orquestrações e entidades de dentro de funções disparadas por HTTP.

### <a name="built-in-http-apis"></a>APIs HTTP internas

A extensão Durable Functions adiciona automaticamente um conjunto de APIs HTTP ao host Azure Functions. Essas APIs permitem que você interaja e gerencie orquestrações e entidades sem escrever nenhum código.

Há suporte para as seguintes APIs HTTP internas.

* [Iniciar nova orquestração](durable-functions-http-api.md#start-orchestration)
* [Instância de orquestração de consulta](durable-functions-http-api.md#get-instance-status)
* [Encerrar instância de orquestração](durable-functions-http-api.md#terminate-instance)
* [Enviar um evento externo para uma orquestração](durable-functions-http-api.md#raise-event)
* [Limpar o histórico de orquestração](durable-functions-http-api.md#purge-single-instance-history)
* [Enviar um evento de operação para uma entidade](durable-functions-http-api.md#signal-entity)
* [Consultar o estado de uma entidade](durable-functions-http-api.md#query-entity)

Consulte o artigo [APIs http](durable-functions-http-api.md) para obter uma descrição completa de todas as APIs http internas expostas pela extensão Durable functions.

### <a name="http-api-url-discovery"></a>Descoberta de URL de API HTTP

A [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) expõe APIs que podem ser usadas para gerar conteúdos de resposta http convenientes. Por exemplo, ele pode criar uma resposta que contém links para APIs de gerenciamento para uma instância de orquestração específica. Aqui está um exemplo de função de gatilho HTTP que demonstra como usar essa API para uma nova instância de orquestração:

#### <a name="precompiled-c"></a>Pré-compiladoC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C#Prescritiva

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente funções 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Iniciar uma função de orquestrador usando as funções de gatilho HTTP mostradas acima pode ser feito usando qualquer cliente HTTP. O comando de ondulação a seguir inicia uma função `DoWork`de orquestrador chamada.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Aqui está um exemplo de resposta para uma orquestração `abc123` com como sua ID (alguns detalhes foram removidos para maior clareza):

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

Cada um dos `~Uri` campos no exemplo anterior corresponde a APIs http internas. Essas APIs podem ser usadas para gerenciar a instância de orquestração de destino.

> [!NOTE]
> O formato das URLs de webhook pode diferir dependendo da versão do host de Azure Functions que você está executando. O exemplo acima é para o host Azure Functions 2,0.

Para obter uma descrição de todas as APIs HTTP internas, consulte a documentação de [referência da API http](durable-functions-http-api.md) .

### <a name="async-operation-tracking"></a>Acompanhamento de operação assíncrona

A resposta HTTP mencionada anteriormente foi projetada para ajudar a implementar APIs assíncronas HTTP de longa execução com Durable Functions. Esse padrão é, às vezes, chamado de *padrão de consumidor de sondagem*. O fluxo de cliente/servidor funciona da seguinte maneira:

1. O cliente emite uma solicitação HTTP para iniciar um processo de execução demorada, como uma função de orquestrador.
2. O gatilho http de destino retorna uma resposta http 202 com `Location` um cabeçalho com `statusQueryGetUri` o valor.
3. O cliente sonda a URL no `Location` cabeçalho. Ele continua vendo as respostas http 202 com um `Location` cabeçalho.
4. Quando a instância é concluída (ou falha), o ponto de extremidade `Location` no cabeçalho retorna http 200.

Esse protocolo permite coordenar processos de execução longa com clientes externos ou serviços que dão suporte à sondagem de um ponto `Location` de extremidade http e ao seguir o cabeçalho. As implementações de cliente e servidor desse padrão são incorporadas às APIs HTTP Durable Functions.

> [!NOTE]
> Por padrão, todas as ações baseadas em HTTP fornecidas pelos [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/) dão suporte ao padrão de operação assíncrona padrão. Esse recurso possibilita inserir uma função durável de execução longa como parte de um fluxo de trabalho de aplicativos lógicos. Mais detalhes sobre o suporte a aplicativos lógicos para padrões HTTP assíncronos podem ser encontrados na [documentação de ações e gatilhos de fluxo de trabalho dos aplicativos lógicos do Azure](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> A interação com orquestrações pode ser feita a partir de qualquer tipo de função, não apenas a funções disparadas por HTTP.

Para obter mais informações sobre como gerenciar orquestrações e entidades usando APIs de cliente, consulte o artigo [Gerenciamento de instâncias](durable-functions-instance-management.md) .

## <a name="consuming-http-apis"></a>Consumindo APIs HTTP

As funções de orquestrador não têm permissão para fazer e/s diretamente, conforme descrito em [restrições de código de função de orquestrador](durable-functions-code-constraints.md). Em vez disso, as funções de orquestrador normalmente chamam [funções de atividade](durable-functions-types-features-overview.md#activity-functions) que executam operações de e/s.

A partir do Durable Functions 2,0, as orquestrações são capazes de consumir nativamente APIs HTTP usando a [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> A capacidade de chamar pontos de extremidade HTTP diretamente de funções de orquestrador ainda não está disponível em JavaScript.

O código de exemplo a seguir C# mostra uma função de orquestrador fazendo uma solicitação HTTP `CallHttpAsync` de saída usando a API do .net:

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

A ação "chamar HTTP" permite que você execute as seguintes ações em suas funções de orquestrador:

* Chame APIs HTTP diretamente de funções de orquestração (com algumas limitações mencionadas posteriormente).
* Suporte automático para padrões de sondagem de status HTTP 202 do lado do cliente.
* Use [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md) para fazer chamadas http autorizadas para outros pontos de extremidade do Azure.

A capacidade de consumir APIs HTTP diretamente de funções de orquestrador é destinada a ser uma conveniência para um determinado conjunto de cenários comuns. É possível implementar todos esses recursos por conta própria usando funções de atividade. Em muitos casos, as funções de atividade podem lhe proporcionar mais flexibilidade.

### <a name="http-202-handling"></a>Manipulação de HTTP 202

A API "chamar HTTP" pode implementar automaticamente o lado do cliente do *padrão de consumidor de sondagem*. Se uma API chamada retornar uma resposta http 202 com um `Location` cabeçalho, a função de orquestrador sondará automaticamente `Location` o recurso até que uma resposta não 202 seja retornada. A resposta não 202 será a resposta retornada ao código de função do orquestrador.

> [!NOTE]
> As funções de orquestrador também dão suporte nativo ao *padrão de consumidor de sondagem*no lado do servidor, conforme descrito em [acompanhamento de operação assíncrona](#async-operation-tracking). Isso significa que as orquestrações em um aplicativo de funções podem coordenar facilmente as funções de orquestrador em outros aplicativos de funções. Isso é semelhante ao conceito de [suborquestração](durable-functions-sub-orchestrations.md) , mas com suporte para comunicação entre aplicativos. Isso é particularmente útil para o desenvolvimento de aplicativos no estilo de microserviço.

### <a name="managed-identities"></a>Identidades geridas

Durable Functions nativamente dá suporte a APIs de chamada que aceitam tokens do Azure AD para autorização. Esse suporte usa [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md) para adquirir esses tokens.

O código a seguir é um exemplo de uma função de orquestrador do .NET que faz chamadas autenticadas para reiniciar uma máquina virtual usando a [API REST de máquinas virtuais](https://docs.microsoft.com/rest/api/compute/virtualmachines)Azure Resource Manager.

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
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

No exemplo anterior, o `tokenSource` parâmetro é configurado para adquirir tokens do Azure ad para [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) (identificado pelo "URI de recurso `https://management.core.windows.net`"). O exemplo supõe que o aplicativo de função atual está sendo executado localmente ou foi implantado como um aplicativo Azure Functions com uma identidade gerenciada. Presume-se que a identidade local ou a identidade gerenciada tenha permissão para gerenciar VMs no grupo de recursos `myRG`especificado,.

Em tempo de execução, a origem do token configurada retorna automaticamente um token de acesso OAuth 2,0 e o adiciona como `Authorization` um token de portador ao cabeçalho da solicitação de saída. Esse modelo é uma melhoria sobre a adição manual de cabeçalhos de autorização a solicitações HTTP porque:

* A atualização de token é manipulada automaticamente. Você não precisa se preocupar com os tokens expirados.
* Os tokens nunca são armazenados no estado de orquestração durável.
* Você não precisa escrever nenhum código para lidar com a aquisição de token.

Um exemplo mais completo pode ser encontrado nos [exemplos pré-compilados C# "RestartVMs"](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

Identidades gerenciadas não são limitadas ao gerenciamento de recursos do Azure. Identidades gerenciadas podem ser usadas para acessar qualquer API que aceite tokens de portador do Azure AD, incluindo serviços primários do Azure ou aplicativos Web de terceiros. O aplicativo Web de terceiros poderia até ser outro aplicativo de funções. Para obter uma lista de serviços primários do Azure que dão suporte à autenticação com o Azure AD, consulte [Serviços do Azure que dão suporte à autenticação do Azure ad](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Limitações

O suporte interno para chamar APIs HTTP é um recurso de conveniência e não é apropriado para todos os cenários. As solicitações HTTP enviadas pelas funções do Orchestrator e suas respostas são serializadas e mantidas como mensagens da fila. Esse comportamento de enfileiramento garante que as chamadas HTTP sejam [confiáveis e seguras para reprodução de orquestração](durable-functions-orchestrations.md#reliability). No entanto, esse comportamento de enfileiramento também significa que:

* Cada solicitação HTTP envolve latência adicional quando comparada a um cliente HTTP nativo.
* Mensagens grandes de solicitação ou resposta que não cabem em uma mensagem da fila podem degradar significativamente o desempenho da orquestração. A degradação potencial do desempenho é devido à sobrecarga de descarregar cargas de mensagens no armazenamento de BLOBs.
* Não há suporte para cargas de streaming, partes e binárias.
* A capacidade de personalizar o comportamento do cliente HTTP é limitada.

Se qualquer uma dessas limitações puder afetar seu caso de uso, considere usar funções de atividade e bibliotecas de cliente HTTP específicas de idioma para fazer chamadas HTTP de saída.

> [!NOTE]
> Se você for um desenvolvedor do .net, você deve estar se perguntando por que `DurableHttpRequest` esse `DurableHttpResponse` recurso usa e tipos em vez do .NET `HttpRequestMessage` interno `HttpResponseMessage`e do. Essa escolha de design foi intencional. O principal motivo é que os tipos personalizados ajudam a garantir que os usuários não façam suposições incorretas sobre os comportamentos com suporte do cliente HTTP interno. Os tipos específicos de duráveis também possibilitam a simplificação do design de API e a criação mais fácil de recursos especiais, como [integração de identidade gerenciada](#managed-identities) e [padrão de consumidor de sondagem](#http-202-handling).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre entidades duráveis](durable-functions-entities.md)
