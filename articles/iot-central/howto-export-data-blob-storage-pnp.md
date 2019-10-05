---
title: Exportar seus dados para o armazenamento de BLOBs do Azure | Microsoft Docs
description: Como exportar dados de seu aplicativo de IoT Central do Azure para o armazenamento de BLOBs do Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973231"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Exportar seus dados para o armazenamento de BLOBs do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Este tópico se aplica aos administradores do.*

Este artigo descreve como usar o recurso de exportação de dados contínuos no Azure IoT Central para exportar dados periodicamente para sua **conta de armazenamento de BLOBs do Azure** ou **Azure data Lake Storage Gen2 conta de armazenamento**. Você pode exportar os **modelos** de **telemetria**, **dispositivos**e dispositivos para arquivos no formato JSON. Os dados exportados podem ser usados para a análise de caminho frio, como modelos de treinamento em Azure Machine Learning ou análise de tendência de longo prazo no Microsoft Power BI.

> [!Note]
> Ao ativar a exportação de dados contínuas, você obtém apenas os dados desse momento em diante. Atualmente, os dados não podem ser recuperados por um horário quando a exportação de dados contínuas estava desativada. Para manter mais dados históricos, ative a exportação contínua de dados antecipadamente.


## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um administrador em seu aplicativo IoT Central

## <a name="create-storage-account"></a>Criar conta de armazenamento
Se você não tiver um armazenamento existente para exportar para o, siga estas etapas:

