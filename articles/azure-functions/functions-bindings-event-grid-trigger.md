---
title: Gatilho da grelha de eventos azure para funções azure
description: Aprenda a executar código quando os eventos da Grelha de Eventos em Funções Azure forem enviados.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 2027629e1e9e297c97cbf40485ebe7dc2e3e6c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277729"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Gatilho da grelha de eventos azure para funções azure

Utilize o gatilho de função para responder a um evento enviado a um tópico da Grelha de Eventos.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-event-grid.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

Para um exemplo de gatilho HTTP, consulte [Receber eventos para um ponto final HTTP](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C# (2.x e superior)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que se liga a: `EventGridEvent`

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

Para mais informações, consulte Pacotes, [Atributos,](#attributes-and-annotations) [Configuração](#configuration)e [Utilização.](#usage)

### <a name="version-1x"></a>Versão 1.x

O exemplo seguinte mostra uma [função](functions-dotnet-class-library.md) Funções 1.x C# que se liga a: `JObject`

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação.

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

### <a name="version-2x-and-higher"></a>Versão 2.x e superior

Aqui está um exemplo que `EventGridEvent`se liga a:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Para mais informações, consulte Pacotes, [Atributos,](#attributes-and-annotations) [Configuração](#configuration)e [Utilização.](#usage)

### <a name="version-1x"></a>Versão 1.x

Aqui está as Funções 1.x C# `JObject`código de script que se liga a:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Aqui está o código JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="python"></a>[Pitão](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

Esta secção contém os seguintes exemplos:

* [Gatilho da grelha de eventos, parâmetro de corda](#event-grid-trigger-string-parameter)
* [Gatilho da Grelha de Eventos, parâmetro POJO](#event-grid-trigger-pojo-parameter)

Os seguintes exemplos mostram a ligação do gatilho em [Java](functions-reference-java.md) que `String` usa a encadernação e imprime um evento, recebendo primeiro o evento como um POJO.

### <a name="event-grid-trigger-string-parameter"></a>Gatilho da grelha de eventos, parâmetro de corda

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

### <a name="event-grid-trigger-pojo-parameter"></a>Gatilho da Grelha de Eventos, parâmetro POJO

Este exemplo utiliza o seguinte POJO, representando as propriedades de alto nível de um evento da Grelha de Eventos:

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

À chegada, a carga útil JSON do evento ```EventSchema``` é desserializada no POJO para utilização pela função. Este processo permite que a função aceda às propriedades do evento de forma orientada para o objeto.

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

Na [biblioteca de tempo de funcionamento das funções java,](/java/api/overview/azure/functions/runtime)utilize a `EventGridTrigger` anotação em parâmetros cujo valor viria da EventGrid. Os parâmetros com estas anotações fazem com que a função funcione quando um evento chega.  Esta anotação pode ser usada com tipos nativos de `Optional<T>`Java, POJOs ou valores nuníveis usando .

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o atributo [EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Aqui está `EventGridTrigger` um atributo numa assinatura de método:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte o exemplo de C#.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A anotação [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) permite-lhe configurar declarativamente uma ligação da Grelha de Eventos, fornecendo valores de configuração. Consulte o [exemplo](#example) e as secções de [configuração](#configuration) para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela que se segue explica as propriedades de configuração de ligação que definiu no ficheiro *função.json.* Não existem parâmetros ou propriedades de `EventGridTrigger` construtores para definir no atributo.

|propriedade fun.json |Descrição|
|---------|---------|
| **tipo** | Obrigatório - deve ser `eventGridTrigger`definido para . |
| **direção** | Obrigatório - deve ser `in`definido para . |
| **nome** | Obrigatório - o nome variável utilizado no código de função para o parâmetro que recebe os dados do evento. |

## <a name="usage"></a>Utilização

# <a name="c"></a>[C #](#tab/csharp)

Nas funções Azure 1.x, pode utilizar os seguintes tipos de parâmetros para o gatilho da Grelha de Eventos:

* `JObject`
* `string`

Nas Funções Azure 2.x e superior, também tem a opção de utilizar o seguinte tipo de parâmetro para o gatilho da Grelha de Eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Define propriedades para os campos comuns a todos os tipos de eventos.

> [!NOTE]
> Nas funções v1 se tentar `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`ligar-se, o compilador apresentará uma mensagem "depreciada" e aconselhará a utilizar `Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para utilizar o tipo mais recente, faça referência ao pacote [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet e qualifique totalmente o nome do `EventGridEvent` tipo, prefixando-o com `Microsoft.Azure.EventGrid.Models`.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Nas funções Azure 1.x, pode utilizar os seguintes tipos de parâmetros para o gatilho da Grelha de Eventos:

* `JObject`
* `string`

Nas Funções Azure 2.x e superior, também tem a opção de utilizar o seguinte tipo de parâmetro para o gatilho da Grelha de Eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Define propriedades para os campos comuns a todos os tipos de eventos.

> [!NOTE]
> Nas funções v1 se tentar `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`ligar-se, o compilador apresentará uma mensagem "depreciada" e aconselhará a utilizar `Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para utilizar o tipo mais recente, faça referência ao pacote [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet e qualifique totalmente o nome do `EventGridEvent` tipo, prefixando-o com `Microsoft.Azure.EventGrid.Models`. Para obter informações sobre como referenciar pacotes NuGet numa função de script C#, consulte [utilizar pacotes NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A instância Da Grelha de Eventos está disponível através do `name` parâmetro configurado na propriedade do ficheiro *function.json.*

# <a name="python"></a>[Pitão](#tab/python)

A instância Da Grelha de Eventos está disponível através do `name` parâmetro configurado `func.EventGridEvent`na propriedade do ficheiro *fun.json,* dactilografado como .

# <a name="java"></a>[Java](#tab/java)

A instância de eventos da Grelha de `EventGridTrigger` Eventos está disponível `EventSchema`através do parâmetro associado ao atributo, dactilografado como um . Veja o [exemplo](#example) para mais detalhes.

---

## <a name="event-schema"></a>Esquema de eventos

Os dados de um evento da Grelha de Eventos são recebidos como um objeto JSON no corpo de um pedido HTTP. O JSON é semelhante ao seguinte exemplo:

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

O exemplo mostrado é uma variedade de um elemento. A Grelha de Eventos envia sempre uma matriz e pode enviar mais de um evento na matriz. O tempo de funcionamento invoca a sua função uma vez para cada elemento de matriz.

As propriedades de alto nível no caso de os dados da JSON são os mesmos entre todos os tipos de eventos, enquanto o conteúdo da `data` propriedade é específico de cada tipo de evento. O exemplo mostrado é para um evento de armazenamento de bolhas.

Para obter explicações sobre as propriedades comuns e específicas do evento, consulte [as propriedades](../event-grid/event-schema.md#event-properties) do Evento na documentação da Grelha de Eventos.

O `EventGridEvent` tipo define apenas as propriedades de alto nível; a `Data` propriedade `JObject`é um .

## <a name="create-a-subscription"></a>Criar uma subscrição

Para começar a receber pedidos de Grelha de Eventos HTTP, crie uma subscrição de Rede de Eventos que especifique o URL de ponto final que invoca a função.

### <a name="azure-portal"></a>Portal do Azure

Para funções que desenvolve no portal Azure com o gatilho da Grelha de Eventos, selecione **Adicionar a subscrição da Grelha de Eventos**.

![Criar subscrição no portal](media/functions-bindings-event-grid/portal-sub-create.png)

Ao selecionar este link, o portal abre a página de Subscrição de **Eventos Create** com o URL final pré-preenchido.

![URL de ponto final pré-preenchido](media/functions-bindings-event-grid/endpoint-url.png)

Para obter mais informações sobre como criar subscrições utilizando o portal Azure, consulte [Create evento personalizado - Portal Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Rede de Eventos.

### <a name="azure-cli"></a>CLI do Azure

Para criar uma subscrição utilizando [o Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)utilize o comando de criação de eventos [az eventgrid.](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create)

O comando requer o URL do ponto final que invoca a função. O exemplo que se segue mostra o padrão DE URL específico da versão:

#### <a name="version-2x-and-higher-runtime"></a>Versão 2.x (e maior) tempo de execução

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>Versão 1.x tempo de execução

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

A chave do sistema é uma chave de autorização que tem de ser incluída no URL final para um gatilho da Grelha de Eventos. A secção seguinte explica como obter a chave do sistema.

Aqui está um exemplo que subscreve uma conta de armazenamento blob (com um espaço reservado para a chave do sistema):

#### <a name="version-2x-and-higher-runtime"></a>Versão 2.x (e maior) tempo de execução

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>Versão 1.x tempo de execução

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Para obter mais informações sobre como criar uma subscrição, consulte [o arranque rápido do armazenamento de blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou os outros quickstarts da Rede de Eventos.

### <a name="get-the-system-key"></a>Obtenha a chave do sistema

Pode obter a chave do sistema utilizando a seguinte API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Versão 2.x (e maior) tempo de execução

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Versão 1.x tempo de execução

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Esta é uma API administradora, por isso requer a [chave principal](functions-bindings-http-webhook-trigger.md#authorization-keys)da sua app de funções. Não confunda a chave do sistema (para invocar uma função de gatilho da Grelha de Eventos) com a chave principal (para executar tarefas administrativas na aplicação de função). Quando subscrever um tópico da Grelha de Eventos, certifique-se de utilizar a chave do sistema.

Aqui está um exemplo da resposta que fornece a chave do sistema:

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
> A chave principal proporciona ao administrador acesso à sua aplicação de funções. Não partilhe esta chave com terceiros nem a distribua em aplicações de clientes nativos.

Para mais informações, consulte [as chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys) no artigo de referência do gatilho HTTP.

Em alternativa, pode enviar um HTTP PUT para especificar o valor-chave por si mesmo.

## <a name="local-testing-with-viewer-web-app"></a>Teste local com aplicação web do espectador

Para testar um gatilho da Grelha de Eventos localmente, você tem que obter pedidos de Rede de Eventos HTTP entregues da sua origem na nuvem para a sua máquina local. Uma maneira de o fazer é capturando pedidos on-line e reenviando-os manualmente na sua máquina local:

1. [Crie uma aplicação web de espectadores](#create-a-viewer-web-app) que capture mensagens de eventos.
1. [Crie uma subscrição da Rede de Eventos](#create-an-event-grid-subscription) que envie eventos para a aplicação do espectador.
1. [Gere um pedido](#generate-a-request) e copie o corpo de pedidos da aplicação do espectador.
1. [Publique manualmente o pedido](#manually-post-the-request) no URL local da sua função de gatilho da Grelha de Eventos.

Quando terminar os testes, pode utilizar a mesma subscrição para produção atualizando o ponto final. Utilize a atualização de [subscrição de eventos az eventgrid](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI comando.

### <a name="create-a-viewer-web-app"></a>Criar uma aplicação web do espectador

Para simplificar a captura de mensagens de eventos, pode implementar uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Vê o site, mas ainda não foram publicados eventos no mesmo.

![Ver novo site](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Crie uma subscrição da Grelha de Eventos do tipo que pretende testar e dê-lhe o URL da sua aplicação web como ponto final para a notificação do evento. O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`. Então, o URL completo é`https://<your-site-name>.azurewebsites.net/api/updates`

Para obter informações sobre como criar subscrições utilizando o portal Azure, consulte [Create evento personalizado - Portal Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Rede de Eventos.

### <a name="generate-a-request"></a>Gerar um pedido

Desencadeie um evento que gerará tráfego HTTP para o seu ponto final da aplicação web.  Por exemplo, se criou uma subscrição de armazenamento de bolhas, faça upload ou elimine uma bolha. Quando um pedido aparecer na sua aplicação web, copie o organismo de pedido.

O pedido de validação de subscrição será recebido primeiro; ignorar quaisquer pedidos de validação e copiar o pedido de evento.

![Copy request body from web app](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Postar manualmente o pedido

Execute a sua função de Grelha de Eventos localmente.

Utilize uma ferramenta como [o Carteiro](https://www.getpostman.com/) ou [o Curl](https://curl.haxx.se/docs/httpscripting.html) para criar um pedido HTTP POST:

* Dete `Content-Type: application/json` teto.
* Dete `aeg-event-type: Notification` teto.
* Colar os dados do RequestBin no organismo de pedido.
* Poste no URL da função de gatilho da grelha de eventos.
  * Para 2.x e mais utilize o seguinte padrão:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Para 1.x utilização:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

O `functionName` parâmetro deve ser o nome `FunctionName` especificado no atributo.

As seguintes imagens mostram os cabeçalhos e o corpo de pedido no Carteiro:

![Cabeçalhos no Carteiro](media/functions-bindings-event-grid/postman2.png)

![Solicitar corpo no Carteiro](media/functions-bindings-event-grid/postman.png)

A função de gatilho da Grelha de Eventoexecuta e mostra registos semelhantes ao seguinte exemplo:

![Registos de função de gatilho da grelha de eventos da amostra](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Passos seguintes

* [Despachar um evento da Grelha de Eventos](./functions-bindings-event-grid-trigger.md)
