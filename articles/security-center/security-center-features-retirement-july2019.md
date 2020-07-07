---
title: Aposentadoria do Centro de Segurança (julho 2019) Microsoft Docs
description: Este artigo descreve as funcionalidades no Centro de Segurança que foram retiradas a 31 de julho de 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 4a760fe1e5df7cf614a68f8fa8a05926326d3edf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80583223"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Recursos do Centro de Aposentações (julho 2019)

> [!NOTE]
> Este documento detalha a lista de funcionalidades que foram retiradas do Azure Security Center a 31 de julho de 2019.
>
>

Fizemos [várias melhorias](https://azure.microsoft.com/updates/?product=security-center) no Azure Security Center ao longo dos seis meses que antecederam julho de 2019.
Com estas capacidades melhoradas, removemos algumas funcionalidades redundantes e APIs relacionadas do Security Center a 31 de julho de 2019.

A maioria destas funcionalidades aposentadas pode ser substituída por outras funcionalidades no Azure Security Center ou no Azure Log Analytics. Outras funcionalidades podem ser implementadas usando [Azure Sentinel (pré-visualização)](https://azure.microsoft.com/services/azure-sentinel/).

As funcionalidades do Centro de Segurança Aposentado incluem:

- [Painel de eventos](#menu_events)
- [Entrada no menu de pesquisa](#menu_search)
- [Ver identidade clássica & Link de acesso à identidade e ao acesso (pré-visualização)](#menu_classicidentity)
- [Botão de mapa de eventos de segurança no mapa de alertas de segurança (pré-visualização)](#menu_securityeventsmap)
- [Regras de alerta personalizado (pré-visualização)](#menu_customalerts)
- [Investigue botão em alertas de segurança de proteção de ameaças](#menu_investigate)
- [Um subconjunto de soluções de segurança](#menu_solutions)
- [Editar configurações de segurança para políticas de segurança](#menu_securityconfigurations)
- [Painel de segurança e auditoria (originalmente utilizado no portal OMS) para espaços de trabalho log analytics](#menu_securityomsdashboard)

Este artigo fornece informações detalhadas para cada recurso aposentado e os passos que pode tomar para implementar funcionalidades de substituição.

## <a name="events-dashboard"></a>Painel de eventos<a name="menu_events"></a>

O Security Center utiliza o agente Log Analytics para recolher várias configurações e eventos relacionados com a segurança das suas máquinas. Armazena estes eventos nos seus espaços de trabalho. O [painel de eventos](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) permite-lhe visualizar estes dados e dá-lhe um ponto de entrada para o Log Analytics.

Retirámos o painel de eventos que apareceu quando selecionou um espaço de trabalho:

![Painel de eventos][2]

### <a name="events-dashboard---the-new-experience"></a>Painel de eventos - a nova experiência

Nós o encorajamos a usar as capacidades nativas do Azure Log Analytics para ver eventos notáveis nos seus espaços de trabalho.

Se criou eventos notáveis personalizados no Centro de Segurança, eles estarão acessíveis. In Log Analytics, vá a **Selecionar espaço de trabalho**Pesquisas  >  **Guardadas**. Os seus dados não serão perdidos ou modificados. Eventos notáveis nativos também estão disponíveis a partir do mesmo ecrã em Log Analytics.

![Procuras guardadas no espaço de trabalho][3]

## <a name="search-menu-entry"></a>Entrada no menu de pesquisa<a name="menu_search"></a>

O Azure Security Center utiliza atualmente registos do Azure Monitor para recuperar e analisar os seus dados de segurança. Este ecrã serve como uma janela para a página de pesquisa do Log Analytics e permite que os utilizadores executem consultas de pesquisa no seu espaço de trabalho selecionado. Para mais informações, consulte [a pesquisa do Centro de Segurança Azure.](https://docs.microsoft.com/azure/security-center/security-center-search) Retirámos esta janela de pesquisa:

![Página de pesquisa][4]

### <a name="search-menu-entry---the-new-experience"></a>Entrada no menu de pesquisa - a nova experiência

Encorajamo-lo a usar as capacidades nativas do Azure Log Analytics para realizar consultas de pesquisa nos seus espaços de trabalho. Vá ao Azure Log Analytics e selecione **Registos**.

![Log Analytics página de registos][5]

## <a name="classic-identity--access-preview"></a>Acesso clássico & identidade (pré-visualização)<a name="menu_classicidentity"></a>

A experiência Classic Identity & Access in Security Center mostra atualmente um dashboard de identidade e informação de acesso no Log Analytics. Para ver este painel:

1. Selecione **Ver o acesso clássico & identidade**.

   ![Página de identidade][6]

1. Ver o **painel de acesso & identidade**.

    ![Página de identidade - seleção de espaço de trabalho][7]

1. Selecione um espaço de trabalho para abrir o painel **de acesso & identidade** no Log Analytics para ver informações de identidade e acesso no seu espaço de trabalho.

   ![Página de identidade - painel de instrumentos][8]

Retirámos os três ecrãs mostrados nos degraus anteriores. Os seus dados permanecem disponíveis na solução de segurança Log Analytics e não foram modificados ou removidos.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classic Identity & Access (Preview) - a nova experiência

O painel Log Analytics mostrou insights num único espaço de trabalho. No entanto, as capacidades do Centro de Segurança Nativo proporcionam visibilidade a todas as subscrições e a todos os espaços de trabalho associados a elas. Pode aceder a uma vista fácil de usar que lhe permite focar-se no que é importante com recomendações classificadas de acordo com o seu Secure Score.

Todas as funcionalidades do painel **de acesso & identidade** em Log Analytics podem ser alcançadas selecionando identidade & acesso **(Pré-visualização)** dentro do Centro de Segurança.

![Página de identidade - reforma da experiência clássica][9]

## <a name="security-events-map"></a>Mapa de eventos de segurança<a name="menu_securityeventsmap"></a>

O Centro de Segurança fornece-lhe um mapa de alertas de segurança para ajudar a identificar ameaças à segurança. O botão **de mapa de eventos** de segurança no mapa abre um dashboard que lhe permite ver eventos de segurança brutos no espaço de trabalho selecionado.

Removemos o botão **de mapa de eventos** de segurança e o painel de instrumentos por espaço de trabalho.

![Mapa de alertas de segurança - botão][10]

Quando seleciona o botão **de mapa de eventos de segurança,** abriu o painel de inteligência de ameaça (agora reformado).

![Dashboard Informações sobre ameaças][11]

Quando escolhe um espaço de trabalho para ver o seu painel de inteligência de ameaça, abriu o ecrã de alertas de segurança (agora reformado) no Log Analytics.

![Mapa de alertas de segurança no Log Analytics][12]

Os seus dados existentes permanecem disponíveis na solução de segurança Log Analytics e não foram modificados ou removidos.

### <a name="security-events-map---the-new-experience"></a>Mapa de eventos de segurança - a nova experiência

Encorajamo-lo a usar a funcionalidade do mapa de alertas incorporada no Security Center: **Mapa de alertas de segurança (Preview)**. Esta funcionalidade proporciona uma experiência otimizada e funciona em todas as subscrições e espaços de trabalho associados. Dá-lhe uma visão de alto nível em todo o seu ambiente e não está focado num único espaço de trabalho.

## <a name="custom-alert-rules-preview"></a>Regras de alerta personalizados (Pré-visualização)<a name="menu_customalerts"></a>

Retirámos [a experiência de alertas personalizados](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) no dia 30 de junho de 2019 porque a sua infraestrutura subjacente foi reformada. Após a data da aposentadoria, os alertas de segurança personalizados deixaram de ser gerados.
Recomendamos que ative [o Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) e re-crie os seus alertas personalizados. Em alternativa, pode criar os seus alertas com alertas de registo do Azure Monitor.

Para criar alertas personalizados com a Azure Sentinel:

1. [Abra o Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) e selecione o espaço de trabalho onde os seus alertas personalizados estão armazenados
1. Selecione **Analytics** a partir do menu
1. Siga as instruções no seguinte [tutorial](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) sobre como criar alertas personalizados em Azure Sentinel

Se não estiver interessado em utilizar o Azure Sentinel, pode criar os seus alertas com alertas de registo do Azure Monitor. Para obter instruções, consulte [Criar, visualizar e gerir alertas de registo utilizando alertas de Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [Registo Azure no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Alertas personalizados][13]

Para obter mais informações sobre a retirada de alertas personalizados, consulte [as Regras de Alerta Personalizados no Centro de Segurança Azure (Pré-visualização)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## <a name="security-alerts-investigation"></a>Segurança alerta investigação<a name="menu_investigate"></a>

A funcionalidade de investigação no Centro de Segurança ajuda-o a triagem de um potencial incidente de segurança. A funcionalidade permite-lhe compreender o alcance de um incidente e rastrear a sua causa principal. Retirámos esta funcionalidade do Security Center porque foi substituída por uma experiência melhorada no [Azure Sentinel.](https://azure.microsoft.com/services/azure-sentinel/)

![Incidente de segurança][14]

Quando selecionar o botão **Investigar** a partir de um ecrã de incidente de **Segurança,** abre o Painel de Investigação (Preview) em Log Analytics. Retirámos o painel de investigação.

Os seus dados existentes permanecem disponíveis na solução de segurança Log Analytics e não foram modificados ou removidos.

![Painel de investigação em Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Investigação - a nova experiência

Encorajamo-lo a transitar para [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) para uma rica experiência de investigação. O Azure Sentinel fornece ferramentas de pesquisa e consulta poderosas para caçar ameaças de segurança através das fontes de dados da sua organização.

## <a name="subset-of-security-solutions"></a>Subconjunto de soluções de segurança<a name="menu_solutions"></a>

O Centro de Segurança pode permitir [soluções de segurança integradas no Azure.](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) Retirámos as seguintes soluções parceiras do Centro de Segurança. Estas soluções estão ativadas no [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) juntamente com uma série de fontes de dados adicionais.

- [Soluções de firewall e web application de próxima geração](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integração de soluções de segurança que suportam o Formato Comum de Eventos (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Após a reforma, não é possível adicionar ou modificar nenhum dos tipos de solução mencionados na lista anterior, quer da UI, quer da API. O Azure Security Center deixará de descobrir quaisquer novos casos destas soluções parceiras.

Se tiver soluções conectadas existentes, encorajamo-lo a mudar-se para a Azure Sentinel.

![Soluções de centros de segurança][16]

## <a name="edit-security-configurations-for-security-policies"></a>Editar configurações de segurança para políticas de segurança<a name="menu_securityconfigurations"></a>

O Centro de Segurança do Azure monitoriza as configurações de segurança através da aplicação de um conjunto de [mais de 150 regras recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para proteger o SO. Estas regras dizem respeito a firewalls, auditoria, políticas de senhas e muito mais. Se um computador apresentar uma configuração vulnerável, o Centro de Segurança gerará uma recomendação de segurança. O [ecrã de configuração de segurança Editar](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permite que os clientes personalizem a configuração de segurança padrão do SISTEMA no Centro de Segurança.

Retirámos esta funcionalidade de pré-visualização. Para repor as suas configurações de segurança de volta aos seus valores predefinidos após a data de aposentação, faça-o através da API ou powershell utilizando as [seguintes instruções](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization).

![Editar configurações de segurança][17]

### <a name="edit-security-configurations---the-new-experience"></a>Editar configurações de segurança - a nova experiência

Pretendemos permitir que o Centro de Segurança apoie o [agente de configuração do Convidado.](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) Esta atualização permitirá um conjunto de funcionalidades muito mais rico, incluindo suporte para mais sistemas operativos e integração de políticas azure in-guest para configurações de hóspedes. Depois de estas alterações estarem ativadas, também terá a capacidade de controlar configurações em escala e aplicá-las automaticamente a novos recursos.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Painel de segurança e auditoria para espaços de trabalho log analytics<a name="menu_securityomsdashboard"></a>

O painel de segurança e auditoria foi originalmente utilizado no portal OMS. No Log Analytics, o dashboard fornece uma visão geral por espaço de trabalho de eventos e ameaças de segurança notáveis, um mapa de inteligência de ameaça, e uma avaliação de identidade e acesso de eventos de segurança salvos no espaço de trabalho. Removemos o painel. Como já recomendamos na UI do painel de instrumentos, aconselhamos a transição para o Centro de Segurança Azure.

![Painel de segurança Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Segurança e painel de auditoria - a nova experiência

Aconselhamos que mude para o Centro de Segurança Azure. Fornece a mesma visão geral de segurança em várias subscrições e nos espaços de trabalho associados a elas, além de um conjunto de funcionalidades mais rico.

Você pode obter as consultas originais do Log Analytics que povoam o painel de segurança e auditoria no [repositório GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) para o Centro de Segurança.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Saiba mais sobre [o Azure Sentinel.](https://docs.microsoft.com/azure/sentinel)

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
