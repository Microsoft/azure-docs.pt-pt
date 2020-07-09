---
title: Guia de desenvolvimento - IoT Plug e Play Preview / Microsoft Docs
description: Descrição da modelação do dispositivo para desenvolvedores IoT Plug e Play
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77605218"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Guia de modelação IoT Plug e Play Preview

IoT Plug e Play Preview permite-lhe construir dispositivos que anunciam as suas capacidades para aplicações Azure IoT. Os dispositivos IoT Plug e Play não necessitam de configuração manual quando um cliente os liga a aplicações IoT Plug e Play. O IoT Central é um exemplo de uma aplicação IoT Plug e Play-enabled.

Para construir um dispositivo IoT Plug and Play, é necessário criar uma descrição do dispositivo. A descrição é feita com uma linguagem de definição simples chamada Linguagem de Definição de Gémeos Digitais (DTDL).

## <a name="device-capability-model"></a>Modelo de capacidade do dispositivo

Com o DTDL, cria-se um _modelo de capacidade do dispositivo_ para descrever as partes do seu dispositivo. Um dispositivo IoT típico é composto por:

- Peças personalizadas, que são as coisas que tornam o seu dispositivo único.
- Peças padrão, que são coisas que são comuns a todos os dispositivos.

Estas peças são chamadas _interfaces_ num modelo de capacidade do dispositivo. As interfaces definem os detalhes de cada parte que o seu dispositivo implementa.

O exemplo a seguir mostra o modelo de capacidade do dispositivo para um dispositivo termóstato:

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

- `@id`: um ID único sob a forma de um simples Nome de Recurso Uniforme.
- `@type`: declara que este objeto é um modelo de capacidade.
- `@context`: especifica a versão DTDL utilizada para o modelo de capacidade.
- `implements`: lista as interfaces que o seu dispositivo implementa.

Cada entrada na lista de interfaces na secção de implementações tem um:

- `name`: o nome de programação da interface.
- `schema`: a interface que o modelo de capacidade implementa.

Existem campos opcionais adicionais que pode utilizar para adicionar mais detalhes ao modelo de capacidade, como o nome do ecrã e a descrição. As interfaces que são declaradas dentro de um modelo de capacidade podem ser consideradas como componentes do dispositivo. Para visualização pública, a lista de interfaces pode ter apenas uma entrada por esquema.

## <a name="interface"></a>Interface

Com o DTDL, descreve as capacidades do seu dispositivo utilizando interfaces. As interfaces descrevem as _propriedades,_ _telemetria,_ e _comanda_ uma parte dos seus dispositivos:

- `Properties`. As propriedades são campos de dados que representam o estado do seu dispositivo. Utilize propriedades para representar o estado duradouro do dispositivo, como o estado de saída de uma bomba de refrigeração. As propriedades também podem representar propriedades básicas do dispositivo, como a versão firmware do dispositivo. Pode declarar propriedades apenas para leitura ou legitável.
- `Telemetry`. Os campos de telemetria representam medições dos sensores. Sempre que o seu dispositivo efetuar uma medição do sensor, deve enviar um evento de telemetria contendo os dados do sensor.
- `Commands`. Os comandos representam métodos que os utilizadores do seu dispositivo podem executar no dispositivo. Por exemplo, um comando de reset ou um comando para ligar ou desligar uma ventoinha.

O exemplo a seguir mostra a interface de um dispositivo termóstato:

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

- `@id`: um ID único sob a forma de um simples Nome de Recurso Uniforme.
- `@type`: declara que este objeto é uma interface.
- `@context`: especifica a versão DTDL utilizada para a interface.
- `contents`: lista as propriedades, a telemetria e os comandos que compõem o seu dispositivo.

Neste exemplo simples, há apenas um único campo de telemetria. Uma descrição mínima de campo tem um:

- `@type`: especifica o tipo de capacidade: `Telemetry` `Property` , , ou `Command` .
- `name`: fornece o nome do valor da telemetria.
- `schema`: especifica o tipo de dados para a telemetria. Este valor pode ser um tipo primitivo, como duplo, inteiro, booleano ou corda. Os tipos complexos de objetos, matrizes e mapas também são suportados.

Outros campos opcionais, como o nome do ecrã e a descrição, permitem adicionar mais detalhes à interface e às capacidades.

### <a name="properties"></a>Propriedades

Por padrão, as propriedades são apenas de leitura. Propriedades apenas de leitura significam que o dispositivo reporta atualizações de valor da propriedade para o seu hub IoT. Seu hub IoT não pode definir o valor de uma propriedade só de leitura.

Também pode marcar uma propriedade como escrita numa interface. Um dispositivo pode receber uma atualização para uma propriedade escrita a partir do seu hub IoT, bem como reportar atualizações de valor da propriedade para o seu hub.

Os dispositivos não têm de ser ligados para definir valores de propriedade. Os valores atualizados são transferidos quando o dispositivo se liga ao hub. Este comportamento aplica-se a propriedades apenas de leitura e de escrita.

