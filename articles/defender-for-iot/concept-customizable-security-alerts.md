---
title: Alertas de segurança personalizados
description: Saiba mais sobre alertas de segurança personalizáveis e remediação recomendada utilizando o Defender para funcionalidades e serviços IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 021b05176da141fad2f4555b0617b5a4a51e453f
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809291"
---
# <a name="defender-for-iot-custom-security-alerts"></a>Defender alertas de segurança personalizados IoT

O Defender for IoT analisa continuamente a sua solução IoT utilizando análises avançadas e inteligência de ameaça para alertá-lo para atividades maliciosas.

Encorajamo-lo a criar alertas personalizados com base no seu conhecimento do comportamento esperado do dispositivo para garantir que os alertas atuam como os indicadores mais eficientes de potencial compromisso na sua implantação e paisagem organizacional únicas.

As seguintes listas de alertas Defender para IoT são definíveis por si com base no seu comportamento ioT hub e/ou dispositivo esperado. Para obter mais informações sobre como personalizar cada alerta, consulte [criar alertas personalizados.](quickstart-create-custom-alerts.md)

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


## <a name="agent-based-security-custom-alerts"></a>Alertas personalizados de segurança baseados em agente

| Gravidade | Nome do alerta | Origem de dados | Description | Remediação sugerida |
|--|--|--|--|--|
| Baixo | Alerta personalizado - O número de ligações ativas está fora do alcance permitido | Módulo de segurança clássico, Azure RTOS | O número de ligações ativas dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível. | Investigue os registos do dispositivo. Saiba onde a ligação se originou e determine se é benigna ou maliciosa. Se for mal-intencionado, remova possível malware e compreenda a fonte. Se for benigno, adicione a fonte à lista de ligação permitida. |
| Baixo | Alerta personalizado - A ligação de saída criada para um IP que não é permitido | Módulo de segurança clássico, Azure RTOS | Uma ligação de saída foi criada para um IP que está fora da sua lista de IP permitida. | Investigue os registos do dispositivo. Saiba onde a ligação se originou e determine se é benigna ou maliciosa. Se for mal-intencionado, remova possível malware e compreenda a fonte. Se for benigno, adicione a fonte à lista de IP permitida. |
| Baixo | Alerta personalizado - O número de logins locais falhados está fora do alcance permitido | Módulo de segurança clássico, Azure RTOS | O número de logins locais falhados dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível. |  |
| Baixo | Alerta personalizado - O sinal de um utilizador que não está na lista de utilizadores permitido | Módulo de segurança clássico, Azure RTOS | Um utilizador local fora da sua lista de utilizadores permitido, iniciou sessão no dispositivo. | Se estiver a guardar dados brutos, navegue na sua conta de análise de registo e utilize os dados para investigar o dispositivo, identifique a fonte e, em seguida, corrija a lista de permitis/blocos para essas definições. Se não estiver a guardar dados brutos, vá ao dispositivo e corrija a lista de admissões/blocos para essas definições. |
| Baixo | Alerta personalizado - Foi executado um processo que não é permitido | Módulo de segurança clássico, Azure RTOS | Um processo que não é permitido foi executado no dispositivo. | Se estiver a guardar dados brutos, navegue na sua conta de análise de registo e utilize os dados para investigar o dispositivo, identifique a fonte e, em seguida, corrija a lista de permitis/blocos para essas definições. Se não estiver a guardar dados brutos, vá ao dispositivo e corrija a lista de admissões/blocos para essas definições. |
|

## <a name="next-steps"></a>Passos seguintes

- Saiba como [personalizar um alerta](quickstart-create-custom-alerts.md)
- Visão [geral](overview.md) do serviço defender para ioT
- Saiba como aceder aos [seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
