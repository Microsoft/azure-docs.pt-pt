---
title: Funcionalidades de extinção do Centro de segurança (Julho de 2019) | Documentos da Microsoft
description: Este artigo fornece detalhes sobre os recursos no Centro de segurança que vão ser descontinuados a 31 de Julho de 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 392782310d8bc3b38a3dd1349cb1760ca287acd1
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875591"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Extinção dos recursos de centro de segurança (Julho de 2019)

Efetuamos várias [melhorias](https://azure.microsoft.com/updates/?product=security-center) ao centro de segurança do Azure durante os últimos 6 meses.  
Com os recursos aprimorados estamos a remover um número de recursos redundantes, bem como APIs relacionadas, do Centro de segurança de 31 de Julho de 2019.  

A maioria dos recursos extintos pode ser substituída por novos recursos do Centro de segurança do Azure ou o Log Analytics; e algumas funcionalidades podem ser implementadas usando [do Azure (pré-visualização) de sentinela](https://azure.microsoft.com/services/azure-sentinel/), recentemente anunciado.

A lista de recursos a ser retirada a partir do Centro de segurança inclui:

- [Dashboard de eventos](#menu_events)
- [Entrada de menu de pesquisa](#menu_search)
- [Modo de exibição clássica identidade e acesso ligação sobre identidade e acesso (pré-visualização)](#menu_classicidentity)
- [Eventos de segurança mapeiam botão num mapa de alertas de segurança (pré-visualização)](#menu_securityeventsmap)
- [Regras de alerta personalizadas (pré-visualização)](#menu_customalerts)
- [Investigar o botão nos alertas de segurança de proteção de ameaças](#menu_investigate)
- [Um subconjunto de soluções de segurança](#menu_solutions)
- [Editar configurações de segurança para as políticas de segurança](#menu_securityconfigurations)
- [Segurança e auditoria dashboard (utilizado originalmente no portal do OMS) para áreas de trabalho do Log Analytics.](#menu_securityomsdashboard)

A seguir fornece informações detalhadas para cada funcionalidade extinto e os passos que pode seguir usando os recursos de substituição.

## Dashboard de eventos<a name="menu_events"></a>
Usos do Centro de segurança do Microsoft Monitoring Agent para recolher vários segurança e configurações relacionados com eventos das suas máquinas e armazena esses eventos em suas áreas de trabalho. O [dashboard de eventos](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) permite ver estes dados e essencialmente fornece outro ponto de entrada para o Log Analytics.

Daqui em diante o dashboard de eventos vai ser descontinuado:

![Ecrã de seleção de área de trabalho de eventos][1]

O dashboard de eventos que é apresentado quando um utilizador clica na área de trabalho, também vão ser descontinuados:

![Dashboard de eventos][2]

### <a name="events-dashboard---new-experience"></a>Dashboard de eventos - nova experiência

Os clientes são incentivados a usar os recursos nativos do Log Analytics para ver eventos notáveis em suas áreas de trabalho.
Se já tiver criado eventos notáveis personalizados a partir do Centro de segurança, estes serão acessíveis através do Log analytics -> selecione pesquisas guardadas -> de área de trabalho. Os dados não serão perdidos nem modificados. Eventos notáveis nativos também estão disponíveis no mesmo ecrã.

![Área de trabalho pesquisas guardada][3]

## Entrada de menu de pesquisa<a name="menu_search"></a>
Atualmente, o Centro de segurança do Azure utiliza pesquisa de registos do Azure Monitor para recuperar e analisar os seus dados de segurança. Este ecrã serve essencialmente como uma fachada para o Log Analytics "[pesquisa](https://docs.microsoft.com/azure/security-center/security-center-search)" página – que permite aos utilizadores para executarem consultas de pesquisa na sua área de trabalho selecionada. Vai ser descontinuado a daqui em diante esta janela de pesquisa:

![Página de pesquisa][4]

### <a name="search-menu-entry---new-experience"></a>Entrada de menu - nova experiência de pesquisa

Os clientes são incentivados a usar **do Log Analytics** recursos nativos para executar consultas de pesquisa em suas áreas de trabalho. Para fazer isso, eles podem ir para o Log analytics no Azure e selecione: "Registos":

![Página de registos do log Analytics][5]

## Clássica identidade e acesso (pré-visualização)<a name="menu_classicidentity"></a>
A experiência de identidade e acesso de "Clássico" no Centro de segurança tinha fornecido uma forma para os clientes ver a sua identidade e aceder a informações relacionadas no log analytics. Isso foi feito ao seguir os cliques abaixo:

Clique em "Acesso e identidade de exibição clássica"

![Página de identidade][6]

Juntamente com a tela abertos nesta página, "dashboard de identidade e acesso":

![Página de identidade - seleção de área de trabalho][7]

Um clique na área de trabalho abre o "Acesso e identidades" log analytics dashboard onde os clientes podem ver identidade e aceder a informações na sua área de trabalho:

![Página de identidade - dashboard][8]

Daqui em diante todas as três telas acima, será descontinuado. Os dados permanecerão disponíveis da solução de segurança do log analytics e serão não modificados ou removidos.

### <a name="classic-identity--access-preview---new-experience"></a>Identidade e acesso (pré-visualização) - nova experiência clássicos
Embora o dashboard de análise de registo forneceu informações numa área de trabalho específica apenas, os recursos nativos do Centro de segurança fornecem visibilidade de todas as subscrições e todas as áreas de trabalho associadas a eles, numa fácil-para utilizar a vista que lhe permite se concentrar no que do importante, de acordo com a classificação de segurança da sua identidade e acesso (aram).
Todas as funcionalidades da identidade e acesso dashboard de análise de registo podem ser conectadas, selecionando "Identidade e acesso (pré-visualização)" no Centro de segurança:

![Extinção de experiência de página de identidade - clássica][9]

## Mapa de eventos de segurança<a name="menu_securityeventsmap"></a>
Centro de segurança fornece-lhe com um [mapa](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) que ajuda a identificar ameaças de segurança contra o ambiente. O botão "Ir para o mapa de eventos de segurança" nesse mapa leva a um dashboard que permite ver eventos de segurança bruto na área de trabalho selecionada.
O botão, juntamente com o dashboard por área de trabalho, será removido após a descontinuação.

![Botão de mapa - de alertas de segurança][10]

Hoje em dia quando clica em "Ir para o mapa de eventos de segurança" é aberto o dashboard de informações sobre ameaças. O dashboard de informações sobre ameaças vai ser descontinuado.  

![Dashboard Informações sobre ameaças][11]

Ao escolher uma área de trabalho para ver o seu dashboard de inteligência de ameaças, a segurança alertas ecrã map(Preview) *no Log Analytics* é aberto. Este ecrã, será descontinuado.

![Mapa de alertas de segurança no Log Analytics][12]

Os dados existentes permanecerão disponíveis na solução de segurança de análise de registo e não irão ser modificados nem removidos.

### <a name="security-events-map---new-experience"></a>Mapa de eventos de segurança - nova experiência
Nós o encorajamos os nossos clientes para utilizar a funcionalidade de mapa de alertas incorporada no Centro de segurança – "mapeiam de alertas de segurança (pré-visualização)". Isto proporciona uma experiência otimizada e funciona em todas as subscrições e áreas de trabalho associadas, permitindo uma macro ver no seu ambiente e não se concentrar num único espaço de trabalho.

## Regras de alerta personalizadas (pré-visualização)<a name="menu_customalerts"></a>
A experiência de alertas personalizados seja [extinto](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) em 30 de Junho de 2019 devido a extinção da infraestrutura subjacente tenha sido criada nas. Num período de tempo até que a desaprovação, os utilizadores poderão editar regras de alerta personalizadas existentes, mas não será possível adicionar novas etiquetas. Os usuários são aconselhados a ativar [Azure sentinela](https://azure.microsoft.com/services/azure-sentinel/) com a integração de um clique para migrar os seus alertas existentes e criar novos ou voltar a criar seus alertas com o Azure Monitor em alternativa automaticamente alertas de registo.

Para manter os alertas existentes e migrá-los para o Azure sentinela,. Inicie sentinela do Azure. Como primeiro passo, selecione a área de trabalho onde estão armazenadas os seus alertas personalizados e, em seguida, selecione o item de menu 'Analytics' para migrar automaticamente os alertas.

![Alertas personalizados][13]

Os clientes não interessados na adesão ao Azure sentinela são incentivados a voltar a criar seus alertas com alertas de registo do Azure Monitor. Para obter instruções, consulte: [criar, ver e gerir alertas de registo com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Para obter instruções sobre como criar alertas de registo, consulte: [Alertas de registo no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Para obter mais informações sobre a extinção de alertas personalizados, consulte a [documentação de alertas do Centro de segurança personalizada](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Investigação de alertas de segurança<a name="menu_investigate"></a>
[A funcionalidade de investigação](https://docs.microsoft.com/azure/security-center/security-center-investigation) no Centro de segurança permite-lhe Triar, compreender o escopo e rastrear a causa raiz dos incidentes de segurança potencial. Esta funcionalidade será removida do Centro de segurança como ele foi substituído por uma experiência aprimorada em [Azure sentinela](https://azure.microsoft.com/services/azure-sentinel/).

![Incidente de segurança][14]

Ao clicar no botão "Investigar" acima, abre o "Dashboard de investigação (pré-visualização)" no Log Analytics. O Dashboard de investigação, será descontinuado.  
Os dados existentes permanecerão disponíveis na solução de segurança do Log Analytics e não irão ser modificados nem removidos.

![Dashboard de investigação do Log Analytics][15]

### <a name="investigation---new-experience"></a>Investigação - nova experiência

Nós o encorajamos os nossos clientes para carregar [Azure sentinela](https://azure.microsoft.com/services/azure-sentinel/) para uma experiência de investigação avançadas, com tecnologia de alertas caçar funcionalidade. Sentinel do Azure fornece ferramentas de pesquisa e consulta de arquivo terá poderosas para ameaças de segurança entre origens de dados da sua organização.  

## Subconjunto de soluções de segurança<a name="menu_solutions"></a>

Centro de segurança tem a capacidade de permitir [integrado de soluções de segurança no Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). As seguintes soluções de parceiros seriam removidas e suportadas no [Azure sentinela](https://azure.microsoft.com/services/azure-sentinel/), juntamente com origens de dados ainda mais.

- Próxima geração de firewall e as soluções de Firewall de aplicações da Web (Azure sentinela [documentação](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- Integração de soluções de segurança que suporte o formato de evento comum (CEF) (Azure sentinela [documentação](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (Azure Sentinel [documentação](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Identity Protection (Azure Sentinel [documentação](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

Após a remoção, os utilizadores não poderá adicionar novo ou modificar soluções ligadas existentes dos tipos mencionados acima, da interface do Usuário e a API.
As soluções ligadas existentes são encorajadas a mover para o Azure sentinela no final deste período.

![Soluções de centros de segurança][16]

## Editar configurações de segurança para as políticas de segurança<a name="menu_securityconfigurations"></a>
Centro de segurança do Azure monitoriza as configurações de segurança através da aplicação de um conjunto de [mais de 150 recomendado regras](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para proteger o sistema operacional, incluindo regras relacionadas com firewalls, auditoria, as políticas de palavra-passe e muito mais. Se uma máquina é encontrada ter uma configuração vulnerável, o Centro de segurança gera uma recomendação de segurança. O [ecrã de configuração de segurança de edição](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permite que os clientes personalizar a configuração de segurança do sistema operacional padrão no Centro de segurança.

Esta funcionalidade está em pré-visualização e vai ser descontinuada.

![Editar configurações de segurança][17]

### <a name="edit-security-configurations---new-experience"></a>Editar configurações de segurança – nova experiência

Centro de segurança irá suportar o [agente in-Guest](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) num futuro próximo, permitindo que um conjunto de recursos muito mais avançado – incluindo suporte para sistemas operativos adicionais e integração com a configuração de convidado com as políticas do Azure (no convidado políticas). Isso também fornecerá a capacidade de controlar em escala e aplicar automaticamente sobre os novos recursos.

## Segurança e auditoria dashboard (utilizado originalmente no portal do OMS) para áreas de trabalho do Log Analytics<a name="menu_securityomsdashboard"></a>

O dashboard de segurança no Log analytics fornece uma visão geral de por área de trabalho de eventos de segurança notáveis e ameaças, um mapa de inteligência de ameaças e avaliação de identidade e acesso de eventos de segurança guardado na área de trabalho. O dashboard será removido no futuro. Como já foi recomendado no dashboard de interface do Usuário, nossos usuários são aconselhados a utilizar o Centro de segurança do Azure no futuro.

![Dashboard de segurança do log analytics][18]

### <a name="security--audit-dashboard---new-experience"></a>Dashboard de segurança e auditoria - nova experiência
Os nossos clientes são aconselhados a utilizar o Centro de segurança do Azure, que fornece a mesma descrição geral da segurança em várias subscrições e áreas de trabalho associadas a eles, juntamente com um conjunto de funcionalidades mais avançado.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/)
- Saiba mais sobre [sentinela do Azure](https://docs.microsoft.com/azure/sentinel)

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
