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
ms.openlocfilehash: e6df6a1f751106f62cdfecc3a7b5efb0fe4c63bf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876003"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportar seus dados no Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

*Este tópico se aplica aos administradores do.*

Este artigo descreve como usar o recurso de exportação de dados contínuos no Azure IoT Central para exportar seus dados para seus próprios **hubs de eventos do Azure**e instâncias **do barramento de serviço do Azure** . Você pode exportar **medidas**, **dispositivos**e **modelos de dispositivo** para seu próprio destino para análises e insights de caminho quentes. Isso inclui disparar regras personalizadas no Azure Stream Analytics, disparar fluxos de trabalho personalizados em aplicativos lógicos do Azure ou transformar os dados e passá-los por meio de Azure Functions. 

> [!Note]
> Mais uma vez, ao ativar a exportação de dados contínuas, você obtém apenas os dados desse momento em diante. Atualmente, os dados não podem ser recuperados por um horário quando a exportação de dados contínuas estava desativada. Para manter mais dados históricos, ative a exportação contínua de dados antecipadamente.


## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um administrador em seu aplicativo IoT Central

## <a name="set-up-export-destination"></a>Configurar destino de exportação

Se você não tiver um hub de eventos/barramento de serviço existente para exportar para o, siga estas etapas:

## <a name="create-event-hubs-namespace"></a>Criar namespace de hubs de eventos

