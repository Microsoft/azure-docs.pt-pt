---
title: Gerir incidentes de segurança no Centro de Segurança Azure Microsoft Docs
description: Este documento ajuda-o a usar o Azure Security Center para gerir incidentes de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: c31d3136ec4bcdf6da7680d41de078f8bd03d256
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447101"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerir incidentes de segurança no Centro de Segurança Azure

Triagem e investigação de alertas de segurança pode ser demorado até para os analistas de segurança mais qualificados. Para muitos, é difícil saber por onde começar. 

O Security Center utiliza [análises](security-center-detection-capabilities.md) para ligar a informação entre [alertas de segurança distintos](security-center-managing-and-responding-alerts.md). Utilizando estas ligações, o Security Center pode fornecer uma única visão de uma campanha de ataque e seus alertas relacionados para ajudá-lo a entender as ações do atacante e os recursos afetados.

Esta página fornece uma visão geral dos incidentes no Centro de Segurança.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

No Centro de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com padrões de [cadeia de ataque](alerts-reference.md#intentions). Incidentes aparecem na página [de alertas de segurança.](security-center-managing-and-responding-alerts.md) Selecione um incidente para ver os alertas relacionados e obter mais informações.

## <a name="managing-security-incidents"></a>Gerir incidentes de segurança

1. Na página geral do Centro de Segurança, selecione o azulejo alertas de **segurança.** Os incidentes e alertas estão listados. Note que os incidentes de segurança têm um ícone diferente dos alertas de segurança.

    ![Ver incidentes de segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para ver detalhes, selecione um incidente. A página **de incidentes** de Segurança mostra mais detalhes. 

    [![Responda a incidentes de segurança no Centro de Segurança Azure](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    O painel esquerdo da página do incidente de segurança mostra informações de alto nível sobre o incidente de segurança: título, gravidade, estado, tempo de atividade, descrição e o recurso afetado. Junto ao recurso afetado pode ver as tags Azure relevantes. Utilize estas etiquetas para inferir o contexto organizacional do recurso ao investigar o alerta.

    O painel direito inclui o separador **Alertas** com os alertas de segurança que foram correlacionados como parte deste incidente. 

    >[!TIP]
    > Para mais informações sobre um alerta específico, selecione-o. 

    [![Aba é de ação do incidente](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Para mudar para o separador **de ação,** selecione o separador ou o botão na parte inferior do painel direito. Utilize este separador para tomar outras ações tais como:
    - *Mitigate the threat* - fornece medidas de reparação manual para este incidente de segurança
    - *Prevenir futuros ataques* - fornece recomendações de segurança para ajudar a reduzir a superfície de ataque, aumentar a postura de segurança e prevenir futuros ataques
    - *Trigger automated response* - fornece a opção de desencadear uma App Lógica como resposta a este incidente de segurança
    - *Suprimir alertas semelhantes* - fornece a opção de suprimir futuros alertas com características semelhantes se o alerta não for relevante para a sua organização 

   > [!NOTE]
   > O mesmo alerta pode existir como parte de um incidente, bem como ser visível como um alerta autónomo.

1. Para remediar as ameaças no incidente, siga as medidas de reparação fornecidas com cada alerta.


## <a name="next-steps"></a>Passos seguintes

Esta página explicou as capacidades de incidente de segurança do Centro de Segurança. Para obter informações relacionadas, consulte as seguintes páginas:

- [Alertas de segurança no Centro de Segurança](security-center-alerts-overview.md)
- [Gerir e responder a alertas de segurança](security-center-managing-and-responding-alerts.md)