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
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e17c7d6618d67d66c89875696c5c529af3a85ea9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440549"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gerir e responder a alertas de segurança no Centro de Segurança do Azure

Este tópico mostra-lhe como visualizar e processar os alertas que recebeu para proteger os seus recursos. 

* Para saber mais sobre os diferentes tipos de alertas, consulte [os tipos de alerta de segurança](alerts-reference.md).
* Para uma visão geral de como o Centro de Segurança gera alertas, consulte [como o Azure Security Center deteta e responde a ameaças.](security-center-alerts-overview.md)

> [!NOTE]
> Para permitir deteções avançadas, ative o Azure Defender. Está disponível uma avaliação gratuita. Para atualizar, selecione O Nível de Preços na [Política de Segurança](tutorial-security-policy.md). Veja [Preços do Centro de Segurança do Azure](security-center-pricing.md) para saber mais.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque.

> [!NOTE]
> Para obter mais informações sobre como funcionam as capacidades de deteção do Centro de Segurança, consulte [como o Azure Security Center deteta e responde a ameaças.](security-center-alerts-overview.md#detect-threats)

## <a name="manage-your-security-alerts"></a>Gerencie os seus alertas de segurança

1. A partir do painel do Centro de Segurança, consulte o azulejo  **de proteção** de ameaças para visualizar e ver os alertas.

    ![Mosaico Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Para ver mais detalhes sobre os alertas, clique no azulejo.

   ![Os Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para filtrar os alertas apresentados, clique em **Filtro**, e na lâmina **do filtro** que se abre, selecione as opções de filtro que pretende aplicar. A lista atualiza de acordo com o filtro selecionado. Filtrar pode ser muito útil. Por exemplo, pode pretender resolver alertas de segurança que ocorreram nas últimas 24 horas, porque está a investigar uma potencial violação no sistema.

    ![Filtragem de alertas no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Responder a alertas de segurança

1. Na lista de **alertas de segurança,** clique num alerta de segurança. Os recursos envolvidos e os passos que precisa de tomar para remediar um ataque são mostrados.

    ![Responder a alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Depois de rever a informação, clique num recurso que foi atacado.

    O painel esquerdo da página de alerta de segurança mostra informações de alto nível sobre o alerta de segurança: título, gravidade, estado, tempo de atividade, descrição da atividade suspeita e recurso afetado. A par do recurso afetado estão as tags Azure relevantes para o recurso. Use-os para inferir o contexto organizacional do recurso ao investigar o alerta.

    O painel direito inclui o separador **Detalhes de Alerta** contendo mais detalhes do alerta para ajudá-lo a investigar o problema: endereços IP, ficheiros, processos e muito mais.
     
    ![Sugestões para o que fazer sobre alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Também no painel de ação certo está o **separador de ação Take.** Utilize este separador para tomar mais ações relativas ao alerta de segurança. Ações como:
    - *Mitigate the threat* - fornece medidas de reparação manual para este alerta de segurança
    - *Prevenir futuros ataques* - fornece recomendações de segurança para ajudar a reduzir a superfície de ataque, aumentar a postura de segurança e, assim, prevenir futuros ataques
    - *Trigger automated response* - fornece a opção de desencadear uma app lógica como resposta a este alerta de segurança
    - *Suprimir alertas semelhantes* - fornece a opção de suprimir futuros alertas com características semelhantes se o alerta não for relevante para a sua organização

    ![Tome o separador de ação](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Consulte também

Neste documento, aprendeu a ver alertas de segurança. Consulte as seguintes páginas para obter material relacionado:

- [Configurar regras de supressão de alerta](alerts-suppression-rules.md)
- [Automatizar respostas a alertas e recomendações com automatização do fluxo de trabalho](workflow-automation.md)
