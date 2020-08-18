---
title: Ingerir telemetria do Hub IoT
titleSuffix: Azure Digital Twins
description: Veja como ingerir mensagens de telemetria do dispositivo a partir do IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 47e4bb291d031c41c89c88435a795004490e20a1
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505330"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingesteia IoT Hub em Azure Digital Twins

A Azure Digital Twins é conduzida com dados de dispositivos IoT e outras fontes. Uma fonte comum para os dados do dispositivo a utilizar em Azure Digital Twins é [o IoT Hub](../iot-hub/about-iot-hub.md).

O processo de ingerir dados em Azure Digital Twins é criar um recurso de computação externa, como uma [função Azure,](../azure-functions/functions-overview.md)que recebe os dados e utiliza as [APIs digitalTwins](how-to-use-apis-sdks.md) para definir propriedades ou eventos de telemetria em [gémeos digitais](concepts-twins-graph.md) em conformidade. 

Este documento de como escrever uma função Azure que pode ingerir telemetria a partir do IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar com este exemplo, terá de configurar os seguintes recursos como pré-requisitos:
* **Um hub IoT.** Para obter instruções, consulte a secção *Criar um hub IoT* [deste quickstart IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
* **Uma Função Azure** com as permissões corretas para ligar para a sua instância digital twin. Para obter instruções, consulte [*Como fazer: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md). 
* **Um exemplo de Azure Digital Twins** que receberá a telemetria do seu dispositivo. Para obter instruções, consulte [*Como fazer: Configurar uma instância e autenticação de Gémeos Digitais Azure*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Cenário de telemetria de exemplo

Este como-descrever como enviar mensagens do IoT Hub para Azure Digital Twins, utilizando uma função Azure. Existem muitas configurações possíveis e estratégias de correspondência que pode usar para isso, mas o exemplo para este artigo contém as seguintes partes:
* Um dispositivo termómetro no IoT Hub, com uma identificação de dispositivo conhecido.
* Um gémeo digital para representar o dispositivo, com um ID correspondente

> [!NOTE]
> Este exemplo utiliza uma correspondência de ID simples entre o ID do dispositivo e o ID de um gémeo digital correspondente, mas é possível fornecer mapeamentos mais sofisticados do dispositivo para o seu gémeo (como com uma tabela de mapeamento).

Sempre que um evento de telemetria de temperatura for enviado pelo dispositivo termómetro, a propriedade da *temperatura* do gémeo digital deve ser atualizada. Este cenário está delineado num diagrama abaixo:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Um diagrama mostrando um gráfico de fluxo. Na tabela, um dispositivo IoT Hub envia telemetria de temperatura através do IoT Hub para uma Função Azure, que atualiza uma propriedade de temperatura num gémeo em Azure Digital Twins." border="false":::

## <a name="add-a-model-and-twin"></a>Adicione um modelo e um gémeo

Vai precisar de um gémeo para atualizar com informações do hub IoT.

O modelo é assim:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Para **fazer o upload deste modelo para a sua instância gémea,** abra o CLI Azure e execute o seguinte comando:
```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Em seguida, terá de **criar um gémeo utilizando este modelo.** Utilize o seguinte comando para criar um gémeo e definir 0.0 como um valor de temperatura inicial.
```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

A saída de um comando de criação de gémeos bem sucedido deve ser assim:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Esta secção utiliza os mesmos passos de arranque do Estúdio Visual e esqueleto de função Azure de [*How-to: Configurar uma função Azure para o processamento de dados*](how-to-create-azure-function.md). O esqueleto lida com a autenticação e cria um cliente de serviço, pronto para processar dados e ligar para as APIs de Gémeos Digitais da Azure em resposta. 

Nos passos que se seguem, irá adicionar-lhe código específico para o processamento de eventos de telemetria IoT a partir do IoT Hub.  

### <a name="add-telemetry-processing"></a>Adicionar processamento de telemetria
    
Os eventos de telemetria vêm sob a forma de mensagens do dispositivo. O primeiro passo para adicionar código de processamento de telemetria é extrair a parte relevante desta mensagem do dispositivo do evento Event Grid. 

Diferentes dispositivos podem estruturar as suas mensagens de forma diferente, pelo que o código **para este passo depende do dispositivo conectado.** 

O código que se segue mostra um exemplo para um dispositivo simples que envia telemetria como JSON. Esta amostra é totalmente explorada em [*Tutorial: Conecte uma solução de ponta a ponta.*](./tutorial-end-to-end.md) O código a seguir encontra a identificação do dispositivo que enviou a mensagem, bem como o valor da temperatura.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

A próxima amostra de código pega no ID e no valor da temperatura e usa-os para "corrigir" (fazer atualizações para) esse gémeo.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Atualize o seu código de função Azure

Agora que compreende o código das amostras anteriores, abra o Visual Studio e substitua o código da sua função Azure por este código de amostra.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>Ligue a sua função ao IoT Hub

1. Crie um destino de eventos para os dados do hub. No [portal Azure,](https://portal.azure.com/)navegue para o seu ioT Hub. Em **Eventos,** crie uma subscrição para a sua função Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Screenshot do portal Azure que mostra adicionar uma subscrição de evento.":::

2. Na página **'Criar Subscrição de** Eventos', preencha os campos da seguinte forma:
    1. Under **Name**, nomeie a subscrição o que quiser.
    2. Em **Esquema de Evento,** escolha **o Esquema de Grelha de Eventos.**
    3. Em **Nome tópico do sistema,** escolha um nome único.
    4. Nos **Tipos de Eventos,** escolha **a Telemetria do Dispositivo** como o tipo de evento para filtrar.
    5. Em **Detalhes do Ponto Final,** selecione a sua função Azure como ponto final.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Screenshot do portal Azure que mostra os detalhes da subscrição do evento":::

## <a name="send-simulated-iot-data"></a>Enviar dados IoT simulados

Para testar a sua nova função de entrada, utilize o simulador do dispositivo a partir do [*Tutorial: Ligue uma solução de ponta a ponta*](./tutorial-end-to-end.md). Este tutorial é conduzido por um projeto de amostra escrito em C#. O código de amostra está localizado aqui: [amostras de Gémeos Digitais Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Vais usar o projeto **DeviceSimulator** nesse repositório.

No tutorial de ponta a ponta, complete os seguintes passos:
1. [*Registar o dispositivo simulado com ioT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Configure e executar a simulação*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Valide os seus resultados

Enquanto executa o simulador do dispositivo acima, o valor de temperatura do seu gémeo digital será alterado. No CLI Azure, executar o seguinte comando para ver o valor da temperatura.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

A sua saída deve conter um valor de temperatura como este:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Para ver a mudança de valor, executar repetidamente o comando de consulta acima.

## <a name="next-steps"></a>Passos seguintes

Leia sobre a entrada de dados e saída com a Azure Digital Twins:
* [*Conceitos: Integração com outros serviços*](concepts-integration.md)
