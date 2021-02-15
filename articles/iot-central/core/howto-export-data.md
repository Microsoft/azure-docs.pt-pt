---
title: Dados de exportação da Azure IoT Central | Microsoft Docs
description: Como utilizar os novos dados exportam para exportar os seus dados IoT para destinos em nuvem Azure e personalizados.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 7152012c7c4a342c7491e5f8b835eaede4269c4c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522619"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Exportar dados de IoT para destinos em nuvem usando exportação de dados

> [!Note]
> Este artigo descreve as características de exportação de dados na IoT Central. Para obter informações sobre as funcionalidades de exportação de dados antigos, consulte [os dados do Export IoT para destinos em nuvem utilizando a exportação de dados (legado)](./howto-export-data-legacy.md).

Este artigo descreve como utilizar a nova funcionalidade de exportação de dados na Azure IoT Central. Utilize esta funcionalidade para exportar continuamente dados de IoT filtrados e enriquecidos da sua aplicação IoT Central. A exportação de dados impulsiona as mudanças em tempo quase real para outras partes da sua solução de nuvem para insights de caminhos quentes, análises e armazenamento.

Pode, por exemplo:

- Exporte continuamente dados de telemetria e alterações de propriedade no formato JSON em tempo quase real.
- Filtrar os fluxos de dados para exportar dados que correspondam às condições personalizadas.
- Enriqueça os fluxos de dados com valores personalizados e valores de propriedade do dispositivo.
- Envie os dados para destinos como Azure Event Hubs, Azure Service Bus, Azure Blob Storage e pontos finais webhook.

> [!Tip]
> Quando liga a exportação de dados, obtém-se apenas os dados a partir desse momento. Atualmente, os dados não podem ser recuperados por um tempo em que a exportação de dados estava desligada. Para reter mais dados históricos, ligue a exportação de dados mais cedo.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar as funcionalidades de exportação de dados, tem de ter uma [aplicação V3,](howto-get-app-info.md)e tem de ter a permissão [de exportação de Dados.](howto-manage-users-roles.md)

