---
title: Protocolos e portas de comunicação do Azure IoT Hub Microsoft Docs
description: Guia do desenvolvedor - descreve os protocolos de comunicação suportados para comunicações dispositivo-a-nuvem e nuvem-para-dispositivo e os números de porta que devem estar abertos.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 1792535fab79ed20bdf77f96b4fc39f13b0c7bbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016010"
---
# <a name="reference---choose-a-communication-protocol"></a>Referência - escolha um protocolo de comunicação

o Hub IoT permite que os dispositivos utilizem os seguintes protocolos para comunicações do lado do dispositivo –

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT através de WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP através de WebSockets
* HTTPS

Para obter informações sobre como estes protocolos suportam funcionalidades específicas do IoT Hub, consulte [a orientação de comunicações dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) e [a orientação de comunicações Cloud-to-device](iot-hub-devguide-c2d-guidance.md).

A tabela a seguir fornece as recomendações de alto nível para a sua escolha do protocolo:

| Protocolo | Quando deve escolher este protocolo |
| --- | --- |
| MQTT <br> MQTT sobre WebSocket |Utilize em todos os dispositivos que não necessitem de ligar vários dispositivos (cada um com as suas próprias credenciais por dispositivo) sobre a mesma ligação TLS. |
| AMQP <br> AMQP sobre WebSocket |Utilize em gateways de campo e nuvem para aproveitar a ligação multiplexing através dos dispositivos. |
| HTTPS |Utilize dispositivos que não suportem outros protocolos. |

Considere os seguintes pontos quando escolher o seu protocolo para comunicações do lado do dispositivo:

* **Padrão nuvem-a-dispositivo.** HTTPS não tem uma forma eficiente de implementar o impulso do servidor. Como tal, quando estiver a utilizar HTTPS, os dispositivos pesquisam ioT Hub para mensagens cloud-to-device. Esta abordagem é ineficiente tanto para o dispositivo como para o IoT Hub. De acordo com as atuais diretrizes HTTPS, cada dispositivo deve sondar mensagens a cada 25 minutos ou mais. O servidor de suporte MQTT e AMQP pressiona ao receber mensagens nuvem-dispositivo. Permitem empurrar imediatamente as mensagens do IoT Hub para o dispositivo. Se a latência da entrega for uma preocupação, MQTT ou AMQP são os melhores protocolos a utilizar. Para dispositivos raramente ligados, o HTTPS também funciona.

* **Portais de campo.** MQTT e HTTPS suportam apenas uma identidade de dispositivo (ID mais credenciais) por ligação TLS. Por esta razão, estes protocolos não são suportados para [cenários de gateway de campo](iot-hub-devguide-endpoints.md#field-gateways) que requerem mensagens multiplexing usando identidades de vários dispositivos através de um único ou um conjunto de ligações a montante ao IoT Hub. Tais gateways podem usar um protocolo que suporta múltiplas identidades de dispositivos por ligação, como AMQP, para o seu tráfego a montante.

* **Dispositivos de baixo recurso.** As bibliotecas MQTT e HTTPS têm uma pegada menor do que as bibliotecas AMQP. Como tal, se o dispositivo tiver recursos limitados (por exemplo, menos de 1-MB RAM), estes protocolos podem ser a única implementação do protocolo disponível.

* **Atravessação de rede**. O protocolo padrão amQP utiliza a porta 5671, e mQTT ouve na porta 8883. A utilização destas portas pode causar problemas em redes que estão fechadas a protocolos não-HTTPS. Utilize MQTT sobre WebSockets, AMQP sobre WebSockets ou HTTPS neste cenário.

* **Tamanho da carga**útil. MQTT e AMQP são protocolos binários, que resultam em cargas mais compactas do que HTTPS.

> [!WARNING]
> Ao utilizar o HTTPS, cada dispositivo deve sondar mensagens nuvem-dispositivo não mais do que uma vez a cada 25 minutos. Em desenvolvimento, cada dispositivo pode sondar com mais frequência, se desejar.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="port-numbers"></a>Números portuários

Os dispositivos podem comunicar com o IoT Hub em Azure utilizando vários protocolos. Normalmente, a escolha do protocolo é impulsionada pelos requisitos específicos da solução. A tabela que se segue lista as portas de saída que devem estar abertas para que um dispositivo possa utilizar um protocolo específico:

| Protocolo | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT através de WebSockets |443 |
| AMQP |5671 |
| AMQP através de WebSockets |443 |
| HTTPS |443 |

Depois de ter criado um hub IoT numa região de Azure, o hub IoT mantém o mesmo endereço IP para a vida daquele hub IoT. No entanto, se a Microsoft mudar o hub IoT para uma unidade de escala diferente para manter a qualidade do serviço, então é atribuído um novo endereço IP.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como o IoT Hub implementa o protocolo MQTT, consulte [Comunicar com o seu hub IoT utilizando o protocolo MQTT](iot-hub-mqtt-support.md).
