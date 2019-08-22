---
title: Guia do desenvolvedor-visualização de Plug and Play de IoT | Microsoft Docs
description: Descrição da modelagem de dispositivo para desenvolvedores de Plug and Play IoT
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6d5247454fe65e5539a2401330192f1db9a65114
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880569"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Guia do desenvolvedor de modelagem da visualização do IoT Plug and Play

A visualização de Plug and Play IoT permite criar dispositivos que anunciam seus recursos para os aplicativos IoT do Azure. Os dispositivos IoT Plug and Play não exigem configuração manual quando um cliente os conecta aos aplicativos habilitados para IoT Plug and Play. IoT Central é um exemplo de um aplicativo habilitado para Plug and Play de IoT.

Para criar um dispositivo de Plug and Play IoT, você precisa criar uma descrição do dispositivo. A descrição é feita com uma linguagem de definição simples chamada DTDL (digital gêmeos Definition Language).

## <a name="device-capability-model"></a>Modelo de funcionalidade do dispositivo

Com o DTDL, você cria um _modelo de capacidade de dispositivo_ para descrever as partes do seu dispositivo. Um dispositivo IoT típico é composto de:

- Partes personalizadas, que são as coisas que tornam seu dispositivo exclusivo.
- As partes padrão, que são coisas comuns a todos os dispositivos.

Essas partes são chamadas de _interfaces_ em um modelo de capacidade de dispositivo. As interfaces definem os detalhes de cada parte que seu dispositivo implementa.

O exemplo a seguir mostra o modelo de capacidade de dispositivo para um dispositivo termostato:

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
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Um modelo de funcionalidade tem alguns campos obrigatórios:

- `@id`: uma ID exclusiva na forma de um simples nome de recurso uniforme.
- `@type`: declara que esse objeto é um modelo de funcionalidade.
- `@context`: especifica a versão do DTDL usada para o modelo de funcionalidade.
- `implements`: lista as interfaces que seu dispositivo implementa.

Cada entrada na lista de interfaces na seção implementações tem um:

- `name`: o nome de programação da interface.
- `schema`: a interface que o modelo de funcionalidade implementa.

Há outros campos opcionais que você pode usar para adicionar mais detalhes ao modelo de funcionalidade, como nome de exibição e descrição. As interfaces declaradas em um modelo de funcionalidade podem ser consideradas como componentes do dispositivo. Para a visualização pública, a lista de interface pode ter apenas uma entrada por esquema.

## <a name="interface"></a>Interface

Com o DTDL, você descreve os recursos do seu dispositivo usando interfaces. As interfaces descrevem as _Propriedades_, a telemetria e os _comandos_ que uma parte do seu dispositivo implementa:

- `Properties`. As propriedades são campos de dados que representam o estado do seu dispositivo. Use Propriedades para representar o estado durável do dispositivo, como o estado ligado de uma bomba refrigeração. As propriedades também podem representar as propriedades básicas do dispositivo, como a versão do firmware do dispositivo. Você pode declarar propriedades como somente leitura ou gravável.
- `Telemetry`. Campos de telemetria representam medições de sensores. Sempre que o dispositivo toma uma medida de sensor, ele deve enviar um evento de telemetria contendo os dados do sensor.
- `Commands`. Os comandos representam métodos que os usuários do seu dispositivo podem executar no dispositivo. Por exemplo, um comando de redefinição ou um comando para ativar ou desativar um ventilador.

