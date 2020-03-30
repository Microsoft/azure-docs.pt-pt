---
title: Gerir incidentes de segurança no Centro de Segurança Azure Microsoft Docs
description: Este documento ajuda-o a usar o Centro de Segurança Azure para gerir incidentes de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415670"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerir incidentes de segurança no Centro de Segurança Azure

A triagem e a investigação de alertas de segurança podem ser demorados até para os analistas de segurança mais qualificados, e para muitos é difícil saber por onde começar. Através da utilização de [análises](security-center-detection-capabilities.md) para ligar as informações entre [alertas de segurança](security-center-managing-and-responding-alerts.md) distintos, o Centro de Segurança pode fornecer-lhe uma vista única de uma campanha de ataque e todos os alertas relacionados. Deste modo, pode perceber rapidamente quais as ações realizadas pelo atacante e que recursos foram afetados.

Este tópico explica sobre incidentes no Centro de Segurança e como remediar os seus alertas.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

No Centro de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com padrões de [cadeia de ataque](alerts-reference.md#intentions). Incidentes aparecem na lista de Alertas de [Segurança.](security-center-managing-and-responding-alerts.md) Clique num incidente para ver os alertas relacionados, o que lhe permite obter mais informações sobre cada ocorrência.

## <a name="managing-security-incidents"></a>Gerir incidentes de segurança

1. No painel do Centro de Segurança, clique no azulejo de alertas de **segurança.** Os incidentes e alertas estão listados. Observe que a descrição do incidente de segurança tem um ícone diferente em comparação com outros alertas.

    ![Ver incidentes de segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para ver detalhes, clique num incidente. O **incidente de segurança detetado** lâmina apresenta mais detalhes. A secção de **Informação Geral** pode dar uma ideia do que desencadeou o alerta de segurança. Exibe informações como o recurso-alvo, endereço IP de origem (quando aplicável), se o alerta ainda estiver ativo, e recomendações sobre como remediar.  

    ![Responda a incidentes de segurança no Centro de Segurança Azure](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Para obter mais informações sobre cada alerta, clique num alerta. A remediação sugerida pelo Centro de Segurança varia de acordo com o alerta de segurança.

   > [!NOTE]
   > O mesmo alerta pode existir como parte de um incidente, bem como ser visível como um alerta autónomo.

    ![Detalhes do alerta](./media/security-center-incident/security-center-incident-alert.png)

1. Siga os passos de reparação dados para cada alerta.


## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a utilizar a capacidade de incidentes de segurança no Centro de Segurança. Para obter informações relacionadas, consulte o seguinte:

* [Proteção contra ameaças no Centro de Segurança Azure](threat-protection.md)
* [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md)
* [Gerir alertas de segurança](security-center-managing-and-responding-alerts.md)