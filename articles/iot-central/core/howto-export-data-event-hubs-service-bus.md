---
title: Exporte os seus dados para Azure Event Hubs e Azure Service Bus [ Azure Event Hubs] Microsoft Docs
description: Como exportar dados da sua aplicação Azure IoT Central para Azure Event Hubs e Azure Service Bus
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 84539c4cd5e4e1712c28ab70e9afc259e8d6d04b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990067"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exporte os seus dados em Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

*Este tópico aplica-se aos administradores.*

Este artigo descreve como utilizar a funcionalidade contínua de exportação de dados na Azure IoT Central para exportar os seus dados para os seus **próprios Hubs de Eventos Azure**e para as instâncias **do Azure Service Bus.** Pode exportar **medições,** **dispositivos**e **modelos de dispositivos** para o seu próprio destino para insights de caminho quente e análise. Isto inclui desencadear regras personalizadas no Azure Stream Analytics, desencadear fluxos de trabalho personalizados em Aplicações Lógicas Azure, ou transformar os dados e passá-lo através das Funções Azure. 

> [!Note]
> Mais uma vez, quando liga a exportação contínua de dados, obtém apenas os dados a partir desse momento. Atualmente, os dados não podem ser recuperados por um tempo em que a exportação contínua de dados estava desligada. Para reter dados mais históricos, ligue a exportação contínua de dados mais cedo.


## <a name="prerequisites"></a>Pré-requisitos

- Deve ser administrador na sua aplicação IoT Central

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

Se não tiver um Event Hubs/Service Bus existente para exportar, siga estes passos para criar um:

### <a name="create-event-hubs-namespace"></a>Criar espaço de nome sinuosos do Event Hubs

