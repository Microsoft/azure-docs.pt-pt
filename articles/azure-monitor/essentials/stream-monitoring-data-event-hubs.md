---
title: Stream Azure monitorizando dados para centro de eventos e parceiros externos
description: Saiba como transmitir os seus dados de monitorização do Azure para um centro de eventos para obter os dados num parceiro SIEM ou numa ferramenta de análise.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.subservice: ''
ms.openlocfilehash: e541869d040cc1eead2d295583c42016b64d5edb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616441"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Stream Azure monitorando dados para um centro de eventos ou parceiro externo

O Azure Monitor fornece uma solução completa de monitorização de stacks para aplicações e serviços em Azure, em outras nuvens e no local. Além de utilizar o Azure Monitor para analisar esses dados e alavancar os mesmos para diferentes cenários de monitorização, poderá ser necessário enviá-lo para outras ferramentas de monitorização no seu ambiente. Na maioria dos casos, o método mais eficaz para transmitir dados de monitorização para ferramentas externas é a utilização de [Hubs de Eventos Azure](../../event-hubs/index.yml). Este artigo fornece uma breve descrição sobre como fazê-lo e, em seguida, lista alguns dos parceiros onde você pode enviar dados. Alguns têm uma integração especial com o Azure Monitor e podem ser hospedados no Azure.  

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Antes de configurar o streaming para qualquer fonte de dados, precisa [de criar um espaço de nomes e um centro de eventos do Event Hubs.](../../event-hubs/event-hubs-create.md) Este espaço de nome e centro de eventos é o destino de todos os seus dados de monitorização. Um espaço de nomes do Event Hubs é um agrupamento lógico de centros de eventos que partilham a mesma política de acesso, tal como uma conta de armazenamento tem bolhas individuais dentro dessa conta de armazenamento. Considere os seguintes detalhes sobre os centros de nomes e centros de eventos que utiliza para transmitir dados de monitorização:

* O número de unidades de produção permite-lhe aumentar a escala de produção para os seus centros de eventos. Normalmente, apenas uma unidade de produção é necessária. Se precisar de aumentar a escala à medida que o seu registo aumenta, pode aumentar manualmente o número de unidades de produção para o espaço de identificação ou ativar a insuflação automática.
* O número de divisórias permite-lhe paralelizar o consumo entre muitos consumidores. Uma única partição pode suportar até 20MBps ou aproximadamente 20.000 mensagens por segundo. Dependendo da ferramenta que consome os dados, pode ou não suportar o consumo de várias divisórias. Quatro divisórias é razoável para começar se não tiver certeza sobre o número de divisórias a definir.
* Você define a retenção de mensagens no seu centro de eventos para pelo menos 7 dias. Se a sua ferramenta consumista se avariar durante mais de um dia, isto garante que a ferramenta pode recompensá-la para eventos até 7 dias de idade.
* Deve utilizar o grupo de consumidores predefinido para o seu centro de eventos. Não há necessidade de criar outros grupos de consumidores ou usar um grupo de consumidores separado, a menos que planeie ter duas ferramentas diferentes que consumam os mesmos dados do mesmo centro de eventos.
* Para o registo de atividades Azure, você escolhe um espaço de nomes de Event Hubs, e o Azure Monitor cria um centro de eventos dentro desse espaço de nome chamado _insights-logs-logs-logs-logs-logs._ Para outros tipos de registo, pode escolher um hub de eventos existente ou ter o Azure Monitor a criar um hub de eventos por categoria de registo.
* A porta de saída 5671 e 5672 deve ser normalmente aberta no computador ou no VNET, consumindo dados do centro de eventos.

## <a name="monitoring-data-available"></a>Dados de monitorização disponíveis
[Fontes de dados de monitorização do Azure Monitor](../agents/data-sources.md) descrevem os diferentes níveis de dados para aplicações Azure e os tipos de dados de monitorização disponíveis para cada um. A tabela que se segue lista cada um destes níveis e uma descrição de como esses dados podem ser transmitidos para um centro de eventos. Siga os links previstos para mais detalhes.

