---
title: Gatilho de grade de eventos para Azure Functions
description: Entenda como lidar com eventos de grade de eventos no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: fbe41bdc5f253f1a605aa291a31191b7339b9850
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030575"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Gatilho de grade de eventos para Azure Functions

Este artigo explica como lidar com eventos de [grade de eventos](../event-grid/overview.md) no Azure functions.

A grade de eventos é um serviço do Azure que envia solicitações HTTP para notificá-lo sobre eventos que acontecem em *Publicadores*. Um Publicador é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de BLOBs do Azure é um Publicador, e [um upload ou uma exclusão de blob é um evento](../storage/blobs/storage-blob-event-overview.md). Alguns [Serviços do Azure têm suporte interno para publicar eventos na grade de eventos](../event-grid/overview.md#event-sources).

Os *manipuladores* de eventos recebem e processam eventos. Azure Functions é um dos vários [Serviços do Azure que têm suporte interno para lidar com eventos de grade de eventos](../event-grid/overview.md#event-handlers). Neste artigo, você aprenderá a usar um gatilho de grade de eventos para invocar uma função quando um evento for recebido da grade de eventos.

Se preferir, você pode usar um gatilho HTTP para manipular eventos de grade de eventos; consulte [usar um gatilho http como um gatilho de grade de eventos](#use-an-http-trigger-as-an-event-grid-trigger) mais adiante neste artigo. No momento, você não pode usar um gatilho de grade de eventos para um aplicativo Azure Functions quando o evento é entregue no [esquema CloudEvents](../event-grid/cloudevents-schema.md). Em vez disso, use um gatilho HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

O gatilho de grade de eventos é fornecido no pacote NuGet [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) , versão 2. x. O código-fonte do pacote está no repositório GitHub [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

O gatilho de grade de eventos é fornecido no pacote NuGet [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) , versão 1. x. O código-fonte do pacote está no repositório GitHub [Azure-Functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Exemplo

Consulte o exemplo específico de linguagem para um gatilho de grade de eventos:

* C#
* [Script do c# (.csx)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Para um exemplo de gatilho HTTP, consulte [como usar o gatilho http](#use-an-http-trigger-as-an-event-grid-trigger) mais adiante neste artigo.

### <a name="c-2x"></a>C#(2. x)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) de funções 2. x que é associada a `EventGridEvent`:

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

### <a name="c-version-1x"></a>C#(Versão 1. x)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) de funções 1. x que é associada a `JObject`:

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

### <a name="c-script-example"></a>C#exemplo de script

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

#### <a name="c-script-version-2x"></a>C#script (versão 2. x)

Aqui está o código de script C# do Functions 2. x que se associa a `EventGridEvent`:

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

#### <a name="c-script-version-1x"></a>C#script (versão 1. x)

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

### <a name="javascript-example"></a>Exemplo de JavaScript

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

### <a name="python-example"></a>Exemplo de Python

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
import logging
import azure.functions as func


def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Gatilho-exemplos de Java

Esta secção contém os exemplos seguintes:

* [Gatilho de grade de eventos, parâmetro de cadeia de caracteres](#event-grid-trigger-string-parameter-java)
* [Gatilho de grade de eventos, parâmetro POJO](#event-grid-trigger-pojo-parameter-java)

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

#### <a name="event-grid-trigger-string-parameter-java"></a>Gatilho de grade de eventos, parâmetro de cadeia de caracteres (Java)

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: " + content);      
  }
```

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Gatilho de grade de eventos, parâmetro POJO (Java)

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

Na chegada, a carga JSON do evento é desserializada para o ```EventSchema``` POJO para uso pela função. Isso permite que a função acesse as propriedades do evento de forma orientada a objeto.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `EventGridTrigger` em parâmetros cujo valor venha de EventGrid. Parâmetros com essas anotações fazem com que a função seja executada quando um evento chega.  Essa anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando `Optional<T>`.

## <a name="attributes"></a>Atributos

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

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você definiu no arquivo *Function. JSON* . Não há parâmetros ou propriedades de construtor para definir no atributo `EventGridTrigger`.

|propriedade de Function |Descrição|
|---------|---------|
| **type** | Obrigatório-deve ser definido como `eventGridTrigger`. |
| **direction** | Obrigatório-deve ser definido como `in`. |
| **name** | Obrigatório – o nome da variável usada no código de função para o parâmetro que recebe os dados do evento. |

## <a name="usage"></a>Utilização

Para C# funções F# e no Azure Functions 1. x, você pode usar os seguintes tipos de parâmetro para o gatilho de grade de eventos:

* `JObject`
* `string`

Para C# funções F# e no Azure Functions 2. x, você também tem a opção de usar o seguinte tipo de parâmetro para o gatilho de grade de eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-define as propriedades para os campos comuns a todos os tipos de evento.

> [!NOTE]
> No functions v1, se você tentar associar a `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, o compilador exibirá uma mensagem "preterida" e o aconselhará a usar `Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para usar o tipo mais recente, referencie o pacote NuGet [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) e qualifique totalmente o nome do tipo `EventGridEvent` prefixando-o com `Microsoft.Azure.EventGrid.Models`. Para obter informações sobre como fazer referência a pacotes NuGet C# em uma função de script, consulte [usando pacotes NuGet](functions-reference-csharp.md#using-nuget-packages)

Para funções de JavaScript, o parâmetro nomeado pela propriedade *Function. json* `name` tem uma referência ao objeto de evento.

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

O tipo `EventGridEvent` define apenas as propriedades de nível superior; a propriedade `Data` é uma `JObject`.

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

#### <a name="version-2x-runtime"></a>Tempo de execução da versão 2. x

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Tempo de execução da versão 1. x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

A chave do sistema é uma chave de autorização que deve ser incluída na URL do ponto de extremidade para um gatilho de grade de eventos. A seção a seguir explica como obter a chave do sistema.

Aqui está um exemplo que assina uma conta de armazenamento de BLOBs (com um espaço reservado para a chave do sistema):

#### <a name="version-2x-runtime"></a>Tempo de execução da versão 2. x

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

#### <a name="version-2x-runtime"></a>Tempo de execução da versão 2. x

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

* Defina um cabeçalho `Content-Type: application/json`.
* Defina um cabeçalho `aeg-event-type: Notification`.
* Cole os dados de RequestBin no corpo da solicitação.
* Poste para a URL da sua função de gatilho de grade de eventos.
  * Para 2. x, use o seguinte padrão:

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

## <a name="local-testing-with-ngrok"></a>Teste local com ngrok

Outra maneira de testar um gatilho de grade de eventos localmente é automatizar a conexão HTTP entre a Internet e o computador de desenvolvimento. Você pode fazer isso com uma ferramenta como [ngrok](https://ngrok.com/):

1. [Criar um ponto de extremidade ngrok](#create-an-ngrok-endpoint).
1. [Execute a função de gatilho de grade de eventos](#run-the-event-grid-trigger-function).
1. [Crie uma assinatura de grade de eventos](#create-a-subscription) que envia eventos para o ponto de extremidade ngrok.
1. [Disparar um evento](#trigger-an-event).

Quando terminar o teste, você poderá usar a mesma assinatura para produção atualizando o ponto de extremidade. Use o comando [AZ eventgrid Event-Subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) CLI do Azure.

### <a name="create-an-ngrok-endpoint"></a>Criar um ponto de extremidade ngrok

Baixe *ngrok. exe* de [ngrok](https://ngrok.com/)e execute com o seguinte comando:

```
ngrok http -host-header=localhost 7071
```

O parâmetro-host-header é necessário porque o tempo de execução do Functions espera solicitações do localhost quando ele é executado no localhost. 7071 é o número da porta padrão quando o tempo de execução é executado localmente.

O comando cria uma saída semelhante à seguinte:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Você usará a URL `https://{subdomain}.ngrok.io` para sua assinatura da grade de eventos.

### <a name="run-the-event-grid-trigger-function"></a>Executar a função de gatilho de grade de eventos

A URL ngrok não obtém tratamento especial por grade de eventos, portanto, sua função deve estar em execução localmente quando a assinatura é criada. Se não estiver, a resposta de validação não será enviada e a criação da assinatura falhará.

### <a name="create-a-subscription"></a>Criar uma subscrição

Crie uma assinatura de grade de eventos do tipo que você deseja testar e dê a ela seu ponto de extremidade ngrok.

Use este padrão de ponto de extremidade para o Functions 2. x:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Use este padrão de ponto de extremidade para o Functions 1. x:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

O parâmetro `{FUNCTION_NAME}` deve ser o nome especificado no atributo `FunctionName`.

Veja um exemplo usando o CLI do Azure:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Para obter informações sobre como criar uma assinatura, consulte [criar uma assinatura](#create-a-subscription) anteriormente neste artigo.

### <a name="trigger-an-event"></a>Acionar um evento

Dispare um evento que gerará tráfego HTTP para seu ponto de extremidade ngrok.  Por exemplo, se você criou uma assinatura de armazenamento de BLOBs, carregue ou exclua um blob.

A função de gatilho de grade de eventos é executada e mostra logs semelhantes ao exemplo a seguir:

![Logs de função de gatilho de grade de eventos de exemplo](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Usar um gatilho HTTP como um gatilho de grade de eventos

Eventos de grade de eventos são recebidos como solicitações HTTP, para que você possa manipular eventos usando um gatilho HTTP em vez de um gatilho de grade de eventos. Um motivo possível para fazer isso é obter mais controle sobre a URL do ponto de extremidade que invoca a função. Outro motivo é quando você precisa receber eventos no [esquema CloudEvents](../event-grid/cloudevents-schema.md). Atualmente, o gatilho de grade de eventos não dá suporte ao esquema CloudEvents. Os exemplos nesta seção mostram soluções para o esquema de grade de eventos e o esquema CloudEvents.

Se você usar um gatilho HTTP, precisará escrever código para o que o gatilho de grade de eventos faz automaticamente:

* Envia uma resposta de validação para uma [solicitação de validação de assinatura](../event-grid/security-authentication.md#webhook-event-delivery).
* Invoca a função uma vez por elemento da matriz de eventos contida no corpo da solicitação.

Para obter informações sobre a URL a ser usada para invocar a função localmente ou quando ela é executada no Azure, consulte a [documentação de referência de associação de gatilho http](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Esquema do Event Grid

O código de C# exemplo a seguir para um gatilho http simula o comportamento do gatilho de grade de eventos. Use este exemplo para eventos entregues no esquema da grade de eventos.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.LogInformation("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.LogInformation($"Subject: {eventGridEvent.Subject}");
        log.LogInformation($"Time: {eventGridEvent.EventTime}");
        log.LogInformation($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

O código JavaScript de exemplo a seguir para um gatilho HTTP simula o comportamento do gatilho de grade de eventos. Use este exemplo para eventos entregues no esquema da grade de eventos.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = messages[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        // Event Grid schema delivers events in an array.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Seu código de manipulação de eventos entra dentro do loop por meio da matriz `messages`.

### <a name="cloudevents-schema"></a>Esquema CloudEvents

O código de C# exemplo a seguir para um gatilho http simula o comportamento do gatilho de grade de eventos.  Use este exemplo para eventos entregues no esquema CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

O código JavaScript de exemplo a seguir para um gatilho HTTP simula o comportamento do gatilho de grade de eventos. Use este exemplo para eventos entregues no esquema CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre a grade de eventos](../event-grid/overview.md)