O exemplo a seguir mostra a interface para um dispositivo termostato:

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
  "@context": "http://azureiot.com/v1/contexts/Interface.json"
}
```

Uma interface tem alguns campos obrigatórios:

- `@id`: uma ID exclusiva na forma de um simples nome de recurso uniforme.
- `@type`: declara que esse objeto é uma interface.
- `@context`: especifica a versão DTDL usada para a interface.
- `contents`: lista as propriedades, a telemetria e os comandos que compõem seu dispositivo.

Neste exemplo simples, há apenas um único campo de telemetria. Uma descrição mínima do campo tem um:

- `@type`: especifica o tipo de recurso: `Telemetry`, `Property`ou `Command`.
- `name`: fornece o nome do valor de telemetria.
- `schema`: especifica o tipo de dados para a telemetria. Esse valor pode ser um tipo primitivo, como duplo, inteiro, booliano ou cadeia de caracteres. Também há suporte para tipos de objetos complexos, matrizes e mapas.

Outros campos opcionais, como nome de exibição e descrição, permitem que você adicione mais detalhes à interface e aos recursos.

### <a name="properties"></a>properties

Por padrão, as propriedades são somente leitura. Propriedades somente leitura significam que o valor da propriedade relatórios de dispositivo é atualizado para o Hub IoT. O Hub IoT não pode definir o valor de uma propriedade somente leitura.

Você também pode marcar uma propriedade como gravável em uma interface. Um dispositivo pode receber uma atualização para uma propriedade gravável do Hub IoT, bem como relatar atualizações de valor de propriedade para seu hub.

Os dispositivos não precisam estar conectados para definir valores de propriedade. Os valores atualizados são transferidos quando o dispositivo se conecta ao Hub. Esse comportamento se aplica a propriedades somente leitura e graváveis.

Não use Propriedades para enviar telemetria do seu dispositivo. Por exemplo, uma propriedade ReadOnly, como `temperatureSetting=80` deveria significar que a temperatura do dispositivo foi definida como 80, e o dispositivo está tentando chegar ou permanecer nessa temperatura.

Para propriedades graváveis, o aplicativo do dispositivo retorna um código de status de estado desejado, versão e descrição para indicar se ele recebeu e aplicou o valor da propriedade.

### <a name="telemetry"></a>Telemetria

Por padrão, o Hub IoT roteia todas as mensagens de telemetria de dispositivos para seu ponto de extremidade voltado para o [serviço interno (**mensagens/eventos**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) que é compatível com os [hubs de eventos](https://azure.microsoft.com/documentation/services/event-hubs/).

Você pode usar os [pontos de extremidade personalizados do Hub IOT e as regras de roteamento](../iot-hub/iot-hub-devguide-messages-d2c.md) para enviar telemetria para outros destinos, como o armazenamento de BLOBs ou outros hubs de eventos. As regras de roteamento usam Propriedades de mensagem para selecionar mensagens.

### <a name="commands"></a>Comandos

Os comandos são síncronos ou assíncronos. Um comando síncrono deve ser executado dentro de 30 segundos por padrão e o dispositivo deve ser conectado quando o comando chegar. Se o dispositivo responder no tempo ou o dispositivo não estiver conectado, o comando falhará.

Use comandos assíncronos para operações de longa execução. O dispositivo envia informações de progresso usando mensagens de telemetria. Essas mensagens de progresso têm as seguintes propriedades de cabeçalho:

- `iothub-command-name`: o nome do comando, por `UpdateFirmware`exemplo.
- `iothub-command-request-id`: a ID da solicitação gerada no lado do servidor e enviada para o dispositivo na chamada inicial.
- `iothub-interface-id`:  A ID da interface em que esse comando é definido, por exemplo `urn:example:AssetTracker:1`.
 `iothub-interface-name`: o nome da instância dessa interface, por exemplo `myAssetTracker`.
- `iothub-command-statuscode`: o código de status retornado do dispositivo, por exemplo `202`.

## <a name="register-a-device"></a>Registar um dispositivo

A Plug and Play de IoT facilita a divulgação dos recursos do seu dispositivo. Com Plug and Play de IoT, depois que o dispositivo se conectar ao Hub IoT, você deverá registrar seu modelo de capacidade de dispositivo. O registro permite que os clientes usem os recursos de Plug and Play de IoT do seu dispositivo.

Este guia mostra como registrar um dispositivo usando o SDK do dispositivo IoT do Azure para C.

Para cada interface que seu dispositivo implementa, você deve criar uma interface e conectá-la à sua implementação.

Para a interface termostato mostrada anteriormente, usando o SDK do C, você cria e conecta sua interface termostato à sua implementação:

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

Repita esse código para cada interface implementada pelo seu dispositivo.

Depois de criar uma interface, Registre seu modelo de funcionalidade de dispositivo e as interfaces com o Hub IoT:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaceHandleList, 2,
    null, null);
```

## <a name="use-a-device"></a>Usar um dispositivo

O Plug and Play IoT permite usar dispositivos que registraram seus recursos com o Hub IoT. Por exemplo, você pode acessar as propriedades e os comandos de um dispositivo diretamente.

Para usar um dispositivo de Plug and Play IoT conectado ao seu hub IoT, use a API REST do Hub IoT ou um dos SDKs da linguagem IoT. Os exemplos a seguir usam a API REST do Hub IoT.

Para obter o valor de uma propriedade de dispositivo, como a versão do firmware`fwVersion`() `DeviceInformation` na interface no termostato, use a API REST do digital gêmeos.

Se o dispositivo termostato for chamado `t-123`, você obterá todas as propriedades implementadas por seu dispositivo com uma chamada Get da API REST:

```REST
GET /digitalTwins/t-123/interfaces
```

Em geral, todas as propriedades são acessadas com esse modelo `{device-id}` de API REST, em que é o identificador do dispositivo:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Se você souber o nome da interface e quiser obter propriedades para essa interface específica, faça o escopo da solicitação para uma interface específica por nome:

```REST
GET /digitalTwins/t-123/interfaces/info
```

Mais geralmente, as propriedades de uma interface específica podem ser acessadas por meio desse `device-id` modelo de API REST, em que `{interface-name}` é o identificador do dispositivo e é o nome da interface:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Você pode chamar os comandos do dispositivo IoT Plug and Play diretamente. Se a `Thermostat` interface `t-123` no dispositivo tiver um `restart` comando, você poderá chamá-lo com uma pós-chamada à API REST:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Em geral, os comandos podem ser chamados por meio desse modelo de API REST:

- `device-id`: o identificador do dispositivo.
- `interface-name`: o nome da interface da seção implementações no modelo de funcionalidade do dispositivo.
- `command-name`: o nome do comando.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu sobre a modelagem de dispositivo, aqui estão alguns recursos adicionais:

- [DTDL (digital treme Definition Language)](https://aka.ms/DTDL)
- [SDK de dispositivo para C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](https://docs.microsoft.com/rest/api/iothub/device)
