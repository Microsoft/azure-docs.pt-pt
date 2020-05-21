---
title: Compreender o formato de mensagem Azure IoT Hub [ Formato de mensagem Hub Azure IoT ] Microsoft Docs
description: Guia de desenvolvedores - descreve o formato e o conteúdo esperado das mensagens IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 77145c691f5b2b6364de64e491aac3c84495d464
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726133"
---
# <a name="create-and-read-iot-hub-messages"></a>Criar e ler mensagens do Hub IoT

Para suportar a interoperabilidade perfeita entre os protocolos, o IoT Hub define um formato de mensagem comum para todos os protocolos virados para dispositivos. Este formato de mensagem é utilizado tanto para mensagens [de encaminhamento de dispositivos para nuvem](iot-hub-devguide-messages-d2c.md) como [cloud-to-device.](iot-hub-devguide-messages-c2d.md) 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O IoT Hub implementa mensagens dispositivo-cloud usando um padrão de mensagens de streaming. As mensagens dispositivo-to-cloud do IoT Hub são mais como [eventos de Event Hubs](/azure/event-hubs/) *events* do que *mensagens* [de Ônibus de serviço,](/azure/service-bus-messaging/) na qual há um grande volume de eventos que passam pelo serviço que pode ser lido por vários leitores.

Uma mensagem IoT Hub consiste em:

* Um conjunto pré-determinado de propriedades do *sistema* listadas abaixo.

* Um conjunto de propriedades de *aplicação.* Um dicionário de propriedades de cordas que a aplicação pode definir e aceder, sem necessidade de desserializar o corpo da mensagem. O IoT Hub nunca modifica estas propriedades.

* Um corpo binário opaco.

