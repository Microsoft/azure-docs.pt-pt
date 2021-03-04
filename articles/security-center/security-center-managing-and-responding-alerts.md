---
title: Gerir alertas de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a utilizar as capacidades de Centro de Segurança do Azure para gerir e responder a alertas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 490f94c71611e07e765f5882cb4e3eec13033a6a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099340"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gerir e responder a alertas de segurança no Centro de Segurança Azure

Este tópico mostra-lhe como visualizar e processar os alertas do Security Center e proteger os seus recursos.

As deteções avançadas que desencadeiam alertas de segurança só estão disponíveis com o Azure Defender. Está disponível uma avaliação gratuita. Para atualizar, consulte [Enable Azure Defender](enable-azure-defender.md).

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque.

Para conhecer os diferentes tipos de alertas, consulte alertas de [segurança - um guia de referência.](alerts-reference.md)

Para uma visão geral de como o Centro de Segurança gera alertas, veja [como o Azure Security Center deteta e responde a ameaças.](security-center-alerts-overview.md)


## <a name="manage-your-security-alerts"></a>Gerencie os seus alertas de segurança

1. A partir da página geral do Centro de Segurança, selecione o azulejo de **alertas** de segurança no topo da página ou o link da barra lateral..

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Chegar à página de alertas de segurança da página geral do Azure Security Center":::

    A página de alertas de segurança abre.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de segurança do Azure Security Center":::

1. Para filtrar a lista de alertas, selecione qualquer um dos filtros relevantes. Pode opcionalmente adicionar mais filtros com a opção **filtro Adicionar.**

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Adicionar filtros à vista de alertas" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    A lista atualiza de acordo com as opções de filtragem que selecionou. Filtrar pode ser muito útil. Por exemplo, pode pretender resolver alertas de segurança que ocorreram nas últimas 24 horas, porque está a investigar uma potencial violação no sistema.


## <a name="respond-to-security-alerts"></a>Responder a alertas de segurança

1. A partir da lista **de alertas de segurança,** selecione um alerta. Um painel lateral abre e mostra uma descrição do alerta e de todos os recursos afetados. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Mini detalhes vista de um alerta de segurança":::

    > [!TIP]
    > Com este painel lateral aberto, pode rever rapidamente a lista de alertas com as setas para cima e para baixo no teclado.

1. Para mais informações, **selecione Ver todos os detalhes.**

    O painel esquerdo da página de alerta de segurança mostra informações de alto nível sobre o alerta de segurança: título, gravidade, estado, tempo de atividade, descrição da atividade suspeita e recurso afetado. A par do recurso afetado estão as tags Azure relevantes para o recurso. Use-os para inferir o contexto organizacional do recurso ao investigar o alerta.

    O painel direito inclui o separador **Detalhes de Alerta** contendo mais detalhes do alerta para ajudá-lo a investigar o problema: endereços IP, ficheiros, processos e muito mais.
     
    ![Sugestões para o que fazer sobre alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Também no painel de ação certo está o **separador de ação Take.** Utilize este separador para tomar mais ações relativas ao alerta de segurança. Ações como:
    - *Mitigate the threat* - fornece medidas de reparação manual para este alerta de segurança
    - *Prevenir futuros ataques* - fornece recomendações de segurança para ajudar a reduzir a superfície de ataque, aumentar a postura de segurança e, assim, prevenir futuros ataques
    - *Trigger automated response* - fornece a opção de desencadear uma app lógica como resposta a este alerta de segurança
    - *Suprimir alertas semelhantes* - fornece a opção de suprimir futuros alertas com características semelhantes se o alerta não for relevante para a sua organização

    ![Tome o separador de ação](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Ver também

Neste documento, aprendeu a ver alertas de segurança. Consulte as seguintes páginas para obter material relacionado:

- [Configurar regras de supressão de alerta](alerts-suppression-rules.md)
- [Automatizar respostas aos gatilhos do Centro de Segurança](workflow-automation.md)
- [Alertas de segurança – um guia de referência](alerts-reference.md)
