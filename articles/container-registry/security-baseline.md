---
title: Linha de Base de Segurança Azure para registo de contentores Azure
description: Linha de Base de Segurança Azure para registo de contentores Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ccb287a63cf372ff78c750a4690188de7ccc59e6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807942"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Linha de Base de Segurança Azure para registo de contentores Azure

A Linha de Base de Segurança Azure para o Registo de Contentores Azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, consulte [Controlo de Segurança: Segurança da Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: A Rede Virtual Azure fornece redes seguras e privadas para os seus recursos Azure e no local. Ao limitar o acesso ao seu registo privado de contentores Azure a partir de uma rede virtual Azure, garante que apenas os recursos na rede virtual acedem ao registo. Para cenários de premissas cruzadas, também pode configurar regras de firewall para permitir o acesso ao registo apenas a partir de endereços IP específicos. Por detrás de uma firewall, configure as regras de acesso à firewall e as etiquetas de serviço para aceder ao registo do seu contentor.

Restringir o acesso a um registo de contentores Azure utilizando uma rede virtual Azure ou regras de firewall: https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Configure as regras de acesso a um registo de contentores Azure atrás de uma firewall: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação**: Utilize o Centro de Segurança Azure e remedia as recomendações de proteção da rede para ajudar a proteger os seus recursos de rede em Azure. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego.

Como ativar os registos de fluxo NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Proteja os seus recursos de rede: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável. O benchmark destina-se ao Azure App Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative a proteção DDoS Standard nas suas redes virtuais para proteções contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.  Implemente o Azure Firewall em cada um dos limites de rede da organização com a Threat Intelligence habilitada e configurada a "alertar e negar" para tráfego de rede malicioso.

Pode utilizar o Centro de Segurança Azure Apenas no Tempo O acesso à Rede de Configuração de NSGs para limitar a exposição dos pontos finais a endereços IP aprovados por um período limitado. Além disso, utilize o Hardening de Rede Adaptável do Centro de Segurança Azure para recomendar configurações NSG que limitem portas e IPs de origem com base na inteligência real de tráfego e ameaça.

Como configurar a proteção DDoS:  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Como implantar a Firewall Azure: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Compreenda a Inteligência Integrada de Ameaças do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Compreenda o endurecimento da rede adaptável do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Centro de Segurança Azure Just in Time Network Access Control: https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Ative os registos de fluxo do grupo de segurança da rede (NSG) para o NSG ligado à sub-rede que está a ser utilizada para proteger o registo do seu contentor Azure. Pode gravar os registos de fluxo NSG numa Conta de Armazenamento Azure para gerar registos de fluxo. Se necessário para investigar atividades anómalas, ative a captura de pacotes do Azure Network Watcher.

Como ativar os registos de fluxo NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador de Rede: https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil. Se a deteção e/ou prevenção de intrusões baseada na inspeção de carga útil não for um requisito, a Azure Firewall com Threat Intelligence pode ser usada. A filtragem baseada em inteligência Azure Firewall Threat pode alertar e negar tráfego de e para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

Implemente a solução de firewall à sua escolha em cada um dos limites de rede da sua organização para detetar e/ou negar tráfego malicioso.

Mercado Azure:  https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Como implantar a Firewall Azure: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alertas com a Azure Firewall: https://docs.microsoft.com/azure/firewall/threat-intel


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável. O benchmark destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para os recursos que necessitem de acesso ao seu registo de contentores, utilize etiquetas de serviço de rede virtuais para o serviço de registo de contentores Azure para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome de marca de serviço "AzureContainerRegistry" no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Permitir o acesso por etiqueta de serviço: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para os recursos de rede associados aos seus registos de contentores Azure com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.ContainerRegistry" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus registos de contentores. 

Pode utilizar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de gestor de recursos Azure, controlos rbac azure e políticas, numa única definição de planta. Aplique facilmente o projeto a novas subscrições e ajuste o controlo e a gestão através da versão.

Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como criar uma Planta Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: O cliente pode utilizar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlos e políticas do Azure RBAC, numa única definição de planta. Aplique facilmente o projeto a novas subscrições e ajuste o controlo e a gestão através da versão.

Como criar uma Planta Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações dos recursos de rede e detetar alterações nos recursos de rede relacionados com os registos dos seus contentores. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos de Registo de Atividades Azure:  https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Azure Monitor:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para recursos Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para os seus recursos de computação.

Como configurar a sincronização temporal para os recursos computativos Azure: https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados por um registo de contentores Azure. Dentro do Azure Monitor, utilize o Log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Registos do Registo de Contentores Azure para avaliação e auditoria de diagnóstico:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: O Azure Monitor recolhe registos de recursos (anteriormente chamados registos de diagnóstico) para eventos orientados pelo utilizador no seu registo. Recolher e consumir estes dados para auditar eventos de autenticação de registo e fornecer um rasto completo de atividade em artefactos de registo, tais como pull e push eventos para que você possa diagnosticar problemas de segurança com o seu registo.

Registos do Registo de Contentores Azure para avaliação e auditoria de diagnóstico: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável. O benchmark destina-se a recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desa um ponto final no período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Como definir parâmetros de retenção de registos para os espaços de trabalho do Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Analise e monitorize os registos do registo do contentor Azure para o comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo.

Registos do Registo de Contentores Azure para avaliação e auditoria de diagnóstico:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Compreender o espaço de trabalho do Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o espaço de trabalho Azure Log Analytics para monitorizar e alertar sobre atividades anómalas em registos de segurança e eventos relacionados com o seu registo de contentores Azure.

Registos do Registo de Contentores Azure para avaliação e auditoria de diagnóstico: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Como alertar os dados do registo de registos de registos de registos de registos:  https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável. O Registo do Contentor Azure não processa nem produz registos relacionados com anti-malware.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável. O Registo do Contentor Azure é um ponto final e não inicia a comunicação, e o serviço não consulta o DNS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável. O benchmark destina-se a recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Para cada registo de contentores Azure, localize se a conta de administração incorporada está ativada ou desativada. Desative a conta quando não estiver a ser utilizada.

Como obter um papel de diretório em Azure AD com PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório em Azure AD com PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Conta de administração do Registo de Contentores Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O Azure Ative Directory (Azure AD) não tem o conceito de palavras-passe padrão. Outros recursos Azure que requerem uma palavra-passe forçam uma senha a ser criada com requisitos de complexidade e um comprimento mínimo de senha, que diferem consoante o serviço. É responsável por aplicações de terceiros e serviços do Marketplace que podem utilizar senhas padrão.

Se a conta de administração predefinida de um registo de contentores Azure estiver ativada, as palavras-passe complexas são automaticamente criadas e devem ser rotativas. Desative a conta quando não estiver a ser utilizada.

Conta de administração do Registo de Contentores Azure: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, crie procedimentos para permitir a conta de administração incorporada de um registo de contentores. Desative a conta quando não estiver a ser utilizada.

Compreenda a identidade e o acesso do Centro de Segurança Azure:  https://docs.microsoft.com/azure/security-center/security-center-identity-access

Conta de administração do Registo de Contentores Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

Para acesso individual ao registo do contentor, utilize login individual integrado com o Azure Ative Directory.

Compreender SSO com Azure AD:  https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Sessão individual de registo de um contentor:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a autenticação multi-factor do Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity and Access Management.

Como permitir o MFA em Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com MFA configurados para iniciar sessão e configurar recursos Azure.

Saiba mais sobre estações de acesso privilegiadas:  https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Como permitir o MFA em Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitorização do Centro de Segurança Azure**: N/A

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

Como identificar utilizadores de AD Azure sinalizados para atividade de risco:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar localizações nomeadas em Azure:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Como criar e configurar um exemplo AD Azure: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Compreenda a Azure AD reportando:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como utilizar comentários de acesso à identidade do Azure:  https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Tem acesso a Azure Ative Directory (Azure AD) Fontes de registo de registo de eventos de auditoria e risco, que lhe permitem integrar-se com qualquer ferramenta de Gestão de Informações de Segurança e Gestão de Eventos (SIEM) /Ferramenta de monitorização.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores do Azure Ative Directory e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

Como integrar os Registos de Atividade do Azure no Monitor Azure:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de Proteção de Risco e Identidade Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores. 

Como ver a Azure AD a iniciar súmis arriscados: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e permitir políticas de risco de proteção de identidade: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não disponível; O Bloqueio do Cliente não é atualmente suportado para o Registo do Contentor Azure.

Lista de serviços suportados pelo Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Proteção de Dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas de recursos para ajudar a rastrear os registos de contentores Azure que armazenam ou processam informações sensíveis.

Etiquetar e ver imagens de contentores ou outros artefactos num registo, e bloquear imagens ou repositórios, para ajudar no rastreio de imagens que armazenam ou processam informações sensíveis.

Como criar e utilizar tags:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Recomendações para a marcação e a versão das imagens dos contentores:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Bloqueie uma imagem de contentor num registo de contentores Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar registos separados de contentores, subscrições e/ou grupos de gestão para desenvolvimento, teste e produção. Os recursos que armazenam ou processam dados sensíveis devem ser suficientemente isolados.

Os recursos devem ser separados por rede virtual ou sub-rede, marcados adequadamente e protegidos por um grupo de segurança de rede (NSG) ou Azure Firewall.

Como criar subscrições adicionais do Azure:  https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar grupos de gestão:  https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Restringir o acesso a um registo de contentores Azure utilizando uma rede virtual Azure ou regras de firewall: https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Como criar um NSG com um config de segurança: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Como implantar a Firewall Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Como configurar alerta ou alerta e negar com a Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Implemente uma ferramenta automatizada em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie tais transferências enquanto alerta os profissionais de segurança da informação.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Certifique-se de que qualquer cliente que se conecte ao seu Registo de Contentores Azure seja capaz de negociar TLS 1.2 ou superior. Os recursos da Microsoft Azure negoceiam TLS 1.2 por padrão.

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, sempre que aplicável.

Compreenda a encriptação em trânsito com o Azure:  https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Registo do Contentor de Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso a dados e recursos num registo de contentores Azure. 

Como configurar o Azure RBAC:  https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Funções e permissões do Registo de Contentores Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Se necessário para o cumprimento dos recursos computativos, implemente uma ferramenta de terceiros, como uma solução automatizada de prevenção de perda de dados baseada em hospedeiros, para impor controlos de acesso aos dados mesmo quando os dados são copiados de um sistema.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Utilize a encriptação em repouso em todos os recursos Azure. Por predefinição, todos os dados de um registo de contentores Azure são encriptados em repouso utilizando as teclas geridas pela Microsoft.

Compreenda a encriptação em repouso em Azure: https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Chaves geridas pelo cliente no Registo do Contentor Azure:  https://aka.ms/acr/cmk



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: O Azure Monitor recolhe registos de recursos (anteriormente chamados registos de diagnóstico) para eventos orientados pelo utilizador no seu registo. Recolher e consumir estes dados para auditar eventos de autenticação de registo e fornecer um rasto completo de atividade em artefactos de registo, tais como pull e pull eventos para que você possa diagnosticar problemas operacionais com o seu registo.

Registos do Registo de Contentores Azure para avaliação e auditoria de diagnóstico: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nas imagens do seu contentor. Opcionalmente implementar soluções de terceiros a partir do Azure Marketplace para realizar avaliações de vulnerabilidade de imagem.

Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integração do Registo de Contentores Azure com o Centro de Segurança (Pré-visualização):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: A Microsoft realiza a gestão de patchs nos sistemas subjacentes que suportam o Registo do Contentor Azure.

Automatizar as atualizações de imagem do contentor quando forem detetadas atualizações para basear imagens do sistema operativo e outras correções.

Sobre as atualizações de imagem base para as tarefas do Registo de Contentores Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: Pode utilizar a solução de terceiros para corrigir imagens de aplicações.  Além disso, pode executar tarefas de Registo de Contentores Azure para automatizar atualizações para imagens de aplicação num registo de contentores com base em patches de segurança ou outras atualizações em imagens base.

Sobre as atualizações de imagem base para tarefas ACR:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Integrar o Registo de Contentores Azure (ACR) com o Azure Security Center para permitir a verificação periódica de imagens de contentores para vulnerabilidades. Implementar opcionalmente soluções de terceiros a partir do Azure Marketplace para realizar análises periódicas de vulnerabilidade de imagem.

Integração do Registo de Contentores Azure com o Centro de Segurança (Pré-visualização):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Integrar o Registo de Contentores Azure (ACR) com o Azure Security Center para permitir a digitalização periódica de imagens de contentores para vulnerabilidades e classificar riscos. Implementar opcionalmente soluções de terceiros do Azure Marketplace para realizar análises periódicas de vulnerabilidade de imagem e classificação de risco.

Integração do Registo de Contentores Azure com o Centro de Segurança (Pré-visualização):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorização do Centro de Segurança Azure**: N/A

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s).  Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

