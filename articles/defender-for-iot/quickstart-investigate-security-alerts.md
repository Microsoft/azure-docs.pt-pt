---
title: 'Quickstart: Investigar alertas de segurança'
description: Compreenda, investigue e investigue o Defender por alertas de segurança IoT nos seus dispositivos IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: 172ae82288c2cb948839b69955b9491715eb4690
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90947340"
---
# <a name="quickstart-investigate-security-alerts"></a>Quickstart: Investigar alertas de segurança

A investigação agendada e a reparação dos alertas emitidos pelo Defender para ioT é a melhor forma de garantir o cumprimento e proteção em toda a sua solução IoT.

Neste quickstart, vamos explorar a informação disponível em cada alerta de segurança IoT, e explicar como perfurar e usar os detalhes de cada dispositivo alerta e relacionado, para responder e remediar. 

Vamos começar. 


## <a name="investigate-new-security-alerts"></a>Investigue novos alertas de segurança

A lista de alerta de segurança IoT Hub apresenta todos os alertas de segurança agregados para o seu Hub IoT. 

1. No portal Azure, abra o **Hub IoT** que pretende investigar para novos alertas.
1. No menu **Segurança,** selecione **Alertas**. Todos os alertas de segurança para o Hub IoT serão exibidos, e os alertas com uma **nova** bandeira marcam os vossos alertas das últimas 24 horas.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Investigue novos alertas de segurança IoT usando a nova bandeira de alerta":::
1. Selecione e abra qualquer alerta da lista para abrir os detalhes do alerta e aprofundar para os detalhes de alerta. 

## <a name="security-alert-details"></a>Detalhes do alerta de segurança

A abertura de cada alerta agregado apresenta a descrição detalhada do alerta, as etapas de reparação, o ID do dispositivo para cada dispositivo que desencadeou um alerta, bem como a gravidade do alerta e o acesso direto à investigação utilizando o Log Analytics. 

1. Selecione e abra qualquer alerta de segurança da lista de alertas de segurança do **hub IoT.**  >    >   
1. Reveja a **descrição** do alerta , **gravidade,** **origem da deteção,** detalhes do **dispositivo** de todos os dispositivos que emitiram este alerta durante o período de agregação.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Aprofundar e rever os detalhes de cada dispositivo em um alerta agregado "::: 
1. Depois de rever as especificidades do alerta, utilize as instruções **de remediação manual** para ajudar a remediar e/ou resolver o problema que causou o alerta. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Siga as medidas de reparação manual para ajudar a resolver ou remediar os alertas de segurança do dispositivo":::

1. Se for necessária uma investigação mais aprofundada, **investigue os alertas no Log Analytics** utilizando o link. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Para investigar mais aprofundadamente um alerta, use a investigação usando o link de análise de registo fornecido no ecrã":::

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para saber mais sobre os tipos de alerta do Defender e possíveis personalizações...

> [!div class="nextstepaction"]
> [Compreender alertas de segurança IoT](concept-security-alerts.md)
