---
title: Ingerir telemetria do Hub IoT
titleSuffix: Azure Digital Twins
description: Veja como ingerir mensagens de telemetria do dispositivo a partir do IoT Hub.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 4d2b849df7371815a120e59cfff3d8bc7596b1ed
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725856"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingesteia IoT Hub em Azure Digital Twins

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

A Azure Digital Twins é conduzida com dados de dispositivos IoT e outras fontes. Uma fonte comum para os dados do dispositivo a utilizar em Azure Digital Twins é [o IoT Hub](../iot-hub/about-iot-hub.md).

Durante a pré-visualização, o processo de ingerir dados em Azure Digital Twins é criar um recurso de computação externa, como uma [função Azure](../azure-functions/functions-overview.md), que recebe os dados e utiliza as [APIs digitalTwins](how-to-use-apis-sdks.md) para definir propriedades ou eventos de telemetria em [gémeos digitais](concepts-twins-graph.md) em conformidade. 

Este documento de como escrever uma função Azure que pode ingerir telemetria a partir do IoT Hub.

## <a name="example-telemetry-scenario"></a>Cenário de telemetria de exemplo

Este como-descrever como enviar mensagens do IoT Hub para Azure Digital Twins, utilizando uma função Azure. Existem muitas configurações possíveis e estratégias de correspondência que pode usar para isso, mas o exemplo para este artigo contém as seguintes partes:
* Um dispositivo termómetro no IoT Hub, com uma identificação de dispositivo conhecido.
* Um gémeo digital para representar o dispositivo, com um ID correspondente
* Um gémeo digital representando um quarto

> [!NOTE]
> Este exemplo utiliza uma correspondência de ID simples entre o ID do dispositivo e o ID de um gémeo digital correspondente, mas é possível fornecer mapeamentos mais sofisticados do dispositivo para o seu gémeo (como com uma tabela de mapeamento).

Sempre que um evento de telemetria de temperatura for enviado pelo dispositivo termómetro, a propriedade da *temperatura* do twin *da Sala* deve ser atualizada. Para que isso aconteça, você vai mapear de um evento de telemetria em um dispositivo para um conjunto de propriedades no twin digital. Você usará informações de topologia do [gráfico gémeo](concepts-twins-graph.md) para encontrar o *quarto* twin, e então você pode definir a propriedade do gémeo. Noutros cenários, os utilizadores podem querer definir um imóvel no gémeo correspondente (neste exemplo, o gémeo com o ID de *123).* A Azure Digital Twins dá-lhe muita flexibilidade para decidir como os mapas de dados de telemetria em gémeos. 

