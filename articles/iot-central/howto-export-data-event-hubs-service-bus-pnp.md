---
title: Exportar seus dados para os hubs de eventos do Azure e o barramento de serviço do Azure | Microsoft Docs
description: Como exportar dados de seu aplicativo de IoT Central do Azure para os hubs de eventos do Azure e o barramento de serviço do Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: fed9c924274cb66671e233a7dc6d431d81e0dbfb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973172"
---
# <a name="export-your-data-in-azure-iot-central-preview-features"></a>Exportar seus dados no Azure IoT Central (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Este tópico se aplica aos administradores do.*

Este artigo descreve como usar o recurso de exportação de dados contínuos no Azure IoT Central para exportar seus dados para seus próprios **hubs de eventos do Azure**e instâncias **do barramento de serviço do Azure** . Você pode exportar os **modelos** de **telemetria**, **dispositivos**e dispositivos para seu próprio destino para análises e insights de caminho quentes. Isso inclui o disparo de regras personalizadas no Azure Stream Analytics, o disparo de fluxos de trabalho personalizados em aplicativos lógicos do Azure ou a passagem por Azure Functions ser transformada.

> [!Note]
> Mais uma vez, ao ativar a exportação de dados contínuas, você obtém apenas os dados desse momento em diante. Atualmente, os dados não podem ser recuperados por um horário quando a exportação de dados contínuas estava desativada. Para manter mais dados históricos, ative a exportação contínua de dados antecipadamente.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um administrador em seu aplicativo IoT Central

## <a name="set-up-export-destination"></a>Configurar destino de exportação

Se você não tiver um hub de eventos/barramento de serviço existente para exportar para o, siga estas etapas para criar um:

### <a name="create-event-hubs-namespace"></a>Criar namespace de hubs de eventos

