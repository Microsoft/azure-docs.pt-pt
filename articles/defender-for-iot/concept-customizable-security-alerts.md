---
title: Alertas de segurança personalizáveis
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
ms.openlocfilehash: 5a5b9182081e267f8e20cb0818202b9cb5ecd904
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939530"
---
# <a name="defender-for-iot-customizable-security-alerts"></a>Defender alertas de segurança personalizáveis IoT

O Defender for IoT analisa continuamente a sua solução IoT utilizando análises avançadas e inteligência de ameaça para alertá-lo para atividades maliciosas.

Encorajamo-lo a criar alertas personalizados com base no seu conhecimento do comportamento esperado do dispositivo para garantir que os alertas atuam como os indicadores mais eficientes de potencial compromisso na sua implantação e paisagem organizacional únicas.

A seguinte lista de alertas Defender para IoT é definível por si com base no seu comportamento ioT hub e/ou dispositivo esperado. Para obter mais detalhes sobre como personalizar cada alerta, consulte [criar alertas personalizados.](quickstart-create-custom-alerts.md)

## <a name="iot-hub-alerts-available-for-customization"></a>Alertas IoT Hub disponíveis para personalização

| Gravidade | Nome do alerta | Origem de dados | Descrição | Remediação sugerida|
|---|---|---|---|---|
| Baixo      | Alerta personalizado - número de mensagens de nuvem para dispositivo no protocolo AMQP está fora da gama permitida          | IoT Hub     | O número de mensagens de nuvem para dispositivo (protocolo AMQP) dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível.||
| Baixo      | Alerta personalizado - número de mensagens de dispositivo rejeitadas no protocolo AMQP está fora do alcance permitido | IoT Hub     | Número de mensagens de nuvem para dispositivo (protocolo AMQP) rejeitado pelo dispositivo, dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível.||
| Baixo      | Alerta personalizado - número de mensagens em nuvem no protocolo AMQP está fora da gama permitida      | IoT Hub     | A quantidade de dispositivo para mensagens em nuvem (protocolo AMQP) dentro de uma janela de tempo específica está fora do alcance atualmente configurado e permitido.|   |
| Baixo      | Alerta personalizado - número de invocações de método direto está fora do alcance permitido | IoT Hub     | A quantidade de método direto invoca dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível.||
| Baixo      | Alerta personalizado - o número de uploads de ficheiros está fora do alcance permitido | IoT Hub     | A quantidade de uploads de ficheiros dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível.| |
| Baixo      | Alerta personalizado - número de mensagens de nuvem para dispositivo no protocolo HTTP está fora do alcance permitido | IoT Hub     | A quantidade de nuvem para mensagens de dispositivo (protocolo HTTP) numa janela de tempo não está na gama permitida configurada                                  |
| Baixo      | Alerta personalizado - número de nuvem rejeitada para mensagens de dispositivo no protocolo HTTP não está na gama permitida | IoT Hub     | A quantidade de nuvem para mensagens de dispositivo (protocolo HTTP) dentro de uma janela de tempo específica está fora do alcance atualmente configurado e permitido. |
| Baixo      | Alerta personalizado - número de mensagens de dispositivo para cloud no protocolo HTTP está fora do alcance permitido | IoT Hub| A quantidade de dispositivo para mensagens em nuvem (protocolo HTTP)dentro de uma janela de tempo específica está fora do alcance atualmente configurado e permitido.|    |
| Baixo      | Alerta personalizado - número de mensagens de nuvem para dispositivo no protocolo MQTT está fora da gama permitida | IoT Hub     | A quantidade de nuvem para mensagens de dispositivo (protocolo MQTT) dentro de uma janela de tempo específica está fora do alcance atualmente configurado e permitido.|   |
| Baixo      | Alerta personalizado - número de mensagens de dispositivo rejeitadas no protocolo MQTT está fora do alcance permitido | IoT Hub     | A quantidade de nuvem para mensagens de dispositivo (protocolo MQTT) rejeitada pelo dispositivo dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível. |
| Baixo      | Alerta personalizado - número de mensagens de dispositivo para cloud no protocolo MQTT está fora da gama permitida          | IoT Hub     | A quantidade de dispositivo para mensagens em nuvem (protocolo MQTT) dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível.|
| Baixo      | Alerta personalizado - número de purgas de fila de comando está fora do alcance permitido                               | IoT Hub     | A quantidade de purgas de fila de comando dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível.||
| Baixo      | Alerta personalizado - número de atualizações gémeas do módulo está fora da gama permitida                                       | IoT Hub     | A quantidade de duas atualizações do módulo dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível.|
| Baixo      | Alerta personalizado - número de operações não autorizadas está fora do alcance permitido  | IoT Hub     | A quantidade de operações não autorizadas dentro de um intervalo de tempo específico está fora do alcance atualmente configurado e admissível.|
|

## <a name="agent-alerts-available-for-customization"></a>Alertas de agente disponíveis para personalização

| Gravidade | Nome do alerta | Origem de dados | Descrição | Remediação sugerida|
|---|---|---|---|---|
| Baixo      | Alerta personalizado - o número de ligações ativas está fora da gama permitida  | Agente       | O número de ligações ativas dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível.|  Investigue os registos do dispositivo. Saiba onde a ligação se originou e determine se é benigna ou maliciosa. Se for mal-intencionado, remova possível malware e compreenda a fonte. Se for benigno, adicione a fonte à lista de ligação permitida.  |
| Baixo      | Alerta personalizado - ligação de saída criada para um IP que não é permitido                             | Agente       | Uma ligação de saída foi criada para um IP que está fora da sua lista de IP permitida. |Investigue os registos do dispositivo. Saiba onde a ligação se originou e determine se é benigna ou maliciosa. Se for mal-intencionado, remova possível malware e compreenda a fonte. Se for benigno, adicione a fonte à lista de IP permitida.                        |
| Baixo      | Alerta personalizado - número de logins locais falhados está fora do alcance permitido                               | Agente       | A quantidade de logins locais falhados dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível. |   |
| Baixo      | Alerta personalizado - login de um utilizador que não está na lista de utilizadores permitido | Agente       | Um utilizador local fora da sua lista de utilizadores permitido, iniciou sessão no dispositivo.|  Se estiver a guardar dados brutos, navegue na sua conta de análise de registo e utilize os dados para investigar o dispositivo, identifique a fonte e, em seguida, corrija a lista de permitis/blocos para essas definições. Se não estiver a guardar dados brutos, vá ao dispositivo e corrija a lista de admissões/blocos para essas definições.|
| Baixo      | Alerta personalizado - um processo foi executado que não é permitido | Agente       | Um processo que não é permitido foi executado no dispositivo. |Se estiver a guardar dados brutos, navegue na sua conta de análise de registo e utilize os dados para investigar o dispositivo, identifique a fonte e, em seguida, corrija a lista de permitis/blocos para essas definições. Se não estiver a guardar dados brutos, vá ao dispositivo e corrija a lista de admissões/blocos para essas definições.  |
|

## <a name="next-steps"></a>Passos seguintes

- Saiba como [personalizar um alerta](quickstart-create-custom-alerts.md)
- Visão [geral](overview.md) do serviço defender para ioT
- Saiba como aceder aos [seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
