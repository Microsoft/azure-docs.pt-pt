---
title: 'Quickstart: Investigar recomendações de segurança'
description: Investigue recomendações de segurança com o Centro de Segurança Azure para o serviço de segurança IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: mlottner
ms.openlocfilehash: aa241a9108be32f88357065732c6f283d312be6c
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88144220"
---
# <a name="quickstart-investigate-security-recommendations"></a>Quickstart: Investigar recomendações de segurança


A análise oportuna e a mitigação das recomendações do Azure Security Center for IoT é a melhor forma de melhorar a postura de segurança e reduzir a superfície de ataque através da sua solução IoT.

Neste quickstart, vamos explorar a informação disponível em cada recomendação de segurança IoT, e explicar como aprofundar e usar os detalhes de cada recomendação e dispositivos relacionados, para reduzir o risco.

Vamos começar.

## <a name="investigate-new-recommendations"></a>Investigue novas recomendações

A lista de recomendações do IoT Hub apresenta todas as recomendações de segurança agregadas para o seu Hub IoT.

1.  No portal Azure, abra o **IoT Hub**   que pretende investigar para novas recomendações.

1.  No **Security**   menu Segurança, selecione **Recomendações.** Todas as recomendações de segurança para o IoT Hub serão apresentadas, e as recomendações com uma **nova**   bandeira, marcam as suas recomendações das últimas 24 horas. 

    [![Investigar recomendações de segurança com a ASC para IoT](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  Selecione e abra qualquer recomendação da lista para abrir os detalhes da recomendação e aprofundar as especificidades.

## <a name="security-recommendation-details"></a>Detalhes da recomendação de segurança

Abra cada recomendação agregada para apresentar a descrição detalhada da recomendação, etapas de reparação, ID do dispositivo para cada dispositivo que desencadeou uma recomendação. Também exibe gravidade de recomendação e acesso à investigação direta usando o Log Analytics.

1.  Selecione e abra qualquer recomendação de segurança da lista de recomendações de segurança do **IoT**   \>  **Security**   \>  **Recommendations**   Hub.

1.  Reveja a **descrição**da recomendação , **gravidade,** detalhes do **dispositivo**   de todos os dispositivos que emitiram esta recomendação durante o período de agregação. 

1.  Depois de rever as especificações da recomendação, utilize as instruções **de remediação manual**para ajudar a   remediar e resolver o problema que causou a recomendação. 

    [![Remediar recomendações de segurança com a ASC para ioT](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  Explore os detalhes da recomendação para um dispositivo específico selecionando o dispositivo pretendido na página de perfuração.

    [![Investigue recomendações de segurança específicas para um dispositivo com ASC para IoT](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  Se for necessária uma investigação mais aprofundada, **investigue a recomendação no Log Analytics**utilizando o   link. 


## <a name="next-steps"></a>Próximos passos

Avance para o próximo artigo para aprender a criar alertas personalizados...

> [!div class="nextstepaction"]
> [Criar alertas personalizados](quickstart-create-custom-alerts.md)
