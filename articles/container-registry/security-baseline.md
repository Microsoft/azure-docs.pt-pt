---
title: Linha de base de segurança Azure para o registo de contentores
description: A linha de base de segurança do registo de contentores fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: container-registry
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3b3303ae04f9300025c3c42fc63abe8b2aa46a83
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563723"
---
# <a name="azure-security-baseline-for-container-registry"></a>Linha de base de segurança Azure para o registo de contentores

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) ao Registo de Contentores. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Registo de Contentores. Foram excluídos **os controlos** não aplicáveis ao registo de contentores.

 
Para ver como o registo de contentores mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança do registo do contentor completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A Rede Virtual Azure fornece redes seguras e privadas para os seus recursos Azure e no local. Ao limitar o acesso ao seu registo privado de contentores Azure a partir de uma rede virtual Azure, garante que apenas os recursos na rede virtual acedem ao registo. Para cenários de premissas cruzadas, também pode configurar regras de firewall para permitir o acesso ao registo apenas a partir de endereços IP específicos.
Por detrás de uma firewall, configure as regras de acesso à firewall e as etiquetas de serviço para aceder ao registo do seu contentor.

- [Restringir o acesso a um registo de contentores Azure utilizando uma rede virtual Azure ou regras de firewall](container-registry-vnet.md) 