| Escalão de serviço | Dados | Método |
|:---|:---|:---|
| [Inquilino do Azure](../agents/data-sources.md#azure-tenant) | Registos de auditoria do Azure Ative Directory | Configure um cenário de diagnóstico de inquilino no seu inquilino da AAD. Consulte  [tutorial: Stream Azure Ative Directory registra-se para um centro de eventos Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) para mais detalhes. |
| [Subscrição do Azure](../agents/data-sources.md#azure-subscription) | Registo de Atividades do Azure | Crie um perfil de registo para exportar eventos de Registo de Atividades para Centros de Eventos.  Consulte [os registos da plataforma Stream Azure nos Hubs de Eventos Azure](../essentials/resource-logs.md#send-to-azure-event-hubs) para obter mais detalhes. |
| [Recursos do Azure](../agents/data-sources.md#azure-resources) | Métricas de plataforma<br> Registos do recurso |Ambos os tipos de dados são enviados para um centro de eventos utilizando uma definição de diagnóstico de recursos. Consulte [os registos de recursos do Stream Azure num centro de eventos](../essentials/resource-logs.md#send-to-azure-event-hubs) para obter mais detalhes. |
| [Sistema operativo (convidado)](../agents/data-sources.md#operating-system-guest) | Máquinas virtuais do Azure | Instale a [extensão de diagnóstico azure](../agents/diagnostics-extension-overview.md) em máquinas virtuais Windows e Linux em Azure. Consulte [os dados de Streaming Azure Diagnostics no caminho quente, utilizando os Centros de Eventos](../agents/diagnostics-extension-stream-event-hubs.md) para obter detalhes sobre VMs do Windows e [use a extensão de diagnóstico do Linux para monitorizar métricas e registos](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) para obter detalhes sobre Os VMs do Linux. |
| [Código de aplicação](../agents/data-sources.md#application-code) | Application Insights | O Application Insights não fornece um método direto para transmitir dados para centros de eventos. Pode [configurar a exportação contínua](../app/export-telemetry.md) dos dados do Application Insights para uma conta de armazenamento e, em seguida, utilizar uma App Lógica para enviar os dados para um centro de eventos, conforme descrito no [streaming manual com a Logic App.](#manual-streaming-with-logic-app) |

## <a name="manual-streaming-with-logic-app"></a>Streaming manual com App Lógica
Para dados que não é possível transmitir diretamente para um centro de eventos, pode escrever para o armazenamento do Azure e, em seguida, usar uma Aplicação Lógica acionada pelo tempo que [retira dados do armazenamento de bolhas](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [empurra-os como uma mensagem para o centro do evento.](../../connectors/connectors-create-api-azure-event-hubs.md#add-action) 


## <a name="partner-tools-with-azure-monitor-integration"></a>Ferramentas de parceiro com integração do Azure Monitor

Encaminhar os seus dados de monitorização para um centro de eventos com o Azure Monitor permite-lhe integrar-se facilmente com SIEM externo e ferramentas de monitorização. Exemplos de ferramentas com integração do Azure Monitor incluem:

| Ferramenta | Hospedado em Azure | Description |
|:---|:---| :---|
|  IBM QRadar | No | O Microsoft Azure DSM e o Microsoft Azure Event Hub Protocol estão disponíveis para download a partir do website de [suporte da IBM](https://www.ibm.com/support). Pode saber mais sobre a integração com o Azure na [configuração do DSM QRadar.](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) |
| Splunk | No | [Microsoft Azure Add-On para a Splunk](https://splunkbase.splunk.com/app/3757/) é um projeto de código aberto disponível em Splunkbase. <br><br> Se não conseguir instalar um addon no seu caso Splunk, se, por exemplo, estiver a utilizar um proxy ou a correr em Splunk Cloud, pode encaminhar estes eventos para o Splunk HTTP Event Collector utilizando [a Função Azure Para Splunk,](https://github.com/Microsoft/AzureFunctionforSplunkVS)que é desencadeada por novas mensagens no centro de eventos. |
| SumoLogic | No | As instruções para a criação da SumoLogic para consumir dados de um centro de eventos estão disponíveis no [Collect Logs para a App de Auditoria Azure do Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | No | O conector inteligente ArcSight Azure Event Hub está disponível como parte da coleção de [conector inteligente ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Syslog server | No | Se pretender transmitir os dados do Azure Monitor diretamente para um servidor sislog, pode utilizar uma [solução baseada numa função Azure](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | No| As instruções para configurar o LogRhythm para recolher registos de um centro de eventos estão disponíveis [aqui](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Yes | Para obter mais informações, consulte [Começar com a monitorização e o registo utilizando Logz.io para aplicações Java em execução no Azure](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Outros parceiros também podem estar disponíveis. Para obter uma lista mais completa de todos os parceiros do Azure Monitor e suas capacidades, consulte [as integrações de parceiros do Azure Monitor.](../platform/partners.md)

## <a name="next-steps"></a>Passos Seguintes
* [Arquivar o registo de Atividades numa conta de armazenamento](./activity-log.md#legacy-collection-methods)
* [Leia a visão geral do diário de atividades do Azure](../essentials/platform-logs-overview.md)
* [Configurar um alerta com base num evento de registo de atividade](../alerts/alerts-log-webhook.md)

