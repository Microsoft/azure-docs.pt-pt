---
title: Gerir alertas de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a utilizar as capacidades de Centro de Segurança do Azure para gerir e responder a alertas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/27/2019
ms.author: memildin
ms.openlocfilehash: 203e3718cedca693dc029a4ba20a86ca50ab3e32
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603854"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gerir e responder a alertas de segurança no Centro de Segurança Azure

Este tópico mostra-lhe como ver e processar os alertas que recebeu para proteger os seus recursos. 

* Para conhecer os diferentes tipos de alertas, consulte [os tipos](security-center-alerts-overview.md#security-alert-types)de alerta de segurança .
* Para uma visão geral de como o Security Center gera alertas, veja como o [Azure Security Center deteta e responde a ameaças](security-center-alerts-overview.md#detect-threats).

> [!NOTE]
> Para ativar as deteções avançadas, atualize para o Centro de Segurança do Azure Standard. Está disponível uma avaliação gratuita. Para atualizar, selecione Escalão de Preço na [Política de Segurança](tutorial-security-policy.md). Veja [Preços do Centro de Segurança do Azure](security-center-pricing.md) para saber mais.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e soluções de parceiros ligadas, tal como soluções de proteção de ponto final e firewall, para detetar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque.

> [!NOTE]
> Para obter mais informações sobre como funcionam as capacidades de deteção do Security Center, consulte como o [Azure Security Center deteta e responde a ameaças](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Gerencie os seus alertas de segurança

1. A partir do painel do Centro de Segurança, consulte o azulejo de **proteção da ameaça** para visualizar e visão geral dos alertas.

    ![Mosaico Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Para ver mais detalhes sobre os alertas, clique no azulejo.

   ![Os Alertas de segurança no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para filtrar os alertas mostrados, clique em **Filter**, e a partir da lâmina **filter** que se abre, selecione as opções de filtro que pretende aplicar. A lista atualiza de acordo com o filtro selecionado. A filtragem pode ser muito útil. Por exemplo, pode pretender resolver alertas de segurança que ocorreram nas últimas 24 horas, porque está a investigar uma potencial violação no sistema.

    ![Filtragem de alertas no Centro de Segurança](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Responder a alertas de segurança

1. A partir da lista de alertas de **Segurança,** clique num alerta de segurança. Os recursos envolvidos e os passos que precisa de tomar para remediar um ataque são mostrados.

    ![Responder a alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Depois de rever a informação, clique num recurso que foi atacado.

    ![Sugestões para o que fazer sobre alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    A secção de **Informação Geral** pode dar uma ideia do que desencadeou o alerta de segurança. Exibe informações como o recurso-alvo, endereço IP de origem (quando aplicável), se o alerta ainda estiver ativo, e recomendações sobre como remediar.  

    > [!NOTE]
    >Em alguns casos, o endereço IP de origem não está disponível, alguns registos de eventos de segurança do Windows não incluem o endereço IP.

1. As medidas de reparação sugeridas pelo Centro de Segurança variam de acordo com o alerta de segurança. Siga-os para cada alerta. Em alguns casos, para mitigar um alerta de deteção de ameaças, poderá ter de utilizar outros controlos ou serviços do Azure para implementar a reparação recomendada. 

    Os seguintes tópicos guiam-no através dos diferentes alertas, de acordo com os tipos de recursos:
    
    * [Alertas de VMs e servidores IaaS](security-center-alerts-iaas.md)
    * [Alertas de computação nativa](security-center-alerts-compute.md)
    * [Alertas de serviços de dados](security-center-alerts-data-services.md)
    
    Os seguintes tópicos explicam como o Security Center utiliza a diferente telemetria que recolhe da integração com a infraestrutura Azure, de forma a aplicar camadas de proteção adicionais para os recursos implantados no Azure:
    
    * [Alertas de camada de serviço](security-center-alerts-service-layer.md)
    * [Deteção de ameaças para a Proteção de DDoS Azure WAF e Azure](security-center-alerts-integration.md)
    
## <a name="see-also"></a>Consulte também

Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Alertas de segurança no Centro de Segurança Azure.](security-center-alerts-overview.md)
* [Lidar com incidentes de segurança](security-center-incident.md)
* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)