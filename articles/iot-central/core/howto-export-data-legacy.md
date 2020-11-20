---
title: Dados de exportação da Azure IoT Central (legado) Microsoft Docs
description: Como exportar dados da sua aplicação Azure IoT Central para Azure Event Hubs, Azure Service Bus e Azure Blob
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 9e5f4fd14f56f0a2dff45dd2650ea552b07fecd7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987359"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>Exportar dados de IoT para destinos em nuvem usando exportação de dados (legado)

> [!Note]
> Este artigo descreve as características antigas da exportação de dados na IoT Central.
>
> - Para obter informações sobre as novas funcionalidades de exportação de dados de pré-visualização, consulte [os dados do IoT de exportação para destinos em nuvem utilizando a exportação de dados.](./howto-export-data.md)
> - Para conhecer as diferenças entre as características de exportação de dados de pré-visualização e as características de exportação de dados legados, consulte a [tabela de comparação](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export).

Este artigo descreve como utilizar a funcionalidade de exportação de dados na Azure IoT Central. Esta funcionalidade permite-lhe exportar os seus dados continuamente para **Azure Event Hubs,** **Azure Service Bus** ou **a azure Blob.** A exportação de dados utiliza o formato JSON e pode incluir informações sobre telemetria, informações do dispositivo e modelo do dispositivo. Utilize os dados exportados para:

- Perspicácias e análises de caminhos quentes. Esta opção inclui o desencadear de regras personalizadas no Azure Stream Analytics, desencadear fluxos de trabalho personalizados em Azure Logic Apps ou passá-lo através de Funções Azure para serem transformados.
- Análises de caminhos frios, como modelos de treinamento em Azure Machine Learning ou análise de tendências a longo prazo no Microsoft Power BI.

> [!Note]
> Quando liga a exportação de dados, obtém-se apenas os dados a partir desse momento. Atualmente, os dados não podem ser recuperados por um tempo em que a exportação de dados estava desligada. Para reter mais dados históricos, ligue a exportação de dados mais cedo.

## <a name="prerequisites"></a>Pré-requisitos

Deve ser administrador na sua aplicação IoT Central ou ter permissões de exportação de Dados.

## <a name="set-up-export-destination"></a>Configurar destino de exportação

O seu destino de exportação deve existir antes de configurar a sua exportação de dados.

### <a name="create-event-hubs-namespace"></a>Criar o espaço de nomes dos Hubs de Eventos

Se você não tem um espaço de nomes de Event Hubs existente para exportar, siga estes passos:

1. Crie um [novo espaço de nomes de Centros de Eventos no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.EventHub) Você pode aprender mais em [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

2. Escolha uma subscrição. Pode exportar dados para outras subscrições que não estejam na mesma subscrição que a sua aplicação IoT Central. Conecte-se usando uma cadeia de ligação neste caso.

3. Crie um centro de eventos no seu espaço de nomes Event Hubs. Vá ao seu espaço de nome e selecione **+ Event Hub** no topo para criar uma instância de centro de eventos.

### <a name="create-service-bus-namespace"></a>Criar espaço de nomes de ônibus de serviço

Se você não tem um espaço de nome de Service Bus existente para exportar para, siga estes passos:

1. Crie um [novo espaço de nomes de Service Bus no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) Pode saber mais em [docs de ônibus da Azure Service](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Escolha uma subscrição. Pode exportar dados para outras subscrições que não estejam na mesma subscrição que a sua aplicação IoT Central. Conecte-se usando uma cadeia de ligação neste caso.

3. Para criar uma fila ou tópico para exportar, vá ao seu espaço de nomes de Service Bus e selecione **+ Fila** ou **+ Tópico**.

Ao escolher o Service Bus como destino de exportação, as filas e tópicos não devem ter Sessões ou Deteção Duplicada ativadas. Se alguma dessas opções estiver ativada, algumas mensagens não chegarão à sua fila ou tópico.

### <a name="create-storage-account"></a>Criar conta de armazenamento

Se não tiver uma conta de armazenamento Azure existente para exportar, siga estes passos:

1. Criar uma [nova conta de armazenamento no portal Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Pode saber mais sobre a criação de novas [contas de armazenamento Azure Blob](../../storage/blobs/storage-quickstart-blobs-portal.md) ou [contas de armazenamento V2 do Azure Data Lake Storage v2](../../storage/common/storage-account-create.md). A exportação de dados só pode escrever dados para contas de armazenamento que suportam bolhas de blocos. A lista que se segue mostra os tipos de conta de armazenamento compatíveis conhecidos:

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Final Geral V2|
    |Standard|Final geral V1|
    |Standard|Armazenamento de blobs|
    |Premium|Armazenamento Blob de bloco|

2. Crie um recipiente na sua conta de armazenamento. Vá para a sua conta de armazenamento. No **serviço Blob**, **selecione Browse Blobs**. Selecione **+ Recipiente** na parte superior para criar um novo recipiente.

## <a name="set-up-data-export"></a>Configurar exportação de dados

Agora que tem um destino para exportar dados, siga estes passos para configurar a exportação de dados.

1. Inscreva-se na sua aplicação IoT Central.

2. No painel esquerdo, selecione **Data export**.

    > [!Tip]
    > Se não vir **a exportação de Dados** no painel esquerdo, então não tem permissões para configurar a exportação de dados na sua app. Fale com um administrador para configurar a exportação de dados.

3. Selecione o botão **+ Novo.** Escolha um dos **Azure Blob Storage,** **Azure Event Hubs,** **Azure Service Bus Queue** ou **Azure Service Bus Topic** como destino da sua exportação. O número máximo de exportações por pedido é de cinco.

4. Insira um nome para a exportação. Na caixa de lista de drop-down, selecione o seu **espaço de nome** ou **introduza uma cadeia de ligação**.

    - Só vê contas de armazenamento, espaços de nomes de Event Hubs e espaços de nomes de Service Bus na mesma subscrição que a sua aplicação IoT Central. Se pretender exportar para um destino fora desta subscrição, escolha Introduzir uma cadeia de **ligação** e ver o passo 6.
    - Para aplicações criadas usando o plano de preços gratuitos, a única forma de configurar a exportação de dados é através de uma cadeia de conexão. As aplicações no plano de preços gratuitos não têm uma subscrição associada do Azure.

    ![Criar novo Centro de Eventos](media/howto-export-data-legacy/export-event-hub.png)

5. Escolha um centro de eventos, fila, tópico ou recipiente da caixa de lista de drop-down.

6. (Opcional) Se escolheu **Introduzir uma cadeia de ligação,** aparece uma nova caixa para colar a sua cadeia de ligação. Para obter a cadeia de ligação para o seu:

    - Event Hubs ou Service Bus, vá ao espaço de nomes no portal Azure:
        - Para utilizar uma cadeia de ligação para todo o espaço de nome:
            1. Em **Definições**, **selecione Políticas de Acesso Partilhado**
            2. Crie uma nova chave ou escolha uma chave existente que tenha permissões **enviar.**
            3. Copie a cadeia de ligação primária ou secundária
        - Para utilizar o string de ligação para uma instância específica do centro de eventos ou fila ou tópico do Service Bus, vá a **Entidades > Centros** de Eventos ou **Entidades > Filas** ou **Entidades > Tópicos**. Escolha uma instância específica e siga os mesmos passos acima para obter uma cadeia de ligação.
    - Conta de armazenamento, vá à conta de armazenamento no portal Azure:
        - Apenas as cadeias de ligação para toda a conta de armazenamento são suportadas. As cordas de ligação a um único recipiente não são suportadas.
          1. Em **Definições**, selecione **teclas de acesso**
          2. Copie a cadeia de ligação key1 ou a cadeia de ligação key2

    Cole na corda de ligação. Digite o nome do **recipiente** ou sensível à caixa.

7. Em **Dados para exportar,** escolha os tipos de dados para exportar, fixando o tipo para **On**.

8. Para ativar a exportação de dados, certifique-se de que o toggle **ativado** está **ligado**. Selecione **Guardar**.

9. Após alguns minutos, os seus dados aparecem no seu destino escolhido.

## <a name="export-contents-and-format"></a>Conteúdos de exportação e formato

Os dados de telemetria exportados contêm a totalidade da mensagem que os seus dispositivos enviaram para a IoT Central, e não apenas os próprios valores de telemetria. Os dados dos dispositivos exportados contêm alterações nas propriedades e metadados de todos os dispositivos, e os modelos de dispositivos exportados contêm alterações em todos os modelos do dispositivo.

Para o Event Hubs e Service Bus, os dados são exportados em quase tempo real. Os dados `body` estão na propriedade e estão em formato JSON. Veja abaixo, por exemplo.

Para o armazenamento blob, os dados são exportados uma vez por minuto, com cada ficheiro contendo o lote de alterações desde o último ficheiro exportado. Os dados exportados são colocados em três pastas no formato JSON. Os caminhos predefinidos na sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/telemetria/{YYYY}/{MM}/{dd}/{mm}/{filename}_
- Dispositivos: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Modelos de dispositivo: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{filename}_

Para navegar nos ficheiros exportados no portal Azure, navegue para o ficheiro e selecione o **separador 'Edição' blob.**

## <a name="telemetry"></a>Telemetria

Para o Event Hubs e Service Bus, a IoT Central exporta uma nova mensagem rapidamente depois de receber a mensagem de um dispositivo. Cada mensagem exportada contém a mensagem completa que o dispositivo enviou na propriedade do corpo em formato JSON.

Para o armazenamento blob, as mensagens são em lotadas e exportadas uma vez por minuto. Os ficheiros exportados utilizam o mesmo formato que os ficheiros de mensagens exportados pelo [encaminhamento de mensagens IoT Hub](../../iot-hub/tutorial-routing.md) para o armazenamento de bolhas.

> [!NOTE]
> Para o armazenamento blob, certifique-se de que os seus dispositivos estão a enviar mensagens que têm `contentType: application/JSON` e `contentEncoding:utf-8` (ou `utf-16` , `utf-32` ). Consulte a documentação do [IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) para dar um exemplo.

O dispositivo que enviou a telemetria é representado pelo ID do dispositivo (ver as seguintes secções). Para obter os nomes dos dispositivos, exportar dados do dispositivo e correlacionar cada mensagem utilizando a **ligaçãoDeviceId** que corresponde ao **dispositivoId** da mensagem do dispositivo.

O exemplo a seguir mostra uma mensagem recebida de um centro de eventos ou fila de autocarros de serviço ou tópico:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Esta mensagem não inclui a identificação do dispositivo do dispositivo de envio.

Para recuperar o ID do dispositivo a partir dos dados da mensagem numa consulta Azure Stream Analytics, utilize a função [GetMetadataPropertyValue.](/stream-analytics-query/getmetadatapropertyvalue) Por exemplo, consulte a consulta em [Extend Azure IoT Central com regras personalizadas usando Stream Analytics, Funções Azure e SendGrid](./howto-create-custom-rules.md).

Para recuperar o ID do dispositivo num espaço de trabalho Azure Databricks ou Apache Spark, utilize [as propriedades do sistema](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md). Por exemplo, consulte o espaço de trabalho Databricks em [Extend Azure IoT Central com análises personalizadas utilizando Azure Databricks](./howto-create-custom-analytics.md).

O exemplo a seguir mostra um registo exportado para o armazenamento de bolhas:

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

Cada mensagem ou registo num instantâneo representa uma ou mais alterações num dispositivo e no seu dispositivo e propriedades na nuvem desde a última mensagem exportada. A mensagem inclui:

- `id` do dispositivo na IoT Central
- `displayName` do dispositivo
- ID do modelo do dispositivo em `instanceOf`
- `simulated` bandeira, verdade se o dispositivo é um dispositivo simulado
- `provisioned` bandeira, verdade se o dispositivo foi a provisionado
- `approved` bandeira, verdade se o dispositivo foi aprovado para enviar dados
- Valores patrimoniais
- `properties` incluindo valores de propriedades de dispositivos e nuvem

Os dispositivos apagados não são exportados. Atualmente, não existem indicadores em mensagens exportadas para dispositivos eliminados.

Para o Event Hubs e Service Bus, a IoT Central envia mensagens que contenham dados do dispositivo para o seu centro de eventos ou fila de autocarros de serviço ou tópico em tempo real.

Para o armazenamento blob, um novo instantâneo contendo todas as alterações desde a última escrita é exportado uma vez por minuto.

A seguinte mensagem de exemplo mostra informações sobre dispositivos e dados de propriedades num centro de eventos ou fila de autocarros de serviço ou tópico:

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

Esta imagem é um exemplo que mostra os dados de dispositivos e propriedades no armazenamento blob. Os ficheiros exportados contêm uma única linha por registo.

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

Cada registo de mensagens ou instantâneos representa uma ou mais alterações num modelo de dispositivo publicado desde a última mensagem exportada. As informações enviadas em cada mensagem ou registo incluem:

- `id` do modelo do dispositivo que corresponde ao `instanceOf` fluxo dos dispositivos acima
- `displayName` do modelo de dispositivo
- O dispositivo `capabilityModel` incluindo a sua `interfaces` , e a telemetria, propriedades e definições de comandos
- `cloudProperties` definições
- Sobreposições e valores iniciais, em linha com o `capabilityModel`

Os modelos de dispositivos eliminados não são exportados. Atualmente, não existem indicadores em mensagens exportadas para modelos de dispositivos eliminados.

Para os Centros de Eventos e Autocarro de Serviço, a IoT Central envia mensagens que contenham dados do modelo do dispositivo para o seu centro de eventos ou fila de autocarros de serviço ou tópico em tempo real.

Para o armazenamento blob, um novo instantâneo contendo todas as alterações desde a última escrita é exportado uma vez por minuto.

Este exemplo mostra uma mensagem sobre os dados dos modelos do dispositivo no centro de eventos ou na fila ou tópico do Service Bus:

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

Este instantâneo de exemplo mostra uma mensagem que contém dados de dispositivos e propriedades no armazenamento blob. Os ficheiros exportados contêm uma única linha por registo.

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

## <a name="data-format-change-notice"></a>Aviso de alteração do formato de dados

> [!Note]
> O formato de dados de fluxo de telemetria não é afetado por esta alteração. Apenas os dispositivos e modelos de dispositivos fluxos de dados são afetados.

Se tiver uma exportação de dados existente na sua aplicação de pré-visualização com os *fluxos de modelos* *de dispositivos* e dispositivos ligados, atualize a sua exportação até **30 de junho de 2020**. Este requisito aplica-se às exportações para o armazenamento Azure Blob, Azure Event Hubs e Azure Service Bus.

A partir de 3 de fevereiro de 2020, todas as novas exportações em aplicações com modelos de Dispositivos e Dispositivos ativados terão o formato de dados acima descrito. Todas as exportações criadas antes desta data permanecem no antigo formato de dados até 30 de junho de 2020, altura em que estas exportações serão automaticamente migradas para o novo formato de dados. O novo formato de dados corresponde ao [dispositivo](/rest/api/iotcentral/devices/get), propriedade do [dispositivo,](/rest/api/iotcentral/devices/getproperties) [propriedade da nuvem do dispositivo](/rest/api/iotcentral/devices/getcloudproperties)e objetos de modelo de [dispositivo](/rest/api/iotcentral/devicetemplates/get) na API pública IoT Central.

Para **dispositivos,** diferenças notáveis entre o antigo formato de dados e o novo formato de dados incluem:
- `@id` para o dispositivo é removido, `deviceId` é renomeado para `id` 
- `provisioned` bandeira é adicionada para descrever o estado de provisionamento do dispositivo
- `approved` bandeira é adicionada para descrever o estado de aprovação do dispositivo
- `properties` incluindo propriedades de dispositivos e nuvem, corresponde a entidades na API pública

Para **os modelos do Dispositivo,** diferenças notáveis entre o antigo formato de dados e o novo formato de dados incluem:

- `@id` para o modelo do dispositivo é renomeado para `id`
- `@type` para o modelo do dispositivo é renomeado para `types` , e é agora uma matriz

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Dispositivos (formato precotado a partir de 3 de fevereiro de 2020)

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

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Modelos de dispositivo (formato preterido a partir de 3 de fevereiro de 2020)

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

## <a name="next-steps"></a>Próximos passos

Agora que sabe exportar os seus dados para a Azure Event Hubs, Azure Service Bus e Azure Blob, continue para o próximo passo:

> [!div class="nextstepaction"]
> [Como executar análises personalizadas com Databricks](./howto-create-custom-analytics.md)