Não utilize propriedades para enviar telemetria do seu dispositivo. Por exemplo, uma propriedade de leitura apenas, como `temperatureSetting=80` deve significar que a temperatura do dispositivo foi definida para 80, e o dispositivo está tentando chegar ou ficar a esta temperatura.

Para propriedades writable, a aplicação do dispositivo devolve um código de estado, versão e descrição desejadas para indicar se recebeu e aplicou o valor da propriedade.

### <a name="telemetry"></a>Telemetria

Por padrão, o IoT Hub encaminha todas as mensagens de telemetria dos dispositivos para o seu [ponto final de assistência ao serviço incorporado **(mensagens/eventos)**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) que é compatível com os Centros de [Eventos.](https://azure.microsoft.com/documentation/services/event-hubs/)

Você pode usar [os pontos finais personalizados do IoT Hub e regras de encaminhamento](../iot-hub/iot-hub-devguide-messages-d2c.md) para enviar telemetria para outros destinos, como armazenamento de bolhas ou outros centros de eventos. As regras de encaminhamento utilizam propriedades de mensagens para selecionar mensagens.

### <a name="commands"></a>Comandos

Os comandos são sincronizados ou assíncronos. Um comando sincronizado deve ser executado dentro de 30 segundos por defeito, e o dispositivo deve ser ligado quando o comando chegar. Se o dispositivo responder a tempo ou o dispositivo não estiver ligado, o comando falha.

Utilize comandos assíncronos para operações de longa duração. O dispositivo envia informações de progresso utilizando mensagens de telemetria. Estas mensagens de progresso têm as seguintes propriedades do cabeçalho:

- `iothub-command-name`: o nome de comando, por exemplo `UpdateFirmware` .
- `iothub-command-request-id`: o ID de pedido gerado no lado do servidor e enviado para o dispositivo na chamada inicial.
- `iothub-interface-id`: O ID da interface neste comando é definido, por `urn:example:AssetTracker:1` exemplo.
 `iothub-interface-name`: o nome da instância desta interface, por exemplo `myAssetTracker` .
- `iothub-command-statuscode`: o código de estado devolvido do dispositivo, por exemplo `202` .

## <a name="register-a-device"></a>Registar um dispositivo

IoT Plug e Play facilitam a divulgação das capacidades do seu dispositivo. Com o IoT Plug e Play, depois de o seu dispositivo se ligar ao IoT Hub, tem de registar o seu modelo de capacidade do dispositivo. O registo permite que os clientes utilizem as capacidades IoT Plug e Play do seu dispositivo.

Este guia mostra-lhe como registar um dispositivo utilizando o Dispositivo Azure IoT SDK para C.

Para cada interface o seu dispositivo implementa, deve criar uma interface e conectá-la à sua implementação.

Para a interface do termóstato mostrada anteriormente, utilizando o SDK C, cria e liga a interface do termóstato à sua implementação:

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

Depois de criar uma interface, registe o modelo de capacidade do dispositivo e interfaces com o seu hub IoT:

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

Para utilizar um dispositivo IoT Plug and Play que esteja ligado ao seu hub IoT, utilize a API IoT Hub REST ou um dos SDKs ioT. Os exemplos a seguir usam a API IoT Hub REST. A versão atual da API é `2019-07-01-preview` . Apêndice `?api-version=2019-07-01-preview` às suas chamadas DE REPOUSO PI.

Para obter o valor de uma propriedade do dispositivo, como a versão firmware ( `fwVersion` ) na interface no `DeviceInformation` termóstato, você usa os gémeos digitais REST API.

Se o seu dispositivo termóstato for `t-123` chamado, obtém todas as propriedades em todas as interfaces implementadas pelo seu dispositivo com uma chamada REST API GET:

```REST
GET /digitalTwins/t-123/interfaces
```

De uma forma mais geral, todas as propriedades em todas as interfaces são acedidas com este modelo de API REST onde `{device-id}` está o identificador do dispositivo:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Se conhecer o nome da interface, `deviceInformation` como, e pretender obter propriedades para essa interface específica, adisca o pedido para uma interface específica pelo nome:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

De uma forma mais geral, as propriedades para uma interface específica podem ser acedidas através deste modelo de API REST onde `device-id` é o identificador do dispositivo e `{interface-name}` é o nome da interface:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Pode ligar diretamente para os comandos do dispositivo IoT Plug and Play. Se a `Thermostat` interface do dispositivo tiver um `t-123` `restart` comando, pode chamá-lo com uma chamada REST API POST:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

De uma forma mais geral, os comandos podem ser chamados através deste modelo de API REST:

- `device-id`: o identificador do dispositivo.
- `interface-name`: o nome da interface a partir da secção de implementações no modelo de capacidade do dispositivo.
- `command-name`: o nome do comando.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu sobre modelação de dispositivos, aqui estão alguns recursos adicionais:

- [Linguagem de definição digital twin (DTDL)](https://aka.ms/DTDL)
- [SDK de Dispositivo C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
