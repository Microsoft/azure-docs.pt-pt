---
title: Stream Azure monitoriza os dados para o centro de eventos
description: Saiba como transmitir os dados de monitorização do Seu Azure para um centro de eventos para obter os dados numa ferramenta de análise siEM ou parceira.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 915df5d6356e2246c8937cb167c8068b00e0917b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854614"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Transmitir os dados de monitorização do Azure para um hub de eventos
O Azure Monitor fornece uma solução completa de monitorização de pilhas completas para aplicações e serviços em Azure, em outras nuvens e no local. Além de utilizar o Monitor Azure para analisar esses dados e alavancar os mesmos para diferentes cenários de monitorização, poderá ser necessário enviá-lo para outras ferramentas de monitorização no seu ambiente. O método mais eficaz para transmitir dados de monitorização a ferramentas externas na maioria dos casos é a utilização de Hubs de [Eventos Azure](/azure/event-hubs/). Este artigo fornece uma breve descrição de como pode transmitir dados de monitorização de diferentes fontes para um centro de eventos e ligações a orientações detalhadas.


## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Antes de configurar o streaming para qualquer fonte de dados, precisa criar um espaço de nome e centro de eventos do [Event Hubs.](../../event-hubs/event-hubs-create.md) Este espaço de nome e centro de eventos é o destino para todos os seus dados de monitorização. Um espaço de nome de Event Hubs é um agrupamento lógico de centros de eventos que partilham a mesma política de acesso, tal como uma conta de armazenamento tem bolhas individuais dentro dessa conta de armazenamento. Considere os seguintes detalhes sobre o espaço de nome do evento hubs e centros de eventos que você usa para transmitir dados de monitorização:

* O número de unidades de produção permite-lhe aumentar a escala de entrada para os seus centros de eventos. Normalmente, apenas uma unidade de entrada é necessária. Se precisar de aumentar à medida que o seu registo aumenta, pode aumentar manualmente o número de unidades de entrada para o espaço de nome ou ativar a inflação automática.
* O número de divisórias permite-lhe paralelamente o consumo entre muitos consumidores. Uma única partição pode suportar até 20MBps ou aproximadamente 20.000 mensagens por segundo. Dependendo da ferramenta que consome os dados, pode ou não suportar o consumo de várias divisórias. Quatro divisórias são razoáveis para começar se não tiver certeza sobre o número de divisórias a definir.
* Definiu a retenção de mensagens no seu centro de eventos para pelo menos 7 dias. Se a sua ferramenta de consumo se abater durante mais de um dia, isto garante que a ferramenta pode retomar onde ficou para eventos até 7 dias de idade.
* Deve utilizar o grupo de consumidores padrão para o seu centro de eventos. Não há necessidade de criar outros grupos de consumidores ou de utilizar um grupo de consumidores separado, a menos que planeie que duas ferramentas diferentes consumam os mesmos dados do mesmo centro de eventos.
* Para o registo de atividade seleção do Azure, você escolhe um espaço de nome de Hubs de Eventos, e o Azure Monitor cria um centro de eventos dentro desse espaço de nome chamado _insights-logs-operational-logs_. Para outros tipos de registo, pode escolher um centro de eventos existente ou fazer com que o Azure Monitor crie um centro de eventos por categoria de registo.
* A porta de saída 5671 e 5672 deve normalmente ser aberta no computador ou VNET consumindo dados do centro do evento.

## <a name="monitoring-data-available"></a>Dados de monitorização disponíveis
[As fontes de dados de monitorização](data-sources.md) do Azure Monitor descrevem os diferentes níveis de dados para aplicações do Azure e os tipos de dados de monitorização disponíveis para cada um. A tabela seguinte lista cada um destes níveis e uma descrição de como esses dados podem ser transmitidos para um centro de eventos. Siga os links fornecidos para mais detalhes.