Se tiver uma aplicação V2, consulte [a sua aplicação V2 IoT Central para V3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Configurar destino de exportação

O seu destino de exportação deve existir antes de configurar a sua exportação de dados. Os seguintes tipos de destino estão atualmente disponíveis:

- Azure Event Hubs
- Fila do Azure Service Bus
- Tópico do Azure Service Bus
- Armazenamento de Blobs do Azure
- Webhook

### <a name="create-an-event-hubs-destination"></a>Criar um destino de Centros de Eventos

Se você não tem um espaço de nomes de Event Hubs existente para exportar, siga estes passos:

1. Crie um [novo espaço de nomes de Centros de Eventos no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.EventHub) Você pode aprender mais em [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md).

1. Crie um centro de eventos no seu espaço de nomes Event Hubs. Vá ao seu espaço de nome e selecione **+ Event Hub** no topo para criar uma instância de centro de eventos.

1. Gere uma chave para usar quando configurar a exportação de dados na IoT Central:

    - Selecione a instância do centro de eventos que criou.
    - Selecione **Definições > Políticas de acesso partilhado**.
    - Crie uma nova chave ou escolha uma chave existente que tenha permissões **enviar.**
    - Copie a cadeia de ligação primária ou secundária. Utilize esta cadeia de ligação para configurar um novo destino na IoT Central.
    - Em alternativa, pode gerar uma cadeia de ligação para todo o espaço de nomes do Event Hubs:
        1. Vá ao seu espaço de nomes do Event Hubs no portal Azure.
        2. Em **Definições**, **selecione Políticas de Acesso Partilhado**
        3. Crie uma nova chave ou escolha uma chave existente que tenha permissões **enviar.**
        4. Copie a cadeia de ligação primária ou secundária
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Crie uma fila de ônibus de serviço ou destino tópico

Se você não tem um espaço de nome de Service Bus existente para exportar para, siga estes passos:

1. Crie um [novo espaço de nomes de Service Bus no portal Azure.](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5) Pode saber mais em [docs de ônibus da Azure Service](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Para criar uma fila ou tópico para exportar, vá ao seu espaço de nomes de Service Bus e selecione **+ Fila** ou **+ Tópico**.

1. Gere uma chave para usar quando configurar a exportação de dados na IoT Central:

    - Selecione a fila ou tópico que criou.
    - Selecione **Definições/Políticas de acesso partilhado**.
    - Crie uma nova chave ou escolha uma chave existente que tenha permissões **enviar.**
    - Copie a cadeia de ligação primária ou secundária. Utilize esta cadeia de ligação para configurar um novo destino na IoT Central.
    - Em alternativa, pode gerar uma cadeia de ligação para todo o espaço de nomes do Service Bus:
        1. Vá ao seu espaço de nomes de Service Bus no portal Azure.
        2. Em **Definições**, **selecione Políticas de Acesso Partilhado**
        3. Crie uma nova chave ou escolha uma chave existente que tenha permissões **enviar.**
        4. Copie a cadeia de ligação primária ou secundária

### <a name="create-an-azure-blob-storage-destination"></a>Criar um destino de armazenamento Azure Blob

Se não tiver uma conta de armazenamento Azure existente para exportar, siga estes passos:

1. Criar uma [nova conta de armazenamento no portal Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Pode saber mais sobre a criação de novas [contas de armazenamento Azure Blob](../../storage/blobs/storage-quickstart-blobs-portal.md) ou [contas de armazenamento V2 do Azure Data Lake Storage v2](../../storage/common/storage-account-create.md). A exportação de dados só pode escrever dados para contas de armazenamento que suportam bolhas de blocos. A lista que se segue mostra os tipos de conta de armazenamento compatíveis conhecidos:

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Final Geral V2|
    |Standard|Final geral V1|
    |Standard|Armazenamento de blobs|
    |Premium|Armazenamento Blob de bloco|

1. Para criar um recipiente na sua conta de armazenamento, vá à sua conta de armazenamento. No **serviço Blob**, **selecione Browse Blobs**. Selecione **+ Recipiente** na parte superior para criar um novo recipiente.

1. Gere uma cadeia de ligação para a sua conta de armazenamento indo para **as definições > teclas de acesso**. Copie uma das duas cordas de ligação.

### <a name="create-a-webhook-endpoint"></a>Criar um ponto final webhook

Pode exportar dados para um ponto final httphook disponível publicamente. Pode criar um ponto final de teste webhook utilizando [o RequestBin](https://requestbin.net/). PedidoAderbin solicita quando o limite de pedido é atingido:

1. Open [RequestBin](https://requestbin.net/).
2. Crie um novo RequestBin e copie o **URL do Bin.** Utilize este URL quando testa a exportação de dados.

## <a name="set-up-data-export"></a>Configurar exportação de dados

Agora que tem um destino para exportar os seus dados, crie exportação de dados na sua aplicação IoT Central:

1. Inscreva-se na sua aplicação IoT Central.

1. No painel esquerdo, selecione **Data export**.

    > [!Tip]
    > Se não vir **a exportação de Dados** no painel esquerdo, então não tem permissões para configurar a exportação de dados na sua app. Fale com um administrador para configurar a exportação de dados.

1. **Selecione + Nova exportação.**

1. Introduza um nome de exibição para a sua nova exportação e certifique-se de que a exportação de dados está **Ativada**.

1. Escolha o tipo de dados para exportar. O quadro que se segue enumera os tipos de exportação de dados suportados:

    | Tipo de dados | Descrição | Formato de dados |
    | :------------- | :---------- | :----------- |
    |  Telemetria | Exporte mensagens de telemetria de dispositivos em tempo quase real. Cada mensagem exportada contém todo o conteúdo da mensagem original do dispositivo, normalizada.   |  [Formato de mensagem de telemetria](#telemetry-format)   |
    | Alterações de propriedade | Exportar alterações para propriedades de dispositivos e nuvem em tempo quase real. Para as propriedades do dispositivo apenas de leitura, são exportadas alterações aos valores reportados. Para propriedades de leitura-escrita, os valores relatados e desejados são exportados. | [Formato de mensagem de alteração de propriedade](#property-changes-format) |

<a name="DataExportFilters"></a>
1. Opcionalmente, adicione filtros para reduzir a quantidade de dados exportados. Existem diferentes tipos de filtro disponíveis para cada tipo de exportação de dados:

    Para filtrar a telemetria, pode:

    - **Filtrar** o fluxo exportado para conter apenas telemetria de dispositivos que correspondam ao nome do dispositivo, iD do dispositivo e condição do filtro do modelo do dispositivo.
    - **Filtrar** as capacidades: Se escolher um item de telemetria no dropdown **Name,** o fluxo exportado contém apenas telemetria que satisfaz a condição do filtro. Se escolher um item de propriedade de dispositivo ou nuvem no dropdown **Name,** o fluxo exportado contém apenas telemetria de dispositivos com propriedades correspondentes à condição do filtro.
    - **Filtro de propriedade de mensagem**: Os dispositivos que utilizam o dispositivo SDKs podem enviar *propriedades de mensagens* ou *propriedades de aplicação* em cada mensagem de telemetria. As propriedades são um saco de pares de valores-chave que marcam a mensagem com identificadores personalizados. Para criar um filtro de propriedade de mensagem, insira a chave de propriedade de mensagem que procura e especifique uma condição. Apenas são exportadas mensagens de telemetria com propriedades que correspondam ao estado do filtro especificado. Os seguintes operadores de comparação de cordas são apoiados: iguais, não iguais, contém, não contém, existe, não existe. [Saiba mais sobre as propriedades da aplicação a partir de docs IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

    Para filtrar as alterações de propriedade, utilize um **filtro Capability**. Escolha um item de propriedade no dropdown. O fluxo exportado contém apenas alterações na propriedade selecionada que satisfaz a condição do filtro.

<a name="DataExportEnrichmnents"></a>
1. Opcionalmente, enriqueça mensagens exportadas com metadados de par de chaves adicionais. Estão disponíveis os seguintes enriquecimentos para a telemetria e alteram os tipos de exportação de dados:

    - **Cadeia personalizada**: Adiciona uma cadeia estática personalizada a cada mensagem. Introduza qualquer chave e introduza qualquer valor de cadeia.
    - **Propriedade**: Adiciona o valor de propriedade ou propriedade reportada ao dispositivo atual a cada mensagem. Introduza qualquer chave e escolha um dispositivo ou propriedade na nuvem. Se a mensagem exportada for de um dispositivo que não tenha a propriedade especificada, a mensagem exportada não recebe o enriquecimento.

1. Adicione um novo destino ou adicione um destino que já criou. Selecione o **Criar um novo** link de um e adicione as seguintes informações:

    - **Nome do destino**: o nome de exposição do destino na IoT Central.
    - **Tipo de destino**: escolha o tipo de destino. Se ainda não definiu o seu destino, consulte [o destino de exportação.](#set-up-export-destination)
    - Para Azure Event Hubs, fila ou tópico do Azure Service Bus, cole a cadeia de ligação para o seu recurso e introduza o centro de eventos sensível ao caso, a fila ou o nome do tópico, se necessário.
    - Para o armazenamento Azure Blob, cole o fio de ligação para o seu recurso e introduza o nome do recipiente sensível à caixa, se necessário.
    - Para webhook, cole o URL de retorno para o seu ponto final webhook. Pode configurar opcionalmente a autorização webhook (OAuth 2.0 e Token de Autorização) e adicionar cabeçalhos personalizados. 
        - Para a OAuth 2.0, apenas o fluxo de credenciais do cliente é suportado. Quando o destino for guardado, a IoT Central comunicará com o seu fornecedor OAuth para recuperar um token de autorização. Este token será anexado ao cabeçalho "Autorização" para cada mensagem enviada para este destino.
        - Para sinal de autorização, pode especificar um valor simbólico que será diretamente anexado ao cabeçalho "Autorização" para cada mensagem enviada para este destino.
    - Selecione **Criar**.

1. Selecione **+ Destino** e escolha um destino a partir do dropdown. Você pode adicionar até cinco destinos para uma única exportação.

1. Quando terminar de configurar a sua exportação, **selecione Save**. Após alguns minutos, os seus dados aparecem nos seus destinos.

## <a name="monitor-your-export"></a>Monitorize a sua exportação

Além de ver o estado das suas exportações na IoT Central, pode utilizar [o Azure Monitor](../../azure-monitor/overview.md) para ver quantos dados está a exportar e quaisquer erros de exportação. Pode aceder às métricas de saúde de exportação e dispositivo em gráficos no portal Azure, com uma API REST, ou com consultas no PowerShell ou no Azure CLI. Atualmente, pode monitorizar as seguintes métricas de exportação de dados no Azure Monitor:

- Número de mensagens a chegar à exportação antes de os filtros serem aplicados.
- Número de mensagens que passam através de filtros.
- Número de mensagens exportadas com sucesso para destinos.
- Número de erros encontrados.

Para saber mais, consulte [Monitor a saúde geral de uma aplicação IoT Central](howto-monitor-application-health.md).

## <a name="destinations"></a>Destinos

### <a name="azure-blob-storage-destination"></a>Destino de armazenamento Azure Blob

Os dados são exportados uma vez por minuto, com cada ficheiro contendo o lote de alterações desde a exportação anterior. Os dados exportados são guardados no formato JSON. Os caminhos padrão para os dados exportados na sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Alterações de propriedade: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{filename}_

Para navegar nos ficheiros exportados no portal Azure, navegue para o ficheiro e **selecione Editar blob**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs e destinos de ônibus da Azure Service

Os dados são exportados em tempo real. Os dados estão no corpo da mensagem e estão em formato JSON codificados como UTF-8.

As anotações ou propriedades do sistema contêm o `iotcentral-device-id` , , e campos que têm os `iotcentral-application-id` `iotcentral-message-source` `iotcentral-message-type` mesmos valores que os campos correspondentes no corpo da mensagem.

### <a name="webhook-destination"></a>Destino Webhook

Para os destinos webhooks, os dados também são exportados em tempo real. Os dados no corpo da mensagem estão no mesmo formato que para Os Centros de Eventos e Autocarro de Serviço.

## <a name="telemetry-format"></a>Formato de telemetria

Cada mensagem exportada contém uma forma normalizada da mensagem completa que o dispositivo enviou no corpo da mensagem. A mensagem está no formato JSON e codificada como UTF-8. A informação em cada mensagem inclui:

- `applicationId`: Identificação da aplicação IoT Central.
- `messageSource`: A fonte da mensagem - `telemetry` .
- `deviceId`: A identificação do dispositivo que enviou a mensagem de telemetria.
- `schema`: O nome e a versão do esquema de carga útil.
- `templateId`: O ID do modelo do dispositivo associado ao dispositivo.
- `enrichments`: Quaisquer enriquecimentos estabelecidos na exportação.
- `messageProperties`: Propriedades adicionais que o dispositivo enviou com a mensagem. Estas propriedades são por vezes referidas como *propriedades de aplicação.* [Saiba mais com os docs do IoT Hub.](../../iot-hub/iot-hub-devguide-messages-construct.md)

Para o Event Hubs e Service Bus, a IoT Central exporta uma nova mensagem rapidamente depois de receber a mensagem de um dispositivo. Nas propriedades do utilizador (também designadas como propriedades de aplicação) de cada mensagem, o `iotcentral-device-id` `iotcentral-application-id` , e são `iotcentral-message-source` incluídos automaticamente.

Para o armazenamento blob, as mensagens são em lotadas e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de telemetria exportada:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>Propriedades de mensagens

As mensagens de telemetria têm propriedades para metadados para além da carga útil da telemetria. O corte anterior mostra exemplos de mensagens do sistema como `deviceId` e `enqueuedTime` . Para saber mais sobre as propriedades da mensagem do sistema, consulte [as propriedades do sistema de mensagens D2C IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Pode adicionar propriedades a mensagens de telemetria se precisar de adicionar metadados personalizados às suas mensagens de telemetria. Por exemplo, é necessário adicionar um tempotando quando o dispositivo cria a mensagem.

O seguinte corte de código mostra como adicionar a `iothub-creation-time-utc` propriedade à mensagem quando a cria no dispositivo:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

O seguinte corte mostra esta propriedade na mensagem exportada para o armazenamento Blob:

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Formato de alterações de propriedade

Cada mensagem ou registo representa uma alteração para um dispositivo ou propriedade na nuvem. Para as propriedades do dispositivo, apenas as alterações no valor reportado são exportadas como uma mensagem separada. As informações na mensagem exportada incluem:

- `applicationId`: Identificação da aplicação IoT Central.
- `messageSource`: A fonte da mensagem - `properties` .
- `messageType`: `cloudPropertyChange` `devicePropertyDesiredChange` Ou, ou `devicePropertyReportedChange` . .
- `deviceId`: A identificação do dispositivo que enviou a mensagem de telemetria.
- `schema`: O nome e a versão do esquema de carga útil.
- `templateId`: O ID do modelo do dispositivo associado ao dispositivo.
- `enrichments`: Quaisquer enriquecimentos estabelecidos na exportação.

Para o Event Hubs e Service Bus, a IoT Central exporta novos dados de mensagens para o seu centro de eventos ou fila de autocarros de serviço ou tópico em tempo real. Nas propriedades do utilizador (também designadas por propriedades de aplicação) de cada mensagem, o `iotcentral-device-id` , , e são `iotcentral-application-id` `iotcentral-message-source` `iotcentral-message-type` incluídos automaticamente.

Para o armazenamento blob, as mensagens são em lotadas e exportadas uma vez por minuto.

O exemplo a seguir mostra uma mensagem de mudança de propriedade exportada recebida no Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Comparação da exportação de dados antigos e exportação de dados

O quadro a seguir mostra as diferenças entre a [exportação de dados antigos](howto-export-data-legacy.md) e as novas características de exportação de dados:

| Capacidades  | Exportação de dados antigos | Exportação de novos dados |
| :------------- | :---------- | :----------- |
| Tipos de dados disponíveis | Telemetria, Dispositivos, Modelos de Dispositivos | Telemetria, Alterações de Propriedade |
| Filtragem | Nenhum | Depende do tipo de dados exportado. Para telemetria, filtragem por telemetria, propriedades de mensagens, valores de propriedade |
| Enriquecimentos | Nenhum | Enriqueça com uma corda personalizada ou um valor de propriedade no dispositivo |
| Destinos | Azure Event Hubs, filas e tópicos de autocarro da Azure Service, Azure Blob Storage | O mesmo que para a exportação de dados antigos mais webhooks|
| Versões de aplicações suportadas | V2, V3 | Apenas V3 |
| Limites notáveis | 5 exportações por app, 1 destino por exportação | 10 ligações exportações-destino por app |

## <a name="next-steps"></a>Passos seguintes

Agora que sabe usar a nova exportação de dados, um próximo passo sugerido é aprender [a usar analítica na IoT Central](./howto-create-analytics.md)
