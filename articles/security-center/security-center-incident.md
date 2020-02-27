---
title: Gerir incidentes de segurança no Centro de Segurança Azure Microsoft Docs
description: Este documento ajuda-o a usar o Centro de Segurança Azure para gerir incidentes de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: 1a6dbaeac5355d50edb93a7f215d7f8e88231e98
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615977"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerir incidentes de segurança no Centro de Segurança Azure

A triagem e a investigação de alertas de segurança podem ser demorados até para os analistas de segurança mais qualificados, e para muitos é difícil saber por onde começar. Através da utilização de [análises](security-center-detection-capabilities.md) para ligar as informações entre [alertas de segurança](security-center-managing-and-responding-alerts.md) distintos, o Centro de Segurança pode fornecer-lhe uma vista única de uma campanha de ataque e todos os alertas relacionados. Deste modo, pode perceber rapidamente quais as ações realizadas pelo atacante e que recursos foram afetados.

Este tópico explica sobre incidentes no Centro de Segurança e como remediar os seus alertas.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

No Centro de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com padrões de [cadeia de ataque](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Incidentes aparecem na lista de Alertas de [Segurança.](security-center-managing-and-responding-alerts.md) Clique num incidente para ver os alertas relacionados, o que lhe permite obter mais informações sobre cada ocorrência.

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

Para mais informações sobre alertas, [Gestão e resposta a alertas](security-center-managing-and-responding-alerts.md)de segurança .

Os seguintes tópicos guiam-no através dos diferentes alertas, de acordo com os tipos de recursos:

* [Alertas para máquinas IaaS Windows](threat-protection.md#windows-machines)
* [Alertas para máquinas IaaS Linux](threat-protection.md#linux-machines)
* [Alertas para o Serviço de Aplicações Azure](threat-protection.md#app-services)
* [Alertas para contentores Azure](threat-protection.md#azure-containers)
* [Alertas para Base de Dados SQL e Armazém de Dados SQL](threat-protection.md#data-sql)
* [Alertas para armazenamento azure](threat-protection.md#azure-storage)
* [Alertas para Cosmos DB](threat-protection.md#cosmos-db)

Os seguintes tópicos explicam como o Security Center utiliza a diferente telemetria que recolhe da integração com a infraestrutura Azure, de forma a aplicar camadas de proteção adicionais para os recursos implantados no Azure:

* [Alertas para camada de gestão Azure (Gestor de Recursos Azure) (Pré-visualização)](threat-protection.md#management-layer)
* [Alertas para cofre de chaves Azure (Pré-visualização)](threat-protection.md#azure-keyvault)
* [Alertas para camada de rede Azure](threat-protection.md#network-layer)
* [Alertas de outros serviços](threat-protection.md#alerts-other)

## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a utilizar a capacidade de incidentes de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Alertas de segurança no Centro de Segurança Azure.](security-center-alerts-overview.md)
* [Gerir alertas de segurança](security-center-managing-and-responding-alerts.md)
* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
