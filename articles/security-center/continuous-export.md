---
title: Exportação do Centro de Segurança Azure alerta e recomendações para SIEMs / Microsoft Docs
description: Este artigo explica como configurar a exportação contínua de alertas de segurança e recomendações para os SIEMs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: cf8fdd8d91c035d374277c4752fb761c0c4e72c7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905628"
---
# <a name="export-security-alerts-and-recommendations"></a>Exportar alertas e recomendações de segurança

O Centro de Segurança Azure gera alertas e recomendações de segurança detalhadas. Pode vê-los no portal ou através de ferramentas programáticas. Também poderá ser necessário exportar estas informações ou enviá-la para outras ferramentas de monitorização no seu ambiente. 

Este artigo descreve o conjunto de ferramentas que lhe permitem exportar alertas e recomendações manualmente ou de forma contínua e contínua.

Utilizando estas ferramentas pode:

* Exportação contínua para espaços de trabalho Log Analytics
* Exportação contínua para Azure Event Hubs (para integrações com SIEMs de terceiros)
* Exportação para CSV (uma vez)



## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Gratuito|
|Funções e permissões necessárias:|**Papel de administrador de segurança** no grupo de recursos (ou **Proprietário)**<br>Também deve ter permissões de escrita para o recurso alvo|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![Yes](./media/icons/yes-icon.png) China Gov (para Event Hub), Outro Gov|
|||



## <a name="set-up-a-continuous-export"></a>Criar uma exportação contínua

Os passos abaixo são necessários quer esteja a configurar uma exportação contínua para o log analytics ou para o Azure Event Hubs.

1. A partir da barra lateral do Security Center, **selecione definições de preços &**.

1. Selecione a subscrição específica para a qual pretende configurar a exportação de dados.
    
