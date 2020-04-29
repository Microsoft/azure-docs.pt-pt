---
title: Centro de Segurança Export Azure alerta e recomenda ções para SIEMs Microsoft Docs
description: Este artigo explica como configurar a exportação contínua de alertas e recomendações de segurança para sIEMs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80158979"
---
# <a name="export-security-alerts-and-recommendations"></a>Exportar alertas e recomendações de segurança

O Azure Security Center gera alertas e recomendações de segurança detalhadas. Pode vê-los no portal ou através de ferramentas programáticas. Também pode ser necessário exportar esta informação ou enviá-la para outras ferramentas de monitorização no seu ambiente. 

Este artigo descreve o conjunto de ferramentas que lhe permitem exportar alertas e recomendações manualmente ou de forma contínua e contínua.

Usando estas ferramentas pode:

* Exportação contínua para espaços de trabalho Log Analytics
* Exportação contínua para Hubs de Eventos Azure (para integrações com SIEMs de terceiros)
* Exportação para CSV (uma vez)


## <a name="setting-up-a-continuous-export"></a>Criação de uma exportação contínua

Os passos abaixo são necessários quer esteja a preparar uma exportação contínua para log Analytics workspace ou Azure Event Hubs.

1. A partir da barra lateral do Security Center, selecione **definições de preços &**.

1. Selecione a subscrição específica para a qual pretende configurar a exportação de dados.
    
1. A partir da barra lateral da página de definições para essa subscrição, selecione **Exportação Contínua**.

    [Opções de exportação no Centro de Segurança Azure ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Aqui vê as opções de exportação. Há uma conta para cada alvo de exportação disponível. 

1. Selecione o tipo de dados que pretende exportar e escolha entre os filtros de cada tipo (por exemplo, exportar apenas alertas de alta gravidade).

1. A partir da área "Export target", escolha onde gostaria que os dados sejam guardados. Os dados podem ser guardados num alvo numa subscrição diferente (por exemplo, numa instância do Centro de Eventos ou num espaço de trabalho central do Log Analytics).

1. Clique em **Guardar**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Configurar a integração do SIEM através dos Hubs de Eventos Azure

O Azure Event Hubs é uma ótima solução para consumir programaticamente quaisquer dados de streaming. Para alertas e recomendações do Azure Security Center, é a forma preferida de se integrar com um SIEM de terceiros.

> [!NOTE]
> O método mais eficaz para transmitir dados de monitorização a ferramentas externas na maioria dos casos é a utilização de Hubs de Eventos Azure. [Este artigo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fornece uma breve descrição de como pode transmitir dados de monitorização de diferentes fontes para um Hub de Eventos e ligações a orientações detalhadas.

> [!NOTE]
> Se já exportou alertas do Security Center para um SIEM utilizando o registo de atividade do Azure, o procedimento abaixo substitui essa metodologia.

Para ver os esquemas de eventos dos tipos de dados exportados, visite os [schemas](https://aka.ms/ASCAutomationSchemas)do evento Hub .


### <a name="to-integrate-with-a-siem"></a>Integrar com um SIEM 

Depois de configurar a exportação contínua dos dados do Centro de Segurança escolhidos para os Hubs de Eventos Azure, pode configurar o conector apropriado para o seu SIEM:

* **Azure Sentinel** - Use o Centro de Segurança Azure nativo alerta [o conector](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) de dados oferecido lá.
* **Splunk** - Use o [Add-On do Monitor Azure para Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - Utilize uma fonte de [registo manualmente configurada](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** - Use [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Além disso, se quiser mover os dados continuamente exportados do seu Hub de Eventos configurado para o Azure Data Explorer, utilize as instruções em [dados da Ingest do Event Hub para o Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Exportação contínua para um espaço de trabalho log Analytics

Se quiser analisar os dados do Azure Security Center dentro de um espaço de trabalho do Log Analytics ou utilizar alertas Azure juntamente com o Security Center, instale uma exportação contínua para o seu espaço de trabalho Log Analytics.

Para exportar para um espaço de trabalho log Analytics, você deve ter soluções log Analytics do Security Center ativadas no seu espaço de trabalho. Se estiver a utilizar o portal Azure, a solução de nível livre do Security Center está ativada automaticamente quando permite a exportação contínua. No entanto, se estiver a configurar as definições de exportação contínua sem programadamente, deve selecionar manualmente o nível de preços gratuito ou padrão para o espaço de trabalho necessário a partir das **definições de Preços &**.  

### <a name="log-analytics-tables-and-schemas"></a>Tabelas de Log Analytics e schemas

Os alertas e recomendações de segurança são armazenados nas tabelas *De Recomendações* de Segurança Alert e *Security,* respectivamente. O nome da solução Log Analytics que contém estas tabelas depende se você está no nível livre ou padrão (ver [preços](security-center-pricing.md)): Segurança ('Segurança e Auditoria') ou SecurityCenterFree.

![A tabela *SecurityAlert* no Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Para ver os esquemas de eventos dos tipos de dados exportados, visite os [esquemas](https://aka.ms/ASCAutomationSchemas)de tabela Log Analytics .

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Ver alertas e recomendações de segurança exportadas no Monitor Azure

Em alguns casos, pode optar por ver os Alertas e/ou recomendações de Segurança exportados no [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

O Azure Monitor fornece uma experiência de alerta unificada para uma variedade de alertas Azure, incluindo Registo de Diagnóstico, alertas métricos e alertas personalizados baseados em consultas de espaço de trabalho log Analytics.

Para visualizar alertas e recomendações do Centro de Segurança do Monitor Azure, configure uma regra de alerta com base em consultas de Log Analytics (Alerta de Log):

1. A partir da página **de Alertas** do Monitor Azure, clique em **Nova regra**de alerta .

    ![Página de alertas do Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na página de regras de criação, configure a sua nova regra (da mesma forma que configurar uma regra de alerta de [log no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * Para **Recurso**, selecione o espaço de trabalho log Analytics para o qual exportou alertas e recomendações de segurança.

    * Para **condição,** selecione Procura de **registo personalizado**. Na página que aparece, configure a consulta, o período de retrospetiva e o período de frequência. Na consulta de pesquisa, pode escrever *SecurityAlert* ou *SecurityRecommendation* para consultar os tipos de dados que o Security Center exporta continuamente para a medida em que permite a exportação contínua para log Analytics. 
    
    * Opcionalmente, configure o Grupo de [Ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) que gostaria de desencadear. Os grupos de ação podem desencadear o envio de e-mails, bilhetes ITSM, WebHooks e muito mais.
    ![Regra de alerta do Monitor Azure](./media/continuous-export/azure-monitor-alert-rule.png)

Verá agora novos alertas ou recomendações do Azure Security Center (dependendo da sua configuração) em alertas do Monitor Azure, com o disparo automático de um grupo de ação (se fornecido).

## <a name="manual-one-time-export-of-security-alerts"></a>Exportação manual de alertas de segurança

Para fazer o download de um relatório CSV para alertas ou recomendações, abra a página de **alertas** de Segurança ou **Recomendações** e clique no botão de **relatório CSV** de download.

[![Descarregue os dados de alertas como um ficheiro CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Estes relatórios contêm alertas e recomendações para recursos das subscrições atualmente selecionadas.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar as exportações contínuas das suas recomendações e alertas. Também aprendeu a descarregar os seus dados de alerta como ficheiro CSV. 

Para material relacionado, consulte a seguinte documentação: 

- [Documentação dos Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/)
- [Documentação Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentação do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/)
- [Automação de fluxo de trabalho e tipos de dados de exportação contínua schemas](https://aka.ms/ASCAutomationSchemas)