- [Configure regras para aceder a um registo de contentores Azure atrás de uma firewall](container-registry-firewall-access-rules.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Política Azure - Microsoft.ContainerRegistry**:

[!INCLUDE [Resource Policy for Microsoft.ContainerRegistry 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerregistry-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Utilize o Centro de Segurança Azure e remedia as recomendações de proteção da rede para ajudar a proteger os seus recursos de rede em Azure. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Proteja os seus recursos de rede](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative a proteção DDoS Standard nas suas redes virtuais para proteções contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados. Implemente o Azure Firewall em cada um dos limites de rede da organização com a Threat Intelligence habilitada e configurada a "alertar e negar" para tráfego de rede malicioso.

Pode utilizar o Centro de Segurança Azure Apenas no Tempo O acesso à Rede de Configuração de NSGs para limitar a exposição dos pontos finais a endereços IP aprovados por um período limitado.
Além disso, utilize o Hardening de Rede Adaptável do Centro de Segurança Azure para recomendar configurações NSG que limitem portas e IPs de origem com base na inteligência real de tráfego e ameaça.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)
- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)
- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)
- [Compreender o Hardenive de Rede Adaptável do Centro de Segurança Azure](../security-center/security-center-adaptive-network-hardening.md)
- [Centro de Segurança Azure Apenas no Tempo Controlo de Acesso à Rede](../security-center/security-center-just-in-time.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Ative os registos de fluxo do grupo de segurança da rede (NSG) para o NSG ligado à sub-rede que está a ser utilizada para proteger o registo do seu contentor Azure. Pode gravar os registos de fluxo NSG numa Conta de Armazenamento Azure para gerar registos de fluxo. Se necessário para investigar atividades anómalas, ative a captura de pacotes do Azure Network Watcher.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil. Se a deteção e/ou prevenção de intrusões baseada na inspeção de carga útil não for um requisito, a Azure Firewall com Threat Intelligence pode ser usada. A filtragem baseada em inteligência Azure Firewall Threat pode alertar e negar tráfego de e para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

Implemente a solução de firewall à sua escolha em cada um dos limites de rede da sua organização para detetar e/ou negar tráfego malicioso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para os recursos que necessitem de acesso ao seu registo de contentores, utilize etiquetas de serviço de rede virtuais para o serviço de registo de contentores Azure para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome de marca de serviço "AzureContainerRegistry" no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Permitir o acesso por etiqueta de serviço](./container-registry-firewall-access-rules.md#allow-access-by-service-tag)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para os recursos de rede associados aos seus registos de contentores Azure com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.ContainerRegistry" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus registos de contentores. 

Pode utilizar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de gestor de recursos Azure, controlos rbac azure e políticas, numa única definição de planta. Aplique facilmente o projeto a novas subscrições e ajuste o controlo e a gestão através da versão.

- [Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure](container-registry-azure-policy.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: O cliente pode utilizar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlos e políticas do Azure RBAC, numa única definição de planta. Aplique facilmente o projeto a novas subscrições e ajuste o controlo e a gestão através da versão.

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações dos recursos de rede e detetar alterações nos recursos de rede relacionados com os registos dos seus contentores. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados por um registo de contentores Azure. Dentro do Azure Monitor, utilize o Log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Registo de registo de contentores Azure para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: O Azure Monitor recolhe registos de recursos (anteriormente chamados registos de diagnóstico) para eventos orientados pelo utilizador no seu registo. Recolher e consumir estes dados para auditar eventos de autenticação de registo e fornecer um rasto completo de atividade em artefactos de registo, tais como pull e push eventos para que você possa diagnosticar problemas de segurança com o seu registo.

- [Registo de registo de contentores Azure para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável. O benchmark destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desa um ponto final no período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Como definir parâmetros de retenção de registos para log analytics workspaces](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize os registos do registo do contentor Azure para o comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo.

- [Registo de registo de contentores Azure para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)

- [Compreender log analytics workspace](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o espaço de trabalho Azure Log Analytics para monitorizar e alertar sobre atividades anómalas em registos de segurança e eventos relacionados com o seu registo de contentores Azure.

- [Registo de registo de contentores Azure para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável. O Registo do Contentor Azure não processa nem produz registos relacionados com anti-malware.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável. O Registo do Contentor Azure é um ponto final e não inicia a comunicação, e o serviço não consulta o DNS.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável. O benchmark destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Para cada registo de contentores Azure, localize se a conta de administração incorporada está ativada ou desativada. Desative a conta quando não estiver a ser utilizada.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Conta de administração do Registo de Contentores de Azure](./container-registry-authentication.md#admin-account)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O Azure Ative Directory (Azure AD) não tem o conceito de palavras-passe padrão. Outros recursos Azure que requerem uma palavra-passe forçam uma senha a ser criada com requisitos de complexidade e um comprimento mínimo de senha, que diferem consoante o serviço. É responsável por aplicações de terceiros e serviços do Marketplace que podem utilizar senhas padrão.

Se a conta de administração predefinida de um registo de contentores Azure estiver ativada, as palavras-passe complexas são automaticamente criadas e devem ser rotativas. Desative a conta quando não estiver a ser utilizada.

- [Conta de administração do Registo de Contentores de Azure](./container-registry-authentication.md#admin-account)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, crie procedimentos para permitir a conta de administração incorporada de um registo de contentores. Desative a conta quando não estiver a ser utilizada.

- [Compreender a identidade e o acesso do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

- [Conta de administração do Registo de Contentores de Azure](./container-registry-authentication.md#admin-account)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Sempre que possível, utilize o Azure Ative Directory (Azure AD) SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

Para acesso individual ao registo do contentor, utilize login individual integrado com Azure AD.

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Login individual para um registo de contentores](./container-registry-authentication.md#admin-account)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com autenticação multifactor configurada para iniciar sessão e configurar recursos Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários de acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a Azure Ative Directory (Azure AD) Fontes de registo de registo de eventos de auditoria e risco, que lhe permitem integrar-se com qualquer ferramenta de Gestão de Informações de Segurança e Gestão de Eventos (SIEM) /Ferramenta de monitorização.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize funcionalidades de Proteção de Risco e Identidade Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores. 

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não disponível; O Bloqueio do Cliente não é atualmente suportado para o Registo do Contentor Azure.

- [Lista de serviços suportados pelo Bloqueio do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas de recursos para ajudar a rastrear os registos de contentores Azure que armazenam ou processam informações sensíveis.

Etiquetar e ver imagens de contentores ou outros artefactos num registo, e bloquear imagens ou repositórios, para ajudar no rastreio de imagens que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Recomendações para a marcação e versão das imagens dos contentores](container-registry-image-tag-version.md)

- [Bloqueie uma imagem de contentor num registo de contentores Azure](container-registry-image-lock.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar registos separados de contentores, subscrições e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos que armazenam ou processam dados sensíveis devem ser suficientemente isolados.

Os recursos devem ser separados por rede virtual ou sub-rede, marcados adequadamente e protegidos por um grupo de segurança de rede (NSG) ou Azure Firewall.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Restringir o acesso a um registo de contentores Azure utilizando uma rede virtual Azure ou regras de firewall](container-registry-vnet.md)

- [Como criar um NSG com um config de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alerta ou alerta e negar com a Azure Firewall](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Implemente uma ferramenta automatizada em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie tais transferências enquanto alerta os profissionais de segurança da informação.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Certifique-se de que qualquer cliente que se conecte ao seu Registo de Contentores Azure seja capaz de negociar TLS 1.2 ou superior. Os recursos da Microsoft Azure negoceiam TLS 1.2 por padrão.

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, sempre que aplicável.

- [Compreender a encriptação em trânsito com Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Registo do Contentor de Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso a dados e recursos num registo de contentores Azure. 

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Funções e permissões do Registo de Contentores de Azure](container-registry-roles.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Se necessário para o cumprimento dos recursos computativos, implemente uma ferramenta de terceiros, como uma solução automatizada de prevenção de perda de dados baseada em hospedeiros, para impor controlos de acesso aos dados mesmo quando os dados são copiados de um sistema.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Utilize a encriptação em repouso em todos os recursos Azure. Por predefinição, todos os dados de um registo de contentores Azure são encriptados em repouso utilizando as teclas geridas pela Microsoft.

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md)

- [Chaves geridas pelo cliente no Registo do Contentor Azure](./container-registry-customer-managed-keys.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: O Azure Monitor recolhe registos de recursos (anteriormente chamados registos de diagnóstico) para eventos orientados pelo utilizador no seu registo. Recolher e consumir estes dados para auditar eventos de autenticação de registo e fornecer um rasto completo de atividade em artefactos de registo, tais como pull e pull eventos para que você possa diagnosticar problemas operacionais com o seu registo.

- [Registo de registo de contentores Azure para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nas imagens do seu contentor. Opcionalmente implementar soluções de terceiros a partir do Azure Marketplace para realizar avaliações de vulnerabilidade de imagem.

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

- [Integração do Azure Container Registry no Centro de Segurança (Pré-visualização)](../security-center/defender-for-container-registries-introduction.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: A Microsoft realiza a gestão de patchs nos sistemas subjacentes que suportam o Registo do Contentor Azure.

Automatizar as atualizações de imagem do contentor quando forem detetadas atualizações para basear imagens do sistema operativo e outras correções.

- [Sobre as atualizações de imagem base para as tarefas do Registo de Contentores Azure](container-registry-tasks-base-images.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Pode utilizar a solução de terceiros para corrigir imagens de aplicações.  Além disso, pode executar tarefas de Registo de Contentores Azure para automatizar atualizações para imagens de aplicação num registo de contentores com base em patches de segurança ou outras atualizações em imagens base.

- [Sobre atualizações de imagem base para tarefas ACR](container-registry-tasks-base-images.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Integrar o Registo de Contentores Azure (ACR) com o Azure Security Center para permitir a verificação periódica de imagens de contentores para vulnerabilidades. Implementar opcionalmente soluções de terceiros a partir do Azure Marketplace para realizar análises periódicas de vulnerabilidade de imagem.

- [Integração do Azure Container Registry no Centro de Segurança (Pré-visualização)](../security-center/defender-for-container-registries-introduction.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Integrar o Registo de Contentores Azure (ACR) com o Azure Security Center para permitir a digitalização periódica de imagens de contentores para vulnerabilidades e classificar riscos. Implementar opcionalmente soluções de terceiros do Azure Marketplace para realizar análises periódicas de vulnerabilidade de imagem e classificação de risco.

- [Integração do Azure Container Registry no Centro de Segurança (Pré-visualização)](../security-center/defender-for-container-registries-introduction.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: O Registo do Contentor Azure mantém metadados, incluindo tags e manifestos para imagens num registo. Siga as práticas recomendadas para a marcação de artefactos.

- [Sobre registos, repositórios e imagens](container-registry-concepts.md)

- [Recomendações para a marcação e versão das imagens dos contentores](container-registry-image-tag-version.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: O Registo do Contentor Azure mantém metadados, incluindo tags e manifestos para imagens num registo. Siga as práticas recomendadas para a marcação de artefactos.

- [Sobre registos, repositórios e imagens](container-registry-concepts.md)

- [Recomendações para a marcação e versão das imagens dos contentores](container-registry-image-tag-version.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Terá de criar um inventário dos recursos Azure aprovados de acordo com as suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure](container-registry-azure-policy.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Analise e monitorize os registos do registo do contentor Azure para o comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo.

- [Registo de registo de contentores Azure para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)

- [Compreender log analytics workspace](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: A Azure Automation proporciona controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos. Pode implementar a sua própria solução para remover recursos Azure não autorizados.

- [Uma introdução à Automatização do Azure](../automation/automation-intro.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável. O benchmark é projetado para recursos compute.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Alavancar a Política Azure para restringir os serviços que pode prestação no seu ambiente.

- [Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure](container-registry-azure-policy.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Não aplicável. O benchmark é projetado para recursos compute.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos recursos de computação Azure.

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Utilize configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos recursos de computação Azure.

- [Por exemplo, como controlar a execução de scripts PowerShell em Ambientes windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&view=powershell-7)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: O software necessário para operações comerciais, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro da sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um Azure Firewall ou um Grupo de Segurança de Rede.

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um config de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize a Política Azure ou o Centro de Segurança Azure para manter configurações de segurança para todos os Recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure](container-registry-azure-policy.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Utilize a recomendação do Centro de Segurança Azure "Remediar vulnerabilidades em configurações de segurança nas suas Máquinas Virtuais" para manter configurações de segurança em todos os recursos de computação.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md)

- [Como remediar as recomendações do Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure](container-registry-azure-policy.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável. O benchmark destina-se a recursos de computação.

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize a Política Azure para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure](container-registry-azure-policy.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Centro de Segurança Azure para efetuar análises de base para os seus Recursos Azure.

Utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados na sua subscrição.

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

- [Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure](container-registry-azure-policy.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem.

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre-Chave](../key-vault/general/quick-create-portal.md)

- [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

- [Utilize uma identidade gerida pelo Azure nas tarefas do Registo de Contentores Azure](container-registry-tasks-authentication-managed-identity.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como configurar identidades geridas](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

- [Utilize uma identidade gerida para autenticar num registo de contentores Azure](container-registry-authentication-managed-identity.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação**: Utilize o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais para monitorizar e defender continuamente os seus recursos. Para o Linux, utilize a solução antimalware de terceiros.

- [Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../security/fundamentals/antimalware.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Registo de Contentores Azure), no entanto não funciona com o conteúdo do cliente.

Pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, etc.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável. O benchmark destina-se a recursos de computação. A Microsoft lida com anti-malware para a plataforma subjacente.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Os dados do seu registo de contentores Microsoft Azure são sempre replicados automaticamente para garantir a durabilidade e a elevada disponibilidade. O Registo de Contentores Azure copia os seus dados de modo a que esteja protegido de eventos planeados e não planeados

Opcionalmente, re replicar um registo de contentores para manter réplicas de registo em várias regiões de Azure. 

- [Geo-replicação no Registo do Contentor de Azure](container-registry-geo-replication.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Apoiar opcionalmente as imagens dos contentores importando de um registo para outro.

Faça o back-up das chaves geridas pelo cliente no Azure Key Vault utilizando ferramentas de linha de comando Azure ou SDKs.

- [Importar imagens de contentores para um registo de contentores](container-registry-import-images.md)

- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste a restauração de chaves geridas pelo cliente em Azure Key Vault utilizando ferramentas ou SDKs da linha de comando Azure.

- [Como restaurar as chaves do cofre da chave Azure em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Pode permitir Soft-Delete no Cofre da Chave Azure para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Como permitir Soft-Delete em Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus testes de penetração não violam as políticas da Microsoft:  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
