---
title: Acionador do Event Grid para as funções do Azure
description: Compreenda como manipular eventos do Event Grid nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: 12056ebec0f0a23ed255532a8ba27b130ecb81d1
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750451"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Acionador do Event Grid para as funções do Azure

Este artigo explica como lidar com [Event Grid](../event-grid/overview.md) eventos nas funções do Azure.

Event Grid é um serviço do Azure que envia pedidos HTTP para ser notificado sobre eventos que ocorrem no *os publicadores*. Um publicador é o serviço ou recurso que tem origem do evento. Por exemplo, uma conta de armazenamento de Blobs do Azure é um publicador, e [um carregamento de BLOBs ou eliminação é um evento](../storage/blobs/storage-blob-event-overview.md). Algumas [serviços do Azure inclui suporte interno para publicar eventos para o Event Grid](../event-grid/overview.md#event-sources).

Evento *manipuladores* receber e processar eventos. As funções do Azure é um dos vários [serviços do Azure que têm suporte incorporado para manipulação de eventos do Event Grid](../event-grid/overview.md#event-handlers). Neste artigo, irá aprender a utilizar um acionador do Event Grid para invocar uma função, quando um evento é recebido do Event Grid.

Se preferir, pode utilizar um acionador HTTP para processar eventos do Event Grid ver [utilizar um acionador HTTP como um acionador do Event Grid](#use-an-http-trigger-as-an-event-grid-trigger) mais adiante neste artigo. Atualmente, não é possível utilizar um acionador do Event Grid para uma aplicação de funções do Azure quando o evento é entregue na [esquema do CloudEvents](../event-grid/cloudevents-schema.md). Em alternativa, utilize um acionador HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

O acionador do Event Grid é fornecido na [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) pacote NuGet, versão 2.x. Código-fonte para o pacote está no [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

O acionador do Event Grid é fornecido na [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) pacote NuGet, versão 1.x. Código-fonte para o pacote está no [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) repositório do GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Exemplo

Veja o exemplo de idioma específico para um acionador do Event Grid:

* C#
* [Script do c# (.csx)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

Para obter um exemplo de Acionador HTTP, consulte [como utilizar o acionador HTTP](#use-an-http-trigger-as-an-event-grid-trigger) mais adiante neste artigo.

### <a name="c-2x"></a>C# (2.x)

O exemplo seguinte mostra um funções 2.x [função c#](functions-dotnet-class-library.md) que se vincula ao `EventGridEvent`:

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

Para obter mais informações, consulte pacotes, [atributos](#attributes), [configuração](#configuration), e [utilização](#usage).

### <a name="c-version-1x"></a>C#(Versão 1.x)

O exemplo seguinte mostra um funções 1.x [função c#](functions-dotnet-class-library.md) que se vincula ao `JObject`:

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

### <a name="c-script-example"></a>Exemplo de script do c#

O exemplo seguinte mostra uma ligação de Acionador num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace.

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

#### <a name="c-script-version-2x"></a>C#script (versão 2.x)

Eis o código de script de 2.x c# das funções que liga `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Para obter mais informações, consulte pacotes, [atributos](#attributes), [configuração](#configuration), e [utilização](#usage).

#### <a name="c-script-version-1x"></a>C#script (versão 1.x)

Eis o código de script de 1.x c# das funções que liga `JObject`:

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

O exemplo seguinte mostra uma ligação de Acionador num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace.

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

O exemplo seguinte mostra uma ligação de Acionador num *Function* ficheiro e uma [função Python](functions-reference-python.md) que utiliza o enlace.

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

Aqui está o código de Python:

```python
import logging
import azure.functions as func

def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Acionador - exemplos de Java

Esta secção contém os exemplos seguintes:

* [Acionador do Event Grid, parâmetro de cadeia de caracteres](#event-grid-trigger-string-parameter-java)
* [Acionador do Event Grid, parâmetro POJO](#event-grid-trigger-pojo-parameter-java)

Os exemplos seguintes mostram a ligação de Acionador num *Function* ficheiro e [das funções do Java](functions-reference-java.md) que usar a ligação e imprimir um evento, em primeiro lugar recebe o evento como ```String``` e o segundo como um POJO.

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

#### <a name="event-grid-trigger-string-parameter-java"></a>Acionador do Event Grid, parâmetro de cadeia de caracteres (Java)

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

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Acionador do Event Grid, parâmetro POJO (Java)

Este exemplo utiliza o POJO seguinte, que representa as propriedades de nível superior de um evento do Event Grid:

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

Quando chega, ao payload JSON do evento é desserializada no ```EventSchema``` POJO para utilização pela função. Isso permite que a função para aceder às propriedades do evento de forma orientada a objeto.

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

Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `EventGridTrigger` anotação em parâmetros cujo valor deve ser proveniente de EventGrid. Parâmetros com essas anotações fazer com que a função ser executada quando um evento é recebido.  Esta anotação pode ser utilizada com tipos nativos de Java, POJOs ou valores anuláveis usando `Optional<T>`.

## <a name="attributes"></a>Atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) atributo.

Aqui está um `EventGridTrigger` atributo numa assinatura do método:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte C# exemplo.

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro. Não há parâmetros do construtor ou propriedades para definir o `EventGridTrigger` atributo.

|propriedade de Function |Descrição|
|---------|---------|----------------------|
| **tipo** | Necessário - tem de ser definido como `eventGridTrigger`. |
| **direção** | Necessário - tem de ser definido como `in`. |
| **name** | Necessário - o nome da variável no código de função para o parâmetro que recebe os dados do evento. |

## <a name="usage"></a>Utilização

Para C# e F# as funções no Azure funções 1.x, pode utilizar os seguintes tipos de parâmetro para o acionador do Event Grid:

* `JObject`
* `string`

Para C# e F# as funções nas funções do Azure 2.x, tem também a opção para utilizar o seguinte tipo de parâmetro para o acionador do Event Grid:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Define as propriedades para os campos comuns a todos os tipos de evento.

> [!NOTE]
> No v1 de funções, se tentar fazer a ligação `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, o compilador irá apresentar uma mensagem "preterido" e deve utilizar `Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para utilizar o tipo mais recente, fazer referência a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet empacotar e qualificar completamente a `EventGridEvent` atribuindo-o com o nome do tipo `Microsoft.Azure.EventGrid.Models`. Para obter informações sobre como fazer referência a pacotes de NuGet numa função de script do c#, consulte [pacotes utilizando NuGet](functions-reference-csharp.md#using-nuget-packages)

Para as funções de JavaScript, o parâmetro com o nome da *Function* `name` propriedade tem uma referência ao objeto de evento.

## <a name="event-schema"></a>Esquema de eventos

Dados de um evento do Event Grid são recebidos como um objeto JSON no corpo de uma solicitação HTTP. O JSON é semelhante ao seguinte exemplo:

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

O exemplo mostrado é uma matriz de um elemento. Event Grid sempre envia uma matriz e pode enviar a mais do que um evento na matriz. O tempo de execução chama sua função, uma vez para cada elemento de matriz.

As propriedades de nível superior no evento dados JSON são os mesmos entre todos os tipos de eventos, enquanto o conteúdo do `data` propriedade são específicas para cada tipo de evento. O exemplo mostrado é para um evento de armazenamento de Blobs.

Para obter explicações das propriedades específicas ao evento e comuns, consulte [propriedades de evento](../event-grid/event-schema.md#event-properties) na documentação do Event Grid.

O `EventGridEvent` tipo define apenas as propriedades de nível superior; a `Data` propriedade é um `JObject`.

## <a name="create-a-subscription"></a>Criar uma subscrição

Para começar a receber pedidos de HTTP de grelha de eventos, crie uma subscrição do Event Grid que especifica o URL de ponto final que invoca a função.

### <a name="azure-portal"></a>Portal do Azure

Para as funções que desenvolver no portal do Azure com o acionador do Event Grid, selecione **subscrição do Event Grid adicionar**.

![Criar subscrição no portal](media/functions-bindings-event-grid/portal-sub-create.png)

Quando selecionar esta ligação, o portal abre o **criar subscrição de evento** prefilled de página com o URL de ponto final.

![URL de ponto final prefilled](media/functions-bindings-event-grid/endpoint-url.png)

Para obter mais informações sobre como criar subscrições com o portal do Azure, consulte [criar evento personalizado - portal do Azure](../event-grid/custom-event-quickstart-portal.md) na documentação do Event Grid.

### <a name="azure-cli"></a>CLI do Azure

Para criar uma subscrição, utilizando [a CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), utilize o [criar subscrição de evento do eventgrid az](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) comando.

O comando requer o URL de ponto final que invoca a função. O exemplo seguinte mostra o padrão de URL específicas da versão:

#### <a name="version-2x-runtime"></a>Versão 2.x do runtime

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>O tempo de execução do versão 1.x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

A chave do sistema é uma chave de autorização que deve ser incluído no URL do ponto final para um acionador do Event Grid. A secção seguinte explica como obter a chave de sistema.

Eis um exemplo que se inscreve para uma conta de armazenamento de BLOBs (com um marcador de posição para a chave do sistema):

#### <a name="version-2x-runtime"></a>Versão 2.x do runtime

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>O tempo de execução do versão 1.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Para obter mais informações sobre como criar uma subscrição, veja [o início rápido de armazenamento de BLOBs](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou os outros guias de introdução Event Grid.

### <a name="get-the-system-key"></a>Obter a chave de sistema

Pode obter a chave do sistema utilizando a seguinte API (HTTP GET):

#### <a name="version-2x-runtime"></a>Versão 2.x do runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>O tempo de execução do versão 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Este é um administrador de API, portanto, ele requer que a aplicação de funções [chave mestra](functions-bindings-http-webhook.md#authorization-keys). Não confunda a chave de sistema (para invocar uma função de Acionador do Event Grid) com a chave mestra (para efetuar tarefas administrativas na aplicação de função). Ao subscrever um tópico do Event Grid, certifique-se de que utiliza a chave de sistema.

Eis um exemplo de resposta que fornece a chave de sistema:

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

Pode obter a chave mestra para a aplicação de função a partir da **as definições da aplicação de função** separador no portal do.

> [!IMPORTANT]
> A chave mestra fornece acesso de administrador à sua aplicação de função. Não partilhe esta chave com terceiros ou distribuí-la em aplicativos de cliente nativo.

Para obter mais informações, consulte [chaves de autorização](functions-bindings-http-webhook.md#authorization-keys) no artigo de referência do acionador HTTP.

Em alternativa, pode enviar um HTTP PUT para especificar o valor da chave por conta própria.

## <a name="local-testing-with-viewer-web-app"></a>Realização de testes locais com a aplicação de web do Visualizador

Para testar um acionador do Event Grid localmente, terá de obter pedidos de HTTP de grelha de eventos, entregues a partir da sua origem na cloud no seu computador local. Uma forma de fazê-lo é ao capturar pedidos online e manualmente reenviá-los no seu computador local:

2. [Criar uma aplicação web do Visualizador](#create-a-viewer-web-app) que captura a mensagens de eventos.
3. [Criar uma subscrição do Event Grid](#create-an-event-grid-subscription) que envia eventos para a aplicação de Visualizador.
4. [Gerar um pedido](#generate-a-request) e copie o corpo do pedido a partir da aplicação de Visualizador.
5. [Publique manualmente o pedido](#manually-post-the-request) para o URL de localhost do seu Event Grid acionar a função.

Quando terminar de teste, pode utilizar a mesma subscrição para produção, atualizando o ponto final. Utilize o [atualização de subscrição de evento de eventgrid az](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) comando da CLI do Azure.

### <a name="create-a-viewer-web-app"></a>Criar uma aplicação web do Visualizador

Para simplificar a captura de mensagens de eventos, pode implementar um [aplicação web pré-criados](https://github.com/Azure-Samples/azure-event-grid-viewer) que apresenta as mensagens de evento. A solução implementada inclui um plano do Serviço de Aplicações, uma aplicação Web do Serviço de Aplicações e o código de origem do GitHub.

Selecione **Implementar no Azure** para implementar a solução para a sua subscrição. No portal do Azure, indique os valores para os parâmetros.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

A implementação pode demorar alguns minutos. Após a implementação ter sido concluída com êxito, verifique a aplicação Web para verificar se está em execução. Num browser, navegue para: `https://<your-site-name>.azurewebsites.net`

Vê o site, mas ainda não foram publicados eventos no mesmo.

![Ver novo site](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

Criar uma subscrição do Event Grid do tipo que pretende testar e dê a ele o URL da sua aplicação web como o ponto final para notificação de eventos. O ponto final para a aplicação Web tem de incluir o sufixo `/api/updates/`. Por isso, é o URL completo `https://<your-site-name>.azurewebsites.net/api/updates`

Para obter informações sobre como criar subscrições com o portal do Azure, consulte [criar evento personalizado - portal do Azure](../event-grid/custom-event-quickstart-portal.md) na documentação do Event Grid.

### <a name="generate-a-request"></a>Gerar um pedido

Acione um evento que irá gerar tráfego HTTP para o ponto de final de aplicação web.  Por exemplo, se tiver criado uma subscrição de armazenamento de BLOBs, carregar ou eliminar um blob. Quando um pedido aparece na sua aplicação web, copie o corpo do pedido.

O pedido de validação de subscrição será recebido pela primeira vez; ignorar quaisquer pedidos de validação e copie o pedido de evento.

![Copiar o corpo do pedido de aplicação web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Publique manualmente o pedido

Execute localmente a função do Event Grid.

Utilize uma ferramenta como [Postman](https://www.getpostman.com/) ou [curl](https://curl.haxx.se/docs/httpscripting.html) para criar um pedido HTTP POST:

* Definir um `Content-Type: application/json` cabeçalho.
* Definir um `aeg-event-type: Notification` cabeçalho.
* Cole os dados de RequestBin no corpo do pedido.
* Publicar o URL da sua função de Acionador do Event Grid.
  * Para 2.x, utilize o seguinte padrão:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Para utilização 1.x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

O `functionName` parâmetro tem de ser o nome especificado no `FunctionName` atributo.

As capturas de ecrã seguintes mostram os cabeçalhos e corpo no Postman do pedido:

![Cabeçalhos no Postman](media/functions-bindings-event-grid/postman2.png)

![Corpo do pedido no Postman](media/functions-bindings-event-grid/postman.png)

A função de Acionador do Event Grid é executado e mostra os registos semelhantes ao seguinte exemplo:

![Registos de função de Acionador de grelha de eventos de exemplo](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Realização de testes com ngrok locais

É outra forma de testar localmente um acionador do Event Grid automatizar a ligação de HTTP entre a Internet e o seu computador de desenvolvimento. Pode fazê-lo com uma ferramenta open-source denominada [ngrok](https://ngrok.com/):

3. [Criar um ponto de extremidade ngrok](#create-an-ngrok-endpoint).
4. [Para executar a função de Acionador do Event Grid](#run-the-event-grid-trigger-function).
5. [Criar uma subscrição do Event Grid](#create-a-subscription) que envia eventos para o ponto de extremidade ngrok.
6. [Acionar um evento](#trigger-an-event).

Quando terminar de teste, pode utilizar a mesma subscrição para produção, atualizando o ponto final. Utilize o [atualização de subscrição de evento de eventgrid az](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) comando da CLI do Azure.

### <a name="create-an-ngrok-endpoint"></a>Criar um ponto de extremidade ngrok

Baixe *ngrok.exe* partir [ngrok](https://ngrok.com/)e executar com o seguinte comando:

```
ngrok http -host-header=localhost 7071
```

O - host-parâmetro de cabeçalho é necessária porque o runtime das funções espera que as solicitações do localhost quando é executada no localhost. 7071 é o número de porta predefinido quando o tempo de execução é executada localmente.

O comando cria uma saída semelhante ao seguinte:

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

Usará o `https://{subdomain}.ngrok.io` URL para a sua subscrição do Event Grid.

### <a name="run-the-event-grid-trigger-function"></a>Executar a função de Acionador do Event Grid

O URL de ngrok não obtém um tratamento especial pelo Event Grid, para que a sua função tem de ser executado localmente quando é criada a subscrição. Caso contrário, a resposta de validação não é enviada e a criação de subscrição falhar.

### <a name="create-a-subscription"></a>Criar uma subscrição

Criar uma subscrição do Event Grid do tipo que pretende testar e atribua o ponto final de ngrok.

Utilize este padrão de ponto final para as funções 2.x:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Utilize este padrão de ponto final para as funções 1.x:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

O `{FUNCTION_NAME}` parâmetro tem de ser o nome especificado no `FunctionName` atributo.

Eis um exemplo com a CLI do Azure:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

Para obter informações sobre como criar uma subscrição, veja [criar uma subscrição](#create-a-subscription) no início deste artigo.

### <a name="trigger-an-event"></a>Acionar um evento

Acione um evento que irá gerar tráfego HTTP para o ponto final de ngrok.  Por exemplo, se tiver criado uma subscrição de armazenamento de BLOBs, carregar ou eliminar um blob.

A função de Acionador do Event Grid é executado e mostra os registos semelhantes ao seguinte exemplo:

![Registos de função de Acionador de grelha de eventos de exemplo](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Utilizar um acionador HTTP como um acionador do Event Grid

Eventos do Event Grid são recebidos como pedidos HTTP, pelo que pode processar eventos com um acionador HTTP em vez de um acionador do Event Grid. Uma razão possível para se fazer isso é obter mais controle sobre o URL de ponto final que invoca a função. Outro motivo é quando precisa receber eventos no [esquema do CloudEvents](../event-grid/cloudevents-schema.md). Atualmente, o acionador do Event Grid não suporta o esquema do CloudEvents. Os exemplos nesta secção mostram soluções para o esquema do Event Grid e o esquema do CloudEvents.

Se utilizar um acionador HTTP, precisa escrever código para o que o acionador do Event Grid faz automaticamente:

* Envia uma resposta de validação para uma [pedido de validação de subscrição](../event-grid/security-authentication.md#webhook-event-delivery).
* Invoca a função, uma vez por elemento da matriz de eventos contido no corpo do pedido.

Para obter informações sobre o URL a utilizar para invocar a função localmente ou quando é executada no Azure, consulte o [documentação de referência de ligação de Acionador HTTP](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Esquema do Event Grid

O seguinte exemplo código c# para um acionador HTTP simula o comportamento de Acionador do Event Grid. Utilize este exemplo para eventos de evento fornecidos esquema de grelha.

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

O seguinte código de JavaScript de exemplo para um acionador HTTP simula o comportamento de Acionador do Event Grid. Utilize este exemplo para eventos de evento fornecidos esquema de grelha.

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

O código de manipulação de eventos vai dentro do loop por meio do `messages` matriz.

### <a name="cloudevents-schema"></a>Esquema do CloudEvents

O seguinte exemplo código c# para um acionador HTTP simula o comportamento de Acionador do Event Grid.  Utilize este exemplo para eventos fornecidos no esquema do CloudEvents.

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

O seguinte código de JavaScript de exemplo para um acionador HTTP simula o comportamento de Acionador do Event Grid. Utilize este exemplo para eventos fornecidos no esquema do CloudEvents.

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre o Event Grid](../event-grid/overview.md)
