---
title: Notas de lançamento para O Centro de Segurança Azure
description: Uma descrição do que é novo e mudado no Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6c69e5923e2b65bab0feca04d10fa67f49df3616
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174488"
---
# <a name="whats-new-in-azure-security-center"></a>O que há de novo no Centro de Segurança Azure?

O Centro de Segurança está em desenvolvimento ativo e recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, esta página fornece-lhe informações sobre novas funcionalidades, correções de bugs e funcionalidades preprecadas.

Esta página é atualizada com frequência, por isso revisite-a com frequência. 

Para saber mais sobre as mudanças *planeadas* que estão a chegar em breve ao Centro de Segurança, consulte [as próximas alterações importantes para o Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Se procura itens com mais de seis meses, vai encontrá-los no Arquivo para o [que há de novo no Azure Security Center.](release-notes-archive.md)



## <a name="march-2021"></a>março de 2021

As atualizações em março incluem:

- [Gestão do Azure Firewall integrada no Centro de Segurança](#azure-firewall-management-integrated-into-security-center)
- [A avaliação da vulnerabilidade SQL inclui agora a experiência "Desativar regra" (pré-visualização)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Livros de trabalho do Monitor Azure integrados no Centro de Segurança e três modelos fornecidos](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [O painel de conformidade regulamentar inclui agora os relatórios da Auditoria Azure (pré-visualização)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Atualizações das políticas de implantação da automatização do fluxo de trabalho](#updates-to-the-policies-for-deploying-workflow-automation)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Gestão do Azure Firewall integrada no Centro de Segurança

Quando abre o Centro de Segurança Azure, a primeira página a aparecer é a página geral. 

Este painel interativo proporciona uma visão unificada da postura de segurança das suas cargas de trabalho em nuvem híbrida. Além disso, mostra alertas de segurança, informações de cobertura, e muito mais.

Como parte de ajudá-lo a ver o seu estado de segurança a partir de uma experiência central, integrámos o Azure Firewall Manager neste dashboard. Agora pode verificar o estado de cobertura do Firewall em todas as redes e gerir centralmente as políticas do Azure Firewall a partir do Security Center.

Saiba mais sobre este dashboard na [página geral do Azure Security Center](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Painel geral do Centro de Segurança com um azulejo para Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>A avaliação da vulnerabilidade SQL inclui agora a experiência "Desativar regra" (pré-visualização)

O Security Center inclui um scanner de vulnerabilidade incorporado para ajudá-lo a descobrir, rastrear e remediar potenciais vulnerabilidades de base de dados. As conclusões das suas análises fornecem uma visão geral do estado de segurança das suas máquinas SQL e detalhes de quaisquer conclusões de segurança.

Se tiver uma necessidade organizacional de ignorar uma descoberta, em vez de remediar, pode desativá-la opcionalmente. As descobertas desativadas não afetam a sua pontuação segura ou geram ruídos indesejados.

Saiba mais em [Desativar descobertas específicas.](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Livros de trabalho do Monitor Azure integrados no Centro de Segurança e três modelos fornecidos

Como parte da Ignite Spring 2021, anunciamos uma experiência integrada de livros de trabalho do Azure Monitor no Security Center.

Você pode aproveitar a nova integração para começar a usar os modelos fora da caixa da galeria do Centro de Segurança. Ao utilizar modelos de livro, pode aceder e construir relatórios dinâmicos e visuais para rastrear a postura de segurança da sua organização. Além disso, pode criar novos livros com base em dados do Security Center ou em quaisquer outros tipos de dados suportados e implementar rapidamente livros comunitários da comunidade GitHub do Security Center.

São fornecidos três relatórios de modelos:

- **Pontuação Segura Ao longo do Tempo** - Acompanhe as pontuações das suas subscrições e altera as recomendações para os seus recursos
- **Atualizações do sistema** - Ver atualizações do sistema em falta por recursos, SO, gravidade e muito mais
- **Resultados da Avaliação da Vulnerabilidade** - Ver as conclusões das análises de vulnerabilidade dos seus recursos Azure

Saiba como usar estes relatórios ou construir os seus próprios [relatórios de criar relatórios ricos e interativos de dados do Security Center.](custom-dashboards-azure-workbooks.md)

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Pontuação segura ao longo do tempo":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>O painel de conformidade regulamentar inclui agora os relatórios da Auditoria Azure (pré-visualização)

A partir da barra de ferramentas do painel de conformidade regulamentar, já pode descarregar relatórios de certificação Azure e Dynamics. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Barra de ferramentas do painel de ferramentas de conformidade regulamentar":::

Pode selecionar o separador para os tipos de relatórios relevantes (PCI, SOC, ISO e outros) e utilizar filtros para encontrar os relatórios específicos de que necessita.

Saiba mais sobre [a gestão das normas no seu painel de conformidade regulamentar.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtrar a lista dos relatórios disponíveis da Auditoria Azure":::



### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Atualizações das políticas de implantação da automatização do fluxo de trabalho

Automatizar os processos de monitorização e resposta a incidentes da sua organização pode melhorar consideravelmente o tempo necessário para investigar e mitigar incidentes de segurança.

Fornecemos três políticas de política Azure 'DeployIfNotExist' que criam e configuram procedimentos de automatização de fluxos de trabalho para que possa implementar as suas automatizações em toda a sua organização:

|Objetivo  |Política  |ID de política  |
|---------|---------|---------|
|Automatização do fluxo de trabalho para alertas de segurança|[Implementar a Automatização do Fluxo de Trabalho para os alertas do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatização do fluxo de trabalho para recomendações de segurança|[Implementar a Automatização do Fluxo de Trabalho para as recomendações do Centro de Segurança do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automatização do fluxo de trabalho para alterações de conformidade regulamentar|[Implementar automatização de fluxo de trabalho para conformidade regulamentar do Centro de Segurança Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Existem duas atualizações para as características destas políticas:

- Quando atribuídos, permanecerão habilitados pela aplicação da lei.
- Agora pode personalizar estas políticas e atualizar qualquer um dos parâmetros mesmo depois de já terem sido implementados. Por exemplo, se um utilizador quiser adicionar outra chave de avaliação, ou editar uma chave de avaliação existente, pode fazê-lo.

Começa com [modelos de automatização de fluxos de trabalho.](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)

Saiba mais sobre como [automatizar respostas aos gatilhos do Centro de Segurança.](workflow-automation.md)



## <a name="february-2021"></a>Fevereiro de 2021

As atualizações em fevereiro incluem:

- [Nova página de alertas de segurança no portal Azure lançado para Disponibilidade Geral (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Recomendações de proteção da carga de trabalho da Kubernetes lançadas para disponibilidade geral (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [O Microsoft Defender para a integração de Endpoint com o Azure Defender suporta agora o Windows Server 2019 e o Windows 10 Virtual Desktop (WVD) (na pré-visualização)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Ligação direta à política a partir da página de detalhes da recomendação](#direct-link-to-policy-from-recommendation-details-page)
- [A recomendação de classificação de dados SQL já não afeta a sua pontuação segura](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [As automatizações de fluxos de trabalho podem ser desencadeadas por alterações às avaliações de conformidade regulamentar (na pré-visualização)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Melhorias da página de inventário de ativos](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Nova página de alertas de segurança no portal Azure lançado para Disponibilidade Geral (GA)

A página de alertas de segurança do Azure Security Center foi redesenhada para fornecer:

- **Melhor experiência de triagem para alertas** - ajudando a reduzir a fadiga dos alertas e focando-se nas ameaças mais relevantes mais fácil, a lista inclui filtros personalizáveis e opções de agrupamento.
- **Mais informações na lista de alertas** - como táticas MITRE ATT&ACK.
- **Botão para criar alertas de amostra -** para avaliar as capacidades do Azure Defender e testar os seus alertas. configuração (para integração de SIEM, notificações de e-mail e automatizações de fluxo de trabalho), pode criar alertas de amostra de todos os planos do Azure Defender.
- **Alinhamento com a experiência de incidente de Azure Sentinel** - para clientes que usam ambos os produtos, alternar entre eles é agora uma experiência mais simples e é fácil aprender um com o outro.
- **Melhor desempenho** para grandes listas de alertas.
- **Navegação de teclado** através da lista de alerta.
- **Alertas do Azure Resource Graph** - pode consultar alertas no Azure Resource Graph, a API semelhante a Kusto para todos os seus recursos. Isto também é útil se estiver a construir os seus próprios painéis de alerta. [Saiba mais sobre o Azure Resource Graph](../governance/resource-graph/index.yml).
- **Criar funcionalidade de alertas** de amostra - Para criar alertas de amostra a partir da experiência dos novos alertas, consulte [os alertas do Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts)da amostra .

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de segurança do Azure Security Center":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Recomendações de proteção da carga de trabalho da Kubernetes lançadas para disponibilidade geral (GA)

Temos o prazer de anunciar a Disponibilidade Geral (GA) do conjunto de recomendações para proteções de carga de trabalho kubernetes.

Para garantir que as cargas de trabalho de Kubernetes estão seguras por defeito, o Security Center adicionou recomendações de endurecimento de nível de Kubernetes, incluindo opções de aplicação com o controlo de admissão de Kubernetes.

Quando o addon Azure Policy for Kubernetes for instalado no seu cluster Azure Kubernetes Service (AKS), todos os pedidos para o servidor API de Kubernetes serão monitorizados contra o conjunto de boas práticas predefinido - apresentado como 13 recomendações de segurança - antes de ser persistido no cluster. Em seguida, pode configurar para impor as melhores práticas e mandatá-las para futuras cargas de trabalho.

Por exemplo, pode ordenar que os contentores privilegiados não sejam criados, e quaisquer pedidos futuros para o fazer serão bloqueados.

Saiba mais sobre [as melhores práticas de proteção da carga de trabalho utilizando o controlo de admissão de Kubernetes.](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)

> [!NOTE]
> Embora as recomendações estivessem em pré-visualização, não tornaram um recurso de cluster AKS insalubre, e não foram incluídos nos cálculos da sua pontuação segura. com este anúncio de GA estes serão incluídos no cálculo da pontuação. Se ainda não os remedia, isto pode resultar num ligeiro impacto na sua pontuação segura. Remedia-as sempre que possível, conforme descrito nas [recomendações do Remediate no Centro de Segurança Azure](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>O Microsoft Defender para a integração de Endpoint com o Azure Defender suporta agora o Windows Server 2019 e o Windows 10 Virtual Desktop (WVD) (na pré-visualização)

O Microsoft Defender for Endpoint é uma solução holística de segurança de ponto final. Fornece gestão e avaliação de vulnerabilidade baseadas em risco, bem como deteção e resposta de pontos finais (EDR). Para obter uma lista completa dos benefícios da utilização do Defender para Endpoint juntamente com o Azure Security Center, consulte [Protect your endpoints with Security Center's integrated EDR solution: Microsoft Defender for Endpoint](security-center-wdatp.md).

Quando ativa o Azure Defender para servidores num servidor windows, está incluída uma licença para Defender para Endpoint. Se já ativou o Azure Defender para servidores e tem servidores Windows 2019 na sua subscrição, receberá automaticamente o Defender para Endpoint com esta atualização. Não é necessária qualquer ação manual. 

O suporte foi agora expandido para incluir o Windows Server 2019 e [o Windows Virtual Desktop (WVD)](../virtual-desktop/overview.md).

> [!NOTE]
> Se estiver a ativar o Defender para o Ponto Final numa máquina do Windows Server 2019, certifique-se de que cumpre os requisitos descritos em [Ativar o Microsoft Defender para integração endpoint](security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration).

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Ligação direta à política a partir da página de detalhes da recomendação

Quando se está a rever os detalhes de uma recomendação, muitas vezes é útil ser capaz de ver a política subjacente. Para cada recomendação apoiada por uma política, há um novo link da página de detalhes da recomendação:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link para a página política do Azure para a política específica que apoia uma recomendação":::

Utilize este link para ver a definição de política e rever a lógica de avaliação. 

Se estiver a rever a lista de recomendações no nosso guia de referência de recomendações de [Segurança,](recommendations-reference.md)também verá ligações às páginas de definição de políticas:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Aceder à página política do Azure para uma política específica diretamente a partir da página de referência do Centro de Segurança Azure" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>A recomendação de classificação de dados SQL já não afeta a sua pontuação segura
A recomendação **Os dados sensíveis nas suas bases de dados SQL devem ser classificados** já não afetam a sua pontuação segura. Esta é a única recomendação no controlo de segurança de classificação de **dados da Apply,** de modo que o controlo tem agora um valor de pontuação seguro de 0.

Para obter uma lista completa de todos os controlos de segurança no Centro de Segurança, juntamente com as suas pontuações e uma lista das recomendações em cada um, consulte [os controlos de segurança e as suas recomendações.](secure-score-security-controls.md#security-controls-and-their-recommendations)

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>As automatizações de fluxos de trabalho podem ser desencadeadas por alterações às avaliações de conformidade regulamentar (na pré-visualização)
Adicionámos um terceiro tipo de dados às opções de gatilho para as suas automatizações de fluxo de trabalho: alterações às avaliações de conformidade regulamentar.

Saiba como utilizar as ferramentas de automatização do fluxo de trabalho em [respostas automatizar para acionar o Centro de Segurança](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Utilização de alterações nas avaliações de conformidade regulamentar para desencadear uma automatização do fluxo de trabalho" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Melhorias da página de inventário de ativos
A página de inventário de ativos do Security Center foi melhorada das seguintes formas:

- Os resumos no topo da página agora incluem **subscrições não registadas,** mostrando o número de subscrições sem o Centro de Segurança ativado.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Contagem de subscrições não registadas nos resumos no topo da página de inventário de ativos":::

- Os filtros foram expandidos e melhorados para incluir:
    - **Conta** - Cada filtro apresenta o número de recursos que satisfazem os critérios de cada categoria

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Conta nos filtros na página de inventário de ativos do Azure Security Center":::

    - **Contém filtro de isenções** (Opcional) - reduza os resultados a recursos que têm/não têm isenções. Este filtro não é mostrado por predefinição, mas está acessível a partir do botão **de filtro Adicionar.**

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Adicionar o filtro 'contém isenção' na página de inventário de ativos do Azure Security Center":::

Saiba mais sobre como [explorar e gerir os seus recursos com o inventário de ativos.](asset-inventory.md)

## <a name="january-2021"></a>Janeiro de 2021

As atualizações em janeiro incluem:

- [A Azure Security Benchmark é agora a iniciativa política padrão para o Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [A avaliação da vulnerabilidade para máquinas no local e multi-nuvens é lançada para disponibilidade geral (GA)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Pontuação segura para grupos de gestão já está disponível na pré-visualização](#secure-score-for-management-groups-is-now-available-in-preview)
- [A API de pontuação segura é lançada para Disponibilidade Geral (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Proteções DNS pendentes adicionadas ao Azure Defender para o Serviço de Aplicações](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Os conectores multi-nuvem são lançados para disponibilidade geral (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Isentar as recomendações inteiras da sua pontuação segura para subscrições e grupos de gestão](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Os utilizadores podem agora solicitar visibilidade ao arrendatário do seu administrador global](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Exportação de CSV de lista filtrada de recomendações](#csv-export-of-filtered-list-of-recommendations)
- [Recursos "não aplicáveis" agora reportados como "conformes" nas avaliações da Política Azure](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportar instantâneos semanais de pontuação segura e dados de conformidade regulamentar com exportação contínua (pré-visualização)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>A Azure Security Benchmark é agora a iniciativa política padrão para o Azure Security Center

A azure Security Benchmark é o conjunto de diretrizes específicas da Microsoft para a segurança e conformidade das melhores práticas com base em quadros comuns de conformidade. Este referencial amplamente respeitado baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do Instituto Nacional de [Normalização e Tecnologia (NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

Nos últimos meses, a lista de recomendações de segurança incorporadas do Centro de Segurança tem crescido significativamente para expandir a nossa cobertura deste benchmark.

A partir desta versão, o benchmark é a base para as recomendações do Centro de Segurança e totalmente integrado como a iniciativa política padrão. 

Todos os serviços da Azure têm uma página de base de segurança na sua documentação. Por exemplo, [esta é a linha de base do Centro de Segurança.](security-baseline.md) Estas linhas de base são construídas no Azure Security Benchmark.

Se estiver a utilizar o painel de conformidade regulamentar do Security Center, verá duas instâncias do benchmark durante um período de transição:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Painel de conformidade regulamentar do Azure Security Center mostrando o Benchmark de Segurança Azure":::

As recomendações existentes não são afetadas e à medida que o benchmark cresce, as mudanças serão automaticamente refletidas no Centro de Segurança. 

Para saber mais, consulte as seguintes páginas:

- [Saiba mais sobre a Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Personalize o conjunto de padrões no seu painel de conformidade regulamentar](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>A avaliação da vulnerabilidade para máquinas no local e multi-nuvens é lançada para disponibilidade geral (GA)

Em outubro, anunciou uma pré-visualização para digitalizar servidores Azure Arc habilitados com [Azure Defender para](defender-for-servers-introduction.md)o scanner integrado de avaliação de vulnerabilidade dos servidores (alimentado por Qualys).

Agora é lançado para Disponibilidade Geral (GA).

Quando tiver ativado o Azure Arc nas suas máquinas não-Azure, o Security Center oferecer-se-á para implementar o scanner integrado de vulnerabilidade neles - manual e à escala.

Com esta atualização, pode libertar o poder do **Azure Defender para os servidores** consolidarem o seu programa de gestão de vulnerabilidades em todos os seus ativos Azure e não-Azure.

Principais capacidades:

- Monitorização do estado de provisionamento do scanner VA (avaliação de vulnerabilidade) nas máquinas Azure Arc
- Provisionando o agente VA integrado às máquinas de Windows e Arú azul desprotegidas (manual e à escala)
- Receber e analisar vulnerabilidades detetadas de agentes implantados (manualmente e à escala)
- Experiência unificada para VMs Azure e máquinas Azure Arc

[Saiba mais sobre a implementação do scanner de vulnerabilidade integrado nas suas máquinas híbridas.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Saiba mais sobre os servidores ativados do Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Pontuação segura para grupos de gestão já está disponível na pré-visualização

A página de pontuação segura mostra agora as pontuações garantidas agregadas para os seus grupos de gestão, além do nível de subscrição. Agora pode ver a lista de grupos de gestão na sua organização e a pontuação para cada grupo de gestão.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Visualizar as pontuações seguras para os seus grupos de gestão.":::

Saiba mais sobre [os controlos de pontuação e segurança seguros no Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>A API de pontuação segura é lançada para Disponibilidade Geral (GA)

Agora pode aceder à sua pontuação através da [pontuação segura API](/rest/api/securitycenter/securescores/). Os métodos API fornecem a flexibilidade para consultar os dados e construir o seu próprio mecanismo de reporte das suas pontuações seguras ao longo do tempo. Por exemplo:

- usar a API **de Pontuações Seguras** para obter a pontuação para uma subscrição específica
- utilize a API **de Controlos de Pontuação Segura** para listar os controlos de segurança e a pontuação atual das suas subscrições

Conheça as ferramentas externas possíveis com a pontuação segura API na [área de pontuação segura da nossa comunidade GitHub.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)

Saiba mais sobre [os controlos de pontuação e segurança seguros no Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Proteções DNS pendentes adicionadas ao Azure Defender para o Serviço de Aplicações

As aquisições de subdomínios são uma ameaça comum e de alta gravidade para as organizações. Uma aquisição de subdomínio pode ocorrer quando tiver um registo DNS que aponta para um web site desprovisionado. Estes registos DNS também são conhecidos como entradas "dNS pendentes". Os registos da CNAME são especialmente vulneráveis a esta ameaça. 

As aquisições de subdomínio permitem que os atores de ameaça redirecionem o tráfego destinado ao domínio de uma organização para um site que executa atividades maliciosas.

O Azure Defender for App Service deteta agora entradas de DNS pendentes quando um website do Serviço de Aplicações é desativado. Este é o momento em que a entrada do DNS está apontando para um recurso inexistente, e o seu site é vulnerável a uma aquisição de subdomínio. Estas proteções estão disponíveis se os seus domínios são geridos com DNS Azure ou um registrador de domínio externo e se aplica tanto ao Serviço de Aplicações no Windows como no Serviço de Aplicações no Linux.

Saiba mais:

- [Tabela de referência de alerta do Serviço de Aplicações](alerts-reference.md#alerts-azureappserv) - Inclui dois novos alertas do Azure Defender que disparam quando é detetada uma entrada de DNS pendente
- [Evite entradas pendentes de DNS e evite a aquisição de subdomínio](../security/fundamentals/subdomain-takeover.md) - Saiba mais sobre a ameaça de aquisição de subdomínio e o aspeto pendente do DNS
- [Introdução ao Azure Defender para o Serviço de Aplicações](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Os conectores multi-nuvem são lançados para disponibilidade geral (GA)

Com cargas de trabalho em nuvem geralmente abrangendo várias plataformas de nuvem, os serviços de segurança na nuvem devem fazer o mesmo.

O Azure Security Center protege cargas de trabalho em Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

A ligação das suas contas AWS ou GCP integra as suas ferramentas de segurança nativas, como o AWS Security Hub e o GCP Security Command Center no Azure Security Center.

Esta capacidade significa que o Centro de Segurança proporciona visibilidade e proteção em todos os principais ambientes de nuvem. Alguns dos benefícios desta integração:

- Provisionamento automático de agentes - O Centro de Segurança utiliza o Azure Arc para implantar o agente Log Analytics nas suas instâncias AWS
- Gestão de políticas
- Gestão de vulnerabilidades
- Deteção e resposta de ponto final incorporados (EDR)
- Deteção de erros de segurança
- Uma única visão mostrando recomendações de segurança de todos os fornecedores de nuvem
- Incorpore todos os seus recursos nos cálculos de pontuação segura do Centro de Segurança
- Avaliações regulamentares de conformidade dos seus recursos AWS e GCP

A partir do menu do Security Center, selecione **conectores multi nuvem** e verá as opções para criar novos conectores:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Adicione o botão de conta AWS na página de conectores multi nuvem do Security Center":::

Saiba mais em:
- [Ligue as suas contas AWS ao Azure Security Center](quickstart-onboard-aws.md)
- [Ligue as suas contas GCP ao Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Isentar as recomendações inteiras da sua pontuação segura para subscrições e grupos de gestão

Estamos a expandir a capacidade de isenção para incluir recomendações inteiras. Fornecendo mais opções para afinar as recomendações de segurança que o Security Center faz para as suas subscrições, grupo de gestão ou recursos.

Ocasionalmente, um recurso será listado como insalubre quando sabe que o problema foi resolvido por uma ferramenta de terceiros que o Security Center não detetou. Ou uma recomendação mostrará num âmbito onde se sente que não pertence. A recomendação pode ser inadequada para uma subscrição específica. Ou talvez a sua organização tenha decidido aceitar os riscos relacionados com o recurso ou recomendação específico.

Com esta funcionalidade de pré-visualização, pode agora criar uma isenção para uma recomendação para:

- **Isentar um recurso** para garantir que não está listado com os recursos insalubres no futuro, e não afeta a sua pontuação segura. O recurso será listado como não aplicável e a razão será mostrada como "isenta" com a justificação específica que seleciona.

- **Isentar um grupo de subscrição ou gestão** para garantir que a recomendação não tenha impacto na sua pontuação segura e não será mostrada para o grupo de subscrição ou gestão no futuro. Isto diz respeito aos recursos existentes e a qualquer um que crie no futuro. A recomendação será marcada com a justificação específica que seleciona para o âmbito que selecionou.

Saiba mais sobre [a isenção de recursos e recomendações da sua pontuação segura](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Os utilizadores podem agora solicitar visibilidade ao arrendatário do seu administrador global

Se um utilizador não tiver permissões para ver os dados do Security Center, irá agora ver um link para solicitar permissões ao administrador global da sua organização. O pedido inclui o papel que gostariam e a justificação para o porquê de ser necessário.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner informando um utilizador que pode solicitar permissões em todo o inquilino.":::

Saiba mais em [Permissões de inquilinos quando as suas são insuficientes.](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure

A Azure Security Benchmark é a iniciativa política padrão no Centro de Segurança Azure. 

Para aumentar a cobertura deste benchmark, foram adicionadas ao Centro de Segurança as seguintes 35 recomendações de pré-visualização.

> [!TIP]
> As recomendações de pré-visualização não tornam um recurso insalubre, e não estão incluídas nos cálculos da sua pontuação segura. Remedia-os sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação. Saiba mais sobre como responder a estas recomendações em [recomendações remedias no Azure Security Center](security-center-remediate-recommendations.md).

| Controlo de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ativar a encriptação em repouso            | - As contas DB da Azure Cosmos devem usar chaves geridas pelo cliente para encriptar dados em repouso<br>- Os espaços de trabalho de aprendizagem automática Azure devem ser encriptados com uma chave gerida pelo cliente (CMK)<br>- Traga a sua própria proteção de dados chave deve ser ativada para servidores MySQL<br>- Traga a sua própria proteção de dados chave deve ser ativada para servidores PostgreSQL<br>- As contas de Serviços Cognitivos devem permitir a encriptação de dados com uma chave gerida pelo cliente (CMK)<br>- Os registos de contentores devem ser encriptados com uma chave gerida pelo cliente (CMK)<br>- As instâncias geridas pela SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso<br>- Os servidores SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso<br>- As contas de armazenamento devem utilizar a chave gerida pelo cliente (CMK) para encriptação                                                                                                                                                              |
| Implementar as melhores práticas de segurança    | - As subscrições devem ter um endereço de e-mail de contacto para questões de segurança<br> - O provisionamento automático do agente Log Analytics deve ser ativado na sua subscrição<br> - A notificação por e-mail para alertas de alta gravidade deve ser ativada<br> - A notificação por e-mail ao proprietário da subscrição para alertas de alta gravidade deve ser ativada<br> - Os cofres-chave devem ter proteção de purga ativada<br> - As abóbadas-chave devem ter uma eliminação suave ativada |
| Gerir acessos e permissões        | - As aplicações de função devem ter "Certificados de Cliente (certificados de cliente incoming)" habilitados |
| Proteja as aplicações contra ataques de DDoS | - Firewall de aplicação web (WAF) deve ser ativado para Gateway de aplicação<br> - Firewall de aplicação web (WAF) deve ser ativado para o serviço de serviço de porta frontal Azure |
| Restringir o acesso não autorizado à rede | - Firewall deve ser ativado no Cofre de Chaves<br> - O ponto final privado deve ser configurado para o Cofre de Chaves<br> - A Configuração da Aplicação deve usar link privado<br> - Azure Cache for Redis deve residir numa rede virtual<br> - Os domínios da Grelha de Eventos Azure devem utilizar link privado<br> - Os tópicos da Grelha de Eventos Azure devem usar link privado<br> - Espaços de aprendizagem de máquinas Azure devem utilizar link privado<br> - O Serviço Azure SignalR deve utilizar link privado<br> - A azure Spring Cloud deve usar injeção de rede<br> - Os registos de contentores não devem permitir o acesso ilimitado à rede<br> - Os registos de contentores devem utilizar ligações privadas<br> - O acesso à rede pública deve ser desativado para servidores MariaDB<br> - O acesso à rede pública deve ser desativado para servidores MySQL<br> - O acesso à rede pública deve ser desativado para servidores PostgreSQL<br> - A conta de armazenamento deve utilizar uma ligação de ligação privada<br> - As contas de armazenamento devem restringir o acesso à rede utilizando regras de rede virtuais<br> - Modelos de construtores de imagem VM devem usar link privado|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre a Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre a Azure Database for MariaDB](../mariadb/overview.md)
- [Saiba mais sobre a Base de Dados Azure para o MySQL](../mysql/overview.md)
- [Saiba mais sobre a Base de Dados Azure para PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Exportação de CSV de lista filtrada de recomendações 

Em novembro de 2020, adicionámos filtros à página de recomendações[(a lista de recomendações inclui agora filtros).](#recommendations-list-now-includes-filters) Em dezembro, expandimos esses filtros[(a página de recomendações tem novos filtros para o ambiente, gravidade e respostas disponíveis).](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses) 

Com este anúncio, estamos a alterar o comportamento do botão **Download para CSV para** que a exportação de CSV apenas inclua as recomendações atualmente apresentadas na lista filtrada. 

Por exemplo, na imagem abaixo pode ver que a lista foi filtrada a duas recomendações. O ficheiro CSV que é gerado inclui os detalhes de estado de cada recurso afetado por essas duas recomendações.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Exportação de recomendações filtradas para um ficheiro CSV":::

Saiba mais em [Recomendações de Segurança no Centro de Segurança Azure.](security-center-recommendations.md)


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Recursos "não aplicáveis" agora reportados como "conformes" nas avaliações da Política Azure

Anteriormente, os recursos que foram avaliados para uma recomendação e que se verificou não serem **aplicáveis** apareceram na Política Azure como "incompatíveis". Nenhuma ação do utilizador poderia mudar o seu estado para "Compliant". Com esta mudança, são reportados como "conformes" para uma maior clareza.

O único impacto será visto na Política Azure, onde o número de recursos em conformidade aumentará. Não haverá impacto na sua pontuação segura no Centro de Segurança Azure.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportar instantâneos semanais de pontuação segura e dados de conformidade regulamentar com exportação contínua (pré-visualização)

Adicionámos uma nova funcionalidade de pré-visualização às ferramentas [de exportação contínuas](continuous-export.md) para exportar instantâneos semanais de pontuação segura e dados de conformidade regulamentar.

Quando definir uma exportação contínua, defina a frequência de exportação:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Escolhendo a frequência da sua exportação contínua":::

- **Streaming** – as avaliações serão enviadas em tempo real quando o estado de saúde de um recurso for atualizado (se não ocorrerem atualizações, não serão enviados dados).
- **Snapshots** – uma imagem do estado atual de todas as avaliações de conformidade regulamentar será enviada todas as semanas (esta é uma funcionalidade de pré-visualização para instantâneos semanais de pontuações seguras e dados de conformidade regulamentar).

Saiba mais sobre as capacidades completas desta funcionalidade nos dados do Centro de [Segurança de exportação contínua.](continuous-export.md)

## <a name="december-2020"></a>Dezembro de 2020

As atualizações em dezembro incluem:

- [O Azure Defender para servidores SQL em máquinas está geralmente disponível](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender para suporte SQL para Azure Synapse Analytics piscina 2 SQL dedicada está geralmente disponível](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Os Administradores Globais podem agora conceder-se permissões ao nível do inquilino](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Dois novos planos do Azure Defender: Azure Defender para DNS e Azure Defender para Gestor de Recursos (na pré-visualização)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Nova página de alertas de segurança no portal Azure (pré-visualização)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Experiência revitalizada do Centro de Segurança na Base de Dados Azure SQL & SQL Gestd Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Ferramentas e filtros de inventário de ativos atualizados](#asset-inventory-tools-and-filters-updated)
- [Recomendação sobre aplicações web que solicitam certificados SSL já não fazem parte da pontuação segura](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [A página de recomendações tem novos filtros para o ambiente, gravidade e respostas disponíveis](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Exportação contínua recebe novos tipos de dados e políticas implementadas melhoradas](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>O Azure Defender para servidores SQL em máquinas está geralmente disponível

O Azure Security Center oferece dois planos Azure Defender para servidores SQL:

- **Azure Defender para servidores de base de dados Azure SQL** - defende os seus Servidores SQL nativos do Azure 
- **Azure Defender para servidores SQL em máquinas** - alarga as mesmas proteções aos seus servidores SQL em ambientes híbridos, multicloud e no local

Com este anúncio, **o Azure Defender para a SQL** protege agora as suas bases de dados e os seus dados onde quer que estejam localizados.

O Azure Defender para SQL inclui capacidades de avaliação de vulnerabilidades. A ferramenta de avaliação de vulnerabilidades inclui as seguintes funcionalidades avançadas:

- **Configuração de base** (Nova!) para refinar inteligentemente os resultados de análises de vulnerabilidade para aqueles que podem representar problemas reais de segurança. Depois de estabelecer o seu estado de segurança de base, a ferramenta de avaliação de vulnerabilidade apenas reporta desvios desse estado de base. Os resultados que correspondem à linha de base são considerados como passando os exames subsequentes. Isto permite que você e os seus analistas concentrem a sua atenção onde importa.
- **Informações detalhadas de referência** para ajudá-lo a *entender* as descobertas descobertas, e por que se relacionam com os seus recursos.
- **Scripts de remediação** para ajudá-lo a mitigar riscos identificados.

Saiba mais sobre [o Azure Defender para a SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender para suporte SQL para Azure Synapse Analytics piscina 2 SQL dedicada está geralmente disponível

Azure Synapse Analytics (anteriormente SQL DW) é um serviço de análise que combina armazenamento de dados empresariais e análise de big data. Piscinas SQL dedicadas são as características de armazenamento de dados da empresa da Azure Synapse. Saiba mais em [O que é Azure Synapse Analytics (anteriormente SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

O Azure Defender for SQL protege as suas piscinas SQL dedicadas com:

- **Proteção avançada de ameaças** para detetar ameaças e ataques 
- **Capacidades de avaliação de vulnerabilidades** para identificar e remediar as configurações erradas de segurança

O Azure Defender para o suporte da SQL para as piscinas SQL Azure Synapse Analytics é automaticamente adicionado ao pacote de bases de dados Azure SQL no Azure Security Center. Encontrará um novo separador "Azure Defender for SQL" na sua página de espaço de trabalho Synapse no portal Azure.

Saiba mais sobre [o Azure Defender para a SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Os Administradores Globais podem agora conceder-se permissões ao nível do inquilino

Um utilizador com o papel **de** Administrador Ativo Azure pode ter responsabilidades em todo o inquilino, mas não tem as permissões do Azure para visualizar essa informação em toda a organização no Azure Security Center. 

Para atribuir permissão ao nível do inquilino, siga as instruções em [permissões de concessão de inquilinos para si mesmo.](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself)


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Dois novos planos do Azure Defender: Azure Defender para DNS e Azure Defender para Gestor de Recursos (na pré-visualização)

Adicionámos duas novas capacidades de proteção contra ameaças nativas da nuvem para o seu ambiente Azure.

Estas novas proteções aumentam consideravelmente a sua resiliência contra ataques de atores ameaças, e aumentam significativamente o número de recursos Azure protegidos pelo Azure Defender.

- **Azure Defender for Resource Manager** - monitoriza automaticamente todas as operações de gestão de recursos realizadas na sua organização. Para obter mais informações, consulte:
    - [Introdução ao Azure Defender para Gestor de Recursos](defender-for-resource-manager-introduction.md)
    - [Responder a alertas do Azure Defender para Resource Manager](defender-for-resource-manager-usage.md)
    - [Lista de alertas fornecidos pelo Azure Defender para Gestor de Recursos](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender for DNS** - monitoriza continuamente todas as consultas dns a partir dos seus recursos Azure. Para obter mais informações, consulte:
    - [Introdução ao Azure Defender para DNS](defender-for-dns-introduction.md)
    - [Responder a alertas do Azure Defender para DNS](defender-for-dns-usage.md)
    - [Lista de alertas fornecidos pelo Azure Defender para DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Nova página de alertas de segurança no portal Azure (pré-visualização)

A página de alertas de segurança do Azure Security Center foi redesenhada para fornecer:

- **Melhor experiência de triagem para alertas** - ajudando a reduzir a fadiga dos alertas e focando-se nas ameaças mais relevantes mais fácil, a lista inclui filtros personalizáveis e opções de agrupamento
- **Mais informações na lista de alertas** - como táticas MITRE ATT&ACK
- **Botão para criar alertas de amostra** - para avaliar as capacidades do Azure Defender e testar a configuração dos seus alertas (para integração do SIEM, notificações de email e automatizações de fluxos de trabalho), pode criar alertas de amostras de todos os planos do Azure Defender
- **Alinhamento com a experiência de incidente de Azure Sentinel** - para clientes que usam ambos os produtos, alternar entre eles é agora uma experiência mais simples e é fácil aprender um com o outro
- **Melhor desempenho** para grandes listas de alertas
- **Navegação de teclado** através da lista de alerta
- **Alertas do Azure Resource Graph** - pode consultar alertas no Azure Resource Graph, a API semelhante a Kusto para todos os seus recursos. Isto também é útil se estiver a construir os seus próprios painéis de alerta. [Saiba mais sobre o Azure Resource Graph](../governance/resource-graph/index.yml).

Para aceder à nova experiência, utilize o link 'experimente agora' a partir do banner no topo da página de alertas de segurança.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banner com ligação à nova experiência de alertas de pré-visualização":::

Para criar alertas de amostra a partir da experiência de novos alertas, consulte [os alertas do Azure Defender](security-center-alert-validation.md#generate-sample-azure-defender-alerts)da amostra.


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Experiência revitalizada do Centro de Segurança na Base de Dados Azure SQL & SQL Gestd Instance 

A experiência do Centro de Segurança dentro do SQL proporciona acesso ao seguinte Centro de Segurança e ao Azure Defender para funcionalidades SQL:

- **Recomendações de segurança** – O Centro de Segurança analisa periodicamente o estado de segurança de todos os recursos ligados da Azure para identificar potenciais erros de segurança. Em seguida, fornece recomendações sobre como remediar essas vulnerabilidades e melhorar a postura de segurança das organizações.
- **Alertas de segurança** – um serviço de deteção que monitoriza continuamente as atividades do Azure SQL para ameaças como injeção de SQL, ataques de força bruta e abuso de privilégios. Este serviço desencadeia alertas de segurança detalhados e orientados para a ação no Security Center e fornece opções para continuar as investigações com a Azure Sentinel, a solução SIEM nativa da Microsoft.
- **Resultados** – um serviço de avaliação de vulnerabilidades que monitoriza continuamente as configurações do Azure SQL e ajuda a remediar vulnerabilidades. As análises de avaliação fornecem uma visão geral dos estados de segurança do Azure SQL, juntamente com as conclusões detalhadas de segurança.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="As funcionalidades de segurança do Azure Security Center para o SQL estão disponíveis dentro do Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Ferramentas e filtros de inventário de ativos atualizados

A página de inventário no Centro de Segurança Azure foi atualizada com as seguintes alterações:

- **Guias e feedback** adicionados à barra de ferramentas. Isto abre um painel com ligações a informações e ferramentas relacionadas. 
- **Filtro de subscrições** adicionado aos filtros predefinidos disponíveis para os seus recursos.
- **Abra a ligação de consulta** para abrir as opções de filtro atual como uma consulta de gráfico de recurso Azure (anteriormente chamada "Ver no explorador de gráficos de recursos").
- **Opções de operador** para cada filtro. Agora pode escolher entre operadores mais lógicos que não '='. Por exemplo, é melhor encontrar todos os recursos com recomendações ativas cujos títulos incluem a cadeia 'encriptar'. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Controlos para a opção do operador nos filtros do inventário de ativos":::

Saiba mais sobre o inventário em [Explore e gerencie os seus recursos com o inventário de ativos.](asset-inventory.md)


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Recomendação sobre aplicações web que solicitam certificados SSL já não fazem parte da pontuação segura

A recomendação "Aplicações Web devem solicitar um certificado SSL para todos os pedidos de entrada" foi transferida do controlo de segurança Gerir o **acesso e permissões** (no máximo 4 pts) para **implementar as melhores práticas** de segurança (o que não vale pontos). 

Garantir que uma aplicação web solicita um certificado certamente torna-o mais seguro. No entanto, para aplicações web viradas para o público é irrelevante. Se aceder ao seu site em HTTP e não HTTPS, não receberá nenhum certificado de cliente. Assim, se a sua candidatura necessitar de certificados de cliente, não deverá permitir pedidos à sua candidatura em HTTP. Saiba mais na [autenticação mútua Configure TLS para o Serviço de Aplicações Azure.](../app-service/app-service-web-configure-tls-mutual-auth.md)

Com esta mudança, a recomendação é agora uma melhor prática recomendada que não afeta a sua pontuação. 

Saiba quais as recomendações em cada controlo de segurança nos [controlos de segurança e nas suas recomendações](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>A página de recomendações tem novos filtros para o ambiente, gravidade e respostas disponíveis

O Azure Security Center monitoriza todos os recursos conectados e gera recomendações de segurança. Use estas recomendações para fortalecer a sua postura híbrida em nuvem e acompanhar o cumprimento das políticas e padrões relevantes para a sua organização, indústria e país.

À medida que o Security Center continua a expandir a sua cobertura e funcionalidades, a lista de recomendações de segurança está a crescer todos os meses. Por exemplo, consulte [29 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Com a lista de crescentes, há necessidade de filtrar as recomendações para encontrar as que têm maior interesse. Em novembro, adicionámos filtros à página de recomendações (ver [lista de recomendações agora inclui filtros).](#recommendations-list-now-includes-filters)

Os filtros adicionados este mês oferecem opções para aperfeiçoar a lista de recomendações de acordo com:

- **Ambiente** - Ver recomendações para os seus recursos AWS, GCP ou Azure (ou qualquer combinação)
- **Severidade** - Ver recomendações de acordo com a classificação de gravidade definida pelo Centro de Segurança
- **Ações de resposta** - Ver recomendações de acordo com a disponibilidade de opções de resposta do Centro de Segurança: Rápida correção, Negar e Impor

    > [!TIP]
    > O filtro de ações de resposta substitui o filtro **Quick Fix disponível (Sim/Não).** 
    > 
    > Saiba mais sobre cada uma destas opções de resposta:
    > - [Reparação rápida](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Evitar configurações incorretas com recomendações Impor/Negar](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Recomendações agrupadas pelo controlo de segurança" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Exportação contínua recebe novos tipos de dados e políticas implementadas melhoradas

As ferramentas de exportação contínuas do Azure Security Center permitem exportar as recomendações e alertas do Centro de Segurança para utilização com outras ferramentas de monitorização no seu ambiente.

A exportação contínua permite-lhe personalizar totalmente o que será exportado e para onde irá. Para obter todos os detalhes, consulte [os dados do Centro de Segurança de exportação contínua.](continuous-export.md)

Estas ferramentas foram melhoradas e expandidas das seguintes formas:

- **As políticas de implantação contínua das exportações reforçaram-se**. As políticas agora:

    - **Verifique se a configuração está ativada.** Se não for, a política mostrará como incompatível e criará um recurso conforme. Saiba mais sobre os modelos de Política Azure fornecidos no "Implementar à escala com separador Azure Policy" em [Configurar uma exportação contínua](continuous-export.md#set-up-a-continuous-export).

    - **Apoiar a exportação de conclusões de segurança.** Ao utilizar os modelos de Política Azure, pode configurar a sua exportação contínua para incluir as descobertas. Isto é relevante na exportação de recomendações que tenham recomendações de 'sub', como resultados de scanners de avaliação de vulnerabilidades ou atualizações específicas do sistema para a recomendação "As atualizações do sistema devem ser instaladas nas suas máquinas".
    
    - **Apoiar a exportação de dados de pontuação segura.**

- **Dados de avaliação da conformidade regulamentar adicionados (na pré-visualização).** Agora pode exportar continuamente atualizações para avaliações de conformidade regulamentar, incluindo para quaisquer iniciativas personalizadas, para um espaço de trabalho log Analytics ou Centro de Eventos. Esta funcionalidade não está disponível nas nuvens nacionais/soberanas.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="As opções para incluir informações de avaliação regulamentares com os seus dados de exportação contínua.":::


## <a name="november-2020"></a>Novembro de 2020

As atualizações em novembro incluem:

- [29 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 adicionado ao painel de conformidade regulamentar do Centro de Segurança](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Lista de recomendações agora inclui filtros](#recommendations-list-now-includes-filters)
- [Experiência de provisionamento automático melhorada e expandida](#auto-provisioning-experience-improved-and-expanded)
- [A pontuação segura está agora disponível em exportação contínua (pré-visualização)](#secure-score-is-now-available-in-continuous-export-preview)
- [Recomendação "Atualizações do sistema devem ser instaladas nas suas máquinas" agora inclui subrecomendas](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Página de gestão de políticas no portal Azure mostra agora o estado das atribuições de política padrão](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 recomendações de pré-visualização adicionadas para aumentar a cobertura do Benchmark de Segurança Azure

A azure Security Benchmark é o conjunto de diretrizes de segurança e conformidade da Microsoft, com base em quadros comuns de conformidade. [Saiba mais sobre a Referência de Segurança do Azure](../security/benchmarks/introduction.md).

As seguintes 29 recomendações de pré-visualização foram adicionadas ao Centro de Segurança para aumentar a cobertura deste benchmark.

As recomendações de pré-visualização não tornam um recurso insalubre, e não estão incluídas nos cálculos da sua pontuação segura. Remedia-os sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação. Saiba mais sobre como responder a estas recomendações em [recomendações remedias no Azure Security Center](security-center-remediate-recommendations.md).

| Controlo de segurança                     | Novas recomendações                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Encriptar dados em trânsito              | - A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL<br>- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL<br>- O TLS deve ser atualizado para a versão mais recente da sua aplicação API<br>- O TLS deve ser atualizado para a versão mais recente da sua aplicação de função<br>- O TLS deve ser atualizado para a versão mais recente da sua aplicação web<br>- FTPS deve ser exigido na sua App API<br>- FTPS deve ser exigido na sua app de função<br>- FTPS deve ser exigido na sua Web App                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Gerir acessos e permissões        | - As aplicações web devem solicitar um certificado SSL para todos os pedidos de entrada<br>- Identidade gerida deve ser usada na sua App API<br>- Identidade gerida deve ser usada na sua função App<br>- Identidade gerida deve ser usada na sua web App                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Restringir o acesso não autorizado à rede | - O ponto final privado deve ser ativado para servidores PostgreSQL<br>- O ponto final privado deve ser ativado para servidores MariaDB<br>- O ponto final privado deve ser ativado para servidores MySQL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Permitir a auditoria e a exploração madeireira          | - Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementar as melhores práticas de segurança    | - A azure Backup deve ser ativado para máquinas virtuais<br>- Cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para a MariaDB<br>- A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para o MySQL<br>- A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para postgreSQL<br>- PHP deve ser atualizado para a versão mais recente para a sua aplicação API<br>- PHP deve ser atualizado para a versão mais recente para a sua aplicação web<br>- A Java deve ser atualizada para a versão mais recente da sua aplicação API<br>- A Java deve ser atualizada para a versão mais recente da sua aplicação de função<br>- Java deve ser atualizado para a versão mais recente para a sua aplicação web<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação API<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação de função<br>- Python deve ser atualizado para a versão mais recente para a sua aplicação web<br>- A retenção de auditoria para servidores SQL deve ser definida para pelo menos 90 dias |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Links relacionados:

- [Saiba mais sobre a Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Saiba mais sobre as aplicações AZURE API](../app-service/app-service-web-tutorial-rest-api.md)
- [Saiba mais sobre aplicações de função Azure](../azure-functions/functions-overview.md)
- [Saiba mais sobre as aplicações web Azure](../app-service/overview.md)
- [Saiba mais sobre a Azure Database for MariaDB](../mariadb/overview.md)
- [Saiba mais sobre a Base de Dados Azure para o MySQL](../mysql/overview.md)
- [Saiba mais sobre a Base de Dados Azure para PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 adicionado ao painel de conformidade regulamentar do Centro de Segurança

A norma NIST SP 800-171 R2 está agora disponível como uma iniciativa integrada para uso com o painel de conformidade regulamentar do Azure Security Center. Os mapeamentos para os controlos são descritos em [Detalhes da iniciativa integrada NIST SP 800-171 R2 Conformidade Regulamentar.](../governance/policy/samples/nist-sp-800-171-r2.md) 

Para aplicar a norma às suas subscrições e monitorizar continuamente o seu estado de conformidade, utilize as instruções em [Personalizar o conjunto de normas no seu painel de conformidade regulamentar](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="A norma NIST SP 800 171 R2 no painel de conformidade regulamentar do Centro de Segurança":::

Para obter mais informações sobre esta norma de conformidade, consulte [nIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Lista de recomendações agora inclui filtros

Pode agora filtrar a lista de recomendações de segurança de acordo com uma série de critérios. No exemplo seguinte, a lista de recomendações foi filtrada para mostrar recomendações que:

- estão **geralmente disponíveis** (isto é, não pré-visualização)
- são para **contas de armazenamento**
- apoiar a **reparação rápida da correção**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtros para a lista de recomendações":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Experiência de provisionamento automático melhorada e expandida

A funcionalidade de provisionamento automático ajuda a reduzir a sobrecarga de gestão instalando as extensões necessárias em novos VMs - e existentes - para que possam beneficiar das proteções do Centro de Segurança. 

À medida que o Azure Security Center cresce, mais extensões foram desenvolvidas e o Centro de Segurança pode monitorizar uma lista maior de tipos de recursos. As ferramentas de fornecimento automático foram agora expandidas para apoiar outras extensões e tipos de recursos, aproveitando as capacidades da Política Azure.

Pode agora configurar o provisionamento automático de:

- Agente do Log Analytics
- (Novo) Azure Policy Add-on para Kubernetes
- (Novo) Agente de dependência da Microsoft

Saiba mais em [agentes de provisionamento automático e extensões do Azure Security Center](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>A pontuação segura está agora disponível em exportação contínua (pré-visualização)

Com a exportação contínua de pontuação segura, pode transmitir alterações à sua pontuação em tempo real para Azure Event Hubs ou um espaço de trabalho Log Analytics. Utilize esta capacidade para:

- rastrear a sua pontuação segura ao longo do tempo com relatórios dinâmicos
- exportar dados de pontuação segura para Azure Sentinel (ou qualquer outro SIEM)
- integrar estes dados com quaisquer processos que já possa estar a usar para monitorizar pontuação segura na sua organização

Saiba mais sobre como [exportar continuamente os dados do Security Center.](continuous-export.md)


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>Recomendação "Atualizações do sistema devem ser instaladas nas suas máquinas" agora inclui subrecomendas

As **atualizações do Sistema devem ser instaladas na** recomendação das suas máquinas. A nova versão inclui sub-nomeações para cada atualização em falta e traz as seguintes melhorias:

- Uma experiência redesenhada nas páginas do Centro de Segurança Azure do portal Azure. A página de detalhes da recomendação para **atualizações do Sistema deve ser instalada nas suas máquinas** inclui a lista de resultados, tal como mostrado abaixo. Ao selecionar uma única descoberta, o painel de detalhes abre-se com um link para a informação de remediação e uma lista de recursos afetados.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Abertura de uma das subrecomendas na experiência do portal para a recomendação atualizada":::

- Dados enriquecidos para a recomendação do Azure Resource Graph (ARG). A ARG é um serviço Azure projetado para fornecer uma exploração eficiente de recursos. Você pode usar ARG para consultar em escala através de um determinado conjunto de subscrições para que você possa efetivamente governar o seu ambiente. 

    Para o Azure Security Center, pode utilizar o ARG e a [Língua De Consulta de Kusto (KQL)](/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança.

    Anteriormente, se questionou esta recomendação em ARG, a única informação disponível era que a recomendação precisa de ser remediada numa máquina. A seguinte consulta da versão melhorada irá devolver cada atualização do sistema em falta agrupadas por máquina.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Página de gestão de políticas no portal Azure mostra agora o estado das atribuições de política padrão

Pode agora ver se as suas subscrições têm ou não a política padrão do Centro de Segurança atribuída, na página de política de segurança do Portal Azure do Centro de **Segurança.**

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="A página de gestão de políticas do Azure Security Center mostrando as atribuições de política padrão":::

## <a name="october-2020"></a>Outubro de 2020

As atualizações em outubro incluem:
- [Avaliação de vulnerabilidade para máquinas no local e multi-nuvens (pré-visualização)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Recomendação do Azure Firewall adicionada (pré-visualização)](#azure-firewall-recommendation-added-preview)
- [As gamas IP autorizadas devem ser definidas na recomendação dos Serviços Kubernetes atualizada com uma correção rápida](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Painel de conformidade regulamentar agora inclui opção para remover normas](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Microsoft.Security/securityStatuses removido do Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Avaliação de vulnerabilidade para máquinas no local e multi-nuvens (pré-visualização)

[O Azure Defender para](defender-for-servers-introduction.md)o scanner integrado de avaliação de vulnerabilidades dos servidores (alimentado por Qualys) agora verifica servidores ativados a Azure Arc.

Quando tiver ativado o Azure Arc nas suas máquinas não-Azure, o Security Center oferecer-se-á para implementar o scanner integrado de vulnerabilidade neles - manual e à escala.

Com esta atualização, pode libertar o poder do **Azure Defender para os servidores** consolidarem o seu programa de gestão de vulnerabilidades em todos os seus ativos Azure e não-Azure.

Principais capacidades:

- Monitorização do estado de provisionamento do scanner VA (avaliação de vulnerabilidade) nas máquinas Azure Arc
- Provisionando o agente VA integrado às máquinas de Windows e Arú azul desprotegidas (manual e à escala)
- Receber e analisar vulnerabilidades detetadas de agentes implantados (manualmente e à escala)
- Experiência unificada para VMs Azure e máquinas Azure Arc

[Saiba mais sobre a implementação do scanner de vulnerabilidade integrado nas suas máquinas híbridas.](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

[Saiba mais sobre os servidores ativados do Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Recomendação do Azure Firewall adicionada (pré-visualização)

Foi adicionada uma nova recomendação para proteger todas as suas redes virtuais com a Azure Firewall.

A recomendação, **as redes virtuais devem ser protegidas pelo Azure Firewall,** aconselha-o a restringir o acesso às suas redes virtuais e a prevenir potenciais ameaças utilizando o Azure Firewall.

Saiba mais sobre [a Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>As gamas IP autorizadas devem ser definidas na recomendação dos Serviços Kubernetes atualizada com uma correção rápida

A recomendação **De que os intervalos de IP autorizados sejam definidos nos Serviços Kubernetes** tem agora uma opção de correção rápida.

Para obter mais informações sobre esta recomendação e todas as outras recomendações do Centro de Segurança, consulte [recomendações de Segurança - um guia de referência](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="As gamas IP autorizadas devem ser definidas na recomendação dos Serviços Kubernetes com a opção de correção rápida":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Painel de conformidade regulamentar agora inclui opção para remover normas

O painel de conformidade regulamentar do Security Center fornece informações sobre a sua postura de conformidade com base na forma como está a cumprir controlos e requisitos específicos de conformidade.

O painel inclui um conjunto padrão de normas regulamentares. Se algum dos padrões fornecidos não for relevante para a sua organização, é agora um processo simples para removê-los da UI para uma subscrição. As normas só podem ser removidas ao nível da *subscrição;* não o âmbito do grupo de gestão.

Saiba mais em [Remover um padrão do seu painel de instrumentos](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft.Security/securityStatuses removido do Azure Resource Graph (ARG)

O Azure Resource Graph é um serviço em Azure que é projetado para fornecer uma exploração eficiente de recursos com a capacidade de consultar em escala através de um determinado conjunto de subscrições para que possa governar eficazmente o seu ambiente. 

Para o Azure Security Center, pode utilizar o ARG e a [Língua De Consulta de Kusto (KQL)](/azure/data-explorer/kusto/query/) para consultar uma ampla gama de dados de postura de segurança. Por exemplo:

- Utilizações de inventário de ativos (ARG)
- Documentamos uma consulta de amostra ARG sobre como [identificar contas sem autenticação de vários fatores (MFA) ativada](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

Dentro da ARG, existem tabelas de dados para utilizar nas suas consultas.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer e as tabelas disponíveis":::

> [!TIP]
> A documentação ARG lista todas as tabelas disponíveis na [tabela Azure Resource Graph e referência do tipo de recurso.](../governance/resource-graph/reference/supported-tables-resources.md)

A partir desta atualização, a tabela **Microsoft.Security/securityStatuses** foi removida. A API de segurançastatuses ainda está disponível.

A substituição de dados pode ser utilizada pela tabela Microsoft.Security/Assessments.

A grande diferença entre microsoft.Security/securityStatuses e Microsoft.Security/Assessments é que, embora a primeira mostre agregação de avaliações, os segundos detenha um único recorde para cada um.

Por exemplo, microsoft.Security/securityStatuses devolveria um resultado com uma série de duas avaliações de política:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Considerando que a Microsoft.Security/Assessments manterá um registo para cada avaliação de política da seguinte forma:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Exemplo de conversão de uma consulta ARG existente utilizando segurançaSés para utilizar agora o quadro de avaliações:**

Consulta que refere Índices de Segurança:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Consulta de substituição do quadro de avaliações:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Saiba mais nos seguintes links:
- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)
- [Linguagem de Consulta Kusto (KQL)](/azure/data-explorer/kusto/query/)