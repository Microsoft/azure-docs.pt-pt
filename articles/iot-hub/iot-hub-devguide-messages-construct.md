---
title: Compreenda o formato de mensagem Azure IoT Hub | Microsoft Docs
description: Guia do desenvolvedor - descreve o formato e o conteúdo esperado das mensagens IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6d6b7122963b51619f26b8d02a8be4ad39261afb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147700"
---
# <a name="create-and-read-iot-hub-messages"></a>Criar e ler mensagens do Hub IoT

Para suportar uma interoperabilidade perfeita através dos protocolos, o IoT Hub define um formato comum de mensagem para todos os protocolos virados para o dispositivo. Este formato de mensagem é utilizado tanto [para o encaminhamento de dispositivos para nuvem como](iot-hub-devguide-messages-d2c.md) para mensagens [cloud-to-device.](iot-hub-devguide-messages-c2d.md) 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O IoT Hub implementa mensagens de dispositivo para nuvem usando um padrão de mensagens de streaming. As mensagens de dispositivo para nuvem do IoT Hub são mais como [eventos de Event Hubs](../event-hubs/index.yml)  do que *mensagens* [de Service Bus,](../service-bus-messaging/index.yml) na qual há um grande volume de eventos que passam pelo serviço que pode ser lido por vários leitores.

Uma mensagem IoT Hub consiste em:

* Um conjunto pré-determinado de propriedades do *sistema* como listado abaixo.

* Um conjunto de propriedades de *aplicação.* Um dicionário de propriedades de cordas que a aplicação pode definir e aceder, sem precisar de desseializar o corpo da mensagem. O IoT Hub nunca modifica estas propriedades.

* Um corpo binário opaco.