1. A partir da barra lateral da página de definições para essa subscrição, selecione **Exportação Contínua**.

    [ ![ Opções de exportação no Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Aqui você vê as opções de exportação. Há um separador para cada alvo de exportação disponível. 

1. Selecione o tipo de dados que pretende exportar e escolha entre os filtros de cada tipo (por exemplo, exportar apenas alertas de alta gravidade).

1. Opcionalmente, se a sua seleção incluir uma destas quatro recomendações, pode incluir os resultados da avaliação da vulnerabilidade juntamente com eles:

    - Os resultados da Avaliação de Vulnerabilidades nas suas bases de dados SQL devem ser remediados
    - As conclusões da Avaliação de Vulnerabilidade nos seus servidores SQL em máquinas devem ser remediadas (Pré-visualização)
    - As vulnerabilidades nas imagens do Registo de Contentores de Azure devem ser remediadas (alimentadas por Qualys)
    - As vulnerabilidades nas suas máquinas virtuais devem ser remediadas

    Para incluir as conclusões com estas recomendações, permitir a opção **incluindo as conclusões de segurança.**

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir resultados de segurança alternando na configuração contínua da exportação" :::


1. A partir da área "Export target", escolha onde quer que os dados guardados. Os dados podem ser guardados num alvo numa subscrição diferente (por exemplo, numa instância Central do Centro de Eventos ou num espaço de trabalho central do Log Analytics).

1. Selecione **Guardar**.


## <a name="set-up-continuous-export-via-the-rest-api"></a>Configurar exportação contínua através da API REST

A funcionalidade de exportação contínua pode ser configurada e gerida através do Azure Security Center [automations API](https://docs.microsoft.com/rest/api/securitycenter/automations). Utilize esta API para criar ou atualizar automatizações para exportação para qualquer um dos seguintes destinos possíveis:

- Hub de Eventos do Azure
- Área de trabalho do Log Analytics
- Azure Logic Apps 

A API fornece funcionalidade adicional não disponível a partir do portal Azure, por exemplo:

* **Maior volume** - A API permite criar múltiplas configurações de exportação numa única subscrição. A página **de Exportação Contínua** no portal UI do Security Center suporta apenas uma configuração de exportação por subscrição.

* **Características adicionais** - A API oferece parâmetros adicionais que não são mostrados na UI. Por exemplo, pode adicionar tags ao seu recurso de automatização, bem como definir a sua exportação com base num conjunto mais alargado de propriedades de alerta e recomendação do que as oferecidas na página **de Exportação Contínua** no portal UI do Security Center.

* **Âmbito mais focado** - A API proporciona um nível mais granular para o âmbito das suas configurações de exportação. Ao definir uma exportação com a API, pode fazê-lo ao nível do grupo de recursos. Se estiver a utilizar a página **de Exportação Contínua** no portal UI do Security Center, tem de defini-la ao nível da subscrição.

    > [!TIP]
    > Se tiver configurado várias configurações de exportação utilizando a API, ou se tiver usado parâmetros apenas para API, essas funcionalidades extra não serão apresentadas na UI do Centro de Segurança. Em vez disso, haverá um banner informando-o de que existem outras configurações.

Saiba mais sobre as automatizações API na [documentação REST API](https://docs.microsoft.com/rest/api/securitycenter/automations).



## <a name="configure-siem-integration-via-azure-event-hubs"></a>Configurar a integração do SIEM através dos Hubs de Eventos Azure

O Azure Event Hubs é uma ótima solução para consumir programaticamente quaisquer dados de streaming. Para alertas e recomendações do Azure Security Center, é a forma preferida de se integrar com um SIEM de terceiros.

> [!NOTE]
> O método mais eficaz para transmitir dados de monitorização a ferramentas externas na maioria dos casos é a utilização de Hubs de Eventos Azure. [Este artigo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fornece uma breve descrição de como pode transmitir dados de monitorização de diferentes fontes para um Centro de Eventos e ligações a orientações detalhadas.

> [!NOTE]
> Se já exportou alertas do Centro de Segurança para um SIEM utilizando o registo de AtividadeS Azure, o procedimento abaixo substitui essa metodologia.

Para ver os esquemas de eventos dos tipos de dados exportados, visite os [esquemas de eventos do Event Hub.](https://aka.ms/ASCAutomationSchemas)


### <a name="to-integrate-with-a-siem"></a>Integrar-se com um SIEM 

Depois de configurar a exportação contínua dos dados do Centro de Segurança escolhidos para a Azure Event Hubs, pode configurar o conector apropriado para o seu SIEM:

* **Azure Sentinel** - Utilize o centro de segurança Azure nativo que alerta [o conector](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) de dados ali oferecido.
* **Splunk** - Use o [Add-On do Monitor Azure para splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - Utilize [uma fonte de log configurada manualmente](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – Use [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Além disso, se pretender mover automaticamente os dados exportados do seu Centro de Eventos configurado para o Azure Data Explorer, utilize as instruções em [Dados do Centro de Eventos para o Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Exportação contínua para um espaço de trabalho Log Analytics

Se quiser analisar os dados do Azure Security Center dentro de um espaço de trabalho do Log Analytics ou utilizar alertas Azure juntamente com o Security Center, confmem de exportação contínua para o seu espaço de trabalho Log Analytics.

Para exportar para um espaço de trabalho log Analytics, você deve ter as soluções log analytics do Security Center ativadas no seu espaço de trabalho. Se estiver a utilizar o portal Azure, a solução gratuita do Security Center é automaticamente ativada quando ativa a exportação contínua. No entanto, se estiver a configurar as suas definições de exportação contínuas programáticamente, deve ligar ou desligar manualmente o Azure Defender dentro ou fora da página de **definições de preços &.**

### <a name="log-analytics-tables-and-schemas"></a>Registar tabelas e esquemas de Analítica

Os alertas e recomendações de segurança são armazenados nas tabelas *SecurityAlert* e *SecurityRecommendations,* respectivamente. O nome da solução Log Analytics que contém estas tabelas depende se tem o Azure Defender ativado: Segurança ('Segurança e Auditoria') ou SecurityCenterFree.

![A tabela *SecurityAlert* em Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Para ver os esquemas de eventos dos tipos de dados exportados, visite os [esquemas](https://aka.ms/ASCAutomationSchemas)de tabela Log Analytics .

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Ver alertas e recomendações de segurança exportadas no Azure Monitor

Em alguns casos, pode optar por visualizar os alertas de segurança exportados e/ou recomendações no [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

O Azure Monitor fornece uma experiência de alerta unificada para uma variedade de alertas Azure, incluindo Log de Diagnóstico, alertas métricos e alertas personalizados baseados em consultas de espaço de trabalho Log Analytics.

Para visualizar alertas e recomendações do Security Center no Azure Monitor, configure uma regra de alerta baseada em consultas de Log Analytics (Alerta de Registo):

1. Na página **alertas** do Monitor Azure, selecione **Nova regra de alerta**.

    ![Página de alertas do Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na página de regras de criar, configuure a sua nova regra (da mesma forma que configuraria uma regra de [alerta de registo no Azure Monitor):](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

    * Para **obter recursos,** selecione o espaço de trabalho Log Analytics para o qual exportou alertas e recomendações de segurança.

    * Para **obter a condição**, selecione Custom log **search**. Na página que aparece, configurar o período de consulta, de resguardo e de frequência. Na consulta de pesquisa, pode escrever *SecurityAlert* ou *SecurityRecommendation* para consultar os tipos de dados que o Security Center exporta continuamente para que permita a exportação Contínua para log analytics. 
    
    * Opcionalmente, configuure o [Grupo de Ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) que gostaria de desencadear. Os grupos de ação podem desencadear o envio de e-mails, bilhetes ITSM, WebHooks e muito mais.
    ![Regra de alerta do Monitor Azure](./media/continuous-export/azure-monitor-alert-rule.png)

Verá agora novos alertas ou recomendações do Azure Security Center (dependendo da sua configuração) nos alertas do Azure Monitor, com o desencadeamento automático de um grupo de ação (se fornecido).

## <a name="manual-one-time-export-of-security-alerts"></a>Exportação manual de alertas de segurança

Para fazer o download de um relatório CSV para alertas ou recomendações, abra a página **alertas** de Segurança ou **Recomendações** e selecione o botão **de relatório de CSV de descarregamento.**

[![Descarregue dados de alerta como um ficheiro CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Estes relatórios contêm alertas e recomendações para recursos das subscrições atualmente selecionadas.



## <a name="faq---continuous-export"></a>FAQ - Exportação contínua

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Quais são os custos envolvidos na exportação de dados?

Não há qualquer custo para permitir uma exportação contínua. Os custos podem ser incorridos para a ingestão e retenção de dados no seu espaço de trabalho Log Analytics, dependendo da sua configuração lá. 

Saiba mais sobre [os preços do espaço de trabalho Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)

Saiba mais sobre [os preços do Azure Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/).


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a configurar as exportações contínuas das suas recomendações e alertas. Também aprendeu a descarregar os seus dados de alerta como um ficheiro CSV. 

Para obter material relacionado, consulte a seguinte documentação: 

- [Documentação dos Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/)
- [Documentação do Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentação do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Automação do fluxo de trabalho e esquemas contínuos de tipos de dados de exportação](https://aka.ms/ASCAutomationSchemas)
