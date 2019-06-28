---
title: Funcionalidades de extinção do Centro de segurança (Julho de 2019) | Documentos da Microsoft
description: Este artigo descreve os recursos no Centro de segurança que vão ser descontinuados a 31 de Julho de 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 1d364da9506124a35c724209c68ff72db4243e80
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341563"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Extinção dos recursos de centro de segurança (Julho de 2019)

Fizemos vários [melhorias](https://azure.microsoft.com/updates/?product=security-center) ao centro de segurança do Azure durante os últimos seis meses.
Com esses recursos aprimorados, podemos está removendo a algumas funcionalidades redundantes e APIs relacionadas no Centro de segurança de 31 de Julho de 2019.  

A maioria destas funcionalidades obsoletos pode ser substituída com a nova funcionalidade no Centro de segurança do Azure ou o Azure Log Analytics. Outros recursos podem ser implementados usando [do Azure (pré-visualização) de sentinela](https://azure.microsoft.com/services/azure-sentinel/).

Funcionalidades do Centro de segurança para serem extinguidos incluem:

- [Dashboard de eventos](#menu_events)
- [Entrada de menu de pesquisa](#menu_search)
- [Modo de exibição clássica identidade e acesso ligação sobre identidade e acesso (pré-visualização)](#menu_classicidentity)
- [Eventos de segurança mapeiam botão num mapa de alertas de segurança (pré-visualização)](#menu_securityeventsmap)
- [Regras de alerta personalizadas (pré-visualização)](#menu_customalerts)
- [Investigar o botão nos alertas de segurança de proteção de ameaças](#menu_investigate)
- [Um subconjunto de soluções de segurança](#menu_solutions)
- [Editar configurações de segurança para as políticas de segurança](#menu_securityconfigurations)
- [Segurança e auditoria do dashboard (utilizado originalmente no portal do OMS) para áreas de trabalho do Log Analytics](#menu_securityomsdashboard)

Este artigo fornece informações detalhadas para cada funcionalidade extinto e os passos que pode tomar para implementar recursos de substituição.

## Dashboard de eventos<a name="menu_events"></a>

Centro de segurança utiliza o Microsoft Monitoring Agent para recolher vários eventos e configurações relacionadas à segurança das máquinas. Ele armazena estes eventos em suas áreas de trabalho. O [dashboard de eventos](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) permite-lhe ver estes dados e dá-lhe um ponto de entrada para o Log Analytics.

Podemos reformar-se o dashboard de eventos que aparece quando seleciona uma área de trabalho:

![Dashboard de eventos][2]

### <a name="events-dashboard---the-new-experience"></a>Dashboard de eventos - a nova experiência

Podemos encorajados a utilizar os recursos nativos do Azure Log Analytics para ver eventos notáveis em suas áreas de trabalho.

Se criou eventos notáveis personalizados no Centro de segurança, eles serão acessíveis. No Log Analytics, aceda a **selecione a área de trabalho** > **pesquisas guardadas**. Os dados não são perdidos ou modificados. Eventos notáveis nativos também estão disponíveis no mesmo ecrã no Log Analytics.

![Área de trabalho pesquisas guardada][3]

## Entrada de menu de pesquisa<a name="menu_search"></a>

Atualmente, o Centro de segurança do Azure utiliza pesquisa de registos do Azure Monitor para recuperar e analisar os seus dados de segurança. Este ecrã serve como uma janela para a página de pesquisa do Log Analytics e os usuários podem executar consultas de pesquisa na sua área de trabalho selecionada. Para obter mais informações, consulte [pesquisa do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-search). Podemos reformar-se esta janela de pesquisa:

![Página de pesquisa][4]

### <a name="search-menu-entry---the-new-experience"></a>Entrada de menu de pesquisa - a nova experiência

Incentivamos a usar os recursos nativos do Azure Log Analytics para executar consultas de pesquisa em suas áreas de trabalho. Aceda ao Azure Log Analytics e selecione **registos**.

![Página de registos do log Analytics][5]

## Clássica identidade e acesso (pré-visualização)<a name="menu_classicidentity"></a>

A experiência clássica de identidade e acesso no Centro de segurança atualmente mostra um dashboard de informações de identidade e acesso no Log Analytics. Para ver este dashboard:

1. Selecione **ver identidade e acesso clássicos**.

   ![Página de identidade][6]

1. Ver os **dashboard de identidade e acesso**.

    ![Página de identidade - seleção de área de trabalho][7]

1. Selecione uma área de trabalho para abrir o **identidade e acesso** dashboard no Log Analytics para ver a identidade e aceder a informações na sua área de trabalho.

   ![Página de identidade - dashboard][8]

Podemos reformar-se todos os três ecrãs mostrados nos passos anteriores. Os dados permanecerão disponíveis na solução de segurança do Log Analytics e não ser modificados ou removidos.

### <a name="classic-identity--access-preview---the-new-experience"></a>Identidade e acesso (pré-visualização) - a nova experiência clássicos

O dashboard do Log Analytics mostrou insights numa única área de trabalho. No entanto, os recursos nativos do Centro de segurança fornecem visibilidade de todas as subscrições e todas as áreas de trabalho associadas a eles. Pode acessar uma fácil-para utilizar o modo de exibição que permite-lhe concentrar-se no que é importante com recomendações classificadas de acordo com a respectiva pontuação segura.

Todos os recursos da **identidade e acesso** dashboard no Log Analytics pode ser alcançado ao selecionar **identidade e acesso (pré-visualização)** no Centro de segurança.

![Extinção de experiência de página de identidade - clássica][9]

## Mapa de eventos de segurança<a name="menu_securityeventsmap"></a>

Centro de segurança fornece-lhe com um [mapa de alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) para ajudar a identificar ameaças de segurança. O **vá para o mapa de eventos de segurança** botão nesse mapa abre um dashboard que permite-lhe ver eventos de segurança bruto na área de trabalho selecionada.

Podemos está removendo a **vá para o mapa de eventos de segurança** botão e o dashboard por área de trabalho.

![Botão de mapa - de alertas de segurança][10]

Quando seleciona a **vá para o mapa de eventos de segurança** botão, abra o dashboard de informações sobre ameaças. Podemos reformar-se o dashboard de informações sobre ameaças.  

![Dashboard Informações sobre ameaças][11]

Ao escolher uma área de trabalho para ver o seu dashboard de inteligência de ameaças, abre o ecrã de mapa (pré-visualização) de alertas de segurança no Log Analytics. Podemos reformar-se neste ecrã.

![Mapa de alertas de segurança no Log Analytics][12]

Os dados existentes permanecerão disponíveis na solução de segurança do Log Analytics e não ser modificados nem removidos.

### <a name="security-events-map---the-new-experience"></a>Mapa de eventos de segurança - a nova experiência

Aconselhamo-lo para utilizar a funcionalidade de mapa de alertas incorporada no Centro de segurança: **Mapa de alertas de segurança (pré-visualização)** . Esta funcionalidade fornece uma experiência otimizada e funciona em todas as subscrições e áreas de trabalho associadas. Ele oferece uma visão de alto nível em todo o ambiente e não está concentrado num único espaço de trabalho.

## Regras de alerta personalizadas (pré-visualização)<a name="menu_customalerts"></a>

Estamos [extinguir o personalizado de alertas experiência](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 30 de Junho de 2019 porque está desativando a respetiva infraestrutura subjacente. Até lá, pode editar regras de alerta personalizadas existentes, mas que não é possível adicionar novas etiquetas. Após a data de retirada, todos os alertas personalizados definidos não vão ter efeito e não serão gerados alertas de segurança com base nestas regras.
Recomendamos que ative [Azure sentinela](https://azure.microsoft.com/services/azure-sentinel/) e voltar a criar seus alertas personalizados lá. Em alternativa, pode criar os seus alertas com alertas de registo do Azure Monitor.

Para manter os alertas existentes e criá-los com o Azure sentinela:

1. [Abra o Azure sentinela](https://portal.azure.com/#create/Microsoft.ASI/preview) e selecione a área de trabalho onde estão armazenadas os seus alertas personalizados
1. Selecione **Analytics** do menu
1. Siga as instruções a seguir [tutorial](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) sobre como criar alertas personalizados no Azure sentinela

Se não estiver interessado em utilizar o Azure sentinela, pode criar os alertas com alertas de registo do Azure Monitor. Para obter instruções, consulte [criar, ver e gerir alertas de registo com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [alertas de registo no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Alertas personalizados][13]

Para obter mais informações sobre a extinção de alertas personalizados, consulte [regras de alerta personalizadas no Centro de segurança do Azure (pré-visualização)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Investigação de alertas de segurança<a name="menu_investigate"></a>

[A funcionalidade de investigação](https://docs.microsoft.com/azure/security-center/security-center-investigation) no Centro de segurança ajuda a fazer a triagem um incidente de segurança potencial. A funcionalidade permite-lhe compreender o escopo de um incidente e rastrear a raiz do problema. Podemos está removendo a esta funcionalidade do Centro de segurança porque ele é foi substituído por uma experiência aprimorada em [Azure sentinela](https://azure.microsoft.com/services/azure-sentinel/).

![Incidente de segurança][14]

Quando seleciona a **investigar** botão a partir de um **incidente de segurança** ecrã, abra o Dashboard de investigação (pré-visualização) no Log Analytics. Podemos reformar-se o Dashboard de investigação.  

Os dados existentes permanecerão disponíveis na solução de segurança do Log Analytics e não ser modificados nem removidos.

![Dashboard de investigação do Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Investigação - a nova experiência

Nós o encorajamos a transição para [Azure sentinela](https://azure.microsoft.com/services/azure-sentinel/) para uma experiência de investigação avançadas. Sentinel do Azure fornece ferramentas avançadas de pesquisa e consulta para ameaças de segurança entre origens de dados da sua organização.  

## Subconjunto de soluções de segurança<a name="menu_solutions"></a>

Pode ativar o Centro de segurança [integrado de soluções de segurança no Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Podemos reformar-se as seguintes soluções de parceiros no Centro de segurança. Estas soluções estão ativadas no [Azure sentinela](https://azure.microsoft.com/services/azure-sentinel/) juntamente com um número de origens de dados adicionais.

- [Próxima geração web e de firewall de aplicação soluções de firewall](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integração de soluções de segurança que suporte o Common Event Format (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Depois de ser retirado, não será possível adicionar ou modificar qualquer um dos tipos de solução mencionados na lista anterior, a partir da interface do Usuário ou a API.

Se tiver soluções ligadas existentes, é recomendável que mova para sentinela do Azure.

![Soluções de centros de segurança][16]

## Editar configurações de segurança para as políticas de segurança<a name="menu_securityconfigurations"></a>

Centro de segurança do Azure monitoriza as configurações de segurança através da aplicação de um conjunto de [mais de 150 recomendado regras](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). para proteger o sistema operacional. Estas regras dizem respeito a firewalls, auditoria, as políticas de palavra-passe e muito mais. Se um computador apresentar uma configuração vulnerável, o Centro de Segurança gerará uma recomendação de segurança. O [ecrã de configuração de segurança de edição](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permite que os clientes personalizar a configuração de segurança do sistema operacional padrão no Centro de segurança.

Podemos reformar-se esta funcionalidade de pré-visualização. Se, após a data de retirada, gostaria de repor as suas configurações de segurança seus valores padrão, pode fazê-lo através da API ou o Powershell utilizando o [seguir instruções](https://aka.ms/ascresetsecurityconfigurations)

![Editar configurações de segurança][17]

### <a name="edit-security-configurations---the-new-experience"></a>Editar configurações de segurança - a nova experiência

Pretendemos ativar o Centro de segurança suportar o [agente convidado da configuração](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Tal uma atualização irá permitir que um conjunto de recursos muito mais avançado, incluindo suporte para obter mais sistemas operativos e na integração das políticas de convidado do Azure para configurações de convidado. Depois destas alterações estiverem ativadas, também terá a capacidade de controlar configurações em escala e aplicá-las automaticamente para novos recursos.

## Dashboard de segurança e auditoria para áreas de trabalho do Log Analytics<a name="menu_securityomsdashboard"></a>

O dashboard de segurança e auditoria tinha sido originalmente utilizado no portal do OMS. No Log Analytics, o dashboard fornece uma visão geral de por área de trabalho de eventos de segurança notáveis e ameaças, um mapa de inteligência de ameaças e uma avaliação de identidade e acesso de eventos de segurança guardado na área de trabalho. Iremos estiver a remover o dashboard. Como já foi recomendado no dashboard de interface do Usuário, aconselhamos que efetuar a transição para o Centro de segurança do Azure.

![Dashboard de segurança do log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Dashboard de segurança e auditoria - a nova experiência

Aconselhamo-lo para mudar para o Centro de segurança do Azure. Ele fornece a mesma descrição geral da segurança em várias subscrições e as áreas de trabalho associadas a eles, além de definir uma funcionalidade mais avançada.

Pode obter as consultas do Log Analytics originais que a preencher o dashboard de segurança e auditoria no [repositório do GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) para o Centro de segurança.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/).
- Saiba mais sobre [Azure sentinela](https://docs.microsoft.com/azure/sentinel).

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