Este cenário está delineado num diagrama abaixo:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Um dispositivo IoT Hub envia telemetria de temperatura através do IoT Hub, Grade de Eventos ou tópicos do sistema para uma Função Azure, que atualiza uma propriedade de temperatura em gémeos em Azure Digital Twins." border="false":::

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar com este exemplo, terá de completar os seguintes pré-requisitos.
1. Criar um hub IoT. Consulte a secção *Criar um hub IoT* [deste quickstart IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md) para obter instruções.
2. Crie pelo menos uma função Azure para processar eventos a partir do IoT Hub. Consulte [como fazer: Crie uma função Azure para o processamento de dados](how-to-create-azure-function.md) para construir uma função Azure básica que pode ligar-se a Azure Digital Twins e chamar a Azure Digital Twins API funções. O resto deste como construir sobre esta função.
3. Crie um destino de eventos para os dados do hub. No [portal Azure,](https://portal.azure.com/)navegue para o seu ioT Hub. Em *Eventos,* crie uma subscrição para a sua função Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Portal Azure: Adicionar uma subscrição de eventos":::

4. Na página *'Criar Subscrição de* Eventos', preencha os campos da seguinte forma:
   * Em *DETALHES DE SUBSCRIÇÃO DE EVENTO*, nomeie a subscrição o que você gostaria
   * Em *TIPOS DE EVENTOs*, escolha *a Telemetria do Dispositivo* como o tipo de evento para filtrar
      - Adicione filtros a outros tipos de eventos, se quiser.
   * Em *DETALHES ENDPOINT*, selecione a sua função Azure como ponto final

## <a name="create-an-azure-function-in-visual-studio"></a>Criar uma função Azure no Estúdio Visual

Esta secção utiliza os mesmos passos de arranque do Estúdio Visual e esqueleto de função Azure de [How-to: Configurar uma função Azure para o processamento de dados](how-to-create-azure-function.md). O esqueleto lida com a autenticação e cria um cliente de serviço, pronto para processar dados e ligar para as APIs de Gémeos Digitais da Azure em resposta. 

O coração da função do esqueleto é o seguinte:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Nos passos que se seguem, irá adicionar-lhe código específico para o processamento de eventos de telemetria IoT a partir do IoT Hub.  

## <a name="add-telemetry-processing"></a>Adicionar processamento de telemetria

Os eventos de telemetria vêm sob a forma de mensagens do dispositivo. O primeiro passo para adicionar código de processamento de telemetria é extrair a parte relevante desta mensagem do dispositivo do evento Event Grid. 

Diferentes dispositivos podem estruturar as suas mensagens de forma diferente, pelo que o código para este passo depende do dispositivo conectado. 

O código que se segue mostra um exemplo para um dispositivo simples que envia telemetria como JSON. A amostra extrai a identificação do dispositivo que enviou a mensagem, bem como o valor da temperatura.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Lembre-se que o objetivo deste exercício é atualizar a temperatura de uma *Sala* dentro do gráfico gémeo. Isto significa que o nosso alvo para a mensagem não é o gémeo digital que está associado a este dispositivo, mas o *quarto* gémeo que é o seu pai. Pode encontrar o gémeo-pai utilizando o valor de identificação do dispositivo que extraiu da mensagem de telemetria utilizando o código acima.

Para isso, utilize as APIs Azure Digital Twins para aceder às relações de entrada com o gémeo que representa o dispositivo (que neste caso tem o mesmo ID que o dispositivo). A partir da relação de entrada, você pode encontrar a identificação do progenitor com o código cortado abaixo.

O código abaixo mostra como recuperar as relações recebidas de um gémeo:

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

O pai do seu gémeo está na propriedade *sourceid* da relação.

É bastante comum para um modelo de gémeo que representa um dispositivo ter apenas uma única relação de entrada. Neste caso, pode escolher a primeira (e única) relação devolvida. Se os seus modelos permitirem vários tipos de relacionamentos com este gémeo, poderá ter de especificar mais para escolher entre múltiplas relações de entrada. Uma forma comum de o fazer é escolher a relação `RelationshipName` por. 

Assim que tiver a identificação do gémeo-mãe que representa a *Sala,* pode "corrigir" (fazer atualizações) esse gémeo. Para tal, utilize o seguinte código:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Código de função Full Azure

Utilizando o código das amostras anteriores, aqui está toda a função Azure em contexto:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

A função de utilidade para encontrar relacionamentos de entrada:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

E a função de utilidade para remendar o gémeo:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

Agora tem uma função Azure que está equipada para ler e interpretar os dados do cenário provenientes do IoT Hub.

## <a name="debug-azure-function-apps-locally"></a>Aplicativos de função Debug Azure localmente

É possível depurar funções de Azure com um gatilho de Grade de Eventos localmente. Para obter mais informações sobre isto, consulte [o gatilho da Grelha de Eventos de Debug localmente.](../azure-functions/functions-debug-event-grid-trigger-local.md)

## <a name="next-steps"></a>Passos seguintes

Leia sobre a entrada de dados e saída com a Azure Digital Twins:
* [Conceitos: Integração com outros serviços](concepts-integration.md)
