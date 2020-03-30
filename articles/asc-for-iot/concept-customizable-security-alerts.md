---
title: Guia de alerta de segurança personalizável para o Centro de Segurança Azure para o IoT Microsoft Docs
description: Conheça alertas de segurança personalizáveis e remediação recomendada utilizando o Azure Security Center para funcionalidades e serviços IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 32d79267559480de8ec91e5e66196bfd57e01cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299578"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>Alertas de segurança do Centro de Segurança do Azure para IoT

O Azure Security Center for IoT analisa continuamente a sua solução IoT utilizando análises avançadas e inteligência de ameaça para alertá-lo para atividades maliciosas.

Encorajamo-lo a criar alertas personalizados com base no seu conhecimento do comportamento esperado do dispositivo para garantir que os alertas atuam como os indicadores mais eficientes de potencial compromisso na sua implantação organizacional única e paisagem. 

A seguinte lista de alertas do Azure Security Center para alertas IoT é definível por si com base no seu comportamento esperado do IoT Hub e/ou do dispositivo. Para mais detalhes sobre como personalizar cada alerta, consulte [criar alertas personalizados](quickstart-create-custom-alerts.md).

## <a name="iot-hub-alerts-available-for-customization"></a>IoT Hub alertas disponíveis para personalização 



| Gravidade | Nome do alerta | Origem de dados | Descrição | Remediação sugerida|
|---|---|---|---|---|
| Baixa      | Alerta personalizado - número de mensagens de nuvem para dispositivo no protocolo AMQP está fora do alcance permitido          | IoT Hub     | O número de mensagens cloud to device (protocolo AMQP) dentro de uma janela de tempo específica está fora do intervalo atualmente configurado e admissível.||
| Baixa      | Alerta personalizado - número de mensagens de nuvem rejeitada para dispositivos no protocolo AMQP está fora do intervalo permitido | IoT Hub     | Número de mensagens de nuvem para dispositivo (protocolo AMQP) rejeitadas pelo dispositivo, dentro de um intervalo de tempo específico está fora do intervalo atualmente configurado e admissível.||
| Baixa      | Alerta personalizado - número de dispositivo sonante para mensagens em nuvem no protocolo AMQP está fora do alcance permitido      | IoT Hub     | A quantidade de dispositivo para cloud messages (protocolo AMQP) dentro de uma janela de tempo específica está fora da gama atualmente configurada e permitida.|   |
| Baixa      | Alerta personalizado - número de invocações de método direto está fora do intervalo permitido | IoT Hub     | A quantidade de método direto invoca dentro de um prazo específico está fora do intervalo atualmente configurado e admissível.||
| Baixa      | Alerta personalizado - o número de uploads de ficheiros está fora do intervalo permitido | IoT Hub     | A quantidade de uploads de ficheiros dentro de um intervalo de tempo específico está fora do intervalo atualmente configurado e admissível.| |
| Baixa      | Alerta personalizado - número de mensagens de nuvem para dispositivo no protocolo HTTP está fora do intervalo permitido | IoT Hub     | A quantidade de cloud para mensagens de dispositivo (protocolo HTTP) em uma janela de tempo não está na gama configurada permitida                                  |
| Baixa      | Alerta personalizado - número de mensagens de nuvem rejeitada para dispositivos no protocolo HTTP não está na gama permitida | IoT Hub     | A quantidade de cloud para mensagens de dispositivo (protocolo HTTP) dentro de um intervalo de tempo específico está fora do intervalo atualmente configurado e admissível. |
| Baixa      | Alerta personalizado - número de dispositivo sonorizador de mensagens em nuvem no protocolo HTTP está fora do intervalo permitido | IoT Hub| A quantidade de dispositivo para cloud messages (protocolo HTTP)dentro de um intervalo de tempo específico está fora do intervalo atualmente configurado e admissível.|    |
| Baixa      | Alerta personalizado - número de mensagens de nuvem para dispositivo no protocolo MQTT está fora do intervalo permitido | IoT Hub     | A quantidade de cloud para mensagens de dispositivo (protocolo MQTT) dentro de uma janela de tempo específica está fora da gama atualmente configurada e permitida.|   |
| Baixa      | Alerta personalizado - número de mensagens de nuvem rejeitada para dispositivos no protocolo MQTT está fora do intervalo permitido | IoT Hub     | A quantidade de cloud para mensagens de dispositivo (protocolo MQTT) rejeitada pelo dispositivo dentro de um intervalo de tempo específico está fora da gama atualmente configurada e permitida. |
| Baixa      | Alerta personalizado - número de dispositivo para mensagens em nuvem no protocolo MQTT está fora do intervalo permitido          | IoT Hub     | A quantidade de dispositivo para cloud messages (protocolo MQTT) dentro de uma janela de tempo específica está fora da gama atualmente configurada e permitida.|
| Baixa      | Alerta personalizado - número de purgas de fila de comando está fora do alcance permitido                               | IoT Hub     | A quantidade de purgas de fila de comando dentro de um intervalo de tempo específico está fora do intervalo atualmente configurado e admissível.||
| Baixa      | Alerta personalizado - número de atualizações de módulos twin está fora do intervalo permitido                                       | IoT Hub     | A quantidade de atualizações de módulos twin dentro de um espaço de tempo específico está fora do intervalo atualmente configurado e admissível.|
| Baixa      | Alerta personalizado - número de operações não autorizadas está fora do alcance permitido  | IoT Hub     | A quantidade de operações não autorizadas dentro de um prazo específico está fora do intervalo atualmente configurado e admissível.|
|

