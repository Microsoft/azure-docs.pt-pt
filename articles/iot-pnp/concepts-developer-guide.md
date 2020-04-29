---
title: Guia de desenvolvedores - IoT Plug e Pré-visualização de reprodução / Microsoft Docs
description: Descrição da modelação do dispositivo para desenvolvedores de plug e play IoT
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77605218"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Guia de modelação de modelação ioT Plug e Play Preview

A Antevisão do IoT Plug e play permite-lhe construir dispositivos que anunciem as suas capacidades para aplicações Azure IoT. Os dispositivos IoT Plug and Play não requerem configuração manual quando um cliente os liga às aplicações IoT Plug e Play-enabled. IoT Central é um exemplo de uma aplicação IoT Plug e Play-enabled.

Para construir um dispositivo IoT Plug and Play, é necessário criar uma descrição do dispositivo. A descrição é feita com uma linguagem de definição simples chamada Linguagem de Definição de Gémeos Digitais (DTDL).

## <a name="device-capability-model"></a>Modelo de capacidade do dispositivo

Com o DTDL, cria-se um modelo de capacidade de _dispositivo_ para descrever as partes do seu dispositivo. Um dispositivo IoT típico é composto por:

- Peças personalizadas, que são as coisas que tornam o seu dispositivo único.
- Peças padrão, que são coisas que são comuns a todos os dispositivos.

Estas peças são chamadas _interfaces_ num modelo de capacidade do dispositivo. As interfaces definem os detalhes de cada parte que o seu dispositivo implementa.

O exemplo que se segue mostra o modelo de capacidade do dispositivo para um dispositivo termóstato:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Um modelo de capacidade tem alguns campos necessários:

- `@id`: uma identificação única sob a forma de um simples Nome uniforme de recurso.
- `@type`: declara que este objeto é um modelo de capacidade.
- `@context`: especifica a versão DTDL utilizada para o modelo de capacidade.
- `implements`: lista as interfaces que o seu dispositivo implementa.

Cada entrada na lista de interfaces na secção de implementações tem:

- `name`: o nome de programação da interface.
- `schema`: a interface que o modelo de capacidade implementa.

Existem campos opcionais adicionais que pode usar para adicionar mais detalhes ao modelo de capacidade, como o nome do ecrã e a descrição. As interfaces declaradas dentro de um modelo de capacidade podem ser consideradas como componentes do dispositivo. Para pré-visualização pública, a lista de interfaces pode ter apenas uma entrada por esquema.

## <a name="interface"></a>Interface

Com o DTDL, descreve as capacidades do seu dispositivo utilizando interfaces. As interfaces descrevem as _propriedades,_ _telemetria,_ e _comanda_ uma parte do seu dispositivo implementa:

- `Properties`. As propriedades são campos de dados que representam o estado do seu dispositivo. Utilize propriedades para representar o estado durável do dispositivo, como o estado de saída de uma bomba de refrigeração. As propriedades também podem representar propriedades básicas do dispositivo, como a versão firmware do dispositivo. Pode declarar propriedades como apenas leitura ou repreensíveis.
- `Telemetry`. Os campos de telemetria representam medições a partir de sensores. Sempre que o seu dispositivo efetua uma medição do sensor, deve enviar um evento de telemetria contendo os dados do sensor.
- `Commands`. Os comandos representam métodos que os utilizadores do seu dispositivo podem executar no dispositivo. Por exemplo, um comando de reset ou um comando para ligar ou desligar uma ventoinha.

O exemplo seguinte mostra a interface para um dispositivo termóstato:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Uma interface tem alguns campos necessários:

- `@id`: uma identificação única sob a forma de um simples Nome uniforme de recurso.
- `@type`: declara que este objeto é uma interface.
- `@context`: especifica a versão DTDL utilizada para a interface.
- `contents`: lista as propriedades, telemetria e comandos que compõem o seu dispositivo.

Neste simples exemplo, há apenas um único campo de telemetria. Uma descrição mínima do campo tem um:

- `@type`: especifica o tipo de `Telemetry` `Property`capacidade: `Command`, ou .
- `name`: fornece o nome do valor da telemetria.
- `schema`: especifica o tipo de dados para a telemetria. Este valor pode ser um tipo primitivo, como duplo, inteiro, booleano ou corda. Tipos complexos de objetos, matrizes e mapas também são suportados.

Outros campos opcionais, como o nome e a descrição do ecrã, permitem-lhe adicionar mais detalhes à interface e capacidades.

### <a name="properties"></a>Propriedades

Por padrão, as propriedades são apenas de leitura. As propriedades apenas de leitura significam que o dispositivo reporta atualizações de valor de propriedade para o seu hub IoT. Seu centro de IoT não pode definir o valor de uma propriedade só para leitura.

Você também pode marcar uma propriedade como recedível em uma interface. Um dispositivo pode receber uma atualização para uma propriedade reempreciável a partir do seu hub IoT, bem como reportar atualizações de valor de propriedade para o seu hub.

Os dispositivos não têm de ser ligados para definir valores de propriedade. Os valores atualizados são transferidos quando o dispositivo se liga ao centro. Este comportamento aplica-se tanto a propriedades de leitura como de escrita.