1. Crie um [novo namespace de hubs de eventos na portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Você pode aprender mais em [documentos dos hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Escolha uma assinatura. 

    > [!Note] 
    > Agora, você pode exportar dados para outras assinaturas que não são **iguais** às do seu aplicativo de IOT central pago conforme o uso. Nesse caso, você se conectará usando uma cadeia de conexão.
3. Crie um hub de eventos em seu namespace de hubs de eventos. Vá para o namespace e selecione **+ Hub de eventos** na parte superior para criar uma instância do hub de eventos.

### <a name="create-service-bus-namespace"></a>Criar namespace do barramento de serviço

1. Crie um [novo namespace do barramento de serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Você pode aprender mais nos [documentos do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Escolha uma assinatura. 

    > [!Note] 
    > Agora, você pode exportar dados para outras assinaturas que não são **iguais** às do seu aplicativo de IOT central pago conforme o uso. Nesse caso, você se conectará usando uma cadeia de conexão.

3. Acesse o namespace do barramento de serviço e selecione **+ fila** ou **+ tópico** na parte superior para criar uma fila ou um tópico para o qual exportar.


## <a name="set-up-continuous-data-export"></a>Configurar a exportação de dados contínuas

Agora que você tem um destino de barramento de serviço/hubs de eventos para exportar dados para o, siga estas etapas para configurar a exportação de dados contínuas. 

1. Entre no seu aplicativo IoT Central.

2. No menu à esquerda, selecione **exportação de dados**.

    > [!Note]
    > Se você não vir a exportação de dados no menu à esquerda, você não é um administrador em seu aplicativo. Converse com um administrador para configurar a exportação de dados.

3. Selecione o botão **+ novo** no canto superior direito. Escolha um dos **hubs de eventos do Azure** ou o **barramento de serviço do Azure** como o destino de sua exportação. 

    > [!NOTE] 
    > O número máximo de exportações por aplicativo é cinco. 

    ![Criar nova exportação de dados contínuas](media/howto-export-data-pnp/export-new2.png)

4. Na caixa de listagem suspensa, selecione o namespace do seu namespace de **hubs de eventos/barramento de serviço**. Você também pode escolher a última opção na lista que é **Inserir uma cadeia de conexão**. 

    > [!NOTE] 
    > Você só verá namespaces de contas de armazenamento/hubs de eventos/namespaces do barramento de serviço na **mesma assinatura que seu aplicativo IOT central**. Se você quiser exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e consulte a etapa 5.

    > [!NOTE] 
    > Para aplicativos de avaliação de 7 dias, a única maneira de configurar a exportação de dados contínuas é por meio de uma cadeia de conexão. Isso ocorre porque os aplicativos de avaliação de 7 dias não têm uma assinatura do Azure associada.

    ![Criar novo hub de eventos CDE](media/howto-export-data-pnp/export-eh.png)

5. Adicional Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para seu:
    - Hubs de eventos ou barramento de serviço, acesse o namespace na portal do Azure.
        - Em **configurações**, selecione **políticas de acesso compartilhado**
        - Escolha o **RootManageSharedAccessKey** padrão ou crie um novo
        - Copie a cadeia de conexão primária ou secundária

6. Escolha um hub de eventos/fila ou tópico na caixa de listagem suspensa.

7. Em **dados a serem exportados**, especifique cada tipo de dados a serem exportados definindo o tipo como **ativado**.

8. Para ativar a exportação de dados contínuas, verifique se a alternância de **exportação de dados** está **ativada**. Selecione **Guardar**.

9. Depois de alguns minutos, seus dados aparecerão no destino escolhido.


## <a name="data-format"></a>Formato de dados

Os dados de telemetria, dispositivos e modelos de dispositivo são exportados para o Hub de eventos ou a fila ou tópico do barramento de serviço quase em tempo real. Os dados de telemetria exportados contêm todo o valor da mensagem que seus dispositivos enviaram para IoT Central, não apenas os próprios valores de telemetria. Dados de dispositivos exportados contém alterações em Propriedades e metadados de todos os dispositivos, e os modelos de dispositivo exportados contêm alterações em todos os modelos de dispositivo. Os dados exportados estão na propriedade "corpo" e estão no formato JSON.

> [!NOTE]
> Ao escolher um barramento de serviço como um destino de exportação, as filas e os tópicos **não devem ter sessões ou detecção de duplicidades habilitadas**. Se qualquer uma dessas opções estiver habilitada, algumas mensagens não chegarão em sua fila ou tópico.

### <a name="telemetry"></a>Telemetria

Uma nova mensagem é exportada rapidamente depois que IoT Central recebe a mensagem de um dispositivo. Cada mensagem exportada nos hubs de eventos e no barramento de serviço contém a mensagem completa que o dispositivo enviou na propriedade "Body" no formato JSON. 

> [!NOTE]
> Os dispositivos que enviam a telemetria são representados por IDs de dispositivo (consulte as seções a seguir). Para obter os nomes dos dispositivos, exporte os dados do dispositivo e correlacione cada mensagem usando o **connectionDeviceId** que corresponde à **DeviceID** da mensagem do dispositivo.

O exemplo a seguir mostra uma mensagem sobre dados de telemetria recebidos no Hub de eventos ou na fila ou no tópico do barramento de serviço.

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

### <a name="devices"></a>Dispositivos

As mensagens que contêm dados do dispositivo são enviadas ao seu hub de eventos ou à fila ou ao tópico do barramento de serviço uma vez a cada poucos minutos. Isso significa que, a cada poucos minutos, um lote de mensagens será enviado com dados sobre
- Novos dispositivos que foram adicionados
- Dispositivos com valores de propriedade alterados

Cada mensagem representa uma ou mais alterações em um dispositivo desde a última mensagem exportada. As informações que serão enviadas em cada mensagem incluirão:
- `@id` do dispositivo no IoT Central
- `name` do dispositivo
- `deviceId` do [serviço de provisionamento de dispositivos](https://aka.ms/iotcentraldocsdps)
- Informações do modelo do dispositivo
- Valores de propriedade

> [!NOTE]
> Dispositivos excluídos desde o último lote não são exportados. No momento, não há indicadores em mensagens exportadas para dispositivos excluídos.
>
> O modelo de dispositivo ao qual cada dispositivo pertence é representado por uma ID de modelo de dispositivo. Para obter o nome do modelo de dispositivo, certifique-se de exportar também os dados do modelo de dispositivo.

O exemplo a seguir mostra uma mensagem sobre os dados do dispositivo no Hub de eventos ou na fila ou no tópico do barramento de serviço:


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

### <a name="device-templates"></a>Modelos do dispositivo

As mensagens que contêm os dados dos modelos de dispositivo são enviadas para o Hub de eventos ou para a fila ou tópico do barramento de serviço uma vez a cada poucos minutos. Isso significa que, a cada poucos minutos, um lote de mensagens será enviado com dados sobre
- Novos modelos de dispositivo que foram adicionados ou com controle de versão
- Modelos de dispositivo com capabilityModels, cloudproperties, substituições e valores iniciais alterados

Cada mensagem representa uma ou mais alterações em um modelo de dispositivo desde a última mensagem exportada. As informações que serão enviadas em cada mensagem incluirão:
- `@id` do modelo de dispositivo
- `name` do modelo de dispositivo
- `version` do modelo de dispositivo
- O dispositivo `capabilityModel` incluindo suas definições de `interfaces` e telemetria, propriedades e comandos
- definições de `cloudProperties`
- Substituições e valores iniciais, embutidos com o `capabilityModel`

> [!NOTE]
> Modelos de dispositivo excluídos desde o último lote não são exportados. No momento, não há indicadores em mensagens exportadas para modelos de dispositivo excluídos.

O exemplo a seguir mostra uma mensagem de modelo de dispositivo no Hub de eventos ou na fila ou no tópico do barramento de serviço:

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

## <a name="next-steps"></a>Passos seguintes

Agora que você sabe como exportar seus dados para os hubs de eventos do Azure e o barramento de serviço do Azure, vá para a próxima etapa:

> [!div class="nextstepaction"]
> [Como disparar Azure Functions](howto-trigger-azure-functions.md)
