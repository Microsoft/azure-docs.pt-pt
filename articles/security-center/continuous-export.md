---
title: Exportar alertas e recomendações da central de segurança do Azure para SIEMs | Microsoft Docs
description: Este artigo explica como configurar a exportação contínua de alertas de segurança e recomendações para os SIEMs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cd26ed446ce676bcec85d8e413d3ec37ac236869
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521998"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Exportar alertas de segurança e recomendações (versão prévia)

A central de segurança do Azure gera recomendações e alertas de segurança detalhados. Você pode exibi-los no portal ou por meio de ferramentas programáticas. Talvez você também precise exportar essas informações ou enviá-las para outras ferramentas de monitoramento em seu ambiente. 

Este artigo descreve o conjunto de ferramentas (versão prévia) que permitem exportar alertas e recomendações de forma manual ou contínua.

Usando essas ferramentas, você pode:

* Gerar relatórios detalhados como CSV
* Exportar para espaços de trabalho Log Analytics
* Exportar para os hubs de eventos do Azure (para integrações com SIEMs de terceiros)

## <a name="setting-up-a-continuous-export"></a>Configurando uma exportação contínua

1. Na barra lateral da central de segurança, clique em **preços & configurações**.

1. Selecione a assinatura específica para a qual você deseja configurar a exportação de dados.
    
1. Na barra lateral da página de configurações dessa assinatura, selecione **exportação contínua (versão prévia)** .

    [![opções de exportação na central de segurança do Azure](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Aqui você vê as opções de exportação. Há uma guia para cada destino de exportação disponível. 

1. Selecione o tipo de dados que você deseja exportar e escolha um dos filtros em cada tipo (por exemplo, exportar somente alertas de severidade alta).

1. Na área "destino de exportação", escolha onde você deseja que os dados sejam salvos. Os dados podem ser salvos em um destino em uma assinatura diferente (por exemplo, em uma instância central de Hub de eventos ou em um espaço de trabalho central Log Analytics).

1. Clique em **Guardar**.

## <a name="continuous-export-through-azure-event-hubs"></a>Exportação contínua por meio dos hubs de eventos do Azure  

> [!NOTE]
> O método mais eficaz para transmitir dados de monitoramento para ferramentas externas na maioria dos casos é usar os hubs de eventos do Azure. [Este artigo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fornece uma breve descrição de como você pode transmitir dados de monitoramento de fontes diferentes para um hub de eventos e links para orientações detalhadas.

> [!NOTE]
> Se você exportou anteriormente alertas da central de segurança para um SIEM usando o log de atividades do Azure, o procedimento a seguir substituirá essa metodologia.

### <a name="to-integrate-with-a-siem"></a>Para integrar com um SIEM 

Depois de configurar a exportação contínua dos dados escolhidos da central de segurança para os hubs de eventos do Azure, você pode configurar o conector apropriado em seu SIEM seguindo as instruções abaixo.

Siga as instruções relevantes para o SIEM [nesta página](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) e use o conector relevante:

* **Splunk** -use o [complemento Azure monitor para Splunk](https://splunkbase.splunk.com/app/3534/)
* **IBM QRadar** -usar [uma fonte de log configurada manualmente](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html)
* **ArcSight** – usar [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Se você estiver usando o **Azure Sentinel**, use o [conector de dados](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) de alertas nativos da central de segurança do Azure oferecido lá.

Além disso, se você quiser mover os dados exportados continuamente automaticamente do hub de eventos configurado para o Azure Data Explorer, use as instruções em [ingerir dados do hub de eventos para o data Explorer do Azure](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).


## <a name="continuous-export-to-log-analytics-workspace"></a>Exportação contínua para o espaço de trabalho Log Analytics

Para exportar para Log Analytics espaço de trabalho, você deve ter a camada gratuita ou Standard da central de segurança Log Analytics soluções habilitadas no seu espaço de trabalho. Se você estiver usando o portal do Azure, a solução de camada gratuita da central de segurança será habilitada automaticamente quando você habilitar a exportação contínua. No entanto, se você estiver configurando suas configurações de exportação contínua de forma programática, deverá selecionar manualmente o tipo de preço gratuito ou Standard para o espaço de trabalho necessário em **configurações de preço &** .  

Alertas de segurança e recomendações são armazenados nas tabelas *SecurityAlert* e *SecurityRecommendations* , respectivamente. O nome da solução de Log Analytics que contém essas tabelas depende se você está na camada gratuita ou Standard (consulte [preços](security-center-pricing.md)): segurança ou SecurityCenterFree.

![A tabela * SecurityAlert * no Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

## <a name="manual-one-time-export-of-security-alerts"></a>Exportação única por vez manual de alertas de segurança

Para baixar um relatório CSV para alertas ou recomendações, abra a página **alertas de segurança** ou **recomendações** e clique no botão **baixar relatório CSV (visualização)** .

[![baixar dados de alertas como um arquivo CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Esses relatórios contêm alertas e recomendações para recursos das assinaturas atualmente selecionadas no diretório + filtro de assinatura no portal do Azure: ![o filtro para selecionar diretório + assinatura](./media/continuous-export/filter-for-export-csv.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a configurar as exportações contínuas de suas recomendações e alertas. Você também aprendeu a baixar seus dados de alertas como um arquivo CSV. 

Para obter material relacionado, consulte a seguinte documentação: 

- [Documentação dos hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/)
- [Documentação do Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentação do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)