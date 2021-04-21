---
title: Gatilho da grelha de eventos Azure para funções Azure
description: Aprenda a executar código quando os eventos de Grade de Eventos em Funções Azure forem despachados.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 17968f2c137eef51eecdb6c7098c7056944dc970
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782194"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Gatilho da grelha de eventos Azure para funções Azure

Utilize o gatilho de função para responder a um evento enviado para um tópico de Grade de Eventos.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-event-grid.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

Para obter um exemplo de gatilho HTTP, consulte [receber eventos num ponto final HTTP](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C# (2.x e mais alto)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que se liga `EventGridEvent` a:

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

Para obter mais informações, consulte Pacotes, [Atributos,](#attributes-and-annotations) [Configuração](#configuration)e [Utilização](#usage).

### <a name="version-1x"></a>Versão 1.x

O exemplo a seguir mostra uma função Funções 1.x [C#](functions-dotnet-class-library.md) que se liga `JObject` a:

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

O exemplo a seguir mostra uma ligação do gatilho numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

Aqui está um exemplo que se liga `EventGridEvent` a:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Para obter mais informações, consulte Pacotes, [Atributos,](#attributes-and-annotations) [Configuração](#configuration)e [Utilização](#usage).

### <a name="version-1x"></a>Versão 1.x

Aqui está o código de script Funções 1.x C# que se liga `JObject` a:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="java"></a>[Java](#tab/java)

Esta secção contém os seguintes exemplos:

* [Gatilho da grelha de evento, parâmetro de corda](#event-grid-trigger-string-parameter)
* [Gatilho da grelha de eventos, parâmetro POJO](#event-grid-trigger-pojo-parameter)

Os exemplos que se seguem mostram a ligação do gatilho em [Java](functions-reference-java.md) que usam a encadernação e imprimem um evento, recebendo primeiro o evento como `String` e segundo como um POJO.

### <a name="event-grid-trigger-string-parameter"></a>Gatilho da grelha de evento, parâmetro de corda

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

### <a name="event-grid-trigger-pojo-parameter"></a>Gatilho da grelha de eventos, parâmetro POJO

Este exemplo utiliza o seguinte POJO, representando as propriedades de nível superior de um evento de Grade de Eventos:

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

À chegada, a carga útil JSON do evento é des-serializada no ```EventSchema``` POJO para utilização pela função. Este processo permite que a função aceda às propriedades do evento de forma orientada para o objeto.

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

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `EventGridTrigger` anotação em parâmetros cujo valor viria do EventGrid. Parâmetros com estas anotações fazem com que a função funcione quando um evento chega.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra como configurar uma ligação de gatilho de rede de eventos no *function.jsficheiro.*

```powershell
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

O evento Event Grid é disponibilizado à função através de um parâmetro denominado `eventGridEvent` , como mostrado no exemplo powerShell seguinte.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação do gatilho numa *function.jsem* ficheiro e uma [função Python](functions-reference-python.md) que utiliza a ligação.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Aqui está um `EventGridTrigger` atributo numa assinatura de método:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte o exemplo C#.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

A anotação [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) permite-lhe configurar declarativamente uma ligação de Grade de Eventos, fornecendo valores de configuração. Consulte as secções [de exemplo](#example) e [configuração](#configuration) para obter mais detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsficheiro.* Não existem parâmetros ou propriedades de construtores para definir no `EventGridTrigger` atributo.

|function.jsna propriedade |Description|
|---------|---------|
| **tipo** | Necessário - deve ser definido para `eventGridTrigger` . |
| **direção** | Necessário - deve ser definido para `in` . |
| **nome** | Requerido - o nome variável utilizado no código de função para o parâmetro que recebe os dados do evento. |

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Nas Funções Azure 1.x, pode utilizar os seguintes tipos de parâmetros para o gatilho da grelha de eventos:

* `JObject`
* `string`

Nas Funções Azure 2.x e superior, também tem a opção de utilizar o seguinte tipo de parâmetro para o gatilho da Grelha de Eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Define propriedades para os campos comuns a todos os tipos de eventos.

> [!NOTE]
> Em Funções v1 se tentar ligar-se, `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` o compilador apresentará uma mensagem "depreciada" e aconselhará-o a `Microsoft.Azure.EventGrid.Models.EventGridEvent` utilizar. Para utilizar o tipo mais recente, consulte o pacote [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet e qualifique totalmente o nome do `EventGridEvent` tipo, pré-fixando-o com `Microsoft.Azure.EventGrid.Models` .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Nas Funções Azure 1.x, pode utilizar os seguintes tipos de parâmetros para o gatilho da grelha de eventos:

* `JObject`
* `string`

Nas Funções Azure 2.x e superior, também tem a opção de utilizar o seguinte tipo de parâmetro para o gatilho da Grelha de Eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Define propriedades para os campos comuns a todos os tipos de eventos.

> [!NOTE]
> Em Funções v1 se tentar ligar-se, `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` o compilador apresentará uma mensagem "depreciada" e aconselhará-o a `Microsoft.Azure.EventGrid.Models.EventGridEvent` utilizar. Para utilizar o tipo mais recente, consulte o pacote [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet e qualifique totalmente o nome do `EventGridEvent` tipo, pré-fixando-o com `Microsoft.Azure.EventGrid.Models` . Para obter informações sobre como referenciar pacotes NuGet numa função de script C#, consulte [usando pacotes NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="java"></a>[Java](#tab/java)

A instância do evento Event Grid está disponível através do parâmetro associado ao `EventGridTrigger` atributo, dactilografado como `EventSchema` um . Veja o [exemplo](#example) para mais detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A instância 'Grade de Evento' está disponível através do parâmetro configurado no *function.jsna* propriedade do `name` ficheiro.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A instância 'Grade de Evento' está disponível através do parâmetro configurado no *function.jsna* propriedade do `name` ficheiro.

# <a name="python"></a>[Python](#tab/python)

A instância da Grade de Eventos está disponível através do parâmetro configurado no *function.jsna* propriedade do `name` ficheiro, dactilografado como `func.EventGridEvent` .

---

## <a name="event-schema"></a>Esquema de eventos

Os dados para um evento de Grade de Eventos são recebidos como um objeto JSON no corpo de um pedido HTTP. O JSON é semelhante ao seguinte exemplo:

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

O exemplo mostrado é uma matriz de um elemento. A Grade de Eventos envia sempre uma matriz e pode enviar mais do que um evento na matriz. O tempo de execução invoca a sua função uma vez para cada elemento de matriz.

As propriedades de alto nível no caso de os dados JSON serem os mesmos entre todos os tipos de eventos, enquanto o conteúdo da propriedade é específico de cada tipo de `data` evento. O exemplo mostrado é para um evento de armazenamento de bolhas.

Para obter explicações sobre as propriedades comuns e específicas do evento, consulte [as propriedades do Evento](../event-grid/event-schema.md#event-properties) na documentação da Grade de Eventos.

O `EventGridEvent` tipo define apenas as propriedades de nível superior; a `Data` propriedade é um `JObject` .

## <a name="create-a-subscription"></a>Criar uma subscrição

Para começar a receber pedidos http da Grelha de Eventos, crie uma subscrição de Grade de Eventos que especifique o URL do ponto final que invoca a função.

### <a name="azure-portal"></a>Portal do Azure

Para as funções que desenvolve no portal Azure com o gatilho da Grelha de Eventos, selecione **Integração,** em seguida, escolha o **Gatilho da Grelha de Eventos** e selecione Criar a **subscrição de 'Criar Grelha de Eventos'.**

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Ligue uma nova subscrição de eventos para desencadear no portal.":::

Quando seleciona este link, o portal abre a página **De Subscrição de Eventos** criar com o ponto final do gatilho atual já definido.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="Criar subscrição de evento com ponto final de função já definido" :::

Para obter mais informações sobre como criar subscrições utilizando o portal Azure, consulte [Criar evento personalizado - Portal Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Grade de Eventos.

### <a name="azure-cli"></a>CLI do Azure

Para criar uma subscrição utilizando [o Azure CLI,](/cli/azure/get-started-with-azure-cli)utilize o comando [de criação de eventos az eventgrid.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)

O comando requer o URL do ponto final que invoca a função. O exemplo a seguir mostra o padrão de URL específico da versão:

#### <a name="version-2x-and-higher-runtime"></a>Versão 2.x (e superior) tempo de execução

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>Versão 1.x tempo de execução

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

A chave do sistema é uma chave de autorização que tem de ser incluída no URL do ponto final para um gatilho de Grade de Eventos. A secção seguinte explica como obter a chave do sistema.

Aqui está um exemplo que subscreve uma conta de armazenamento blob (com um espaço reservado para a chave do sistema):

#### <a name="version-2x-and-higher-runtime"></a>Versão 2.x (e superior) tempo de execução

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>Versão 1.x tempo de execução

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

Para obter mais informações sobre como criar uma subscrição, consulte [o quickstart de armazenamento de bolhas](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou os outros quickstarts da Grade de Eventos.

### <a name="get-the-system-key"></a>Obtenha a chave do sistema

Pode obter a chave do sistema utilizando a seguinte API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Versão 2.x (e superior) tempo de execução

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Versão 1.x tempo de execução

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Esta é uma API administradora, por isso requer a sua [chave principal](functions-bindings-http-webhook-trigger.md#authorization-keys)de aplicação de função. Não confunda a chave do sistema (para invocar uma função de gatilho de Grade de Evento) com a tecla principal (para executar tarefas administrativas na aplicação de função). Quando subscrever um tópico de Grade de Eventos, certifique-se de que utiliza a chave do sistema.

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

Pode obter a chave principal para a sua aplicação de função a partir do separador de definições de **aplicações 'Função'** no portal.

> [!IMPORTANT]
> A chave principal proporciona ao administrador acesso à sua aplicação de função. Não partilhe esta chave com terceiros nem distribua-a em aplicações de clientes nativos.

Para obter mais informações, consulte [as teclas de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys) no artigo de referência do gatilho HTTP.

Em alternativa, pode enviar um HTTP PUT para especificar o valor da chave.

## <a name="local-testing-with-viewer-web-app"></a>Teste local com aplicação web de espectadores

Para testar um gatilho de Grade de Eventos localmente, tem de obter pedidos HTTP da Grelha de Eventos entregues da sua origem na nuvem para a sua máquina local. Uma maneira de o fazer é capturar os pedidos on-line e reencando-os manualmente na sua máquina local:

1. [Crie uma aplicação web de espectadores](#create-a-viewer-web-app) que capture mensagens de evento.
1. [Crie uma subscrição de Grade de Eventos](#create-an-event-grid-subscription) que envie eventos para a aplicação do espectador.
1. [Gere um pedido](#generate-a-request) e copie o corpo de pedido da aplicação do espectador.
1. [Publique manualmente o pedido](#manually-post-the-request) para o URL local da sua função de gatilho de Grelha de Evento.

Quando terminar os testes, pode utilizar a mesma subscrição para produção atualizando o ponto final. Utilize o comando Azure CLI [da atualização de subscrição de eventos](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update) Azure.

### <a name="create-a-viewer-web-app"></a>Criar uma aplicação web de espectadores

Para simplificar a captura de mensagens de evento, pode implementar uma [aplicação web pré-construída](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens do evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Vê o site, mas ainda não foram publicados eventos no mesmo.

![Ver novo site](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Crie uma subscrição de Grade de Eventos do tipo que pretende testar e dê-lhe o URL da sua aplicação web como ponto final para notificação de eventos. O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`. Então, a URL completa é `https://<your-site-name>.azurewebsites.net/api/updates`

Para obter informações sobre como criar subscrições utilizando o portal Azure, consulte [Criar evento personalizado - Portal Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Grade de Eventos.

### <a name="generate-a-request"></a>Gerar um pedido

Desencadeie um evento que irá gerar tráfego HTTP para o ponto final da sua aplicação web.  Por exemplo, se criou uma subscrição de armazenamento de bolhas, faça upload ou elimine uma bolha. Quando um pedido aparece na sua aplicação web, copie o corpo do pedido.

O pedido de validação de subscrição será recebido primeiro; ignorar quaisquer pedidos de validação e copiar o pedido do evento.

![Copiar corpo de pedido a partir de aplicação web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Poste manualmente o pedido

Executar a sua função de Grelha de Evento local. Os `Content-Type` `aeg-event-type` cabeçalhos e os cabeçalhos são necessários para serem definidos manualmente, enquanto todos os outros valores podem ser deixados como padrão.

Utilize uma ferramenta como [o Carteiro](https://www.getpostman.com/) ou [o curl](https://curl.haxx.se/docs/httpscripting.html) para criar um pedido HTTP POST:

* Desemote um `Content-Type: application/json` cabeçalho.
* Desemote um `aeg-event-type: Notification` cabeçalho.
* Cole os dados RequestBin no organismo de pedido.
* Publique no URL da função de gatilho da grelha de evento.
  * Para uma utilização 2.x e superior, o seguinte padrão:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Para utilização de 1.x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

O `functionName` parâmetro deve ser o nome especificado no `FunctionName` atributo.

As imagens que se seguem mostram os cabeçalhos e o corpo de pedido no Carteiro:

![Cabeçalhos no Carteiro](media/functions-bindings-event-grid/postman2.png)

![Pedido de corpo no Carteiro](media/functions-bindings-event-grid/postman.png)

A função de gatilho de 'Grade de Evento' executa e mostra registos semelhantes ao seguinte exemplo:

![Registos de função de gatilho da grelha de evento de amostra](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Passos seguintes

* [Despachar um evento de Grade de Eventos](./functions-bindings-event-grid-output.md)