Os nomes e valores de propriedade só podem conter caracteres alfanuméricos ASCII, além ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` de que envia mensagens dispositivo-a-nuvem utilizando o protocolo HTTPS ou envia mensagens cloud-to-device.

As mensagens dispositivo-a-nuvem com o IoT Hub têm as seguintes características:

* As mensagens dispositivo-nuvem são duráveis e retidas no ponto final de **mensagens/eventos** padrão de um hub IoT durante até sete dias.

* As mensagens dispositivo-nuvem podem ser no máximo 256 KB, e podem ser agrupadas em lotes para otimizar as envios. Os lotes podem ser no máximo 256 KB.

* IoT Hub não permite divisórias arbitrárias. As mensagens dispositivo-nuvem são divididas com base no seu dispositivo de **origemId**.

* Como explicado no [Control access to IoT Hub](iot-hub-devguide-security.md), O IoT Hub permite a autenticação por dispositivo e o controlo de acesso.

* Pode carimbar mensagens com informações que vão para as propriedades da aplicação. Para mais informações, por favor leia as [de informação.](iot-hub-message-enrichments-overview.md)

Para obter mais informações sobre como codificar e descodificar mensagens enviadas utilizando diferentes protocolos, consulte [Azure IoT SDKs](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Propriedades do sistema de mensagens **D2C** IoT Hub

| Propriedade | Descrição  |Definição de utilizador?|Palavra-chave para </br>consulta de encaminhamento|
| --- | --- | --- | --- |
| mensagem id |Um identificador de padrão de utilizador para a mensagem utilizada para padrões de resposta a pedidos. Formato: Uma corda sensível a casos (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  | Yes | messageId |
| iothub-enqueuedtime |Data e hora a mensagem [Dispositivo-a-Nuvem](iot-hub-devguide-d2c-guidance.md) foi recebida pelo IoT Hub. | No | tempo enqueso |
| user-id |Um ID usado para especificar a origem das mensagens. Quando as mensagens são geradas pelo IoT Hub, é definido para `{iot hub name}` . | Yes | userId |
| iothub-connection-device-id |Um ID definido pelo IoT Hub em mensagens dispositivo-a-nuvem. Contém o **dispositivoId** do dispositivo que enviou a mensagem. | No | conexãoDeviceId |
| iothub-connection-module-id |Um ID definido pelo IoT Hub em mensagens dispositivo-a-nuvem. Contém o **móduloId** do dispositivo que enviou a mensagem. | No | conexãoModuleId |
| iothub-conexão-auth-geração id |Um ID definido pelo IoT Hub em mensagens dispositivo-a-nuvem. Contém a **ligaçãoDeviceGenerationId** (de acordo com as propriedades de [identidade do dispositivo)](iot-hub-devguide-identity-registry.md#device-identity-properties)do dispositivo que enviou a mensagem. | No |conexãoDeviceGenerationId |
| iothub-conexão-método auth |Um método de autenticação definido pelo IoT Hub em mensagens dispositivo-a-nuvem. Esta propriedade contém informações sobre o método de autenticação utilizado para autenticar o dispositivo que envia a mensagem.| No | conexão Dezembromethod |
| dt-dataschema | Este valor é definido pelo hub IoT em mensagens dispositivo-a-nuvem. Contém o iD do modelo do dispositivo na ligação do dispositivo. | No | N/D |
| dt-sujeito | O nome do componente que está a enviar as mensagens dispositivo-a-nuvem. | Yes | N/D |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Propriedades do sistema de mensagens **C2D** IoT Hub

| Propriedade | Descrição  |Definição de utilizador?|
| --- | --- | --- |
| mensagem id |Um identificador de padrão de utilizador para a mensagem utilizada para padrões de resposta a pedidos. Formato: Uma corda sensível a casos (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  |Yes|
| sequência-número |Um número (único por fila de dispositivos) atribuído pelo IoT Hub a cada mensagem nuvem-para-dispositivo. |No|
| para |Um destino especificado nas mensagens [Cloud-to-Device.](iot-hub-devguide-c2d-guidance.md) |No|
| prazo de absoluta expiração |Data e hora da validade da mensagem. |No|   |
| correlation-id |Uma propriedade de cadeia numa mensagem de resposta que normalmente contém o MessageId do pedido, em padrões de resposta de pedido. |Yes|
| user-id |Um ID usado para especificar a origem das mensagens. Quando as mensagens são geradas pelo IoT Hub, é definido para `{iot hub name}` . |Yes|
| iothub-ack |Um gerador de mensagens de feedback. Esta propriedade é usada em mensagens nuvem-dispositivo para solicitar ioT Hub para gerar mensagens de feedback como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (predefinido): nenhuma mensagem de feedback é gerada, **positiva:** receber uma mensagem de feedback se a mensagem foi concluída, **negativa:** receber uma mensagem de feedback se a mensagem expirou (ou a contagem máxima de entrega foi atingida) sem ser completada pelo dispositivo, ou **completa:** positiva e negativa. |Yes|

### <a name="system-property-names"></a>Nomes de propriedade do sistema

Os nomes das propriedades do sistema variam em função do ponto final para o qual as mensagens estão a ser encaminhadas. Por favor, consulte a tabela abaixo para mais detalhes sobre estes nomes.

|Nome de propriedade do sistema|Hubs de Eventos|Armazenamento do Azure|Service Bus|Event Grid|
|--------------------|----------|-------------|-----------|----------|
|ID de mensagem|mensagem id|messageId|MessageId|mensagem id|
|ID do utilizador|user-id|userId|IDUtilizador|user-id|
|Id do dispositivo de ligação|iothub-connection-device-id| conexãoDeviceId|iothub-connection-device-id|iothub-connection-device-id|
|Id do módulo de conexão|iothub-connection-module-id|conexãoModuleId|iothub-connection-module-id|iothub-connection-module-id|
|Id de geração de ligação|iothub-conexão-auth-geração id|conexãoDeviceGenerationId| iothub-conexão-auth-geração id|iothub-conexão-auth-geração id|
|Método do autómes de conexão|iothub-conexão-método auth|conexão Dezembromethod|iothub-conexão-método auth|iothub-conexão-método auth|
|conteúdoType|tipo de conteúdo|conteúdoType|ConteúdoType|iothub-tipo de conteúdo|
|contentEncoding|codificação de conteúdos|contentEncoding|ContentEncoding|codificação iothub-content|
|iothub-enqueuedtime|iothub-enqueuedtime|tempo enqueso| N/D |iothub-enqueuedtime|
|CorrelationId|correlation-id|correlationId|CorrelationId|correlation-id|
|dt-dataschema|dt-dataschema|dt-dataschema|dt-dataschema|dt-dataschema|
|dt-sujeito|dt-sujeito|dt-sujeito|dt-sujeito|dt-sujeito|

## <a name="message-size"></a>Tamanho da mensagem

O IoT Hub mede o tamanho da mensagem de forma protocolar-agnóstica, considerando apenas a carga útil real. O tamanho dos bytes é calculado como a soma dos seguintes valores:

* O tamanho do corpo em bytes.
* O tamanho em bytes de todos os valores das propriedades do sistema de mensagem.
* O tamanho em bytes de todos os nomes e valores da propriedade do utilizador.

Os nomes e valores dos imóveis estão limitados aos caracteres ASCII, pelo que o comprimento das cordas é igual ao tamanho dos bytes.

## <a name="anti-spoofing-properties"></a>Propriedades anti-falsificação

Para evitar a falsificação de dispositivos em mensagens de dispositivo para nuvem, o IoT Hub carimba todas as mensagens com as seguintes propriedades:

* **iothub-connection-device-id**
* **iothub-conexão-auth-geração id**
* **iothub-conexão-método auth**

Os dois primeiros contêm o **dispositivoId** e **geraçãoId** do dispositivo de origem, de acordo com [as propriedades de identidade do Dispositivo](iot-hub-devguide-identity-registry.md#device-identity-properties).

A propriedade **iothub-connection-auth-method** contém um objeto serializado JSON, com as seguintes propriedades:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre os limites do tamanho da mensagem no IoT Hub, consulte [as quotas do IoT Hub e o estrangulamento.](iot-hub-devguide-quotas-throttling.md)

* Para aprender a criar e ler mensagens IoT Hub em várias linguagens de programação, consulte os [Quickstarts](quickstart-send-telemetry-node.md).