Não utilize propriedades para enviar telemetria do seu dispositivo. Por exemplo, uma propriedade `temperatureSetting=80` de leitura como deve significar que a temperatura do dispositivo foi definida para 80, e o dispositivo está tentando chegar ou ficar a esta temperatura.

Para propriedades de supreendiáveis, a aplicação do dispositivo devolve um código de estado, versão e descrição desejados para indicar se recebeu e aplicou o valor da propriedade.

### <a name="telemetry"></a>Telemetria

Por padrão, o IoT Hub direciona todas as mensagens de telemetria dos dispositivos para o seu ponto final virado para o [serviço incorporado **(mensagens/eventos)**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) que é compatível com [os Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)de Eventos .

Você pode usar [os pontos finais personalizados do IoT Hub e regras de encaminhamento](../iot-hub/iot-hub-devguide-messages-d2c.md) para enviar telemetria para outros destinos, como armazenamento de blob ou outros centros de eventos. As regras de encaminhamento utilizam propriedades de mensagens para selecionar mensagens.

### <a name="commands"></a>Comandos

Os comandos são sincronizados ou assíncronos. Um comando sincronizado deve ser executado dentro de 30 segundos por padrão e o dispositivo deve ser ligado quando o comando chegar. Se o dispositivo responder a tempo, ou se o dispositivo não estiver ligado, o comando falha.

Utilize comandos assíncronos para operações de longa duração. O dispositivo envia informações de progresso usando mensagens de telemetria. Estas mensagens de progresso têm as seguintes propriedades do cabeçalho:

- `iothub-command-name`: o nome do `UpdateFirmware`comando, por exemplo.
- `iothub-command-request-id`: o ID de pedido gerado no lado do servidor e enviado para o dispositivo na chamada inicial.
- `iothub-interface-id`: A identificação da interface em que `urn:example:AssetTracker:1`este comando é definido, por exemplo.
 `iothub-interface-name`: o nome por exemplo `myAssetTracker`desta interface, por exemplo.
- `iothub-command-statuscode`: o código de estado devolvido `202`do dispositivo, por exemplo.

## <a name="register-a-device"></a>Registar um dispositivo

IoT Plug and Play facilita a divulgação das capacidades do seu dispositivo. Com ioT Plug e Play, depois de o seu dispositivo se ligar ao IoT Hub, tem de registar o seu modelo de capacidade do dispositivo. O registo permite que os clientes utilizem as capacidades ioT Plug e Play do seu dispositivo.

Este guia mostra-lhe como registar um dispositivo utilizando o Dispositivo Azure IoT SDK para C.

Para cada interface que o seu dispositivo implementa, deve criar uma interface e conectá-la à sua implementação.

Para a interface do termóstato mostrada anteriormente, utilizando o C SDK, cria e liga a interface do termóstato à sua implementação:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Repita este código para cada interface que o seu dispositivo implementa.

Depois de criar uma interface, registe o modelo de capacidade do seu dispositivo e as interfaces com o seu hub IoT:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Use um dispositivo

O IoT Plug and Play permite-lhe utilizar dispositivos que tenham registado as suas capacidades com o seu hub IoT. Por exemplo, pode aceder diretamente às propriedades e comandos de um dispositivo.

Para utilizar um dispositivo IoT Plug and Play que esteja ligado ao seu hub IoT, utilize a API Do IoT Hub REST ou um dos SDKs em língua IoT. Os seguintes exemplos utilizam a IoT Hub REST API. A versão atual da `2019-07-01-preview`API é . Anexar `?api-version=2019-07-01-preview` as suas chamadas PI REST.

Para obter o valor de uma propriedade de`fwVersion`dispositivo, `DeviceInformation` como a versão firmware ( ) na interface do termóstato, utiliza-se os gémeos digitais REST API.

Se o seu dispositivo termóstato for chamado, `t-123`obtém todas as propriedades em todas as interfaces implementadas pelo seu dispositivo com uma chamada REST API GET:

```REST
GET /digitalTwins/t-123/interfaces
```

De uma forma mais geral, todas as propriedades em todas `{device-id}` as interfaces são acedidas com este modelo REST API onde está o identificador para o dispositivo:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Se souber o nome da interface, como, por exemplo, `deviceInformation`e pretender obter propriedades para essa interface específica, faça o pedido para uma interface específica pelo nome:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

De uma forma mais geral, as propriedades para uma interface `device-id` específica podem ser acedidas através deste modelo REST API onde está o identificador do dispositivo e `{interface-name}` é o nome da interface:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Pode ligar diretamente para os comandos do dispositivo IoT Plug e Play. Se `Thermostat` a interface `t-123` do `restart` dispositivo tiver um comando, pode chamá-lo com uma chamada REST API POST:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

De uma forma mais geral, os comandos podem ser chamados através deste modelo REST API:

- `device-id`: o identificador do aparelho.
- `interface-name`: o nome da interface da secção de implementação no modelo de capacidade do dispositivo.
- `command-name`: o nome do comando.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre modelação de dispositivos, aqui estão alguns recursos adicionais:

- [Linguagem de Definição Dupla Digital (DTDL)](https://aka.ms/DTDL)
- [SDK de Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
