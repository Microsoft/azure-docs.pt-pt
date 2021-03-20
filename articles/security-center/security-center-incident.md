---
title: Gerir incidentes de segurança no Centro de Segurança Azure | Microsoft Docs
description: Este documento ajuda-o a usar o Azure Security Center para gerir incidentes de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 722a508679c74f9d62df07575ffa1006528f4398
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652111"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerir incidentes de segurança no Centro de Segurança Azure

Triagem e investigação de alertas de segurança pode ser demorado até para os analistas de segurança mais qualificados. Para muitos, é difícil saber por onde começar. 

O Security Center utiliza [análises](./security-center-alerts-overview.md) para ligar a informação entre [alertas de segurança distintos](security-center-managing-and-responding-alerts.md). Utilizando estas ligações, o Security Center pode fornecer uma única visão de uma campanha de ataque e seus alertas relacionados para ajudá-lo a entender as ações do atacante e os recursos afetados.

Esta página fornece uma visão geral dos incidentes no Centro de Segurança.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

No Centro de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com padrões de [cadeia de ataque](alerts-reference.md#intentions). Incidentes aparecem na página [de alertas de segurança.](security-center-managing-and-responding-alerts.md) Selecione um incidente para ver os alertas relacionados e obter mais informações.

## <a name="managing-security-incidents"></a>Gerir incidentes de segurança

1. Na página de alertas do Security Center, utilize o botão **de filtro Adicionar** para filtrar por nome de alerta o nome de alerta Incidente de segurança detetado em **vários recursos**. 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Localizar os incidentes na página de alertas no Centro de Segurança Azure":::

    A lista está agora filtrada para mostrar apenas incidentes. Note que os incidentes de segurança têm um ícone diferente dos alertas de segurança.

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Lista de incidentes na página de alertas no Centro de Segurança Azure":::

1. Para ver detalhes de um incidente, selecione um da lista. Um painel lateral aparece com mais detalhes sobre o incidente.

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="Painel lateral mostrando detalhes do incidente":::

1. Para ver mais detalhes, **selecione Ver todos os detalhes.**

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