1. Crie um novo espaço de [nome Sem Nome Do Evento Hubs no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.EventHub) Você pode aprender mais em [Azure Event Hubs docs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Escolha uma subscrição. 

    > [!Note] 
    > Agora pode exportar dados para outras subscrições que não são as mesmas da sua aplicação IoT Central padrão. Irá ligar-se utilizando uma corda de ligação neste caso.
3. Crie um centro de eventos no seu espaço de nome Sem Eventos. Vá ao seu espaço de nome e selecione **+ Event Hub** no topo para criar uma instância de centro de eventos.

### <a name="create-service-bus-namespace"></a>Criar espaço de nome de ônibus de serviço

1. Crie um novo espaço de [nome service bus no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) Você pode aprender mais em [docs de ônibus de serviço Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Escolha uma subscrição. 

    > [!Note] 
    > Agora pode exportar dados para outras subscrições que não são as mesmas da sua aplicação IoT Central padrão. Irá ligar-se utilizando uma corda de ligação neste caso.

3. Vá ao seu espaço de nome service Bus e selecione **+ Fila** ou **+ Tópico** no topo para criar uma fila ou tópico para exportar.


## <a name="set-up-continuous-data-export"></a>Configurar a exportação contínua de dados

Agora que tem um destino de ônibus de eventos para exportar dados, siga estes passos para configurar a exportação contínua de dados. 

1. Inscreva-se na sua aplicação IoT Central.

2. No painel esquerdo, selecione **Exportação Contínua de Dados**.

    > [!Note]
    > Se não vir a Exportação contínua de dados no painel esquerdo, não é administrador na sua aplicação. Fale com um administrador para configurar a exportação de dados.

3. Selecione o botão **+ Novo** no topo direito. Escolha um dos Hubs de **Eventos Azure** ou **azure Service Bus** como destino da sua exportação. 

    > [!NOTE] 
    > O número máximo de exportações por app é de cinco. 

    ![Criar novas exportações contínuas de dados](media/howto-export-data/export-new2.png)

4. Na caixa de lista seletiva, selecione o seu espaço de **nome Sem Nome Do Event Hubs/Espaço**de nome do Autocarro de Serviço . Também pode escolher a última opção na lista que é Introduzir uma cadeia de **ligação**. 

    > [!NOTE] 
    > Só verá As Contas de Armazenamento/Hubs de Eventos nomeespaços/espaços de nome sacar de ônibus de serviço na **mesma subscrição que a sua aplicação IoT Central**. Se quiser exportar para um destino fora desta subscrição, escolha Entrar numa corda de **ligação** e ver o passo 5.

    > [!NOTE] 
    > Para aplicações de teste de 7 dias, a única maneira de configurar a exportação contínua de dados é através de uma cadeia de ligação. Isto porque as aplicações de teste de 7 dias não têm uma subscrição azure associada.

    ![Criar novo Cde Event Hub](media/howto-export-data/export-eh.png)

5. (Opcional) Se escolher uma corda de **ligação,** aparece uma nova caixa para colar a corda de ligação. Para obter a corda de ligação para o seu:
    - Event Hubs ou Service Bus, vá ao espaço de nome no portal Azure.
        - Em **Definições,** selecione **Políticas de Acesso Partilhado**
        - Escolha o **RootManageSharedAccessKey** padrão ou crie um novo
        - Copiar a cadeia de ligação primária ou secundária
 
6. Escolha um hub/fila de eventos ou tópico na caixa de lista saltada.

7. Em **Dados a exportar,** especifique cada tipo de dados a exportar, definindo o tipo para **On**.

8. Para ativar a exportação contínua de dados, certifique-se de que o toggle de **exportação de Dados** está **ligado**. Selecione **Guardar**.

9. Após alguns minutos, os seus dados aparecerão no seu destino escolhido.


## <a name="data-format"></a>Formato de dados

As medições, dispositivos e modelos de dispositivos são exportados para o seu centro de eventos ou fila de ônibus de serviço ou tópico em tempo quase real. Os dados de medição exportados contêm a totalidade da mensagem que os seus dispositivos enviaram à IoT Central, e não apenas os valores das próprias medições. Os dados dos dispositivos exportados contêm alterações nas propriedades e configurações de todos os dispositivos, e os modelos de dispositivos exportados contêm alterações em todos os modelos do dispositivo. Os dados exportados estão na propriedade "body" e estão em formato JSON.

> [!NOTE]
> Ao escolher um Ônibus de Serviço como destino de exportação, as filas e tópicos **não devem ter Sessões ou Deteção duplicada ativadas**. Se alguma dessas opções estiver ativada, algumas mensagens não chegarão na sua fila ou tópico.

### <a name="measurements"></a>Medições

Uma nova mensagem é exportada rapidamente após a IoT Central receber a mensagem de um dispositivo. Cada mensagem exportada em Event Hubs e Service Bus contém a mensagem completa que o dispositivo enviou na propriedade "body" em formato JSON. 

> [!NOTE]
> Os dispositivos que enviam as medições estão representados por IDs do dispositivo (ver as seguintes secções). Para obter os nomes dos dispositivos, exportar dados do dispositivo e correlacionar cada confusão utilizando a **ligaçãoDeviceId** que corresponde ao **dispositivoId** da mensagem do dispositivo.

O exemplo que se segue mostra uma mensagem sobre os dados de medição recebidos no centro de eventos ou na fila ou tópico do Service Bus.

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
  "enqueuedTimeUtc": "2018-10-02T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Dispositivos

As mensagens que contêm dados do dispositivo são enviadas para o seu centro de eventos ou fila de ônibus de serviço ou tópico uma vez a cada poucos minutos. Isto significa que a cada poucos minutos, um lote de mensagens chegará com dados sobre
- Novos dispositivos que foram adicionados
- Dispositivos com propriedades alteradas e valores de definição

Cada mensagem representa uma ou mais alterações num dispositivo desde a última mensagem exportada. A informação que será enviada em cada mensagem inclui:
- `id` do dispositivo na IoT Central
- `name` do dispositivo
- `deviceId` do Serviço de Provisionamento de [Dispositivos](/azure/iot-central/core/howto-connect-nodejs)
- Informações sobre modelo de dispositivo
- Valores de propriedade
- Definir valores

> [!NOTE]
> Os dispositivos apagados desde que o último lote não são exportados. Atualmente, não existem indicadores em mensagens exportadas para dispositivos apagados.
>
> O modelo de dispositivo a que cada dispositivo pertence é representado por um ID de modelo de dispositivo. Para obter o nome do modelo do dispositivo, certifique-se de exportar dados do modelo do dispositivo também.

O exemplo que se segue mostra uma mensagem sobre os dados do dispositivo no centro de eventos ou na fila de ônibus de serviço ou tópico:


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
  "enqueuedTimeUtc": "2018-10-02T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Modelos de dispositivo

As mensagens que contêm os modelos do dispositivo são enviadas para o seu centro de eventos ou fila de ônibus de serviço ou tópico uma vez a cada poucos minutos. Isto significa que a cada poucos minutos, um lote de mensagens chegará com dados sobre
- Novos modelos de dispositivo que foram adicionados
- Modelos de dispositivo com medições alteradas, propriedades e definições de definição

Cada mensagem representa uma ou mais alterações a um modelo de dispositivo desde a última mensagem exportada. A informação que será enviada em cada mensagem inclui:
- `id` do modelo do dispositivo
- `name` do modelo do dispositivo
- `version` do modelo do dispositivo
- Tipos de dados de medição e valores min/max
- Tipos de dados de propriedade e valores predefinidos
- Definição de tipos de dados e valores predefinidos

> [!NOTE]
> Os modelos do dispositivo apagados desde que o último lote não são exportados. Atualmente, não existem indicadores em mensagens exportadas para modelos de dispositivos eliminados.

O exemplo que se segue mostra uma mensagem sobre os dados dos modelos do dispositivo no centro de eventos ou na fila ou tópico do Ônibus de Serviço:

```json
{ 
  "body":{ 
    "id":"<id>",
    "version":"1.0.0",
    "name":"<templateName>",
    "measurements":{ 
      "telemetry":{ 
        "humidity":{ 
          "dataType":"double",
          "name":"humidity"
        },
        "pressure":{ 
          "dataType":"double",
          "name":"pressure"
        },
        "temp":{ 
          "dataType":"double",
          "name":"temperature"
        }
      }
    },
    "properties":{ 
      "cloud":{ 
        "location":{ 
          "dataType":"string",
          "name":"Location"
        }
      },
      "device":{ 
        "dieNumber":{ 
          "dataType":"double",
          "name":"Die Number"
        }
      }
    },
    "settings":{ 
      "device":{ 
        "fanSpeed":{ 
          "dataType":"double",
          "name":"Fan Speed",
          "initialValue":0
        }
      }
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
  "enqueuedTimeUtc":"2018-10-02T16:23:05.085Z",
  "offset":"<offset>"
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe exportar os seus dados para o Azure Event Hubs e para o Azure Service Bus, continue para o próximo passo:

> [!div class="nextstepaction"]
> [Como desencadear funções azure](howto-trigger-azure-functions.md)