Como criar consultas com gráfico de recursos Azure:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver as suas Subscrições Azure:  https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Compreenda Azure RBAC:  https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: O Registo do Contentor Azure mantém metadados, incluindo tags e manifestos para imagens num registo. Siga as práticas recomendadas para a marcação de artefactos.

Sobre registos, repositórios e imagens: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Recomendações para a marcação e a versão das imagens dos contentores: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: O Registo do Contentor Azure mantém metadados, incluindo tags e manifestos para imagens num registo. Siga as práticas recomendadas para a marcação de artefactos.

Sobre registos, repositórios e imagens: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Recomendações para a marcação e a versão das imagens dos contentores:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação**: Terá de criar um inventário dos recursos Azure aprovados de acordo com as suas necessidades organizacionais.  

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerir a Política de Azure:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com O Gráfico Azure:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Analise e monitorize os registos do registo do contentor Azure para o comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo.

Registos do Registo de Contentores Azure para avaliação e auditoria de diagnóstico:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Compreender o espaço de trabalho do Log Analytics:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Azure Monitor:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: A Azure Automation proporciona controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos.  Pode implementar a sua própria solução para remover recursos Azure não autorizados. Uma introdução à Azure Automation:  https://docs.microsoft.com/azure/automation/automation-intro


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável. O benchmark é projetado para recursos compute.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Alavancar a Política Azure para restringir os serviços que pode prestação no seu ambiente.

Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure:  https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável. O benchmark é projetado para recursos compute.



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azureresources através de scripts