1. Crie uma [nova conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode saber mais sobre como criar novas [contas de armazenamento de BLOBs do Azure](https://aka.ms/blobdocscreatestorageaccount) ou [contas de armazenamento Azure data Lake Storage v2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Se você optar por exportar dados para uma conta de armazenamento ADLS v2, deverá escolher **tipo de conta** como **BlobStorage**. 

    > [!Note] 
    > Você pode exportar dados para contas de armazenamento em assinaturas diferentes daquela para seu aplicativo de IoT Central pré-pago. Nesse caso, você se conectará usando uma cadeia de conexão.

2. Crie um contêiner em sua conta de armazenamento. Vá para sua conta de armazenamento. Em **serviço blob**, selecione **procurar BLOBs**. Selecione **+ contêiner** na parte superior para criar um novo contêiner.


## <a name="set-up-continuous-data-export"></a>Configurar a exportação de dados contínuas

Agora que você tem um destino de armazenamento para o qual exportar dados, siga estas etapas para configurar a exportação de dados contínuas. 

1. Entre no seu aplicativo IoT Central.

2. No menu à esquerda, selecione **exportação de dados**.

    > [!Note]
    > Se você não vir a exportação de dados no menu à esquerda, você não é um administrador em seu aplicativo. Converse com um administrador para configurar a exportação de dados.

3. Selecione o botão **+ novo** no canto superior direito. Escolha o **armazenamento de BLOBs do Azure** como o destino de sua exportação. 

    > [!NOTE] 
    > O número máximo de exportações por aplicativo é cinco. 

    ![Criar nova exportação de dados contínuas](media/howto-export-data-pnp/export-new2.png)

4. Na caixa de listagem suspensa, selecione o namespace da **conta de armazenamento**. Você também pode escolher a última opção na lista que é **Inserir uma cadeia de conexão**. 

    > [!NOTE] 
    > Você só verá namespaces de contas de armazenamento na **mesma assinatura que seu aplicativo IOT central**. Se você quiser exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e consulte a etapa 5.

    > [!NOTE] 
    > Para aplicativos de avaliação de 7 dias, a única maneira de configurar a exportação de dados contínuas é por meio de uma cadeia de conexão. Isso ocorre porque os aplicativos de avaliação de 7 dias não têm uma assinatura do Azure associada.

    ![Criar nova exportação para o blob](media/howto-export-data-pnp/export-create-blob2.png)

5. Adicional Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para sua conta de armazenamento, vá para a conta de armazenamento no portal do Azure:
    - Em **configurações**, selecione **chaves de acesso**
    - Copie a cadeia de conexão key1 ou a cadeia de conexão Key2
 
6. Escolha um contêiner na caixa de listagem suspensa. Se você não tiver um contêiner, vá para sua conta de armazenamento no portal do Azure:
    - Em **serviço blob**, selecione **BLOBs**. Clique em **+ contêiner** e dê um nome ao seu contêiner. Escolha um nível de acesso público para seus dados (qualquer um funcionará com a exportação de dados contínua). Saiba mais nos [documentos do armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7.  Em **dados a serem exportados**, especifique cada tipo de dados a serem exportados definindo o tipo como **ativado**.
   
    > [!NOTE] 
    > Os dados são exportados no formato JSON.

8. Para ativar a exportação de dados contínuas, verifique se a alternância de **exportação de dados** está ativada. Selecione **Guardar**.

   ![Configurar exportação de dados contínuas](media/howto-export-data-pnp/export-list-blob2.png)

9. Depois de alguns minutos, seus dados aparecerão na sua conta de armazenamento.


## <a name="path-structure"></a>Estrutura do caminho

Os dados de telemetria, dispositivos e modelos de dispositivo são exportados para sua conta de armazenamento uma vez por minuto, com cada arquivo que contém o lote de alterações desde o último arquivo exportado. Os dados exportados são colocados em três pastas no formato JSON. Os caminhos padrão em sua conta de armazenamento são:
- Telemetria: {Container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Dispositivos: {Container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Modelos de dispositivo: {Container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

Você pode procurar os arquivos exportados no portal do Azure navegando até o arquivo e escolhendo a guia **Editar blob** .

## <a name="data-format"></a>Formato de dados
### <a name="telemetry"></a>Telemetria

Os dados de telemetria exportados têm todas as novas mensagens recebidas por IoT Central de todos os dispositivos durante esse tempo. Os arquivos exportados usam o mesmo formato que os arquivos de mensagem exportados pelo [Roteamento de mensagens do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) para o armazenamento de BLOBs.

> [!NOTE]
> Verifique se os dispositivos estão enviando mensagens com `contentType: application/JSON` e `contentEncoding:utf-8` (ou `utf-16`, `utf-32`). Consulte a [documentação do Hub IOT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) para obter um exemplo.

> [!NOTE]
> Os dispositivos que enviam a telemetria são representados por IDs de dispositivo (consulte as seções a seguir). Para obter os nomes dos dispositivos, exporte os instantâneos do dispositivo. Correlacione cada registro de mensagem usando o **connectionDeviceId** que corresponde ao **DeviceID** do registro do dispositivo.

O exemplo a seguir mostra um registro no formato JSON.

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

### <a name="devices"></a>Dispositivos

Quando a exportação de dados contínuas é ativada pela primeira vez, um único instantâneo com todos os dispositivos é exportado. Cada dispositivo inclui:
- `@id` do dispositivo no IoT Central
- `name` do dispositivo
- `deviceId` do [serviço de provisionamento de dispositivos](https://aka.ms/iotcentraldocsdps)
- Informações do modelo do dispositivo
- Valores de propriedade

Um novo instantâneo é gravado uma vez por minuto. O instantâneo inclui:

- Novos dispositivos adicionados desde o último instantâneo.
- Dispositivos com valores de propriedade alterados desde o último instantâneo.

> [!NOTE]
> Dispositivos excluídos desde o último instantâneo não são exportados. Atualmente, os instantâneos não têm indicadores para dispositivos excluídos.
>
> O modelo de dispositivo ao qual cada dispositivo pertence é representado pelo campo `instanceOf`. Para obter o nome do modelo de dispositivo, exporte os instantâneos de modelo de dispositivo.

Os arquivos exportados contêm uma única linha por registro. O exemplo a seguir mostra um registro no formato JSON.

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

### <a name="device-templates"></a>Modelos do dispositivo

Quando a exportação de dados contínuas é ativada pela primeira vez, um único instantâneo com todos os modelos de dispositivo é exportado. Cada modelo de dispositivo inclui:
- `@id` do modelo de dispositivo
- `name` do modelo de dispositivo
- `version` do modelo de dispositivo
- O dispositivo `capabilityModel` incluindo suas definições de `interfaces` e telemetria, propriedades e comandos
- definições de `cloudProperties`
- Substituições e valores iniciais, embutidos com o `capabilityModel`

Um novo instantâneo é gravado uma vez por minuto. O instantâneo inclui:

- Novos modelos de dispositivo adicionados desde o último instantâneo. Isso inclui novas versões de modelos de dispositivo.
- Modelos de dispositivo com substituições alteradas, valores iniciais e definições de propriedades de nuvem desde o último instantâneo.

> [!NOTE]
> Modelos de dispositivo excluídos desde o último instantâneo não são exportados. Atualmente, os instantâneos não têm indicadores para modelos de dispositivo excluídos.

Os arquivos exportados contêm uma única linha por registro. O exemplo a seguir mostra um registro no formato JSON.

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

Agora que você sabe como exportar seus dados, continue para a próxima etapa:

> [!div class="nextstepaction"]
> [Como usar a ponte de dispositivo IoT Central para conectar outras nuvens IoT](howto-build-iotc-device-bridge.md)
