---
title: Alertas de segurança personalizados para ioT Hub
description: Saiba mais sobre alertas de segurança personalizáveis e remediação recomendada utilizando o Defender para as funcionalidades e serviços do IoT Hub.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: d7a58bcdb759c3f31290cc7930eba6ca52fcc17b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784735"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Defender para alertas de segurança personalizados IoT Hub

O Defender for IoT analisa continuamente a sua solução IoT utilizando análises avançadas e inteligência de ameaça para alertá-lo para atividades maliciosas.

Encorajamo-lo a criar alertas personalizados com base no seu conhecimento do comportamento esperado do dispositivo para garantir que os alertas atuam como os indicadores mais eficientes de potencial compromisso na sua implantação e paisagem organizacional únicas.

As seguintes listas de alertas defender para IoT são definíveis por si com base no comportamento esperado do Hub IoT. Para obter mais informações sobre como personalizar cada alerta, consulte [criar alertas personalizados.](quickstart-create-custom-alerts.md)

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Alertas personalizados incorporados no IoT Hub

| Gravidade | Nome do alerta | Origem de dados | Description | Remediação sugerida |
|--|--|--|--|--|
| Baixo | Alerta personalizado - O número de mensagens de nuvem para dispositivo no protocolo AMQP está fora do alcance permitido | IoT Hub | O número de mensagens de nuvem para dispositivos (protocolo AMQP) dentro de uma janela de tempo específica está fora do intervalo atualmente configurado e admissível. |  |
| Baixo | Alerta personalizado - O número de mensagens rejeitadas para dispositivos no protocolo AMQP está fora do alcance permitido | IoT Hub | O número de mensagens de nuvem para dispositivos (protocolo AMQP) rejeitado pelo dispositivo, dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível. |  |
| Baixo | Alerta personalizado - O número de mensagens em nuvem no protocolo AMQP está fora do alcance permitido | IoT Hub | A quantidade de dispositivo para mensagens em nuvem (protocolo AMQP) dentro de uma janela de tempo específica está fora do alcance atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado - O número de invocações de métodos diretos está fora do alcance permitido | IoT Hub | A quantidade de método direto invoca dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível. |  |
| Baixo | Alerta personalizado - O número de uploads de ficheiros está fora do alcance permitido | IoT Hub | A quantidade de uploads de ficheiros dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível. |  |
| Baixo | Alerta personalizado - O número de mensagens de nuvem para dispositivo no protocolo HTTP está fora do alcance permitido | IoT Hub | A quantidade de nuvem para mensagens de dispositivo (protocolo HTTP) numa janela de tempo não está na gama permitida configurada |
| Baixo | Alerta personalizado - O número de mensagens rejeitadas para dispositivos no protocolo HTTP não está na gama permitida | IoT Hub | A quantidade de nuvem para mensagens de dispositivo (protocolo HTTP) dentro de uma janela de tempo específica está fora do alcance atualmente configurado e permitido. |
| Baixo | Alerta personalizado - O número de mensagens em nuvem no protocolo HTTP está fora do alcance permitido | IoT Hub | A quantidade de dispositivo para mensagens em nuvem (protocolo HTTP) dentro de uma janela de tempo específica está fora do alcance atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado - O número de mensagens de nuvem para dispositivo no protocolo MQTT está fora do alcance permitido | IoT Hub | A quantidade de nuvem para mensagens de dispositivo (protocolo MQTT) dentro de uma janela de tempo específica está fora do alcance atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado - O número de mensagens rejeitadas para dispositivos no protocolo MQTT está fora do alcance permitido | IoT Hub | A quantidade de nuvem para mensagens de dispositivo (protocolo MQTT) rejeitada pelo dispositivo dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível. |
| Baixo | Alerta personalizado - O número de mensagens de dispositivo para cloud no protocolo MQTT está fora do alcance permitido | IoT Hub | A quantidade de dispositivo para mensagens em nuvem (protocolo MQTT) dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível. |
| Baixo | Alerta personalizado - O número de purgas de fila de comando que estão fora do alcance permitido | IoT Hub | A quantidade de purgas de fila de comando dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível. |  |
| Baixo | Alerta personalizado - O número de atualizações gémeas do módulo está fora da gama permitida | IoT Hub | A quantidade de duas atualizações do módulo dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível. |
| Baixo | Alerta personalizado - O número de operações não autorizadas está fora do alcance permitido | IoT Hub | A quantidade de operações não autorizadas dentro de um intervalo de tempo específico está fora do alcance atualmente configurado e admissível. |

## <a name="next-steps"></a>Passos seguintes

- Saiba como [personalizar um alerta](quickstart-create-custom-alerts.md)
- Visão [geral](overview.md) do serviço defender para ioT
- Saiba como aceder aos [seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