**Orientação**: Utilize configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos recursos de computação Azure.

Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure:  https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Utilize configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos recursos de computação Azure.

Por exemplo, como controlar a execução do script PowerShell em Ambientes Windows:  https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: O software necessário para operações comerciais, mas que pode incorrer em maior risco para a organização, deve ser isolado dentro da sua própria máquina virtual e/ou rede virtual e suficientemente protegido com um Azure Firewall ou um Grupo de Segurança de Rede.

Como criar uma rede virtual:  https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com um config de segurança:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte [Controlo de Segurança: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize a Política Azure ou o Centro de Segurança Azure para manter configurações de segurança para todos os Recursos Azure.

Como configurar e gerir a Política de Azure:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Utilize a recomendação do Centro de Segurança Azure "Remediar vulnerabilidades em configurações de segurança nas suas Máquinas Virtuais" para manter configurações de segurança em todos os recursos de computação.

Como monitorizar as recomendações do Centro de Segurança Azure:  https://docs.microsoft.com/azure/security-center/security-center-recommendations

Como remediar as recomendações do Centro de Segurança Azure:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender os efeitos da Política Azure:  https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável. O benchmark destina-se a recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

Como armazenar código em Azure DevOps:  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos:  https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize a Política Azure para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Como configurar e gerir a Política de Azure:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Utilize o Centro de Segurança Azure para efetuar análises de base para os seus Recursos Azure.

Utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados na sua subscrição.

Como remediar recomendações no Centro de Segurança Azure:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Conformidade de auditoria dos registos de contentores da Azure utilizando a Política Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável. O benchmark aplica-se aos recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem.

Como integrar-se com identidades geridas aZure:  https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Como autenticar o Cofre de Chaves: https://docs.microsoft.com/azure/key-vault/general/authentication

Como atribuir uma política de acesso ao Cofre chave: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

Utilize uma identidade gerida pelo Azure nas tarefas de Registo de Contentores Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente em Azure AD. Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

Como configurar identidades geridas:  https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Utilize uma identidade gerida para autenticar um registo de contentores Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

Como configurar o Scanner Credencial:  https://secdevtools.azurewebsites.net/helpcredscan.html


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Controlo de Segurança: Defesa de Malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Utilize o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais para monitorizar e defender continuamente os seus recursos. Para o Linux, utilize a solução antimalware de terceiros.

Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais:  https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Registo de Contentores Azure), no entanto não funciona com o conteúdo do cliente.

Pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, etc.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável. O benchmark destina-se a recursos de computação. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Os dados do seu registo de contentores Microsoft Azure são sempre replicados automaticamente para garantir a durabilidade e a elevada disponibilidade. O Registo de Contentores Azure copia os seus dados de modo a que esteja protegido de eventos planeados e não planeados

Opcionalmente, re replicar um registo de contentores para manter réplicas de registo em várias regiões de Azure. 

Geo-replicação no Registo do Contentor de Azure:  https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Apoiar opcionalmente as imagens dos contentores importando de um registo para outro.

Faça o back-up das chaves geridas pelo cliente no Azure Key Vault utilizando ferramentas de linha de comando Azure ou SDKs.

Importar imagens de contentores para um registo de contentores:  https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Como fazer backup chaves do cofre em Azure:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Testar a restauração das chaves geridas pelo cliente em Azure Key Vault utilizando ferramentas ou SDKs da linha de comando Azure.

Como restaurar as chaves do cofre da chave Azure em Azure:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Pode permitir Soft-Delete no Cofre da Chave Azure para proteger as chaves contra a eliminação acidental ou maliciosa.

Como permitir Soft-Delete no Cofre de Chaves: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](../security/benchmarks/security-control-incident-response.md)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:  https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Orientação para a construção do seu próprio processo de resposta a incidentes de segurança:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes:  https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

Consulte a publicação do NIST: Guide to Test, Training e Exercise Programs for IT Plans and Capabilities:  https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

Como definir o contacto de segurança do Centro de Segurança Azure:  https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

Como configurar a exportação contínua:  https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para Azure Sentinel:  https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a automatização do fluxo de trabalho e as aplicações lógicas:  https://docs.microsoft.com/azure/security-center/workflow-automation


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte [Controlo de Segurança: Testes de penetração e exercícios da equipa vermelha.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft em nuvem, aqui:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre [as Linhas de Base de Segurança Azure](../security/benchmarks/security-baselines-overview.md)
