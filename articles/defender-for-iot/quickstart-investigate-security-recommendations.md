---
title: Investigar recomendações de segurança"
description: Investigue recomendações de segurança com o Defensor para o serviço de segurança IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: shhazam
ms.openlocfilehash: 0e902db38e4145bf94ab6a235bc1210b520327a1
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809189"
---
# <a name="quickstart-investigate-security-recommendations"></a>Quickstart: Investigar recomendações de segurança


A análise oportuna e a mitigação das recomendações do Defender para IoT é a melhor forma de melhorar a postura de segurança e reduzir a superfície de ataque através da sua solução IoT.

Neste quickstart, vamos explorar a informação disponível em cada recomendação de segurança IoT, e explicar como aprofundar e usar os detalhes de cada recomendação e dispositivos relacionados, para reduzir o risco.

Vamos começar.

## <a name="investigate-new-recommendations"></a>Investigue novas recomendações

A lista de recomendações do IoT Hub apresenta todas as recomendações de segurança agregadas para o seu Hub IoT.

1.  No portal Azure, abra o **IoT Hub** que pretende investigar para novas recomendações.

1.  No menu **Segurança,** selecione **Recomendações.** Todas as recomendações de segurança para o IoT Hub serão apresentadas, e as recomendações com uma **nova** bandeira, marcam as suas recomendações das últimas 24 horas. 

    :::image type="content" source="media/quickstart/investigate-security-recommendations-expanded.png#lightbox" alt-text="Investigar recomendações de segurança com a ASC para IoT[media/quickstart/investigate-security-recommendations-inline.png)":::


1.  Selecione e abra qualquer recomendação da lista para abrir os detalhes da recomendação e aprofundar as especificidades.

## <a name="security-recommendation-details"></a>Detalhes da recomendação de segurança

Abra cada recomendação agregada para apresentar a descrição detalhada da recomendação, etapas de reparação, ID do dispositivo para cada dispositivo que desencadeou uma recomendação. Também exibe gravidade de recomendação e acesso à investigação direta usando o Log Analytics.

1.  Selecione e abra qualquer recomendação de segurança da lista de recomendações de segurança do **IoT**  >    >   Hub.

1.  Reveja a **descrição** da recomendação , **gravidade,** detalhes do **dispositivo** de todos os dispositivos que emitiram esta recomendação durante o período de agregação. 

1.  Depois de rever as especificações da recomendação, utilize as instruções **de remediação manual** para ajudar a remediar e resolver o problema que causou a recomendação. 

    :::image type="content" source="media/quickstart/remediate-security-recommendations-inline.png" alt-text="Remediar recomendações de segurança com a ASC para ioT" lightbox="media/quickstart/remediate-security-recommendations-expanded.png":::

1.  Explore os detalhes da recomendação para um dispositivo específico selecionando o dispositivo pretendido na página de perfuração.

    :::image type="content" source="media/quickstart/explore-security-recommendation-detail-inline.png" alt-text="Investigue recomendações de segurança específicas para um dispositivo com ASC para IoT" lightbox="media/quickstart/explore-security-recommendation-detail-expanded.png":::

1.  Se for necessária uma investigação mais aprofundada, **investigue a recomendação no Log Analytics** utilizando o link. 

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a criar alertas personalizados...

> [!div class="nextstepaction"]
> [Criar alertas personalizados](quickstart-create-custom-alerts.md)
