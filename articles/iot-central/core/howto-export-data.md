---
title: Exporte os seus dados Centrais Azure IoT / Microsoft Docs
description: Como exportar dados da sua aplicação Azure IoT Central para Azure Event Hubs, Azure Service Bus e Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157913"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Exportar dados de IoT para destinos em Azure

*Este tópico aplica-se aos administradores.*

Este artigo descreve como utilizar a funcionalidade contínua de exportação de dados na Azure IoT Central para exportar os seus dados para os Hubs de **Eventos Azure,** **Azure Service Bus**ou para as instâncias de armazenamento **da Azure Blob.** Os dados são exportados em formato JSON e podem incluir telemetria, informações do dispositivo e informações sobre modelos de dispositivos. Utilize os dados exportados para:

- Insights de caminho quente e análise. Esta opção inclui desencadear regras personalizadas no Azure Stream Analytics, desencadear fluxos de trabalho personalizados em Aplicações Lógicas Azure ou passá-la através de Funções Azure para ser transformada.
- Análises de caminhos frios, como modelos de formação em Azure Machine Learning ou análise de tendências a longo prazo no Microsoft Power BI.

> [!Note]
> Quando liga a exportação contínua de dados, obtém-se apenas os dados a partir desse momento. Atualmente, os dados não podem ser recuperados por um tempo em que a exportação contínua de dados estava desligada. Para reter dados mais históricos, ligue a exportação contínua de dados mais cedo.

## <a name="prerequisites"></a>Pré-requisitos

Deve ser administrador na sua aplicação IoT Central, ou ter permissões de exportação de Dados.

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

O seu destino de exportação deve existir antes de configurar a sua exportação contínua de dados.

### <a name="create-event-hubs-namespace"></a>Criar o espaço de nomes dos Hubs de Eventos

Se não tiver um espaço de nome existente para os Hubs de Eventos para exportar, siga estes passos:

1. Crie um novo espaço de [nome Sem Nome Do Evento Hubs no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.EventHub) Você pode aprender mais em [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

2. Escolha uma subscrição. Pode exportar dados para outras subscrições que não estejam na mesma subscrição que a sua aplicação IoT Central. Conecte-se com uma corda de ligação neste caso.

3. Crie um centro de eventos no seu espaço de nome Sem Eventos. Vá ao seu espaço de nome e selecione **+ Event Hub** no topo para criar uma instância de centro de eventos.

### <a name="create-service-bus-namespace"></a>Criar espaço de nome de ônibus de serviço

Se não tiver um espaço de nome de ônibus de serviço existente para exportar, siga estes passos:

1. Crie um novo espaço de [nome service bus no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) Você pode aprender mais em [docs de ônibus de serviço Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Escolha uma subscrição. Pode exportar dados para outras subscrições que não estejam na mesma subscrição que a sua aplicação IoT Central. Conecte-se com uma corda de ligação neste caso.

3. Vá ao seu espaço de nome service Bus e selecione **+ Fila** ou **+ Tópico** no topo para criar uma fila ou tópico para exportar.

Quando escolhe o Ônibus de Serviço como destino de exportação, as filas e tópicos não devem ter Sessões ou Deteção duplicada ativadas. Se alguma dessas opções estiver ativada, algumas mensagens não chegarão na sua fila ou tópico.

### <a name="create-storage-account"></a>Criar conta de armazenamento

Se não tiver uma conta de Armazenamento Azure existente para exportar, siga estes passos:

1. Crie uma nova conta de [armazenamento no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) Pode saber mais sobre a criação de novas contas de [armazenamento de Blob Azure](https://aka.ms/blobdocscreatestorageaccount) ou contas de armazenamento V2 do [Lago De dados Azure.](../../storage/blobs/data-lake-storage-quickstart-create-account.md) A exportação de dados só pode escrever dados para contas de armazenamento que suportem bolhas de blocos. Segue-se uma lista de tipos de contas de armazenamento compatíveis conhecidos: 

    |Nível de Desempenho|Tipo de conta|
    |-|-|
    |Standard|Propósito Geral V2|
    |Standard|Propósito Geral V1|
    |Standard|Blob Storage|
    |Premium|Armazenamento blob bloco|

2. Crie um recipiente na sua conta de armazenamento. Vá para a sua conta de armazenamento. No **serviço Blob,** selecione **Browse Blobs**. Selecione **+ Recipiente** na parte superior para criar um novo recipiente.

## <a name="set-up-continuous-data-export"></a>Configurar a exportação contínua de dados

Agora que tem um destino para exportar dados, siga estes passos para configurar a exportação contínua de dados.

1. Inscreva-se na sua aplicação IoT Central.

2. No painel esquerdo, selecione **Data Export**.

    > [!Note]
    > Se não vir a Exportação de Dados no painel esquerdo, então não tem permissões para configurar a exportação de dados na sua aplicação. Fale com um administrador para configurar a exportação de dados.

3. Selecione o botão **+ Novo** no topo direito. Escolha um dos Hubs de **Eventos Azure,** **Azure Service Bus,** ou **o armazenamento Azure Blob** como destino da sua exportação. O número máximo de exportações por aplicação é de cinco.

    ![Criar novas exportações contínuas de dados](media/howto-export-data/new-export-definition.png)

4. Na caixa de lista seletiva, selecione o espaço de nome do Seu **Event Hubs,** espaço de **nome do Autocarro de Serviço,** espaço de nome da conta de **armazenamento,** ou **introduza uma cadeia**de ligação .

    - Você só vê Contas de Armazenamento, espaços de nome sinuosos de eventos e espaços de nome sinuosos de ônibus de serviço na mesma subscrição que a sua aplicação IoT Central. Se quiser exportar para um destino fora desta subscrição, escolha Entrar numa corda de **ligação** e ver o passo 5.
    - Para aplicações criadas usando o plano de preços gratuitos, a única maneira de configurar a exportação contínua de dados é através de uma cadeia de ligação. As aplicações sobre o plano de preços gratuitos não têm uma subscrição azure associada.

    ![Criar novo Hub de Eventos](media/howto-export-data/export-event-hub.png)

5. (Opcional) Se escolher uma corda de **ligação,** aparece uma nova caixa para colar a corda de ligação. Para obter a corda de ligação para o seu:
    - Event Hubs ou Service Bus, vá ao espaço de nome no portal Azure.
        - Em **Definições,** selecione **Políticas de Acesso Partilhado**
        - Escolha o **RootManageSharedAccessKey** padrão ou crie um novo
        - Copiar a cadeia de ligação primária ou secundária
    - Conta de armazenamento, vá à conta de Armazenamento no portal Azure:
        - Em **Definições,** selecione **Teclas de acesso**
        - Copiar ou a cadeia de ligação key1 ou a cadeia de ligação key2

6. Escolha um centro de eventos, fila, tópico ou recipiente da caixa de listas.

7. Em **Dados para exportação,** escolha os tipos de dados para exportar, definindo o tipo para **On**.

8. Para ativar a exportação contínua de dados, certifique-se de que o toggle **Habilitado** está **ligado**. Selecione **Guardar**.

9. Após alguns minutos, os seus dados aparecem no seu destino escolhido.

## <a name="export-contents-and-format"></a>Conteúdos e formatode exportação

Os dados de telemetria exportados contêm a totalidade da mensagem que os seus dispositivos enviaram à IoT Central, e não apenas os próprios valores de telemetria. Os dados dos dispositivos exportados contêm alterações nas propriedades e metadados de todos os dispositivos, e os modelos de dispositivos exportados contêm alterações em todos os modelos do dispositivo.

Para Os Hubs de Eventos e autocarros de serviço, os dados são exportados em tempo quase real. Os dados situam-se na propriedade do corpo e estão em formato JSON (ver abaixo, por exemplo).

Para o Armazenamento blob, os dados são exportados uma vez por minuto, com cada ficheiro contendo o lote de alterações desde o último ficheiro exportado. Os dados exportados são colocados em três pastas em formato JSON. Os caminhos predefinidos na sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/telemetria/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Dispositivos: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Modelos do dispositivo: _{recipiente}/{app-id}/dispositivoTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Pode navegar nos ficheiros exportados no portal Azure navegando até ao ficheiro e escolhendo o separador **'edição'.**


## <a name="telemetry"></a>Telemetria

Para os Hubs de Eventos e autocarros de serviço, uma nova mensagem é exportada rapidamente após a IoT Central receber a mensagem de um dispositivo, e cada mensagem exportada contém a mensagem completa que o dispositivo enviado na propriedade do corpo em formato JSON.

Para o Armazenamento blob, as mensagens são lotadas e exportadas uma vez por minuto. Os ficheiros exportados utilizam o mesmo formato que os ficheiros de mensagens exportados pela [mensagem IoT Hub, encaminhamento](../../iot-hub/tutorial-routing.md) para armazenamento de bolhas. 

> [!NOTE]
> Para o Armazenamento Blob, certifique-se de `contentType: application/JSON` `contentEncoding:utf-8` que `utf-16`os `utf-32`seus dispositivos estão a enviar mensagens que têm e (ou . ). Consulte a documentação do [IoT Hub,](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) por exemplo.

O dispositivo que enviou a telemetria é representado pelo ID do dispositivo (ver as seguintes secções). Para obter os nomes dos dispositivos, exportar dados do dispositivo e correlacionar cada mensagem utilizando a **ligaçãoDeviceId** que corresponde ao **dispositivoId** da mensagem do dispositivo.

Esta é uma mensagem de exemplo recebida em um hub de eventoou fila de ônibus de serviço ou tópico.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

Este é um exemplo de registo exportado para armazenamento de bolhas:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Dispositivos

Cada mensagem ou registo num instantâneo representa uma ou mais alterações num dispositivo e nas suas propriedades de dispositivo e nuvem desde a última mensagem exportada. Isto inclui:

- `id`do dispositivo em IoT Central
- `displayName`do dispositivo
- Id do modelo do dispositivo`instanceOf`
- `simulated`bandeira, verdade se o dispositivo é um dispositivo simulado
- `provisioned`bandeira, verdade se o dispositivo foi provisionado
- `approved`bandeira, verdade se o dispositivo foi aprovado para enviar dados
- Valores patrimoniais
- `properties`incluindo valores de propriedades de dispositivo e nuvem

Os dispositivos apagados não são exportados. Atualmente, não existem indicadores em mensagens exportadas para dispositivos apagados.

Para Os Hubs de Eventos e autocarrode serviço, as mensagens que contêm dados do dispositivo são enviadas para o seu centro de eventos ou fila de ônibus de serviço ou tópico em tempo real, como aparece na IoT Central. 

Para o Armazenamento Blob, um novo instantâneo que contenha todas as alterações desde a última escrita é exportado uma vez por minuto.

Esta é uma mensagem de exemplo sobre dispositivos e propriedades dados em centro de eventos ou fila de ônibus de serviço ou tópico:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Este é um instantâneo de exemplo contendo dispositivos e propriedades dados no Armazenamento Blob. Os ficheiros exportados contêm uma única linha por registo.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Modelos de dispositivo

Cada mensagem ou registo instantâneo representa uma ou mais alterações a um modelo de dispositivo publicado desde a última mensagem exportada. As informações enviadas em cada mensagem ou registo incluem:

- `id`do modelo de dispositivo `instanceOf` que corresponde ao fluxo de dispositivos acima
- `displayName`do modelo do dispositivo
- O `capabilityModel` dispositivo `interfaces`incluindo as suas definições de telemetria, propriedades e comandos
- `cloudProperties`definições
- Sobreposições e valores iniciais, em linha com o`capabilityModel`

Os modelos de dispositivos apagados não são exportados. Atualmente, não existem indicadores em mensagens exportadas para modelos de dispositivos eliminados.

Para Os Hubs de Eventos e autocarrode serviço, as mensagens que contêm dados de modelo de dispositivo são enviadas para o seu centro de eventos ou fila de ônibus de serviço ou tópico em tempo real, como aparece na IoT Central. 

Para o Armazenamento Blob, um novo instantâneo que contenha todas as alterações desde a última escrita é exportado uma vez por minuto.

Esta é uma mensagem de exemplo sobre os dados dos modelos de dispositivos no centro de eventos ou na fila de ônibus de serviço ou tópico:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Este é um instantâneo de exemplo contendo dispositivos e propriedades dados no Armazenamento Blob. Os ficheiros exportados contêm uma única linha por registo.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```
## <a name="data-format-change-notice"></a>Aviso de alteração de formato de dados

> [!Note]
> O formato de dados de fluxo de telemetria não é afetado por esta alteração. Apenas os fluxos de dispositivos e modelos de dispositivos de dados são afetados.

Se tiver uma exportação de dados existente na sua aplicação de pré-visualização com os fluxos de *modelos de dispositivos* e *dispositivos ligados,* terá de atualizar a sua exportação até 30 de junho de **2020**. Isto aplica-se às exportações para o Azure Blob Storage, Azure Event Hubs e Azure Service Bus.

A partir de 3 de fevereiro de 2020, todas as novas exportações em aplicações com modelos de Dispositivos e Dispositivos ativados terão o formato de dados descrito acima. Todas as exportações criadas antes desta situação permanecerão no antigo formato de dados até 30 de junho de 2020, altura após o qual estas exportações serão automaticamente migradas para o novo formato de dados. O novo formato de dados corresponde ao [dispositivo,](https://docs.microsoft.com/rest/api/iotcentral/devices/get)propriedade do [dispositivo,](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties) [propriedade da nuvem](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) do dispositivo e objetos de modelo de [dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) na IoT Central Public API. 
 
Para **dispositivos,** diferenças notáveis entre o formato de dados antigo e o novo formato de dados incluem:
- `@id`para dispositivo é `deviceId` removido, é renomeado para`id` 
- `provisioned`bandeira é adicionada para descrever o estado de provisionamento do dispositivo
- `approved`bandeira é adicionada para descrever o estado de aprovação do dispositivo
- `properties`incluindo propriedades de dispositivos e nuvem, corresponde a entidades na API pública

Para **os modelos do Dispositivo,** diferenças notáveis entre o formato de dados antigo e o novo formato de dados incluem:

- `@id`para o modelo de dispositivo é renomeado para`id`
- `@type`para o modelo do `types`dispositivo é renomeado para , e agora é uma matriz

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Dispositivos (formato depreciado a partir de 3 de fevereiro de 2020)
```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Modelos de dispositivo (formato depreciado a partir de 3 de fevereiro de 2020)
```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```
## <a name="next-steps"></a>Passos seguintes

Agora que sabe exportar os seus dados para o Azure Event Hubs, Azure Service Bus e Azure Blob Storage, continue para o próximo passo:

> [!div class="nextstepaction"]
> [Como criar webhooks](./howto-create-webhooks.md)
