---
title: Integrar soluções de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como é que o Centro de Segurança do Azure se integra com parceiros, para melhorar a segurança geral dos seus recursos do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 0479918ad2afa99dcd9f18d3ba81f94a3e262e9e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479380"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança no Centro de Segurança do Azure
Este documento ajuda-o a gerir soluções de segurança já ligadas ao Centro de Segurança do Azure e adicionar novas.

> [!NOTE]
> A subset of security solutions has been retired on July 31st, 2019. For more information and alternative services, see [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança do Azure integradas
O Centro de Segurança facilita a ativação de soluções de segurança integradas no Azure. Os benefícios incluem:

- **Implementação simplificada**: o Centro de Segurança oferece aprovisionamento integrado de soluções de parceiros integradas. For solutions like antimalware and vulnerability assessment, Security Center can provision the agent on your virtual machines. For firewall appliances, Security Center can take care of much of the network configuration required.
- **Deteções integradas**: os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Gestão e monitorização do estado de funcionamento unificadas**: os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções de parceiros rapidamente. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.

Currently, integrated security solutions include vulnerability assessment by [Qualys](https://www.qualys.com/public-cloud/#azure) and [Rapid7](https://www.rapid7.com/products/insightvm/) and Microsoft Application Gateway Web application firewall.

> [!NOTE]
> Security Center does not install the Microsoft Monitoring Agent on partner virtual appliances because most security vendors prohibit external agents running on their appliances.
>
>

## <a name="how-security-solutions-are-integrated"></a>Como são integradas as soluções de segurança
As soluções de segurança do Azure implementadas a partir do Centro de Segurança são ligadas automaticamente. You can also connect other security data sources, including computers running on-premises or in other clouds.

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerir soluções de segurança do Azure integradas e outras origens de dados

1. Inicie sessão no [portal do Azure](https://azure.microsoft.com/features/azure-portal/).

2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto.

3. No menu Centro de Segurança, selecione **Soluções de segurança**.

   ![Descrição geral do Centro de Segurança](./media/security-center-partner-integration/overview.png)

In **Security solutions**, you can see the health of integrated Azure security solutions and run basic management tasks.

### <a name="connected-solutions"></a>Soluções ligadas

The **Connected solutions** section includes security solutions that are currently connected to Security Center. It also shows the health status of each solution.  

![Soluções ligadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

The status of a partner solution can be:

* Healthy (green) - no health issues.
* Unhealthy (red) - there's a health issue that requires immediate attention.
* Health issues (orange) - the solution has stopped reporting its health.
* Not reported (gray) - the solution hasn't reported anything yet and no health data is available. A solution's status may be unreported if it was connected recently and is still deploying.

> [!NOTE]
> If health status data is not available, Security Center shows the date and time of the last event received to indicate whether the solution is reporting or not. If no health data is available and no alerts were received within the last 14 days, Security Center indicates that the solution is unhealthy or not reporting.
>
>

1. Select **VIEW** for additional information and options such as:

   - **Solution console**. Opens the management experience for this solution.
   - **Link VM**. Opens the Link Applications page. Aqui pode ligar recursos à solução de parceiros.
   - **Delete solution**.
   - **Configure**.

   ![Detalhe de solução de parceiros](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluções detetadas

Security Center automatically discovers security solutions running in Azure but not connected to Security Center and displays the solutions in the **Discovered solutions** section. These  solutions include Azure solutions, like [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), and partner solutions.

> [!NOTE]
> É necessário o escalão Standard do Centro de Segurança ao nível da subscrição para a funcionalidade de soluções detetadas. See [Pricing](security-center-pricing.md) to learn more about the pricing tiers.
>
>

Select **CONNECT** under a solution to integrate with Security Center and be notified of security alerts.

### <a name="add-data-sources"></a>Adicionar origens de dados

A secção **Adicionar origens de dados** inclui outras origens de dados disponíveis que podem ser ligadas. Para obter instruções sobre como adicionar dados a partir de qualquer uma destas origens, clique em **ADICIONAR**.

![Origens de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exporting data to a SIEM

> [!NOTE]
> For details of a simpler method (currently in preview) for exporting data to a SIEM, see [Export security alerts and recommendations (Preview)](continuous-export.md). The new method does not use Activity Log as an intermediator and allows direct export from Security Center to Event Hubs (and then on to your SIEM), it also supports the export of Security Recommendations.


You can configure your SIEMs or other monitoring tools to receive Azure Security Center events.

All events from Azure Security Center are published to Azure Monitor's Azure [Activity log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Azure Monitor uses [a consolidated pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) to  stream the data to an Event Hub where it can then be pulled into your monitoring tool.

The next sections describe how you can configure data to be streamed to an event hub. The steps assume that you already have Azure Security Center configured in your Azure subscription.

### <a name="high-level-overview"></a>Descrição geral de alto nível

![High-Level overview](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>What is the Azure security data exposed to SIEM?

In this version, we expose the [security alerts.](../security-center/security-center-managing-and-responding-alerts.md) In upcoming releases, we will enrich the data set with security recommendations.

### <a name="how-to-set-up-the-pipeline"></a>How to set up the pipeline

#### <a name="create-an-event-hub"></a>Criar um Hub de Eventos

Before you begin, [create an Event Hubs namespace](../event-hubs/event-hubs-create.md) - the destination for all your monitoring data.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream the Azure Activity Log to Event Hubs

See the following article [stream activity log to Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Install a partner SIEM connector 

Routing your monitoring data to an Event Hub with Azure Monitor enables you to easily integrate with partner SIEM and monitoring tools.

See the following article for the list of [supported SIEMs](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Example for Querying data 

Here are some Splunk queries you can use to pull alert data:

| **Description of Query** | **Consulta** |
|----|----|
| All Alerts| index=main Microsoft.Security/locations/alerts|
| Summarize count of operations by their name| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Get Alerts info: Time, Name, State, ID, and Subscription | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Azure Security Center FAQs](security-center-faq.md) (FAQ do Centro de Segurança do Azure). Encontre respostas para as perguntas mais frequentes sobre o Centro de Segurança.
* [Blogue Azure Security](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
