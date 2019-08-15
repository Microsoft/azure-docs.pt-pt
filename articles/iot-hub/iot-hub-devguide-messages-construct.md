---
title: Entender o formato de mensagem do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor-descreve o formato e o conteúdo esperado de mensagens do Hub IoT.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: dd45c68fb7d7a7226d18dd1afc508b3dbf7b770b
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950444"
---
# <a name="create-and-read-iot-hub-messages"></a>Criar e ler mensagens do Hub IoT

Para dar suporte à interoperabilidade direta entre protocolos, o Hub IoT define um formato de mensagem comum para todos os protocolos voltados para o dispositivo. Esse formato de mensagem é usado para o [Roteamento do dispositivo para a nuvem](iot-hub-devguide-messages-d2c.md) e para as mensagens da [nuvem para o dispositivo](iot-hub-devguide-messages-c2d.md) . 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O Hub IoT implementa mensagens do dispositivo para a nuvem usando um padrão de transmissão de mensagens. As mensagens do dispositivo para a nuvem do Hub IoT são mais semelhantes a *eventos* de [hubs de eventos](/azure/event-hubs/) do que *mensagens* do [barramento de serviço](/azure/service-bus-messaging/) , pois há um alto volume de eventos passando pelo serviço que pode ser lido por vários leitores.

Uma mensagem do Hub IoT consiste em:

* Um conjunto predeterminado de *Propriedades do sistema* , conforme listado abaixo.

* Um conjunto de *Propriedades de aplicativo*. Um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acessar, sem a necessidade de desserializar o corpo da mensagem. O Hub IoT nunca modifica essas propriedades.

* Um corpo binário opaco.

Os valores e nomes de propriedade podem conter apenas caracteres alfanuméricos ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` ASCII, além de quando você envia mensagens do dispositivo para a nuvem usando o protocolo HTTPS ou envia mensagens da nuvem para o dispositivo.

As mensagens do dispositivo para a nuvem com o Hub IoT têm as seguintes características:

* As mensagens do dispositivo para a nuvem são duráveis e mantidas em um ponto de extremidade de **mensagens/eventos** padrão do Hub IOT por até sete dias.

* As mensagens do dispositivo para a nuvem podem ter no máximo 256 KB e podem ser agrupadas em lotes para otimizar os envios. Os lotes podem ter no máximo 256 KB.

* O Hub IoT não permite particionamento arbitrário. As mensagens do dispositivo para a nuvem são particionadas com base em suaDeviceID de origem.

* Conforme explicado em [controlar o acesso ao Hub IOT](iot-hub-devguide-security.md), o Hub IOT habilita a autenticação por dispositivo e o controle de acesso.

* Você pode carimbar mensagens com informações que vão para as propriedades do aplicativo. Para obter mais informações, consulte [aprimoramentos de mensagem](iot-hub-message-enrichments-overview.md).

Para obter mais informações sobre como codificar e decodificar mensagens enviadas usando diferentes protocolos, consulte [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Propriedades do sistema de mensagens do Hub IOT **D2C**

| Propriedade | Descrição  |Usuário configurável?|Palavra-chave para consulta de roteamento|
| --- | --- | --- | --- |
| ID da mensagem |Um identificador configurável pelo usuário para a mensagem usada para padrões de solicitação-resposta. Formato: Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (até 128 caracteres) de caracteres alfanuméricos `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`ASCII de 7 bits +.  | Sim | MessageId |
| iothub-enqueuedtime |Data e hora em que a mensagem do [dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) foi recebida pelo Hub IOT. | Não | EnqueuedTime |
| ID do usuário |Uma ID usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, ele é `{iot hub name}`definido como. | Sim | UserId |
| iothub-connection-device-id |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Ele contém a DeviceID do dispositivo que enviou a mensagem. | Não | DeviceId |
| iothub-connection-auth-generation-id |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Ele contém a **generationid** (de acordo com [as propriedades de identidade do dispositivo](iot-hub-devguide-identity-registry.md#device-identity-properties)) do dispositivo que enviou a mensagem. | Não |DeviceGenerationId |
| iothub-Connection-auth-Method |Um método de autenticação definido pelo Hub IoT em mensagens do dispositivo para a nuvem. Essa propriedade contém informações sobre o método de autenticação usado para autenticar o dispositivo que envia a mensagem.| Não | AuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Propriedades do sistema de mensagens do Hub IOT **C2D**

| Propriedade | Descrição  |Usuário configurável?|
| --- | --- | --- |
| ID da mensagem |Um identificador configurável pelo usuário para a mensagem usada para padrões de solicitação-resposta. Formato: Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (até 128 caracteres) de caracteres alfanuméricos `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`ASCII de 7 bits +.  |Sim|
| número de sequência |Um número (exclusivo por fila de dispositivo) atribuído pelo Hub IoT para cada mensagem da nuvem para o dispositivo. |Não|
| para |Um destino especificado em mensagens da [nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md) . |Não|
| absolute-expiry-time |Data e hora da expiração da mensagem. |Não|   |
| ID de correlação |Uma propriedade de cadeia de caracteres em uma mensagem de resposta que normalmente contém a MessageId da solicitação, em padrões de solicitação-resposta. |Sim|
| ID do usuário |Uma ID usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, ele é `{iot hub name}`definido como. |Sim|
| iothub-ACK |Um gerador de mensagens de comentários. Essa propriedade é usada em mensagens da nuvem para o dispositivo para solicitar que o Hub IoT gere mensagens de comentários como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (padrão): nenhuma mensagem de comentário é gerada, **positiva**: receber uma mensagem de comentários se a mensagem foi concluída, **negativa**: receberá uma mensagem de comentários se a mensagem expirou (ou se a contagem máxima de entrega foi atingido) sem ser concluído pelo dispositivo ou **completo**: positivo e negativo. |Sim|

## <a name="message-size"></a>Tamanho da mensagem

O Hub IoT mede o tamanho da mensagem de forma independente de protocolo, considerando apenas a carga real. O tamanho em bytes é calculado como a soma do seguinte:

* O tamanho do corpo em bytes.
* O tamanho em bytes de todos os valores das propriedades do sistema de mensagens.
* O tamanho em bytes de todos os nomes de propriedade de usuário e valores.

Os nomes e valores de propriedade são limitados a caracteres ASCII, portanto, o comprimento das cadeias é igual ao tamanho em bytes.

## <a name="anti-spoofing-properties"></a>Propriedades anti-falsificação

Para evitar a falsificação de dispositivo em mensagens do dispositivo para a nuvem, o Hub IoT carimba todas as mensagens com as seguintes propriedades:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

Os dois primeiros contêm a DeviceID e a **generationid** do dispositivo de origem, de acordo com [as propriedades de identidade do dispositivo](iot-hub-devguide-identity-registry.md#device-identity-properties).

A propriedade **iothub-Connection-auth-Method** contém um objeto JSON serializado, com as seguintes propriedades:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre limites de tamanho de mensagem no Hub IoT, confira [cotas e limitação do Hub IOT](iot-hub-devguide-quotas-throttling.md).

* Para saber como criar e ler mensagens do Hub IoT em várias linguagens de programação, consulte os [guias de início rápido](quickstart-send-telemetry-node.md).
