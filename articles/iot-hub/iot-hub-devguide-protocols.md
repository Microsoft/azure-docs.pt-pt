---
title: Portas e protocolos de comunicação do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – descreve os protocolos de comunicação suportados para comunicações de dispositivo para a cloud e cloud-para-dispositivo e os números de porta tem de estar abertos.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 7082ebc4ca3066f84ca9790797cfa04e437f78a3
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051060"
---
# <a name="reference---choose-a-communication-protocol"></a>Referência - escolher um protocolo de comunicação

IoT Hub permite que os dispositivos utilizar os protocolos seguintes para comunicações de lado do dispositivo:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT através de WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP através de WebSockets
* HTTPS

Para obter informações sobre como esses protocolos suportam funcionalidades específicas do IoT Hub, veja [documentação de orientação do dispositivo-para-cloud comunicações](iot-hub-devguide-d2c-guidance.md) e [orientação de comunicações da Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

A tabela seguinte fornece as recomendações de alto nível da sua preferência de protocolo:

| Protocolo | Quando deve escolher este protocolo |
| --- | --- |
| MQTT <br> MQTT sobre WebSocket |Utilize em todos os dispositivos que não necessitam para ligar vários dispositivos (cada um com suas próprias credenciais por dispositivo) através da mesma ligação de TLS. |
| AMQP <br> AMQP sobre WebSocket |Utilize nos gateways de campo e na cloud para tirar partido da multiplexação em todos os dispositivos de ligação. |
| HTTPS |Utilização para os dispositivos que não é possível suportar outros protocolos. |

Considere os seguintes pontos ao escolher o seu protocolo de comunicações do lado do dispositivo:

* **Padrão de cloud-para-dispositivo**. HTTPS não tem uma maneira eficiente de implementar o push do servidor. Como tal, quando estiver a utilizar o HTTPS, os dispositivos consultar o IoT Hub para mensagens na cloud para o dispositivo. Essa abordagem é ineficiente para o dispositivo e o IoT Hub. Em diretrizes atuais de HTTPS, cada dispositivo deve consultar mensagens de cada 25 minutos ou mais. MQTT e AMQP suportam o push do servidor ao receber mensagens da cloud para o dispositivo. Elas permitem pushes imediatas de mensagens do IoT Hub no dispositivo. Se a latência de entrega é uma preocupação, MQTT ou AMQP são os protocolos melhores para utilizar. Para dispositivos ligados raramente, HTTPS funciona também.

* **Gateways de campo**. Quando utiliza MQTT e HTTPS, não é possível ligar vários dispositivos (cada um com suas próprias credenciais por dispositivo) com a mesma ligação de TLS. Para [cenários de gateway de campo](iot-hub-devguide-endpoints.md#field-gateways) que exigem uma conexão TLS entre o gateway de campo e o IoT Hub para cada dispositivo ligado, esses protocolos são inferior ao ideal.

* **Dispositivos de recursos de baixa**. As bibliotecas MQTT e HTTPS tem um requisito de espaço mais pequeno do que as bibliotecas AMQP. Assim, se o dispositivo é limitado a recursos (por exemplo, menor que 1 MB de RAM), esses protocolos podem ser a única implementação de protocolo disponível.

* **Rede transversal**. O protocolo AMQP padrão utiliza a porta 5671 e MQTT escuta na porta 8883. A utilização destas portas poderia causar problemas em redes que estão fechadas para protocolos não-HTTPS. Utilize MQTT ao longo WebSockets, AMQP através de WebSockets ou HTTPS neste cenário.

* **Tamanho da carga**. MQTT e AMQP são protocolos binários, que resultam em cargas mais compacta do que o HTTPS.

> [!WARNING]
> Ao utilizar o HTTPS, cada dispositivo deve consultar para mensagens na cloud para o dispositivo de cada 25 minutos ou mais. No entanto, durante o desenvolvimento, é aceitável para consultar mais frequentemente do que a cada 25 minutos.

## <a name="port-numbers"></a>Números de porta

Dispositivos podem comunicar com o IoT Hub no Azure utilizando vários protocolos. Normalmente, a escolha do protocolo é orientada pelos requisitos específicos da solução. A tabela seguinte lista as portas de saída que tem de estar abertas para um dispositivo poder utilizar um protocolo específico:

| Protocolo | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT através de WebSockets |443 |
| AMQP |5671 |
| AMQP através de WebSockets |443 |
| HTTPS |443 |

Depois de criar um hub IoT numa região do Azure, o hub IoT mantém o mesmo endereço IP para a vida útil de nesse hub IoT. No entanto, se Microsoft move o hub IoT para uma unidade de escala diferente para manter a qualidade de serviço, em seguida, ele será atribuído um novo endereço IP.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como o IoT Hub implementa o protocolo MQTT, veja [comunicar com o seu hub IoT com o protocolo MQTT](iot-hub-mqtt-support.md).
