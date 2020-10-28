---
title: Transmita os seus alertas do Azure Security Center para sistemas de Informação de Segurança e Gestão de Eventos (SIEM) e outras soluções de monitorização
description: Saiba como transmitir os seus alertas de segurança para soluções Azure Sentinel, SIEMs de terceiros, SOAR ou ITSM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 6ffb6ced6fc828733dd627943a3d4b54e8293ad2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791907"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Alertas de fluxo para uma solução siem, SOAR ou GESTão de Serviços de TI

O Azure Security Center pode transmitir os seus alertas de segurança para as soluções mais populares de Segurança e Gestão de Eventos (SIEM), Resposta Automatizada de Orquestração de Segurança (SOAR) e Gestão de Serviços de TI (ITSM).

Existem ferramentas nativas do Azure para garantir que pode ver os seus dados de alerta em todas as soluções mais populares em uso hoje, incluindo:

- **Azure Sentinel**
- **Splunk Enterprise e Splunk Cloud**
- **QRadar da IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Alertas de fluxo para Azure Sentinel 

O Security Center integra-se de forma nativa com a Solução Azure Sentinel, a solução SIEM nativa da nuvem da Azure e a solução SOAR. 

[Saiba mais sobre o Azure Sentinel.](../sentinel/overview.md)

### <a name="azure-sentinels-connectors-for-security-center"></a>Conectores de Azure Sentinel para o Centro de Segurança

O Azure Sentinel inclui conectores incorporados para o Azure Security Center nos níveis de subscrição e inquilino:

- [Stream alerta para Azure Sentinel ao nível de subscrição](../sentinel/connect-azure-security-center.md)
- [Ligue todas as subscrições do seu inquilino ao Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Configure a ingestão de todos os registos de auditoria no Azure Sentinel 

Outra alternativa para investigar alertas do Security Center em Azure Sentinel é transmitir os seus registos de auditoria para Azure Sentinel:
    - [Ligar aos eventos de segurança do Windows](../sentinel/connect-windows-security-events.md)
    - [Recolher dados de fontes baseadas em Linux utilizando o Syslog](../sentinel/connect-syslog.md)
    - [Conecte dados do registo de atividades Azure](../sentinel/connect-azure-activity.md)

> [!TIP]
> O Azure Sentinel é faturado com base no volume de dados ingeridos para análise em Azure Sentinel e armazenados no espaço de trabalho Azure Monitor Log Analytics. O Azure Sentinel oferece um modelo de preços flexível e previsível. [Saiba mais na página de preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Alertas de streaming com a Microsoft Graph Security API

O Security Center tem integração fora da caixa com a Microsoft Graph Security API. Não é necessária nenhuma configuração e não há custos adicionais. 

Você pode usar esta API para transmitir alertas de todo o seu **inquilino** (e dados de muitos outros produtos microsoft Security) em SIEMs de terceiros e outras plataformas populares:

- **Splunk Enterprise e Splunk Cloud**  -  Utilize o Add-On de segurança de segurança do [Microsoft para Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**  -  [Ligue-se à API de Segurança do Gráfico da Microsoft no Power BI Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [Siga as instruções para instalar e configurar a aplicação API de segurança do Microsoft Graph da Loja ServiceNow](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **Rio QRadar**  -  [Módulo de suporte a dispositivos da IBM para o Azure Security Center via Microsoft Graph API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks** , **Anomali** , **Lookout** , **InSpark** , e muito mais - [Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Saiba mais sobre a Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Alertas de fluxo com monitor Azure 

Para transmitir alertas para **ArcSight** , **Splunk,** **SumoLogic,** Syslog servidores, **LogRhythm,** **Logz.io Cloud Observeability Platform** , e outras soluções de monitorização. ligar o Centro de Segurança com o monitor Azure através dos Hubs de Eventos Azure:

1. Permitir [a exportação contínua](continuous-export.md) para transmitir alertas do Security Center para um Centro de Eventos Azure dedicado ao nível da subscrição. 
    > [!TIP]
    > Para isso ao nível do Grupo de Gestão utilizando a Política Azure, consulte [Criar configurações contínuas de automatização de exportação à escala](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [Ligue o hub do Azure Event à sua solução preferida utilizando os conectores incorporados do Azure Monitor.](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

1. Opcionalmente, transmita os troncos crus para o Azure Event Hub e ligue-se à sua solução preferida. Saiba mais sobre [os dados de monitorização disponíveis.](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#monitoring-data-available)

> [!TIP]
> Para ver os esquemas de eventos dos tipos de dados exportados, visite os [esquemas de eventos do Event Hub.](https://aka.ms/ASCAutomationSchemas)


## <a name="next-steps"></a>Passos seguintes

Esta página explicou como garantir que os dados de alerta do Centro de Segurança Azure estão disponíveis na sua ferramenta de eleição SIEM, SOAR ou ITSM. Para obter material relacionado, consulte:

- [O que é o Azure Sentinel?](../sentinel/overview.md)
- [Validação de alerta no Centro de Segurança Azure](security-center-alert-validation.md) - Verifique se os seus alertas estão corretamente configurados
- [Exportar continuamente dados do Centro de Segurança](continuous-export.md)