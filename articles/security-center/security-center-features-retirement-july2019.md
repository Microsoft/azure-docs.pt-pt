---
title: Aposentadoria dos recursos da central de segurança (julho de 2019) | Microsoft Docs
description: Este artigo descreve os recursos na central de segurança que foram desativados em 31 de julho de 2019.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 6e7263dfa0faf0bf2fd7e8b7d6e050ca6bc95e2a
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202203"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Aposentadoria dos recursos da central de segurança (julho de 2019)

> [!NOTE]
> Este documento detalha a lista de recursos que foram desativados da central de segurança do Azure em 31 de julho de 2019.
>
>

Fizemos várias [melhorias](https://azure.microsoft.com/updates/?product=security-center) na central de segurança do Azure durante os seis meses, levando até 2019 de julho.
Com esses recursos aprimorados, removemos alguns recursos redundantes e APIs relacionadas da central de segurança em 31 de julho de 2019.

A maioria desses recursos desativados pode ser substituída por outras funcionalidades na central de segurança do Azure ou no Azure Log Analytics. Outros recursos podem ser implementados usando o [Azure Sentinel (visualização)](https://azure.microsoft.com/services/azure-sentinel/).

Os recursos desativados da central de segurança incluem:

- [Painel de eventos](#menu_events)
- [Entrada do menu Pesquisar](#menu_search)
- [Exibir a identidade clássica & link de acesso na identidade e no acesso (visualização)](#menu_classicidentity)
- [Botão mapa de eventos de segurança no mapa de alertas de segurança (versão prévia)](#menu_securityeventsmap)
- [Regras de alerta personalizadas (versão prévia)](#menu_customalerts)
- [Botão investigar em alertas de segurança de proteção contra ameaças](#menu_investigate)
- [Um subconjunto de soluções de segurança](#menu_solutions)
- [Editar configurações de segurança para políticas de segurança](#menu_securityconfigurations)
- [Painel de segurança e auditoria (originalmente usado no portal do OMS) para espaços de trabalho Log Analytics](#menu_securityomsdashboard)

Este artigo fornece informações detalhadas para cada recurso desativado e as etapas que você pode seguir para implementar recursos de substituição.

## Painel de eventos<a name="menu_events"></a>

A central de segurança usa Microsoft Monitoring Agent para coletar várias configurações e eventos relacionados à segurança de seus computadores. Ele armazena esses eventos em seus espaços de trabalho. O [painel eventos](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) permite exibir esses dados e fornece um ponto de entrada para log Analytics.

Desativamos o painel eventos que apareceu quando você selecionou um espaço de trabalho:

![Dashboard de eventos][2]

### <a name="events-dashboard---the-new-experience"></a>Painel de eventos – a nova experiência

Incentivamos você a usar os recursos nativos do Azure Log Analytics para exibir eventos notáveis em seus espaços de trabalho.

Se você criou eventos notáveis personalizados na central de segurança, eles estarão acessíveis. Em log Analytics, vá para **selecionar** > **pesquisas salvas**do espaço de trabalho. Seus dados não serão perdidos ou modificados. Os eventos notáveis nativos também estão disponíveis na mesma tela no Log Analytics.

![Pesquisas salvas do espaço de trabalho][3]

## Entrada do menu Pesquisar<a name="menu_search"></a>

A central de segurança do Azure usa atualmente Azure Monitor pesquisa de logs para recuperar e analisar seus dados de segurança. Essa tela serve como uma janela para Log Analytics página de pesquisa e permite que os usuários executem consultas de pesquisa em seu espaço de trabalho selecionado. Para obter mais informações, consulte a [pesquisa da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-search). Desativamos esta janela de pesquisa:

![Página de pesquisa][4]

### <a name="search-menu-entry---the-new-experience"></a>Entrada do menu Pesquisar – a nova experiência

Incentivamos você a usar os recursos nativos do Azure Log Analytics para executar consultas de pesquisa em seus espaços de trabalho. Vá para Log Analytics do Azure e selecione **logs**.

![Página de logs de Log Analytics][5]

## Acesso & de identidade clássica (versão prévia)<a name="menu_classicidentity"></a>

A identidade clássica & experiência de acesso na central de segurança atualmente mostra um painel de identidade e informações de acesso em Log Analytics. Para exibir este painel:

1. Selecione **Exibir identidade clássica & acesso**.

   ![Página de identidade][6]

1. Exiba o **painel identidade & acesso**.

    ![Página de identidade-seleção de espaço de trabalho][7]

1. Selecione um espaço de trabalho para abrir o painel **identidade & acesso** no log Analytics para exibir informações de identidade e acesso em seu espaço de trabalho.

   ![Página de identidade-painel][8]

Desativamos todas as três telas mostradas nas etapas anteriores. Seus dados permanecem disponíveis na solução de segurança Log Analytics e não foram modificados ou removidos.

### <a name="classic-identity--access-preview---the-new-experience"></a>Acesso & de identidade clássica (versão prévia) – a nova experiência

O painel de Log Analytics mostrou informações sobre um único espaço de trabalho. No entanto, os recursos nativos da central de segurança fornecem visibilidade de todas as assinaturas e de todos os espaços de trabalho associados a elas. Você pode acessar uma exibição fácil de usar que permite que você se concentre no que é importante com as recomendações classificadas de acordo com sua pontuação segura.

Todos os recursos do painel **identidade & acesso** no log Analytics podem ser acessados selecionando **identidade & acesso (versão prévia)** na central de segurança.

![Página de identidade-aposentadoria da experiência clássica][9]

## Mapa de eventos de segurança<a name="menu_securityeventsmap"></a>

A central de segurança fornece um [mapa de alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) para ajudar a identificar ameaças à segurança. O botão **ir para mapa de eventos de segurança** no mapa abre um painel que permite que você exiba eventos de segurança brutos no espaço de trabalho selecionado.

Removemos o botão **ir para mapa de eventos de segurança** e o painel por espaço de trabalho.

![Mapa de alertas de segurança-botão][10]

Quando você seleciona o botão **ir para mapa de eventos de segurança** , ele abriu o painel de inteligência contra ameaças (agora desativado).

![Dashboard Informações sobre Ameaças][11]

Ao escolher um espaço de trabalho para exibir seu painel de inteligência contra ameaças, você abriu a tela (visualização) mapa de alertas de segurança (versão prévia) no Log Analytics.

![Mapeamento de alertas de segurança no Log Analytics][12]

Os dados existentes permanecem disponíveis na solução de segurança Log Analytics e não foram modificados ou removidos.

### <a name="security-events-map---the-new-experience"></a>Mapa de eventos de segurança-a nova experiência

Incentivamos você a usar a funcionalidade de mapa de alertas interna na central de segurança: **Mapa de alertas de segurança (versão prévia)** . Essa funcionalidade fornece uma experiência otimizada e funciona em todas as assinaturas e espaços de trabalho associados. Ele fornece uma exibição de alto nível em seu ambiente e não se concentra em um único espaço de trabalho.

## Regras de alerta personalizadas (versão prévia)<a name="menu_customalerts"></a>

[Desativamos a experiência de alertas personalizados](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) em 30 de junho de 2019 porque sua infraestrutura subjacente foi desativada. Após a data de desativação, os alertas de segurança personalizados não são mais gerados.
Recomendamos que você habilite o [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) e crie novamente seus alertas personalizados. Como alternativa, você pode criar alertas com Azure Monitor alertas de log.

Para criar alertas personalizados com o Azure Sentinel:

1. [Abra o Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) e selecione o espaço de trabalho onde os alertas personalizados são armazenados
1. Selecione **análise** no menu
1. Siga as instruções no seguinte [tutorial](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) sobre como criar alertas personalizados no Azure Sentinel

Se não estiver interessado em usar o Azure Sentinel, você poderá criar alertas com Azure Monitor alertas de log. Para obter instruções, consulte [criar, exibir e gerenciar alertas de log usando Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [alertas de log no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Alertas personalizados][13]

Para obter mais informações sobre a desativação de alertas personalizados, consulte [regras de alerta personalizadas na central de segurança do Azure (versão prévia)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Investigação de alertas de segurança<a name="menu_investigate"></a>

[O recurso de investigação](https://docs.microsoft.com/azure/security-center/security-center-investigation) na central de segurança ajuda a fazer uma triagem de um incidente de segurança em potencial. O recurso permite que você compreenda o escopo de um incidente e rastreie sua causa raiz. Removemos esse recurso da central de segurança porque ele foi substituído por uma experiência aprimorada no [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Incidente de segurança][14]

Ao selecionar o botão **investigar** em uma tela de **incidente de segurança** , você abre o painel investigação (versão prévia) no log Analytics. Desativamos o painel de investigação.

Os dados existentes permanecem disponíveis na solução de segurança Log Analytics e não foram modificados ou removidos.

![Painel de investigação no Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Investigação – a nova experiência

Incentivamos você a fazer a transição para o [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) para uma experiência de investigação rica. O Azure Sentinel fornece ferramentas poderosas de pesquisa e consulta para procurar ameaças à segurança nas fontes de dados de sua organização.

## Subconjunto de soluções de segurança<a name="menu_solutions"></a>

A central de segurança pode habilitar [soluções de segurança integradas no Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Desativamos as seguintes soluções de parceiros da central de segurança. Essas soluções são habilitadas no [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) junto com várias fontes de dados adicionais.

- [Firewall da próxima geração e soluções de firewall do aplicativo Web](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integração de soluções de segurança que dão suporte ao formato de evento comum (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Análise avançada de ameaças da Microsoft](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Após a desativação, você não pode adicionar ou modificar nenhum dos tipos de solução mencionados na lista anterior, seja da interface do usuário ou da API. A central de segurança do Azure não descobrirá mais nenhuma nova instância dessas soluções de parceiros.

Se você tiver soluções conectadas existentes, recomendamos que você passe para o Azure Sentinel.

![Soluções de centros de segurança][16]

## Editar configurações de segurança para políticas de segurança<a name="menu_securityconfigurations"></a>

O Centro de Segurança do Azure monitoriza as configurações de segurança através da aplicação de um conjunto de [mais de 150 regras recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para proteger o SO. Essas regras pertencem a firewalls, auditoria, políticas de senha e muito mais. Se um computador apresentar uma configuração vulnerável, o Centro de Segurança gerará uma recomendação de segurança. A [tela Editar configuração de segurança](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permite que os clientes personalizem a configuração de segurança do sistema operacional padrão na central de segurança.

Desativamos esse recurso de visualização. Para redefinir as configurações de segurança de volta para seus valores padrão após a data de desativação, faça isso por meio da API ou do PowerShell usando as [instruções a seguir](https://aka.ms/ascresetsecurityconfigurations)

![Editar configurações de segurança][17]

### <a name="edit-security-configurations---the-new-experience"></a>Editar configurações de segurança-a nova experiência

Pretendemos habilitar a central de segurança para dar suporte ao [agente de configuração de convidado](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Essa atualização permitirá um conjunto de recursos muito mais rico, incluindo suporte para mais sistemas operacionais e integração de políticas do Azure no convidado para configurações de convidado. Depois que essas alterações forem habilitadas, você também terá a capacidade de controlar as configurações em escala e aplicá-las a novos recursos automaticamente.

## Painel de segurança e auditoria para espaços de trabalho do Log Analytics<a name="menu_securityomsdashboard"></a>

O painel de segurança e auditoria foi usado originalmente no portal do OMS. No Log Analytics, o painel fornece uma visão geral por espaço de trabalho de eventos e ameaças de segurança notáveis, um mapa de inteligência contra ameaças e uma avaliação de identidade e acesso de eventos de segurança salvos no espaço de trabalho. Removemos o painel. Como já recomendamos na interface do usuário do painel, aconselhamos a transição para a central de segurança do Azure.

![Painel de segurança do Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Painel de segurança e auditoria-a nova experiência

Aconselhamos que você alterne para a central de segurança do Azure. Ele fornece a mesma visão geral de segurança em várias assinaturas e nos espaços de trabalho associados a elas, além de um conjunto de recursos mais rico.

Você pode obter as consultas de Log Analytics originais que preenchem o painel segurança e auditoria no [repositório GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) para a central de segurança.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/).
- Saiba mais sobre o [Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

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
