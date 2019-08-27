---
title: Gerenciar incidentes de segurança na central de segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar a central de segurança do Azure para gerenciar incidentes de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/27/2019
ms.author: rkarlin
ms.openlocfilehash: df487b5d16568605544fb69111579bd4a9fb045f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047651"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerenciar incidentes de segurança na central de segurança do Azure

A triagem e a investigação de alertas de segurança podem ser demoradas para até mesmo os analistas de segurança mais qualificados e, para muitos, é difícil saber até onde começar. Através da utilização de [análises](security-center-detection-capabilities.md) para ligar as informações entre [alertas de segurança](security-center-managing-and-responding-alerts.md) distintos, o Centro de Segurança pode fornecer-lhe uma vista única de uma campanha de ataque e todos os alertas relacionados. Deste modo, pode perceber rapidamente quais as ações realizadas pelo atacante e que recursos foram afetados.

Este tópico explica os incidentes na central de segurança e como usar a correção de seus alertas.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

No Centro de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com padrões de [cadeia de ataque](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Os incidentes aparecem na lista [alertas de segurança](security-center-managing-and-responding-alerts.md) . Clique em ona um incidente para exibir os alertas relacionados, o que permite obter mais informações sobre cada ocorrência.

## <a name="managing-security-incidents"></a>Gerir incidentes de segurança

1. No painel central de segurança, clique no bloco **alertas de segurança** . Os incidentes e alertas são listados. Observe que a descrição do incidente de segurança tem um ícone diferente em comparação com outros alertas.

    ![Exibir incidentes de segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para exibir detalhes, clique em um incidente. A folha **incidente de segurança detectado** exibe mais detalhes. A seção **informações gerais** pode oferecer uma percepção do que disparou o alerta de segurança. Ele exibe informações como o recurso de destino, endereço IP de origem (quando aplicável), se o alerta ainda estiver ativo e recomendações sobre como corrigir.  

    ![Responder a incidentes de segurança na central de segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Para obter mais informações sobre cada alerta, clique em um alerta. A remediação sugerida pelo Centro de Segurança varia de acordo com o alerta de segurança. A remediação sugerida pelo Centro de Segurança varia de acordo com o alerta de segurança. 

   > [!NOTE]
   > O mesmo alerta pode existir como parte de um incidente, bem como ser visível como um alerta autônomo.

    ![Detalhes do alerta](./media/security-center-incident/security-center-incident-alert.png)

1. Siga as etapas de correção fornecidas para cada alerta.

Para obter mais informações sobre alertas, [Gerenciamento e resposta a alertas de segurança](security-center-managing-and-responding-alerts.md).

Os tópicos a seguir orientam você pelos diferentes alertas, de acordo com os tipos de recursos:

* [Alertas de VMs e servidores IaaS](security-center-alerts-iaas.md)
* [Alertas de computação nativos](security-center-alerts-compute.md)
* [Alertas de serviços de dados](security-center-alerts-data-services.md)

Os tópicos a seguir explicam como a central de segurança usa a telemetria diferente que ela coleta da integração com a infraestrutura do Azure, para aplicar camadas de proteção adicionais para recursos implantados no Azure:

* [Alertas da camada de serviço](security-center-alerts-service-layer.md)
* [Integração com os produtos de segurança do Azure](security-center-alerts-integration.md)

## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a utilizar a capacidade de incidentes de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Alertas de segurança na central de segurança do Azure](security-center-alerts-overview.md).
* [Gerir alertas de segurança](security-center-managing-and-responding-alerts.md)
* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
