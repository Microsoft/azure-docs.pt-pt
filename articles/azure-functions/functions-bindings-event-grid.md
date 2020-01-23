---
title: Gatilho de grade de eventos para Azure Functions
description: Entenda como lidar com eventos de grade de eventos no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: 8062428ae63a572b81a5432c8b29910fe8422e24
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547460"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Gatilho de grade de eventos para Azure Functions

Este artigo explica como lidar com eventos de [grade de eventos](../event-grid/overview.md) no Azure functions. Para obter detalhes sobre como lidar com mensagens de grade de eventos em um ponto de extremidade HTTP, leia [receber eventos para um ponto de extremidade http](../event-grid/receive-events.md).

A grade de eventos é um serviço do Azure que envia solicitações HTTP para notificá-lo sobre eventos que acontecem em *Publicadores*. Um Publicador é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de BLOBs do Azure é um Publicador, e [um upload ou uma exclusão de blob é um evento](../storage/blobs/storage-blob-event-overview.md). Alguns [Serviços do Azure têm suporte interno para publicar eventos na grade de eventos](../event-grid/overview.md#event-sources).

Os *manipuladores* de eventos recebem e processam eventos. Azure Functions é um dos vários [Serviços do Azure que têm suporte interno para lidar com eventos de grade de eventos](../event-grid/overview.md#event-handlers). Neste artigo, você aprenderá a usar um gatilho de grade de eventos para invocar uma função quando um evento for recebido da grade de eventos.

Se preferir, você pode usar um gatilho HTTP para manipular eventos de grade de eventos; consulte [receber eventos para um ponto de extremidade http](../event-grid/receive-events.md). No momento, você não pode usar um gatilho de grade de eventos para um aplicativo Azure Functions quando o evento é entregue no [esquema CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Em vez disso, use um gatilho HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

O gatilho de grade de eventos é fornecido no pacote NuGet [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) , versão 2. x. O código-fonte do pacote está no repositório GitHub [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

O gatilho de grade de eventos é fornecido no pacote NuGet [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) , versão 1. x. O código-fonte do pacote está no repositório GitHub [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para um exemplo de gatilho HTTP, consulte [receber eventos para um ponto de extremidade http](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C#(2. x e superior)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que é associada a `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Para obter mais informações, consulte pacotes, [atributos](#attributes), [configuração](#configuration)e [uso](#usage).

### <a name="version-1x"></a>Versão 1. x

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) de funções 1. x que se associa a `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-script-version-2x-and-higher"></a>C#script (versão 2. x e superior)

Aqui está um exemplo que é associado a `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Para obter mais informações, consulte pacotes, [atributos](#attributes), [configuração](#configuration)e [uso](#usage).

### <a name="version-1x"></a>Versão 1. x

Aqui está o código de script C# 1. x do Functions que se associa a `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Eis o código JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *Function. JSON* e uma [função Python](functions-reference-python.md) que usa a associação.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Este é o código Python:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Esta secção contém os exemplos seguintes:

* [Gatilho de grade de eventos, parâmetro de cadeia de caracteres](#event-grid-trigger-string-parameter)
* [Gatilho de grade de eventos, parâmetro POJO](#event-grid-trigger-pojo-parameter)

Os exemplos a seguir mostram a associação de gatilho em um arquivo *Function. JSON* e [funções Java](functions-reference-java.md) que usam a associação e imprimem um evento, primeiro recebendo o evento como ```String``` e segundo como um POJO.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

### <a name="event-grid-trigger-string-parameter"></a>Gatilho de grade de eventos, parâmetro de cadeia de caracteres

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Gatilho de grade de eventos, parâmetro POJO

Este exemplo usa o seguinte POJO, representando as propriedades de nível superior de um evento de grade de eventos:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Na chegada, a carga JSON do evento é desserializada no ```EventSchema``` POJO para uso pela função. Isso permite que a função acesse as propriedades do evento de forma orientada a objeto.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `EventGridTrigger` em parâmetros cujo valor venha de EventGrid. Parâmetros com essas anotações fazem com que a função seja executada quando um evento chega.  Essa anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando `Optional<T>`.

---

## <a name="attributes"></a>Atributos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o atributo [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) .

Aqui está um atributo `EventGridTrigger` em uma assinatura de método:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, C# consulte exemplo.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) permite configurar declarativamente uma associação de grade de eventos fornecendo valores de configuração. Consulte as seções [exemplo](#example) e [configuração](#configuration) para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você definiu no arquivo *Function. JSON* . Não há parâmetros ou propriedades de construtor para definir no atributo `EventGridTrigger`.

|propriedade de Function |Descrição|
|---------|---------|
| **tipo** | Obrigatório-deve ser definido como `eventGridTrigger`. |
| **direção** | Obrigatório-deve ser definido como `in`. |
| **name** | Obrigatório – o nome da variável usada no código de função para o parâmetro que recebe os dados do evento. |

## <a name="usage"></a>Utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No Azure Functions 1. x, você pode usar os seguintes tipos de parâmetro para o gatilho de grade de eventos:

* `JObject`
* `string`

No Azure Functions 2. x e superior, você também tem a opção de usar o seguinte tipo de parâmetro para o gatilho de grade de eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-define propriedades para os campos comuns a todos os tipos de evento.

> [!NOTE]
> No functions v1, se você tentar associar a `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, o compilador exibirá uma mensagem "preterida" e o aconselhará a usar `Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para usar o tipo mais recente, referencie o pacote NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) e qualifique totalmente o nome do tipo de `EventGridEvent` prefixando-o com `Microsoft.Azure.EventGrid.Models`.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

No Azure Functions 1. x, você pode usar os seguintes tipos de parâmetro para o gatilho de grade de eventos:

* `JObject`
* `string`

No Azure Functions 2. x e superior, você também tem a opção de usar o seguinte tipo de parâmetro para o gatilho de grade de eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-define propriedades para os campos comuns a todos os tipos de evento.

> [!NOTE]
> No functions v1, se você tentar associar a `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, o compilador exibirá uma mensagem "preterida" e o aconselhará a usar `Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para usar o tipo mais recente, referencie o pacote NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) e qualifique totalmente o nome do tipo de `EventGridEvent` prefixando-o com `Microsoft.Azure.EventGrid.Models`. Para obter informações sobre como fazer referência a pacotes NuGet C# em uma função de script, consulte [usando pacotes NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A instância da grade de eventos está disponível por meio do parâmetro configurado na propriedade `name` do arquivo *Function. JSON* .

# <a name="pythontabpython"></a>[Python](#tab/python)

A instância da grade de eventos está disponível por meio do parâmetro configurado na propriedade `name` do arquivo *Function. JSON* , digitada como `func.EventGridEvent`.

# <a name="javatabjava"></a>[Java](#tab/java)

A instância de evento da grade de eventos está disponível por meio do parâmetro associado ao atributo `EventGridTrigger`, digitado como um `EventSchema`. Consulte o [exemplo](#example) para obter mais detalhes.

---

## <a name="event-schema"></a>Esquema de eventos

Os dados de um evento de grade de eventos são recebidos como um objeto JSON no corpo de uma solicitação HTTP. O JSON é semelhante ao seguinte exemplo:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

O exemplo mostrado é uma matriz de um elemento. A grade de eventos sempre envia uma matriz e pode enviar mais de um evento na matriz. O tempo de execução invoca sua função uma vez para cada elemento da matriz.

As propriedades de nível superior nos dados JSON de evento são as mesmas entre todos os tipos de evento, enquanto o conteúdo da propriedade `data` é específico para cada tipo de evento. O exemplo mostrado é para um evento de armazenamento de BLOBs.

Para obter explicações sobre as propriedades comuns e específicas do evento, consulte [Propriedades do evento](../event-grid/event-schema.md#event-properties) na documentação da grade de eventos.

O tipo de `EventGridEvent` define apenas as propriedades de nível superior; a propriedade `Data` é uma `JObject`.

## <a name="create-a-subscription"></a>Criar uma subscrição

Para começar a receber solicitações HTTP da grade de eventos, crie uma assinatura da grade de eventos que especifica a URL do ponto de extremidade que invoca a função.

### <a name="azure-portal"></a>Portal do Azure

Para funções que você desenvolve no portal do Azure com o gatilho de grade de eventos, selecione **Adicionar assinatura de grade de eventos**.

![Criar assinatura no portal](media/functions-bindings-event-grid/portal-sub-create.png)

Quando você seleciona esse link, o portal abre a página **criar assinatura de evento** com a URL do ponto de extremidade preenchida.

![URL de ponto de extremidade preenchida](media/functions-bindings-event-grid/endpoint-url.png)

Para obter mais informações sobre como criar assinaturas usando o portal do Azure, consulte [criar evento personalizado-portal do Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da grade de eventos.

### <a name="azure-cli"></a>CLI do Azure

Para criar uma assinatura usando [o CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), use o comando [AZ eventgrid Event-Subscription Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) .

O comando requer a URL do ponto de extremidade que invoca a função. O exemplo a seguir mostra o padrão de URL específico da versão:

#### <a name="version-2x-and-higher-runtime"></a>Tempo de execução da versão 2. x (e superior)

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Tempo de execução da versão 1. x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

A chave do sistema é uma chave de autorização que deve ser incluída na URL do ponto de extremidade para um gatilho de grade de eventos. A seção a seguir explica como obter a chave do sistema.

Aqui está um exemplo que assina uma conta de armazenamento de BLOBs (com um espaço reservado para a chave do sistema):

#### <a name="version-2x-and-higher-runtime"></a>Tempo de execução da versão 2. x (e superior)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Tempo de execução da versão 1. x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Para obter mais informações sobre como criar uma assinatura, consulte [o guia de início rápido do armazenamento de BLOBs](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou os outros guias de início rápido da grade de eventos.

### <a name="get-the-system-key"></a>Obter a chave do sistema

Você pode obter a chave do sistema usando a seguinte API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Tempo de execução da versão 2. x (e superior)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Tempo de execução da versão 1. x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Essa é uma API de administração, portanto, requer sua [chave mestra](functions-bindings-http-webhook.md#authorization-keys)do aplicativo de funções. Não confunda a chave do sistema (para invocar uma função de gatilho de grade de eventos) com a chave mestra (para executar tarefas administrativas no aplicativo de funções). Ao assinar um tópico da grade de eventos, certifique-se de usar a chave do sistema.

Veja um exemplo da resposta que fornece a chave do sistema:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Você pode obter a chave mestra para seu aplicativo de funções na guia **configurações do aplicativo de funções** no Portal.

> [!IMPORTANT]
> A chave mestra fornece acesso de administrador ao seu aplicativo de funções. Não compartilhe essa chave com terceiros ou distribua-a em aplicativos cliente nativos.

Para obter mais informações, consulte [chaves de autorização](functions-bindings-http-webhook.md#authorization-keys) no artigo de referência do gatilho http.

Como alternativa, você pode enviar um HTTP PUT para especificar o valor da chave por conta própria.

## <a name="local-testing-with-viewer-web-app"></a>Teste local com o aplicativo Web do Visualizador

Para testar um gatilho de grade de eventos localmente, você precisa obter solicitações HTTP da grade de eventos entregues de sua origem na nuvem para seu computador local. Uma maneira de fazer isso é capturando solicitações online e reenviá-las manualmente no computador local:

1. [Crie um aplicativo Web do visualizador](#create-a-viewer-web-app) que capture mensagens de evento.
1. [Crie uma assinatura de grade de eventos](#create-an-event-grid-subscription) que envia eventos para o aplicativo de visualizador.
1. [Gere uma solicitação](#generate-a-request) e copie o corpo da solicitação do aplicativo visualizador.
1. [Poste manualmente a solicitação](#manually-post-the-request) para a URL de localhost da sua função de gatilho de grade de eventos.

Quando terminar o teste, você poderá usar a mesma assinatura para produção atualizando o ponto de extremidade. Use o comando [AZ eventgrid Event-Subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) CLI do Azure.

### <a name="create-a-viewer-web-app"></a>Criar um aplicativo Web do Visualizador

Para simplificar a captura de mensagens de evento, você pode implantar um [aplicativo Web predefinido](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Vê o site, mas ainda não foram publicados eventos no mesmo.

![Ver novo site](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Crie uma assinatura de grade de eventos do tipo que você deseja testar e dê a ela a URL do seu aplicativo Web como o ponto de extremidade para notificação de eventos. O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`. Portanto, a URL completa é `https://<your-site-name>.azurewebsites.net/api/updates`

Para obter informações sobre como criar assinaturas usando o portal do Azure, consulte [criar evento personalizado-portal do Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da grade de eventos.

### <a name="generate-a-request"></a>Gerar uma solicitação

Dispare um evento que gerará tráfego HTTP para seu ponto de extremidade do aplicativo Web.  Por exemplo, se você criou uma assinatura de armazenamento de BLOBs, carregue ou exclua um blob. Quando uma solicitação aparecer em seu aplicativo Web, copie o corpo da solicitação.

A solicitação de validação de assinatura será recebida primeiro; Ignore todas as solicitações de validação e copie a solicitação de evento.

![Copiar corpo da solicitação do aplicativo Web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Postar a solicitação manualmente

Execute a função de grade de eventos localmente.

Use uma ferramenta como [postmaster](https://www.getpostman.com/) ou [ondulação](https://curl.haxx.se/docs/httpscripting.html) para criar uma solicitação HTTP post:

* Definir um cabeçalho de `Content-Type: application/json`.
* Definir um cabeçalho de `aeg-event-type: Notification`.
* Cole os dados de RequestBin no corpo da solicitação.
* Poste para a URL da sua função de gatilho de grade de eventos.
  * Para 2. x e superior, use o seguinte padrão:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Para uso de 1. x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

O parâmetro `functionName` deve ser o nome especificado no atributo `FunctionName`.

As capturas de tela a seguir mostram os cabeçalhos e o corpo da solicitação no postmaster:

![Cabeçalhos no postmaster](media/functions-bindings-event-grid/postman2.png)

![Corpo da solicitação no postmaster](media/functions-bindings-event-grid/postman.png)

A função de gatilho de grade de eventos é executada e mostra logs semelhantes ao exemplo a seguir:

![Logs de função de gatilho de grade de eventos de exemplo](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre a grade de eventos](../event-grid/overview.md)
