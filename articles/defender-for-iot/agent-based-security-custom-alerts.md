---
title: Alertas personalizados de segurança baseados em agente
titleSuffix: Azure Defender for IoT
description: Saiba mais sobre alertas de segurança personalizáveis e remediação recomendada utilizando o Defender para as funcionalidades e serviços do dispositivo IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: 5d0eeb046d7a4ba474a1ed4a2cfb07a07f1c3888
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493310"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Defender para dispositivos IoT alertas de segurança personalizados

O Defender for IoT analisa continuamente a sua solução IoT utilizando análises avançadas e inteligência de ameaça para alertá-lo para atividades maliciosas.

Encorajamo-lo a criar alertas personalizados com base no seu conhecimento do comportamento esperado do dispositivo para garantir que os alertas atuam como os indicadores mais eficientes de potencial compromisso na sua implantação e paisagem organizacional únicas.

As seguintes listas de alertas Defender para IoT são definíveis por si com base no comportamento esperado do dispositivo IoT. Para obter mais informações sobre como personalizar cada alerta, consulte [criar alertas personalizados.](quickstart-create-custom-alerts.md)

## <a name="agent-based-security-custom-alerts"></a>Alertas personalizados de segurança baseados em agente

| Gravidade | Nome do alerta | Origem de dados | Description | Remediação sugerida |
|--|--|--|--|--|
| Baixo | Alerta personalizado - O número de ligações ativas está fora do alcance permitido | Defensor-IoT-micro-agente, Azure RTOS | O número de ligações ativas dentro de uma janela de tempo específica está fora do alcance atualmente configurado e admissível. | Investigue os registos do dispositivo. Saiba onde a ligação se originou e determine se é benigna ou maliciosa. Se for mal-intencionado, remova possível malware e compreenda a fonte. Se for benigno, adicione a fonte à lista de ligação permitida. |
| Baixo | Alerta personalizado - A ligação de saída criada para um IP que não é permitido | Defensor-IoT-micro-agente, Azure RTOS | Uma ligação de saída foi criada para um IP que está fora da sua lista de IP permitida. | Investigue os registos do dispositivo. Saiba onde a ligação se originou e determine se é benigna ou maliciosa. Se for mal-intencionado, remova possível malware e compreenda a fonte. Se for benigno, adicione a fonte à lista de IP permitida. |
| Baixo | Alerta personalizado - O número de logins locais falhados está fora do alcance permitido | Defensor-IoT-micro-agente, Azure RTOS | O número de logins locais falhados dentro de uma janela de tempo específica está fora da gama atualmente configurada e admissível. |  |
| Baixo | Alerta personalizado - O sinal de um utilizador que não está na lista de utilizadores permitido | Defensor-IoT-micro-agente, Azure RTOS | Um utilizador local fora da sua lista de utilizadores permitido, iniciou sessão no dispositivo. | Se estiver a guardar dados brutos, navegue na sua conta de análise de registo e utilize os dados para investigar o dispositivo, identifique a fonte e, em seguida, corrija a lista de permitis/blocos para essas definições. Se não estiver a guardar dados brutos, vá ao dispositivo e corrija a lista de admissões/blocos para essas definições. |
| Baixo | Alerta personalizado - Foi executado um processo que não é permitido | Defensor-IoT-micro-agente, Azure RTOS | Um processo que não é permitido foi executado no dispositivo. | Se estiver a guardar dados brutos, navegue na sua conta de análise de registo e utilize os dados para investigar o dispositivo, identifique a fonte e, em seguida, corrija a lista de permitis/blocos para essas definições. Se não estiver a guardar dados brutos, vá ao dispositivo e corrija a lista de admissões/blocos para essas definições. |
|

## <a name="next-steps"></a>Passos seguintes

- Saiba como [personalizar um alerta](quickstart-create-custom-alerts.md)
- Visão [geral](overview.md) do serviço defender para ioT
- Saiba como aceder aos [seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