| Escalão | Dados | Método |
|:---|:---|:---|
| [Inquilino do Azure](data-sources.md#azure-tenant) | Registos de auditoria do Diretório Ativo Azure | Configure um cenário de diagnóstico de inquilino no seu inquilino AAD. Consulte [Tutorial: Stream Azure Ative Directory logy to a Azure event hub](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) for details. |
| [Subscrição do Azure](data-sources.md#azure-subscription) | Registo de Atividades do Azure | Crie um perfil de registo para exportar eventos de Registo de Atividades para Centros de Eventos.  Consulte [os registos da plataforma Stream Azure para](resource-logs-stream-event-hubs.md) obter mais detalhes sobre o evento. |
| [Recursos do Azure](data-sources.md#azure-resources) | Métricas da plataforma<br> Registos do recurso |Ambos os tipos de dados são enviados para um centro de eventos usando uma definição de diagnóstico de recursos. Consulte os registos de [recursos stream Azure para obter](resource-logs-stream-event-hubs.md) mais detalhes. |
| [Sistema operativo (convidado)](data-sources.md#operating-system-guest) | Máquinas virtuais do Azure | Instale a extensão de [diagnóstico azure](diagnostics-extension-overview.md) nas máquinas virtuais Windows e Linux em Azure. Consulte os dados do [Streaming Azure Diagnostics no caminho quente, utilizando os Hubs](diagnostics-extension-stream-event-hubs.md) de Eventos para obter detalhes sobre os VMs do Windows e utilizar a [extensão de diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) para obter detalhes sobre VMs Linux. |
| [Código de candidatura](data-sources.md#application-code) | Application Insights | Application Insights não fornece um método direto para transmitir dados para centros de eventos. Pode [configurar](../../azure-monitor/app/export-telemetry.md) a exportação contínua dos dados do Application Insights para uma conta de armazenamento e, em seguida, usar uma App Lógica para enviar os dados para um hub de eventos, conforme descrito no [streaming manual com a Logic App](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Streaming manual com App Lógica
Para dados que não pode transmitir diretamente para um centro de eventos, pode escrever para o armazenamento do Azure e, em seguida, usar uma Aplicação Lógica desencadeada pelo tempo que [retira dados do armazenamento de blob](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [empurra-os como uma mensagem para o centro de eventos](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Ferramentas parceiras com integração do Monitor Azure

Encaminhamento dos seus dados de monitorização para um centro de eventos com o Monitor Azure permite-lhe integrar-se facilmente com o SIEM externo e ferramentas de monitorização. Exemplos de ferramentas com integração do Monitor Azure incluem:

| Ferramenta | Hospedado em Azure | Descrição |
|:---|:---| :---|
|  IBM QRadar | No | O Microsoft Azure DSM e o Microsoft Azure Event Hub Protocol estão disponíveis para download a partir do site de [suporte da IBM.](https://www.ibm.com/support) Pode saber mais sobre a integração com o Azure na [configuração DoDSM QRadar](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | No | [O Add-On Azure Monitor para Splunk](https://splunkbase.splunk.com/app/3534/) é um projeto de código aberto disponível na Splunkbase. A documentação está disponível no [Azure Monitor Addon For Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Se não conseguir instalar um addon na sua instância Splunk, se, por exemplo, estiver a usar um proxy ou a correr na Nuvem Splunk, pode encaminhar estes eventos para o Splunk HTTP Event Collector utilizando a [função Azure For Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), que é desencadeada por novas mensagens no centro do evento. |
| SumoLogic | No | Instruções para a criação da SumoLogic para consumir dados de um centro de eventos estão disponíveis na [Collect Logs for the Azure Audit App from Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | No | O conector inteligente ArcSight Azure Event Hub está disponível como parte da coleção de [conector inteligente ArcSight.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Syslog server | No | Se pretender transmitir dados do Monitor Do Azure diretamente para um servidor syslog, pode utilizar uma [solução baseada numa função Azure](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | No| As instruções para configurar o LogRhythm para recolher registos de um centro de eventos estão disponíveis [aqui](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Sim | Para mais informações, consulte [Começar com monitorização e registo usando Logz.io para aplicações Java em execução no Azure](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio)


## <a name="next-steps"></a>Passos Seguintes
* [Arquivar o registo de atividade saem para uma conta de armazenamento](../../azure-monitor/platform/archive-activity-log.md)
* [Leia a visão geral do registo da Atividade Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Configurar um alerta com base num evento de registo de atividade](../../azure-monitor/platform/alerts-log-webhook.md)


