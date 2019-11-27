---
title: Recursos de HTTP no Durable Functions-Azure Functions
description: Saiba mais sobre os recursos de HTTP integrados na extensão de Durable Functions para Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 3fcb777969f7d29b0e8698156dbdd0724f16f0b5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232877"
---
# <a name="http-features"></a>Recursos de HTTP

O Durable Functions tem vários recursos que facilitam a incorporação de orquestrações duráveis e entidades em fluxos de trabalho HTTP. Este artigo apresenta detalhes sobre alguns desses recursos.

## <a name="exposing-http-apis"></a>Expondo APIs HTTP

Orquestrações e entidades podem ser invocadas e gerenciadas usando solicitações HTTP. A extensão de Durable Functions expõe as APIs HTTP internas. Ele também fornece APIs para interagir com orquestrações e entidades de dentro de funções disparadas por HTTP.

### <a name="built-in-http-apis"></a>APIs HTTP internas

A extensão Durable Functions adiciona automaticamente um conjunto de APIs HTTP ao host Azure Functions. Com essas APIs, você pode interagir com e gerenciar orquestrações e entidades sem escrever nenhum código.

Há suporte para as seguintes APIs HTTP internas.

* [Iniciar nova orquestração](durable-functions-http-api.md#start-orchestration)
* [Instância de orquestração de consulta](durable-functions-http-api.md#get-instance-status)
* [Encerrar instância de orquestração](durable-functions-http-api.md#terminate-instance)
* [Enviar um evento externo para uma orquestração](durable-functions-http-api.md#raise-event)
* [Limpar o histórico de orquestração](durable-functions-http-api.md#purge-single-instance-history)
* [Enviar um evento de operação para uma entidade](durable-functions-http-api.md#signal-entity)
* [Consultar o estado de uma entidade](durable-functions-http-api.md#query-entity)

Consulte o [artigo APIs http](durable-functions-http-api.md) para obter uma descrição completa de todas as APIs http internas expostas pela extensão Durable functions.

### <a name="http-api-url-discovery"></a>Descoberta de URL de API HTTP

A [Associação de cliente de orquestração](durable-functions-bindings.md#orchestration-client) expõe APIs que podem gerar conteúdos de resposta http convenientes. Por exemplo, ele pode criar uma resposta que contém links para APIs de gerenciamento para uma instância de orquestração específica. Os exemplos a seguir mostram uma função HTTP-Trigger que demonstra como usar essa API para uma nova instância de orquestração:

#### <a name="precompiled-c"></a>Pré-compiladoC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>Script C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-with-functions-20-or-later-only"></a>JavaScript com funções 2,0 ou posteriores somente

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Iniciar uma função de orquestrador usando as funções HTTP-Trigger mostradas anteriormente pode ser feito usando qualquer cliente HTTP. O comando de rotação a seguir inicia uma função de orquestrador chamada `DoWork`:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Em seguida, há um exemplo de resposta para uma orquestração que tem `abc123` como sua ID. Alguns detalhes foram removidos para fins de clareza.

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

No exemplo anterior, cada um dos campos que terminam em `Uri` corresponde a uma API HTTP interna. Você pode usar essas APIs para gerenciar a instância de orquestração de destino.

> [!NOTE]
> O formato das URLs de webhook depende de qual versão do host de Azure Functions você está executando. O exemplo anterior é para o host Azure Functions 2,0.

Para obter uma descrição de todas as APIs HTTP internas, consulte a [referência da API http](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Acompanhamento de operação assíncrona

A resposta HTTP mencionada anteriormente foi projetada para ajudar a implementar APIs assíncronas HTTP de longa execução com Durable Functions. Esse padrão é, às vezes, chamado de *padrão de consumidor de sondagem*. O fluxo de cliente/servidor funciona da seguinte maneira:

1. O cliente emite uma solicitação HTTP para iniciar um processo de execução longa, como uma função de orquestrador.
1. O gatilho HTTP de destino retorna uma resposta HTTP 202 com um cabeçalho Location que tem o valor "statusQueryGetUri".
1. O cliente sonda a URL no cabeçalho de local. O cliente continua a ver as respostas HTTP 202 com um cabeçalho de local.
1. Quando a instância for concluída ou falhar, o ponto de extremidade no cabeçalho de local retornará HTTP 200.

Esse protocolo permite a coordenação de processos de execução longa com clientes ou serviços externos que podem sondar um ponto de extremidade HTTP e seguir o cabeçalho do local. As implementações de cliente e servidor desse padrão são incorporadas às APIs HTTP Durable Functions.

> [!NOTE]
> Por padrão, todas as ações baseadas em HTTP fornecidas pelos [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/) dão suporte ao padrão de operação assíncrona padrão. Esse recurso possibilita inserir uma função durável de execução longa como parte de um fluxo de trabalho de aplicativos lógicos. Você pode encontrar mais detalhes sobre o suporte a aplicativos lógicos para padrões HTTP assíncronos na [documentação de ações e gatilhos de fluxo de trabalho dos aplicativos lógicos do Azure](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> As interações com orquestrações podem ser feitas de qualquer tipo de função, não apenas funções disparadas por HTTP.

Para obter mais informações sobre como gerenciar orquestrações e entidades usando APIs de cliente, consulte o [artigo gerenciamento de instâncias](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Consumindo APIs HTTP

Conforme descrito nas [restrições de código da função de orquestrador](durable-functions-code-constraints.md), as funções de orquestrador não podem fazer e/s diretamente. Em vez disso, normalmente chamam [funções de atividade](durable-functions-types-features-overview.md#activity-functions) que fazem operações de e/s.

A partir do Durable Functions 2,0, as orquestrações podem consumir nativamente APIs HTTP usando a [Associação de gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> A capacidade de chamar pontos de extremidade HTTP diretamente de funções de orquestrador ainda não está disponível em JavaScript.

O código de exemplo a seguir C# mostra uma função de orquestrador fazendo uma solicitação HTTP de saída usando a API do .NET **CallHttpAsync** :

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

Usando a ação "chamar HTTP", você pode executar as seguintes ações em suas funções de orquestrador:

* Chame APIs HTTP diretamente de funções de orquestração, com algumas limitações que são mencionadas posteriormente.
* Suporta automaticamente padrões de sondagem de status HTTP 202 do lado do cliente.
* Use [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md) para fazer chamadas http autorizadas para outros pontos de extremidade do Azure.

A capacidade de consumir APIs HTTP diretamente de funções de orquestrador é destinada a ser uma conveniência para um determinado conjunto de cenários comuns. Você pode implementar todos esses recursos por conta própria usando funções de atividade. Em muitos casos, as funções de atividade podem lhe proporcionar mais flexibilidade.

### <a name="http-202-handling"></a>Manipulação de HTTP 202

A API "chamar HTTP" pode implementar automaticamente o lado do cliente do padrão de consumidor de sondagem. Se uma API chamada retornar uma resposta HTTP 202 com um cabeçalho de local, a função de orquestrador sondará automaticamente o recurso de local até receber uma resposta diferente de 202. Essa resposta será a resposta retornada ao código de função do orquestrador.

> [!NOTE]
> As funções de orquestrador também dão suporte nativo ao padrão de consumidor de sondagem no lado do servidor, conforme descrito em [acompanhamento de operação assíncrona](#async-operation-tracking). Esse suporte significa que as orquestrações em um aplicativo de funções podem coordenar facilmente as funções de orquestrador em outros aplicativos de funções. Isso é semelhante ao conceito de [suborquestração](durable-functions-sub-orchestrations.md) , mas com suporte para comunicação entre aplicativos. Esse suporte é particularmente útil para o desenvolvimento de aplicativos no estilo de microserviço.

### <a name="managed-identities"></a>Identidades gerenciadas

Durable Functions nativamente dá suporte a chamadas para APIs que aceitam tokens do Azure Active Directory (Azure AD) para autorização. Esse suporte usa [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md) para adquirir esses tokens.

O código a seguir é um exemplo de uma função de orquestrador do .NET. A função faz chamadas autenticadas para reiniciar uma máquina virtual usando a [API REST de Azure Resource Manager máquinas virtuais](https://docs.microsoft.com/rest/api/compute/virtualmachines).

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

No exemplo anterior, o parâmetro `tokenSource` está configurado para adquirir tokens do Azure AD para [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Os tokens são identificados pelo URI de recurso `https://management.core.windows.net`. O exemplo supõe que o aplicativo de função atual seja executado localmente ou implantado como um aplicativo de funções com uma identidade gerenciada. Presume-se que a identidade local ou a identidade gerenciada tenha permissão para gerenciar VMs no grupo de recursos especificado `myRG`.

Em tempo de execução, a origem do token configurada retorna automaticamente um token de acesso OAuth 2,0. A origem, em seguida, adiciona o token como um token de portador ao cabeçalho de autorização da solicitação de saída. Esse modelo é uma melhoria em relação à adição manual de cabeçalhos de autorização a solicitações HTTP pelos seguintes motivos:

* A atualização de token é manipulada automaticamente. Você não precisa se preocupar com os tokens expirados.
* Os tokens nunca são armazenados no estado de orquestração durável.
* Você não precisa escrever nenhum código para gerenciar a aquisição de token.

Você pode encontrar um exemplo mais completo no [exemplo de C# RestartVMs pré-compilado](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

Identidades gerenciadas não são limitadas ao gerenciamento de recursos do Azure. Você pode usar identidades gerenciadas para acessar qualquer API que aceite tokens de portador do Azure AD, incluindo serviços do Azure da Microsoft e aplicativos Web de parceiros. O aplicativo Web de um parceiro pode até ser outro aplicativo de funções. Para obter uma lista dos serviços do Azure da Microsoft que dão suporte à autenticação com o Azure AD, consulte [Serviços do Azure que dão suporte à autenticação do Azure ad](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Limitações

O suporte interno para chamar APIs HTTP é um recurso de conveniência. Não é apropriado para todos os cenários.

Solicitações HTTP enviadas por funções de orquestrador e suas respostas são serializadas e persistentes como mensagens de fila. Esse comportamento de enfileiramento garante que as chamadas HTTP sejam [confiáveis e seguras para reprodução de orquestração](durable-functions-orchestrations.md#reliability). No entanto, o comportamento de enfileiramento também tem limitações:

* Cada solicitação HTTP envolve latência adicional quando comparada a um cliente HTTP nativo.
* Mensagens grandes de solicitação ou resposta que não cabem em uma mensagem da fila podem degradar significativamente o desempenho da orquestração. A sobrecarga de descarregar cargas de mensagens no armazenamento de BLOBs pode causar degradação de desempenho potencial.
* Não há suporte para cargas de streaming, partes e binárias.
* A capacidade de personalizar o comportamento do cliente HTTP é limitada.

Se qualquer uma dessas limitações puder afetar seu caso de uso, considere usar funções de atividade e bibliotecas de cliente HTTP específicas de idioma para fazer chamadas HTTP de saída.

> [!NOTE]
> Se você for um desenvolvedor do .NET, talvez se pergunte por que esse recurso usa os tipos **DurableHttpRequest** e **DurableHttpResponse** em vez dos tipos .NET **HttpRequestMessage** e **HttpResponseMessage** internos.
>
> Essa opção de design é intencional. O principal motivo é que os tipos personalizados ajudam a garantir que os usuários não façam suposições incorretas sobre os comportamentos com suporte do cliente HTTP interno. Tipos específicos para Durable Functions também possibilitam a simplificação do design de API. Eles também podem disponibilizar mais facilmente recursos especiais disponíveis como [integração de identidade gerenciada](#managed-identities) e o [padrão de consumidor de sondagem](#http-202-handling). 

### <a name="extensibility-net-only"></a>Extensibilidade (somente .NET)

É possível personalizar o comportamento do cliente HTTP interno da orquestração usando [Azure Functions injeção de dependência .net](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection). Essa capacidade pode ser útil para fazer pequenas alterações comportamentais. Ele também pode ser útil para o teste de unidade do cliente HTTP injetando objetos fictícios.

O exemplo a seguir demonstra como usar injeção de dependência para desabilitar a validação de certificado SSL para funções de orquestrador que chamam pontos de extremidade HTTP externos.

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre entidades duráveis](durable-functions-entities.md)