1. Crie um [novo namespace de hubs de eventos na portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Você pode aprender mais em [documentos dos hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Escolha uma assinatura. 

    > [!Note] 
    > Agora, você pode exportar dados para outras assinaturas que não são **iguais** às do seu aplicativo de IOT central pago conforme o uso. Nesse caso, você se conectará usando uma cadeia de conexão.
3. Crie um hub de eventos em seu namespace de hubs de eventos. Vá para o namespace e selecione **+ Hub de eventos** na parte superior para criar uma instância do hub de eventos.

## <a name="create-service-bus-namespace"></a>Criar namespace do barramento de serviço

1. Crie um [novo namespace do barramento de serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) . Você pode aprender mais nos [documentos do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Escolha uma assinatura. 

    > [!Note] 
    > Agora, você pode exportar dados para outras assinaturas que não são **iguais** às do seu aplicativo de IOT central pago conforme o uso. Nesse caso, você se conectará usando uma cadeia de conexão.

3. Acesse o namespace do barramento de serviço e selecione **+ fila** ou **+ tópico** na parte superior para criar uma fila ou um tópico para o qual exportar.


## <a name="set-up-continuous-data-export"></a>Configurar a exportação de dados contínuas

Agora que você tem um destino de barramento de serviço/hubs de eventos para exportar dados para o, siga estas etapas para configurar a exportação de dados contínuas. 

1. Entre no seu aplicativo IoT Central.

2. No menu à esquerda, selecione **exportação de dados contínuas**.

    > [!Note]
    > Se você não vir a exportação de dados contínua no menu à esquerda, você não é um administrador em seu aplicativo. Converse com um administrador para configurar a exportação de dados.

    ![Criar novo hub de eventos CDE](media/howto-export-data/export_menu1.png)

3. Selecione o botão **+ novo** no canto superior direito. Escolha um dos **hubs de eventos do Azure** ou o **barramento de serviço do Azure** como o destino de sua exportação. 

    > [!NOTE] 
    > O número máximo de exportações por aplicativo é cinco. 

    ![Criar nova exportação de dados contínuas](media/howto-export-data/export_new1.png)

4. Na caixa de listagem suspensa, selecione o namespace do seu namespace de **hubs de eventos/barramento de serviço**. Você também pode escolher a última opção na lista que é **Inserir uma cadeia de conexão**. 

    > [!NOTE] 
    > Você só verá namespaces de contas de armazenamento/hubs de eventos/namespaces do barramento de serviço na **mesma assinatura que seu aplicativo IOT central**. Se você quiser exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e consulte a etapa 5.

    > [!NOTE] 
    > Para aplicativos de avaliação de 7 dias, a única maneira de configurar a exportação de dados contínuas é por meio de uma cadeia de conexão. Isso ocorre porque os aplicativos de avaliação de 7 dias não têm uma assinatura do Azure associada.

    ![Criar novo hub de eventos CDE](media/howto-export-data/export_create1.png)

5. Adicional Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para seu:
    - Hubs de eventos ou barramento de serviço, acesse o namespace na portal do Azure.
        - Em **configurações**, selecione **políticas de acesso compartilhado**
        - Escolha o **RootManageSharedAccessKey** padrão ou crie um novo
        - Copie a cadeia de conexão primária ou secundária
 
6. Escolha um hub de eventos/fila ou tópico na caixa de listagem suspensa.

7. Em **dados a serem**exportados, especifique cada tipo de dados a serem exportados definindo o tipo como **ativado**.

6. Para ativar a exportação de dados contínuas, verifique se a **exportação de dados** está **ativada**. Selecione **Guardar**.

    ![Configurar exportação de dados contínuas](media/howto-export-data/export_list1.png)

7. Depois de alguns minutos, seus dados aparecerão no destino escolhido.


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Exportar para os hubs de eventos do Azure e o barramento de serviço do Azure

Os dados de medidas, dispositivos e modelos de dispositivo são exportados para o Hub de eventos ou a fila ou tópico do barramento de serviço quase em tempo real. Os dados de medidas exportados contêm a totalidade da mensagem que seus dispositivos enviaram para IoT Central, não apenas os valores das próprias medidas. Dados de dispositivos exportados contém alterações nas propriedades e configurações de todos os dispositivos, e os modelos de dispositivo exportados contêm alterações em todos os modelos de dispositivo. Os dados exportados estão na propriedade "corpo" e estão no formato JSON.

> [!NOTE]
> Ao escolher um barramento de serviço como um destino de exportação, as filas e os tópicos **não devem ter sessões ou detecção de duplicidades habilitadas**. Se qualquer uma dessas opções estiver habilitada, algumas mensagens não chegarão em sua fila ou tópico.

### <a name="measurements"></a>Medições

Uma nova mensagem é exportada rapidamente depois que IoT Central recebe a mensagem de um dispositivo. Cada mensagem exportada nos hubs de eventos e no barramento de serviço contém a mensagem completa que o dispositivo enviou na propriedade "Body" no formato JSON. 

> [!NOTE]
> Os dispositivos que enviam as medidas são representados por IDs de dispositivo (consulte as seções a seguir). Para obter os nomes dos dispositivos, exporte os dados do dispositivo e correlacione cada mensagem usando o **connectionDeviceId** que corresponde à DeviceID da mensagem do dispositivo.

O exemplo a seguir mostra uma mensagem sobre os dados de medidas recebidos no Hub de eventos ou na fila ou no tópico do barramento de serviço.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Dispositivos

As mensagens que contêm dados do dispositivo são enviadas ao seu hub de eventos ou à fila ou ao tópico do barramento de serviço uma vez a cada poucos minutos. Isso significa que, a cada poucos minutos, um lote de mensagens será enviado com dados sobre
- Novos dispositivos que foram adicionados
- Dispositivos com propriedade alterada e valores de configuração

Cada mensagem representa uma ou mais alterações em um dispositivo desde a última mensagem exportada. As informações que serão enviadas em cada mensagem incluirão:
- `id`do dispositivo no IoT Central
- `name`do dispositivo
- `deviceId`do [serviço de provisionamento de dispositivos](https://aka.ms/iotcentraldocsdps)
- Informações do modelo do dispositivo
- Valores de propriedade
- Definindo valores

> [!NOTE]
> Dispositivos excluídos desde o último lote não são exportados. No momento, não há indicadores em mensagens exportadas para dispositivos excluídos.
>
> O modelo de dispositivo ao qual cada dispositivo pertence é representado por uma ID de modelo de dispositivo. Para obter o nome do modelo de dispositivo, certifique-se de exportar também os dados do modelo de dispositivo.

O exemplo a seguir mostra uma mensagem sobre os dados do dispositivo no Hub de eventos ou na fila ou no tópico do barramento de serviço:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Modelos de dispositivo

As mensagens que contêm os dados dos modelos de dispositivo são enviadas para o Hub de eventos ou para a fila ou tópico do barramento de serviço uma vez a cada poucos minutos. Isso significa que, a cada poucos minutos, um lote de mensagens será enviado com dados sobre
- Novos modelos de dispositivo que foram adicionados
- Modelos de dispositivo com medidas alteradas, propriedade e definições de configuração

Cada mensagem representa uma ou mais alterações em um modelo de dispositivo desde a última mensagem exportada. As informações que serão enviadas em cada mensagem incluirão:
- `id`do modelo de dispositivo
- `name`do modelo de dispositivo
- `version`do modelo de dispositivo
- Tipos de dados de medição e valores mínimo/máximo
- Tipos de dados de propriedade e valores padrão
- Definindo tipos de dados e valores padrão

> [!NOTE]
> Modelos de dispositivo excluídos desde o último lote não são exportados. No momento, não há indicadores em mensagens exportadas para modelos de dispositivo excluídos.

O exemplo a seguir mostra uma mensagem sobre os dados de modelos de dispositivo no Hub de eventos ou na fila ou no tópico do barramento de serviço:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que você sabe como exportar seus dados para os hubs de eventos do Azure e o barramento de serviço do Azure, vá para a próxima etapa:

> [!div class="nextstepaction"]
> [Como disparar Azure Functions](howto-trigger-azure-functions.md)
