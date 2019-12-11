---
title: Exportar seus dados de IoT Central do Azure | Microsoft Docs
description: Como exportar dados de seu aplicativo de IoT Central do Azure para os hubs de eventos do Azure, o barramento de serviço do Azure e o armazenamento de BLOBs do Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 1aac5af916e414178676a1caf42fead41109de68
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974466"
---
# <a name="export-your-azure-iot-central-data-preview-features"></a>Exportar seus dados de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Este tópico se aplica aos administradores do.*

Este artigo descreve como usar o recurso de exportação de dados contínuos no Azure IoT Central para exportar seus dados para os **hubs de eventos do Azure**, o **barramento de serviço do Azure**ou as instâncias de **armazenamento de BLOBs do Azure** . Os dados são exportados no formato JSON e podem incluir telemetria, informações do dispositivo e informações do modelo do dispositivo. Use os dados exportados para:

- Análise e insights de caminho quentes. Essa opção inclui o disparo de regras personalizadas no Azure Stream Analytics, o disparo de fluxos de trabalho personalizados em aplicativos lógicos do Azure ou a passagem por Azure Functions ser transformada.
- Análise de caminho frio, como modelos de treinamento em Azure Machine Learning ou análise de tendência de longo prazo no Microsoft Power BI.

> [!Note]
> Ao ativar a exportação de dados contínuas, você obtém apenas os dados desse momento em diante. Atualmente, os dados não podem ser recuperados por um horário quando a exportação de dados contínuas estava desativada. Para manter mais dados históricos, ative a exportação contínua de dados antecipadamente.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ser um administrador em seu aplicativo IoT Central

## <a name="set-up-export-destination"></a>Configurar destino de exportação

O destino de exportação deve existir antes de você configurar a exportação de dados contínuas.

### <a name="create-event-hubs-namespace"></a>Criar o espaço de nomes dos Hubs de Eventos

Se você não tiver um namespace de hubs de eventos existente para exportar para o, siga estas etapas:

1. Crie um [novo namespace de hubs de eventos na portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Você pode aprender mais em [documentos dos hubs de eventos do Azure](../../event-hubs/event-hubs-create.md).

2. Escolha uma subscrição. Você pode exportar dados para outras assinaturas que não estão na mesma assinatura que o seu aplicativo de IoT Central pago pelo uso. Você se conecta usando uma cadeia de conexão nesse caso.

3. Crie um hub de eventos em seu namespace de hubs de eventos. Vá para o namespace e selecione **+ Hub de eventos** na parte superior para criar uma instância do hub de eventos.

### <a name="create-service-bus-namespace"></a>Criar namespace do barramento de serviço

Se você não tiver um namespace do barramento de serviço existente para exportar para o, siga estas etapas:

1. Crie um [novo namespace do barramento de serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Você pode aprender mais nos [documentos do barramento de serviço do Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Escolha uma subscrição. Você pode exportar dados para outras assinaturas que não estão na mesma assinatura que o seu aplicativo de IoT Central pago pelo uso. Você se conecta usando uma cadeia de conexão nesse caso.

3. Acesse o namespace do barramento de serviço e selecione **+ fila** ou **+ tópico** na parte superior para criar uma fila ou um tópico para o qual exportar.

Quando você escolhe o barramento de serviço como um destino de exportação, as filas e os tópicos não devem ter sessões ou detecção de duplicidades habilitadas. Se qualquer uma dessas opções estiver habilitada, algumas mensagens não chegarão em sua fila ou tópico.

### <a name="create-storage-account"></a>Criar conta de armazenamento

Se você não tiver uma conta de armazenamento do Azure existente para a qual exportar, siga estas etapas:

1. Crie uma [nova conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode saber mais sobre como criar novas [contas de armazenamento de BLOBs do Azure](https://aka.ms/blobdocscreatestorageaccount) ou [contas de armazenamento Azure data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md).

    - Se você optar por exportar dados para uma conta de armazenamento Azure Data Lake Storage v2, deverá escolher **BlobStorage** como o **tipo de conta**.
    - Você pode exportar dados para contas de armazenamento em assinaturas diferentes daquela para seu aplicativo de IoT Central pré-pago. Nesse caso, você se conectará usando uma cadeia de conexão.

2. Crie um contêiner em sua conta de armazenamento. Vá para sua conta de armazenamento. Em **serviço blob**, selecione **procurar BLOBs**. Selecione **+ contêiner** na parte superior para criar um novo contêiner.

## <a name="set-up-continuous-data-export"></a>Configurar a exportação de dados contínuas

Agora que você tem um destino para exportar dados para o, siga estas etapas para configurar a exportação de dados contínuas.

1. Entre no seu aplicativo IoT Central.

2. No painel esquerdo, selecione **exportação de dados**.

    > [!Note]
    > Se você não vir a exportação de dados no painel esquerdo, você não é um administrador em seu aplicativo. Converse com um administrador para configurar a exportação de dados.

3. Selecione o botão **+ novo** no canto superior direito. Escolha um dos **hubs de eventos**do Azure, o **barramento de serviço do Azure**ou o **armazenamento de BLOBs do Azure** como o destino de sua exportação. O número máximo de exportações por aplicativo é cinco.

    ![Criar nova exportação de dados contínuas](media/howto-export-data/export-new2.png)

4. Na caixa de listagem suspensa, selecione seu namespace de **hubs de eventos**, **namespace do barramento de serviço**, namespace da conta de **armazenamento**ou **Insira uma cadeia de conexão**.

    - Você só vê contas de armazenamento, namespaces de hubs de eventos e namespaces do barramento de serviço na mesma assinatura que seu aplicativo IoT Central. Se você quiser exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e consulte a etapa 5.
    - Para aplicativos de avaliação de sete dias, a única maneira de configurar a exportação de dados contínuas é por meio de uma cadeia de conexão. Os aplicativos de avaliação de sete dias não têm uma assinatura do Azure associada.

    ![Criar novo hub de eventos](media/howto-export-data/export-eh.png)

5. Adicional Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para seu:
    - Hubs de eventos ou barramento de serviço, acesse o namespace na portal do Azure.
        - Em **configurações**, selecione **políticas de acesso compartilhado**
        - Escolha o **RootManageSharedAccessKey** padrão ou crie um novo
        - Copie a cadeia de conexão primária ou secundária
    - Conta de armazenamento, vá para a conta de armazenamento no portal do Azure:
        - Em **configurações**, selecione **chaves de acesso**
        - Copie a cadeia de conexão key1 ou a cadeia de conexão Key2

6. Escolha um hub de eventos, uma fila, um tópico ou um contêiner na caixa de listagem suspensa.

7. Em **dados a serem exportados**, escolha os tipos de dados a serem exportados definindo o tipo como **ativado**.

8. Para ativar a exportação de dados contínuas, verifique se a alternância de **exportação de dados** está **ativada**. Selecione **Guardar**.

9. Depois de alguns minutos, seus dados aparecerão no destino escolhido.

## <a name="export-contents-and-format"></a>Exportar conteúdo e formato

Os dados de telemetria exportados contêm todo o valor da mensagem que seus dispositivos enviaram para IoT Central, não apenas os próprios valores de telemetria. Dados de dispositivos exportados contém alterações em Propriedades e metadados de todos os dispositivos, e os modelos de dispositivo exportados contêm alterações em todos os modelos de dispositivo.

Para os hubs de eventos e o barramento de serviço, os dados são exportados quase em tempo real. Os dados residem na Propriedade Body e estão no formato JSON (veja abaixo exemplos).

Para o armazenamento de BLOBs, os dados são exportados uma vez por minuto, com cada arquivo que contém o lote de alterações desde o último arquivo exportado. Os dados exportados são colocados em três pastas no formato JSON. Os caminhos padrão em sua conta de armazenamento são:

- Telemetria: _{container}/{app-ID}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Dispositivos: _{container}/{app-ID}/Devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Modelos de dispositivo: _{container}/{app-ID}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Você pode procurar os arquivos exportados no portal do Azure navegando até o arquivo e escolhendo a guia **Editar blob** .


## <a name="telemetry"></a>Telemetria

Para hubs de eventos e barramento de serviço, uma nova mensagem é exportada rapidamente depois que IoT Central recebe a mensagem de um dispositivo e cada mensagem exportada contém a mensagem completa que o dispositivo enviou na Propriedade Body no formato JSON.

Para o armazenamento de BLOBs, as mensagens são agrupadas em lote e exportadas uma vez por minuto. Os arquivos exportados usam o mesmo formato que os arquivos de mensagem exportados pelo [Roteamento de mensagens do Hub IOT](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md) para o armazenamento de BLOBs. 

> [!NOTE]
> Para o armazenamento de BLOBs, verifique se os dispositivos estão enviando mensagens com `contentType: application/JSON` e `contentEncoding:utf-8` (ou `utf-16``utf-32`). Consulte a [documentação do Hub IOT](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) para obter um exemplo.

O dispositivo que enviou a telemetria é representado pela ID do dispositivo (consulte as seções a seguir). Para obter os nomes dos dispositivos, exporte os dados do dispositivo e correlacione cada mensagem usando o **connectionDeviceId** que corresponde à **DeviceID** da mensagem do dispositivo.

Esta é uma mensagem de exemplo recebida em um hub de eventos ou em uma fila ou tópico do barramento de serviço.

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

Este é um registro de exemplo exportado para o armazenamento de BLOBs:

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

Cada mensagem ou registro em um instantâneo representa uma ou mais alterações em um dispositivo e suas propriedades desde a última mensagem exportada. Isto inclui:

- `@id` do dispositivo no IoT Central
- `name` do dispositivo
- `deviceId` do [serviço de provisionamento de dispositivos](../core/howto-connect-nodejs.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
- Informações do modelo do dispositivo
- Valores de propriedade

O modelo de dispositivo ao qual cada dispositivo pertence é representado pelo `instanceOf`. Para obter o nome e informações adicionais sobre o modelo de dispositivo, certifique-se de exportar também os dados do modelo de dispositivo.

Os dispositivos excluídos não são exportados. No momento, não há indicadores em mensagens exportadas para dispositivos excluídos.

Para os hubs de eventos e o barramento de serviço, as mensagens que contêm os dados do dispositivo são enviadas ao Hub de eventos ou à fila ou ao tópico do barramento de serviço quase em tempo real, como aparece na IoT Central. 

Para o armazenamento de BLOBs, um novo instantâneo que contém todas as alterações desde a última gravação é exportado uma vez por minuto.

Esta é uma mensagem de exemplo sobre dispositivos e dados de propriedades no Hub de eventos ou na fila ou no tópico do barramento de serviço:

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
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
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Este é um instantâneo de exemplo que contém os dados de dispositivos e propriedades no armazenamento de BLOBs. Os arquivos exportados contêm uma única linha por registro.

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

## <a name="device-templates"></a>Modelos do dispositivo

Cada registro de mensagem ou de instantâneo representa uma ou mais alterações em um modelo de dispositivo desde a última mensagem exportada. As informações enviadas em cada mensagem ou registro incluem:

- `@id` do modelo de dispositivo que corresponde à `instanceOf` do fluxo de dispositivos acima
- `name` do modelo de dispositivo
- `version` do modelo de dispositivo
- O dispositivo `capabilityModel` incluindo suas `interfaces`e as definições telemetria, propriedades e comandos
- definições de `cloudProperties`
- Substituições e valores iniciais, embutidos com o `capabilityModel`

Os modelos de dispositivo excluídos não são exportados. No momento, não há indicadores em mensagens exportadas para modelos de dispositivo excluídos.

Para hubs de eventos e barramento de serviço, as mensagens que contêm dados de modelo de dispositivo são enviadas para o Hub de eventos ou a fila ou tópico do barramento de serviço quase em tempo real, como aparece na IoT Central. 

Para o armazenamento de BLOBs, um novo instantâneo que contém todas as alterações desde a última gravação é exportado uma vez por minuto.

Esta é uma mensagem de exemplo sobre dados de modelos de dispositivo no Hub de eventos ou na fila ou no tópico do barramento de serviço:

```json
{
  "body":{
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

Este é um instantâneo de exemplo que contém os dados de dispositivos e propriedades no armazenamento de BLOBs. Os arquivos exportados contêm uma única linha por registro.

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

Agora que você sabe como exportar seus dados para os hubs de eventos do Azure, o barramento de serviço do Azure e o armazenamento de BLOBs do Azure, vá para a próxima etapa:

> [!div class="nextstepaction"]
> [Como disparar Azure Functions](../core/howto-trigger-azure-functions.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
