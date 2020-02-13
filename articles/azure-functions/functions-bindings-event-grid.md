---
title: Gatilho de grade de eventos para Azure Functions
description: Entenda como lidar com eventos de grade de eventos no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: 812875be47cabdd23e6307403bb95d8d6ff174ec
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167512"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Gatilho de grade de eventos para Azure Functions

Este artigo explica como lidar com eventos da Grelha de [Eventos](../event-grid/overview.md) em Funções Azure. Para mais detalhes sobre como lidar com as mensagens da Grelha de Eventos num ponto final http, leia [Receber eventos num ponto final http](../event-grid/receive-events.md).

Event Grid é um serviço Azure que envia pedidos http para notificá-lo sobre eventos que acontecem em *editores*. Um Publicador é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de blob Azure é uma editora, e [um upload ou eliminação blob é um evento](../storage/blobs/storage-blob-event-overview.md). Alguns [serviços azure têm apoio integrado para a publicação de eventos à Event Grid.](../event-grid/overview.md#event-sources)

Os *manipuladores de eventos* recebem e processam eventos. A Azure Functions é um dos vários [serviços Azure que têm suporte integrado para lidar com eventos da Rede de Eventos.](../event-grid/overview.md#event-handlers) Neste artigo, você aprenderá a usar um gatilho de grade de eventos para invocar uma função quando um evento for recebido da grade de eventos.

Se preferir, pode utilizar um gatilho HTTP para lidar com eventos da Grelha de Eventos; ver [Receber eventos para um ponto final HTTP](../event-grid/receive-events.md). Atualmente, não é possível utilizar um gatilho da Grelha de Eventos para uma aplicação De Funções Azure quando o evento é entregue no [esquema CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Em vez disso, use um gatilho HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

O gatilho da Grelha de Eventos é fornecido no pacote [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet, versão 2.x. O código fonte para a embalagem está no repositório GitHub de extensão de [funções azure-funções.](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

O gatilho da Grelha de Eventos é fornecido no pacote [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet, versão 1.x. O código fonte para a embalagem está no repositório GitHub de extensão de [funções azure-funções.](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para um exemplo de gatilho HTTP, consulte [Receber eventos para um ponto final HTTP](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C#(2. x e superior)

O exemplo que se segue mostra uma [ C# função](functions-dotnet-class-library.md) que se liga a `EventGridEvent`:

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

Para mais informações, consulte Pacotes, [Atributos,](#attributes) [Configuração](#configuration)e [Utilização.](#usage)

### <a name="version-1x"></a>Versão 1.x

O exemplo seguinte mostra uma [ C# função Funções](functions-dotnet-class-library.md) 1.x que se liga a `JObject`:

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

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

Aqui está um exemplo que se liga a `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Para mais informações, consulte Pacotes, [Atributos,](#attributes) [Configuração](#configuration)e [Utilização.](#usage)

### <a name="version-1x"></a>Versão 1.x

Aqui está o código C# de script Functions 1.x que se liga a `JObject`:

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

O exemplo seguinte mostra uma ligação do gatilho num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

# <a name="pythontabpython"></a>[python](#tab/python)

O exemplo seguinte mostra uma ligação do gatilho num ficheiro *function.json* e uma [função Python](functions-reference-python.md) que utiliza a ligação.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

Aqui está o código Python:

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

* [Gatilho da grelha de eventos, parâmetro de corda](#event-grid-trigger-string-parameter)
* [Gatilho da Grelha de Eventos, parâmetro POJO](#event-grid-trigger-pojo-parameter)

Os seguintes exemplos mostram a ligação do gatilho em [Java](functions-reference-java.md) que usa a encadernação e imprime um evento, recebendo primeiro o evento como `String` e segundo como UM POJO.

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

À chegada, a carga útil JSON do evento é desserializada no ```EventSchema``` POJO para utilização pela função. Isso permite que a função acesse as propriedades do evento de forma orientada a objeto.

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

Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a anotação `EventGridTrigger` em parâmetros cujo valor viria da EventGrid. Os parâmetros com estas anotações fazem com que a função funcione quando um evento chega.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores nuníveis usando `Optional<T>`.

---

## <a name="attributes"></a>Atributos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o atributo [EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Aqui está um atributo `EventGridTrigger` numa assinatura de método:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, C# consulte exemplo.

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) permite-lhe configurar declarativamente uma ligação da Grelha de Eventos, fornecendo valores de configuração. Consulte o [exemplo](#example) e as secções de [configuração](#configuration) para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela que se segue explica as propriedades de configuração de ligação que definiu no ficheiro *função.json.* Não existem parâmetros ou propriedades de construtores para definir no atributo `EventGridTrigger`.

|propriedade de Function |Descrição|
|---------|---------|
| **tipo** | Obrigatório - deve ser definido para `eventGridTrigger`. |
| **direção** | Obrigatório - deve ser definido para `in`. |
| **nome** | Obrigatório – o nome da variável usada no código de função para o parâmetro que recebe os dados do evento. |

## <a name="usage"></a>Utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No Azure Functions 1. x, você pode usar os seguintes tipos de parâmetro para o gatilho de grade de eventos:

* `JObject`
* `string`

No Azure Functions 2. x e superior, você também tem a opção de usar o seguinte tipo de parâmetro para o gatilho de grade de eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Define propriedades para os campos comuns a todos os tipos de eventos.

> [!NOTE]
> Nas funções v1 se tentar ligar-se a `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, o compilador apresentará uma mensagem "depreciada" e aconselhará a utilizá`Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para utilizar o tipo mais recente, faça referência ao pacote [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet e qualifique totalmente o nome do tipo `EventGridEvent`, prefixando-o com `Microsoft.Azure.EventGrid.Models`.

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

No Azure Functions 1. x, você pode usar os seguintes tipos de parâmetro para o gatilho de grade de eventos:

* `JObject`
* `string`

No Azure Functions 2. x e superior, você também tem a opção de usar o seguinte tipo de parâmetro para o gatilho de grade de eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Define propriedades para os campos comuns a todos os tipos de eventos.

> [!NOTE]
> Nas funções v1 se tentar ligar-se a `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, o compilador apresentará uma mensagem "depreciada" e aconselhará a utilizá`Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para utilizar o tipo mais recente, faça referência ao pacote [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet e qualifique totalmente o nome do tipo `EventGridEvent`, prefixando-o com `Microsoft.Azure.EventGrid.Models`. Para obter informações sobre como referenciar pacotes NuGet numa C# função de script, consulte utilizar [pacotes NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A instância Da Grelha de Eventos está disponível através do parâmetro configurado na propriedade `name` do ficheiro *function.json.*

# <a name="pythontabpython"></a>[python](#tab/python)

A instância Da Grelha de Eventos está disponível através do parâmetro configurado na propriedade `name` do ficheiro *fun.json,* dactilografada como `func.EventGridEvent`.

# <a name="javatabjava"></a>[Java](#tab/java)

A instância de eventos da Grelha de Eventos está disponível através do parâmetro associado ao atributo `EventGridTrigger`, dactilografado como `EventSchema`. Veja o [exemplo](#example) para mais detalhes.

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

As propriedades de alto nível no caso de os dados da JSON são os mesmos entre todos os tipos de eventos, enquanto o conteúdo da propriedade `data` é específico de cada tipo de evento. O exemplo mostrado é para um evento de armazenamento de BLOBs.

Para obter explicações sobre as propriedades comuns e específicas do evento, consulte [as propriedades](../event-grid/event-schema.md#event-properties) do Evento na documentação da Grelha de Eventos.

O tipo `EventGridEvent` define apenas as propriedades de alto nível; a propriedade `Data` é uma `JObject`.

## <a name="create-a-subscription"></a>Criar uma subscrição

Para começar a receber solicitações HTTP da grade de eventos, crie uma assinatura da grade de eventos que especifica a URL do ponto de extremidade que invoca a função.

### <a name="azure-portal"></a>Portal do Azure

Para funções que desenvolve no portal Azure com o gatilho da Grelha de Eventos, selecione **Adicionar a subscrição da Grelha de Eventos**.

![Criar assinatura no portal](media/functions-bindings-event-grid/portal-sub-create.png)

Ao selecionar este link, o portal abre a página de Subscrição de **Eventos Create** com o URL final pré-preenchido.

![URL de ponto de extremidade preenchida](media/functions-bindings-event-grid/endpoint-url.png)

Para obter mais informações sobre como criar subscrições utilizando o portal Azure, consulte [Create evento personalizado - Portal Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Rede de Eventos.

### <a name="azure-cli"></a>CLI do Azure

Para criar uma subscrição utilizando [o Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)utilize o comando de criação de eventos [az eventgrid.](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create)

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

Para obter mais informações sobre como criar uma subscrição, consulte [o arranque rápido do armazenamento de blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou os outros quickstarts da Rede de Eventos.

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

Esta é uma API administradora, por isso requer a [chave principal](functions-bindings-http-webhook.md#authorization-keys)da sua app de funções. Não confunda a chave do sistema (para invocar uma função de gatilho de grade de eventos) com a chave mestra (para executar tarefas administrativas no aplicativo de funções). Ao assinar um tópico da grade de eventos, certifique-se de usar a chave do sistema.

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

Pode obter a chave principal para a sua aplicação de função a partir do separador de definições da **aplicação Função** no portal.

> [!IMPORTANT]
> A chave mestra fornece acesso de administrador ao seu aplicativo de funções. Não compartilhe essa chave com terceiros ou distribua-a em aplicativos cliente nativos.

Para mais informações, consulte [as chaves de autorização](functions-bindings-http-webhook.md#authorization-keys) no artigo de referência do gatilho HTTP.

Como alternativa, você pode enviar um HTTP PUT para especificar o valor da chave por conta própria.

## <a name="local-testing-with-viewer-web-app"></a>Teste local com o aplicativo Web do Visualizador

Para testar um gatilho de grade de eventos localmente, você precisa obter solicitações HTTP da grade de eventos entregues de sua origem na nuvem para seu computador local. Uma maneira de fazer isso é capturando solicitações online e reenviá-las manualmente no computador local:

1. [Crie uma aplicação web de espectadores](#create-a-viewer-web-app) que capture mensagens de eventos.
1. [Crie uma subscrição da Rede de Eventos](#create-an-event-grid-subscription) que envie eventos para a aplicação do espectador.
1. [Gere um pedido](#generate-a-request) e copie o corpo de pedidos da aplicação do espectador.
1. [Publique manualmente o pedido](#manually-post-the-request) no URL local da sua função de gatilho da Grelha de Eventos.

Quando terminar o teste, você poderá usar a mesma assinatura para produção atualizando o ponto de extremidade. Utilize a atualização de [subscrição de eventos az eventgrid](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI comando.

### <a name="create-a-viewer-web-app"></a>Criar um aplicativo Web do Visualizador

Para simplificar a captura de mensagens de eventos, pode implementar uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Vê o site, mas ainda não foram publicados eventos no mesmo.

![Ver novo site](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Crie uma assinatura de grade de eventos do tipo que você deseja testar e dê a ela a URL do seu aplicativo Web como o ponto de extremidade para notificação de eventos. O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`. Então, a URL completa é `https://<your-site-name>.azurewebsites.net/api/updates`

Para obter informações sobre como criar subscrições utilizando o portal Azure, consulte [Create evento personalizado - Portal Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Rede de Eventos.

### <a name="generate-a-request"></a>Gerar uma solicitação

Dispare um evento que gerará tráfego HTTP para seu ponto de extremidade do aplicativo Web.  Por exemplo, se você criou uma assinatura de armazenamento de BLOBs, carregue ou exclua um blob. Quando uma solicitação aparecer em seu aplicativo Web, copie o corpo da solicitação.

A solicitação de validação de assinatura será recebida primeiro; Ignore todas as solicitações de validação e copie a solicitação de evento.

![Copiar corpo da solicitação do aplicativo Web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Postar a solicitação manualmente

Execute a função de grade de eventos localmente.

Utilize uma ferramenta como [o Carteiro](https://www.getpostman.com/) ou [o Curl](https://curl.haxx.se/docs/httpscripting.html) para criar um pedido HTTP POST:

* Dete teto de `Content-Type: application/json` cabeçada.
* Dete teto `aeg-event-type: Notification` cabeçada.
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
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre a Grelha de Eventos](../event-grid/overview.md)