## <a name="agent-alerts-available-for-customization"></a>Alertas de agente disponíveis para personalização 

| Gravidade | Nome do alerta | Origem de dados | Descrição | Remediação sugerida|
|---|---|---|---|---|
| Baixa      | Alerta personalizado - o número de ligações ativas está fora do intervalo permitido  | Agente       | O número de ligações ativas dentro de um prazo específico está fora do intervalo atualmente configurado e admissível.|  Investigue os registos do dispositivo. Saiba de onde a ligação originou e determine se é benigna ou maliciosa. Se for malicioso, remova possível malware e compreenda a origem. Se benigno, adicione a fonte à lista de ligação permitida.  |
| Baixa      | Alerta personalizado - ligação de saída criada a um IP que não é permitido                             | Agente       | Foi criada uma ligação de saída a um IP que está fora da sua lista de IP permitida. |Investigue os registos do dispositivo. Saiba de onde a ligação originou e determine se é benigna ou maliciosa. Se for malicioso, remova possível malware e compreenda a origem. Se benigno, adicione a fonte à lista ip permitida.                        |
| Baixa      | Alerta personalizado - número de logins locais falhados está fora do intervalo permitido                               | Agente       | A quantidade de logins locais falhados dentro de um intervalo de tempo específico está fora do intervalo atualmente configurado e admissível. |   |
| Baixa      | Alerta personalizado - login de um utilizador que não está na lista de utilizadores permitida | Agente       | Um utilizador local fora da sua lista de utilizadores permitido, fez login no dispositivo.|  Se estiver a guardar dados brutos, navegue para a sua conta de análise de registo e utilize os dados para investigar o dispositivo, identifique a fonte e, em seguida, corrija a lista de permitir/bloquear para essas definições. Se não estiver atualmente a guardar dados brutos, vá ao dispositivo e corrija a lista de permitir/bloquear para essas definições.|
| Baixa      | Alerta personalizado - foi executado um processo que não é permitido | Agente       | Um processo que não é permitido foi executado no dispositivo. |Se estiver a guardar dados brutos, navegue para a sua conta de análise de registo e utilize os dados para investigar o dispositivo, identifique a fonte e, em seguida, corrija a lista de permitir/bloquear para essas definições. Se não estiver atualmente a guardar dados brutos, vá ao dispositivo e corrija a lista de permitir/bloquear para essas definições.  |
|

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [personalizar um alerta](quickstart-create-custom-alerts.md)
- Azure Security Center for IoT service [Overview](overview.md)
- Saiba como aceder aos [seus dados](how-to-security-data-access.md) de segurança
- Saiba mais sobre [investigar um dispositivo](how-to-investigate-device.md)