Os nomes e valores de propriedade só podem conter caracteres alfanuméricos ASCII, além de ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` enviar mensagens dispositivo-nuvem usando o protocolo HTTPS ou enviar mensagens cloud-to-device.

As mensagens dispositivo-a-nuvem com o IoT Hub têm as seguintes características:

* As mensagens dispositivo-to-cloud são duradouras e retidas no ponto final de **mensagens/eventos** padrão de um hub IoT por um ponto final de até sete dias.

* As mensagens dispositivo-a-nuvem podem ser no máximo 256 KB, e podem ser agruparadas em lotes para otimizar envios. Lotes podem ser no máximo 256 KB.

* O IoT Hub não permite a partilha arbitrária. As mensagens dispositivo-nuvem são divididas com base no seu **dispositivo originárioId**.

* Como explicado no [controle de acesso ao IoT Hub,](iot-hub-devguide-security.md)o IoT Hub permite a autenticação por dispositivo e o controlo de acesso.

* Pode carimbar mensagens com informações que vão para as propriedades da aplicação. Para mais informações, por favor leia as informações sobre o enriquecimento de [mensagens.](iot-hub-message-enrichments-overview.md)

Para obter mais informações sobre como codificar e descodificar mensagens enviadas utilizando diferentes protocolos, consulte [Os DSKs Azure IoT](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Propriedades do sistema de mensagens **D2C** IoT Hub

| Propriedade | Descrição  |Settable do utilizador?|Palavra-chave para </br>consulta de encaminhamento|
| --- | --- | --- | --- |
| mensagem-id |Um identificador de definição de utilizador para a mensagem utilizada para padrões de resposta a pedidos. Formato: Uma cadeia sensível a casos (até 128 caracteres) de caracteres alfanuméricos de 7 bits ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  | Sim | mensagemId |
| iothub-enqueuedtime |Data e hora a mensagem [Dispositivo-a-Nuvem](iot-hub-devguide-d2c-guidance.md) foi recebida pelo IoT Hub. | Não | enqueuedTime |
| user-id |Um ID usado para especificar a origem das mensagens. Quando as mensagens são geradas pelo IoT Hub, está definida para `{iot hub name}` . | Sim | userId |
| iothub-ligação-dispositivo-id |Um ID definido por IoT Hub em mensagens dispositivo-a-nuvem. Contém o **dispositivoId** do dispositivo que enviou a mensagem. | Não | conexãoDeviceId |
| iothub-ligação-módulo-id |Um ID definido por IoT Hub em mensagens dispositivo-a-nuvem. Contém o **móduloId** do dispositivo que enviou a mensagem. | Não | conexãoModuleId |
| iothub-connection-auth-generation-id |Um ID definido por IoT Hub em mensagens dispositivo-a-nuvem. Contém a **ligaçãoDeviceGenerationId** (de acordo com as propriedades de identidade do [Dispositivo)](iot-hub-devguide-identity-registry.md#device-identity-properties)do dispositivo que enviou a mensagem. | Não |conexãoDeviceGenerationId |
| iothub-connection-auth-method |Um método de autenticação definido pelo IoT Hub em mensagens dispositivo-a-nuvem. Esta propriedade contém informações sobre o método de autenticação utilizado para autenticar o dispositivo enviando a mensagem.| Não | conexãoAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Propriedades do sistema de mensagens **C2D** IoT Hub

| Propriedade | Descrição  |Settable do utilizador?|
| --- | --- | --- |
| mensagem-id |Um identificador de definição de utilizador para a mensagem utilizada para padrões de resposta a pedidos. Formato: Uma cadeia sensível a casos (até 128 caracteres) de caracteres alfanuméricos de 7 bits ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  |Sim|
| número de sequência |Um número (único por fila de dispositivos) atribuído pelo IoT Hub a cada mensagem cloud-to-device. |Não|
| para |Um destino especificado nas mensagens [Cloud-to-Device.](iot-hub-devguide-c2d-guidance.md) |Não|
| tempo de caducidade absoluta |Data e hora da expiração da mensagem. |Não|   |
| correlation-id |Uma propriedade de cadeia numa mensagem de resposta que normalmente contém o MessageId do pedido, em padrões de resposta a pedidos. |Sim|
| user-id |Um ID usado para especificar a origem das mensagens. Quando as mensagens são geradas pelo IoT Hub, está definida para `{iot hub name}` . |Sim|
| iothub-ack |Um gerador de mensagens de feedback. Esta propriedade é usada em mensagens cloud-to-device para solicitar ioT Hub para gerar mensagens de feedback como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: nenhum (predefinido): **nenhuma** mensagem de feedback é gerada, **positiva**: receber uma mensagem de feedback se a mensagem estiver concluída, **negativa:** receber uma mensagem de feedback se a mensagem expirar (ou a contagem máxima de entrega foi atingida) sem ser completada pelo dispositivo, ou **completa**: positiva e negativa. |Sim|

### <a name="system-property-names"></a>Nomes de propriedade do sistema

Os nomes de propriedade do sistema variam em função do ponto final para o qual as mensagens estão a ser encaminhadas. Por favor, consulte a tabela abaixo para obter detalhes sobre estes nomes.


|Nome de propriedade do sistema|Hubs de Eventos|Armazenamento do Azure|Service Bus|Event Grid|
|--------------------|----------|-------------|-----------|----------|
|ID da mensagem|mensagem-id|mensagemId|MensagemId|mensagem-id|
|Iot hub enqueued tempo|iothub-enqueuedtime|enqueuedTime|iothub-enqueuedtime|iothub-enqueuedtime|
|ID do utilizador|user-id|userId|IDUtilizador|user-id|
|Id do dispositivo de ligação|iothub-ligação-dispositivo-id| conexãoDeviceId|iothub-ligação-dispositivo-id|iothub-ligação-dispositivo-id|
|Id módulo de ligação|iothub-ligação-módulo-id|conexãoModuleId|iothub-ligação-módulo-id|iothub-ligação-módulo-id|
|Ligação auth generation id|iothub-connection-auth-generation-id|conexãoDeviceGenerationId| iothub-connection-auth-generation-id|iothub-connection-auth-generation-id|
|Método de ligação auth|iothub-connection-auth-method|conexãoAuthMethod|iothub-connection-auth-method|iothub-connection-auth-method|
|conteúdoType|tipo de conteúdo|conteúdoType|Tipo de conteúdo|iothub-conteúdo-tipo|
|contentEncoding|codificação de conteúdo|contentEncoding|Codificação de Conteúdos|iothub-content-codificação|
|iothub-enqueuedtime|iothub-enqueuedtime|enqueuedTime|     |iothub-enqueuedtime|
|iothub-interface-nome|iothub-interface-nome|interfaceNome|Iothub-interface-nome|iothub-interface-nome|
|CorrelationId|correlation-id|correlationId|CorrelationId|correlation-id|

## <a name="message-size"></a>Tamanho da mensagem

O IoT Hub mede o tamanho da mensagem de forma agnóstica protocolar, considerando apenas a carga real. O tamanho dos bytes é calculado como a soma dos seguintes valores:

* O tamanho do corpo em bytes.
* O tamanho em bytes de todos os valores das propriedades do sistema de mensagens.
* O tamanho em bytes de todos os nomes e valores de propriedade do utilizador.

Os nomes e valores de propriedade estão limitados a caracteres ASCII, por isso o comprimento das cordas é igual ao tamanho em bytes.

## <a name="anti-spoofing-properties"></a>Propriedades anti-falsificação

Para evitar a falsificação de dispositivos em mensagens dispositivo-nuvem, o IoT Hub carimba todas as mensagens com as seguintes propriedades:

* **iothub-ligação-dispositivo-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

Os dois primeiros contêm o **dispositivoId** e **geraçãoId** do dispositivo de origem, de acordo com [as propriedades de identidade do Dispositivo](iot-hub-devguide-identity-registry.md#device-identity-properties).

A propriedade **iothub-connection-auth-method** contém um objeto serializado JSON, com as seguintes propriedades:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Próximos passos

* Para obter informações sobre os limites de tamanho da mensagem no IoT Hub, consulte [as quotas do Hub IoT e a aceleração.](iot-hub-devguide-quotas-throttling.md)

* Para aprender a criar e ler mensagens IoT Hub em várias linguagens de programação, consulte os [Quickstarts](quickstart-send-telemetry-node.md).
