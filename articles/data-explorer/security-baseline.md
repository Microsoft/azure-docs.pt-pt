---
title: Linha de base de segurança azure para explorador de dados
description: Linha de base de segurança azure para explorador de dados
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289722"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Linha de base de segurança azure para explorador de dados

A Linha de Base de Segurança Azure para O Explorador de Dados contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: O Azure Data Explorer suporta a implementação de um cluster numa subrede na sua rede virtual. Esta capacidade permite-lhe impor as regras do grupo de segurança de rede (NSG) no tráfego de cluster do Azure Data Explorer, ligar a sua rede no local à subnet do cluster do Cluster Azure Data Explorer e proteger as suas fontes de ligação de dados (Event Hub e Event Grid) com pontos finais de serviço.

Como implantar o seu cluster Azure Data Explorer numa rede virtual:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICS

**Orientação**: Ativar os registos de fluxo do grupo de segurança da rede (NSG) e enviar registos numa Conta de Armazenamento para auditoria de tráfego.

Como ativar os registos de fluxo nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Compreender a Segurança da Rede fornecida pelo Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; A recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a Norma de Proteção DDoS Azure na rede virtual que protege os seus clusters de Data Explorer para proteção contra ataques DDoS. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.

Como configurar a proteção DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Compreender o Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de discos e registos de fluxo

**Orientação**: Ativar os registos de fluxo nos grupos de segurança da rede (NSG) que estão a ser utilizados para proteger o seu cluster Do Explorador de Dados Azure e enviar registos para uma Conta de Armazenamento para auditoria de tráfego.

Como ativar os registos de fluxo nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implementar sistemas de deteção/deteção de intrusões baseados em rede

**Orientação**: Não aplicável; Este controlo é feito no ponto final ou firewall.


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gerir o tráfego para as suas aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtual para definir controlos de acesso à rede em Grupos de Segurança da Rede ou firewalls Azure associados aos seus clusters do Azure Data Explorer. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Compreender e utilizar etiquetas de serviço:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Requisitos de configuração de etiquetas de serviço para o Explorador de Dados Azure:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Cliente para definir e implementar configurações de segurança padrão para recursos de rede com a Política Azure.

O cliente também pode usar as Plantas Azure para simplificar as implementações azure em larga escala, embalagendo artefactos ambientais chave, tais como modelos ARM, controlos RBAC e políticas, numa única definição de planta. Aplique facilmente o projeto a novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de Configuração do Tráfego de Documentos

**Orientação**: Utilize etiquetas para grupos de segurança de rede (NSG) e outros recursos relacionados com a segurança da rede e fluxo de tráfego para os seus clusters de Exploradores de Dados. Para regras nsg individuais, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize a Política Azure para validar (e/ou remediar) a configuração dos recursos de rede.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os recursos Do Azure, os clientes podem atualizar a sincronização do tempo para implementações de cálculo seleções detidas pelo cliente.

Como configurar a sincronização do tempo para os recursos computacionais do Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a Gestão central de registos de segurança

**Orientação**: O Azure Data Explorer utiliza registos de diagnóstico para informações sobre sucessos e falhas de ingestão. Pode exportar registos de operação para O Armazenamento, Hub de Eventos ou Log Analytics para monitorizar o estado de ingestão.

Como monitorizar as operações de ingestão do Explorador de Dados do Azure:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Como ingerir e consultar dados de monitorização no Azure Data Explorer:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos Recursos Azure

**Orientação**: Ative as definições de diagnóstico do Explorador de Dados Azure para acesso e registo a operações específicas de serviço e exploração madeireira. Os registos da Atividade Azure no Monitor Azure, que inclui a exploração madeireira de alto nível sobre o recurso, são ativados por defeito.

Como monitorizar as operações de ingestão do Explorador de Dados do Azure:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Como recolher registos e métricas da plataforma com o Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Visão geral dos registos da plataforma Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Recolher registos de segurança do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registode segurança

**Orientação**: Dentro do Monitor Azure, detete o período de retenção do espaço de trabalho de Log Analytics de acordo com as regras de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Como definir parâmetros de retenção de registos para espaços de trabalho de Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e Registos de Revisão

**Orientação**: Analise e monitorize os registos para comportamentos anómalos e reveja regularmente os resultados. Depois de ativar as definições de diagnóstico para o Azure Data Explorer, utilize o espaço de trabalho de Log Analytics do Azure Monitor para rever os registos e executar consultas em dados de registo.

Compreensão do espaço de trabalho de Análise de Log:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividade anómala

**Orientação**: Não aplicável; O Azure Data Explorer não tem esta capacidade.


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável; O Azure Data Explorer não processa a exploração anti-malware.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável: A consulta do DNS não é uma função do Azure Data Explorer.


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de registos de auditoria da linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Manter o inventário das Contas Administrativas

**Orientação**: No Azure Data Explorer, as funções de segurança definem quais os principais de segurança (utilizadores e aplicações) que têm permissões para operar com um recurso seguro, como uma base de dados ou uma tabela, e quais as operações permitidas.  Pode aproveitar a consulta de Kusto para listar princípios no papel de administrador dos clusters e bases de dados do Azure Data Explorer.
Gestão de funções de segurança no Azure Data Explorer utilizando a consulta de Kusto:https://docs.microsoft.com/azure/kusto/management/security-roles



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: A AD Azure não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma palavra-passe obrigam a criar uma palavra-passe com requisitos de complexidade e um comprimento mínimo de senha, que difere consoante o serviço. Você é responsável por aplicações de terceiros e serviços de mercado que podem usar senhas padrão.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Assegurar a utilização de Contas Administrativas Dedicadas

**Orientação**: Cliente para criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Identidade e Gestão de Acesso do Azure Security Center para monitorizar o número de contas administrativas.

Os clientes também podem ativar um Acesso Just-In-Time / Just-Enough utilizando papéis privilegiados de gestão de identidade privilegiada seletivas azure AD para serviços microsoft, e Azure ARM. 

O que é a Azure AD Privileged Identity Management?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilizar um único signo (SSO) com diretório ativo Azure

**Orientação**: Sempre que possível, o cliente utilize o SSO com o Azure Ative Directory (Azure AD) em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Identidade e Gestão de Acesso do Centro de Segurança Azure.

Compreensão de SSO com AD Azure:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados no Diretório Ativo Azure.

**Orientação**: Permitir a autenticação multifactor (Azure AD) do Diretório Ativo (Azure AD) e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Como monitorizar a identidade e o acesso dentro do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilização de Máquinas Dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as Tarefas Administrativas

**Orientação**: Utilize PAWs (estações de trabalho de acesso privilegiados) com autenticação multifactor (MFA) configurada para iniciar sessão e configurar os recursos Azure.

Saiba mais sobre postos de trabalho de acesso privilegiados:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: Registo e Alerta sobre Atividade Suspeita em Contas Administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente. Utilize o Azure Security Center para monitorizar a atividade de identidade e acesso

Como identificar utilizadores de Anúncios Azure sinalizados para atividades de risco:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Do Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gerir o recurso Azure a partir de apenas locais aprovados

**Orientação**: Cliente utilizar locais nomeados para o Acesso Condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.

Como configurar localizações nomeadas em Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-utilize-azure-active-directory"></a>3.9: Diretório Ativo Da Utilização Azure

**Orientação**: Azure Ative Directory (Azure AD) é o método preferido para autenticar o Azure Data Explorer. Suporta uma série de cenários de autenticação:

Autenticação do utilizador (logon interativo): Utilizado para autenticar os princípios humanos.

Autenticação de aplicações (logon não interativo): Utilizado para autenticar serviços e aplicações que tenham de ser executados/autenticados sem que nenhum utilizador humano apresente.

Visão geral do controlo de acesso do Explorador de Dados Azure:https://docs.microsoft.com/azure/kusto/management/access-control

Autenticação com Diretório Ativo Azure:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso do utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 

Como autenticar com a AD Azure para acesso ao Explorador de Dados Azure:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Relatórios da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como utilizar comentários de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorizar tentativas de acesso a contas desativadas

**Orientação**: Pode utilizar o Diretório Ativo Azure (Azure AD) Assinar fontes de registo de atividade, auditoria e risco para monitorização que lhe permita integrar-se com qualquer ferramenta de Informação de Segurança e Gestão de Eventos (SIEM) /Ferramenta de Monitorização.

 Pode simplificar este processo criando Definições de Diagnóstico para contas de utilizadores do Diretório Ativo Do Azure, enviando os registos de auditoria e registos de log-in para um espaço de trabalho de Log Analytics. Cliente para configurar alertas desejados dentro do espaço de trabalho de Log Analytics.

Como integrar registos de atividade do Azure no Monitor Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o Diretório Ativo Azure (Azure AD) Deteção de Riscos e Proteção de Identidade para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Além disso, pode ingerir dados no Azure Sentinel para mais investigação.

Como ver os sign-ins de risco da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Em cenários de suporte em que a Microsoft precisa de aceder aos dados dos clientes, o Customer Lockbox fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes.

Compreender o bloqueio do cliente:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.

Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os clusters do Azure Data Explorer devem ser separados de outros recursos por rede virtual/subnet, marcados adequadamente e protegidos dentro de um grupo de segurança de rede (NSG) ou Azure Firewall. Os clusters do Data Explorer que armazenam ou processam dados sensíveis devem ser suficientemente isolados.

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como implantar o seu cluster Azure Data Explorer numa rede virtual:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Como criar um NSG com um Config de Segurança:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Não aplicável; Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: O cluster Azure Data Explorer negoceia TLS 1.2 por defeito. Certifique-se de que todos os clientes que se ligam aos seus recursos Azure são capazes de negociar TLS 1.2 ou superior.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Partilhado

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis</div>

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Data Explorer. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Utilizar o Azure RBAC para controlar o acesso aos recursos</div>

**Orientação**: O Azure Data Explorer permite controlar o acesso a bases de dados e tabelas, utilizando um modelo de controlo de acesso baseado em funções (RBAC). Neste modelo, os diretores (utilizadores, grupos e aplicações) são mapeados para funções. Os diretores podem aceder aos recursos de acordo com as funções que lhes são atribuídas.

Lista de funções e permissões e instruções sobre como configurar o RBAC para o Explorador de Dados Azure:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a Prevenção da Perda de Dados baseada no hospedeiro para impor o controlo de acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

A Microsoft gere a infraestrutura subjacente ao Azure Data Explorer e implementou controlos rigorosos para evitar a perda ou exposição de dados dos clientes.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: A Encriptação do Disco Azure ajuda a proteger e salvaguardar os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. Fornece encriptação de volume para o OS e discos de dados das suas máquinas virtuais cluster. Integra-se também com o Azure Key Vault, que nos permite controlar e gerir as chaves e segredos de encriptação do disco, e garantir que todos os dados dos discos VM são encriptados em repouso enquanto estiverem no Armazenamento Azure.

Como permitir a encriptação em repouso para os clusters do Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividade seleções Do Azure para criar alertas para quando ocorrerem alterações ao nível dos recursos nos seus clusters do Azure Data Explorer.

Como criar alertas para eventos de Registo de Atividade seleções do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como criar alertas para eventos de Registo de Atividade seleções do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre a segurança dos seus recursos do Azure Data Explorer.

A Microsoft também realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam o Azure Data Explorer.

Compreender as recomendações do Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas.

**Orientação**: Utilize as classificações de risco padrão (Pontuação Segura) fornecidas pelo Azure Security Center.
Compreenda a pontuação segura do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua subscrição(s). Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Gestor de Recursos Azure para avançar.

Como criar consultas com o Gráfico de Recursos Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como visualizar as suas Assinaturas Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreender o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Pode utilizar convenções de nomeação adequadas, marcação, grupos de gestão ou subscrições separadas, se for caso disso, para organizar e rastrear ativos. Pode utilizar o Azure Resource Graph para conciliar o inventário regularmente e garantir que os recursos não autorizados são eliminados da subscrição atempadamente. 

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar consultas com o Gráfico de Recursos Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos e títulos de software aprovados do Azure.

**Orientação**: Terá de criar um inventário dos recursos azure aprovados e do software aprovado para os recursos de computação de acordo com as suas necessidades organizacionais.  


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos azure não aprovados

**Orientação**: Pode utilizar as políticas do Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição do cliente, utilizando as seguintes definições políticas incorporadas:

    - Tipos de recursos não permitidos

    - Tipos de recursos permitidos

Você será capaz de monitorizar os eventos gerados pela política usando o 

Registos de atividade que podem ser monitorizados utilizando o Monitor Azure.

Além disso, pode utilizar o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s subscrição)."

Tutorial: Criar e gerir políticas para impor o cumprimento:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Quickstart: Execute a sua primeira consulta de gráfico de recursos usando o Explorador de Gráficos de Recursos Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Criar, visualizar e gerir alertas de registo de atividade utilizando o Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos Recursos Compute

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação e ao Azure como um todo.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-utilize-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-utilize-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Pode utilizar as políticas do Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição do cliente, utilizando as seguintes definições políticas incorporadas:

    - Tipos de recursos não permitidos

    - Tipos de recursos permitidos

Tutorial: Criar e gerir políticas para impor o cumprimento:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras de política azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure configurando o "Block access" para a App "Microsoft Azure Management". Isto evitará a criação e alterações nos recursos dentro das suas subscrições do Azure. 

Gerir o acesso à gestão azure com acesso condicional:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco fisicamente ou logicamente segregadas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos Azure. Também pode utilizar definições de Política Azure incorporadas.

Além disso, o Gestor de Recursos Azure tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem/excedem os requisitos de segurança para a sua organização.

Também pode utilizar recomendações do Azure Security Center como base de configuração segura para os seus recursos Azure.

Como visualizar os aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Tutorial: Criar e gerir políticas para impor o cumprimento:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exportação única e multi-recursos para um modelo no portal Azure:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Recomendações de segurança - um guia de referência:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Estabelecer configurações seguras para o seu sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Manter configurações seguras para todos os recursos azure

**Orientação:** Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Pode utilizar soluções como O Rastreio de Alterações, Painel de Conformidade de Políticas ou uma solução personalizada para identificar facilmente alterações de segurança no seu ambiente.

Compreender os efeitos da política azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

Criar e gerir políticas para impor o cumprimento:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Acompanhe as mudanças no seu ambiente com a solução Change Tracking:https://docs.microsoft.com/azure/automation/change-tracking

Obtenha dados de conformidade dos recursos do Azure:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Manter configurações seguras para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos azure

**Orientação**: Utilize o Azure Repos para armazenar e gerir de forma segura o seu código, como políticas personalizadas do Azure, modelos do Gestor de Recursos Azure, scripts de Configuração do Estado Desejados, etc. Para aceder aos recursos que gere no Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com o Azure DevOps, ou Diretório Ativo se integrado seletiva com a TFS.

Como armazenar código em Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Sobre permissões e grupos em Azure DevOps:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Defina e implemente configurações de segurança padrão para os recursos Azure utilizando a Política Azure. Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração da rede dos seus recursos Azure. Também pode utilizar definições políticas incorporadas relacionadas com os seus recursos específicos.  Além disso, pode utilizar a Automatização Azure para implementar alterações de configuração.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como usar pseudónimos:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços do Azure

**Orientação**: Utilize pseudónimos da Política Azure para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Utilize a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente as configurações dos seus recursos Azure.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gerir de forma segura os segredos do Azure

**Orientação**: A encriptação do disco Azure fornece encriptação de volume para o OS e discos de dados das suas máquinas virtuais do cluster Azure Data Explorer. Também se integra com o Azure Key Vault que lhe permite controlar e gerir as chaves e segredos de encriptação do disco, e garantir que todos os dados dos discos VM estão encriptados em repouso enquanto estiver no Armazenamento Azure.

Como proteger o seu cluster no Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gerir de forma segura e automática as identidades

**Orientação**: Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente em Azure AD. Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem qualquer credencial no seu código. Como configurar identidades geridas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Configure as identidades geridas para o seu cluster Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 

Como configurar o Scanner Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, o Azure Data Explorer), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, O Azure Data Explorer), no entanto não funciona com conteúdo do cliente.

Pré-digitalização de qualquer conteúdo que seja enviado para recursos não computacionais do Azure, tais como Azure Data Explorer, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, etc. A Microsoft não pode aceder aos seus dados nestes casos.



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços do Azure, no entanto não funciona com conteúdo de clientes.

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Os dados na sua conta de armazenamento Microsoft Azure utilizados pelo seu cluster Data Explorer são sempre replicados para garantir durabilidade e elevada disponibilidade. O Azure Storage copia os seus dados para que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas na rede ou falhas de energia e desastres naturais maciços. Pode optar por replicar os seus dados dentro do mesmo centro de dados, através de centros de dados zonais dentro da mesma região, ou em regiões geograficamente separadas.

Compreensão do despedimento de armazenamento azure e acordos de nível de serviço:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Dados de exportação para armazenamento:https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e backup de quaisquer chaves geridas pelo cliente

**Orientação**: O Azure Data Explorer encripta todos os dados numa conta de armazenamento em repouso. Por padrão, os dados são encriptados com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, pode fornecer chaves geridas pelo cliente para usar para encriptação de dados. As chaves geridas pelo cliente devem ser armazenadas num Cofre de Chaves Azure. 

Configure as chaves geridas pelo cliente utilizando C#https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Como fazer backup dos certificados do Cofre de Chaves Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste periodicamente a restauração dos dados dos seus segredos do Cofre chave Azure.

Como restaurar os certificados do Cofre de Chaves Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Configure as chaves geridas pelo cliente utilizando C#https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Cliente para ativar soft-Delete no cofre de chaves para proteger as chaves contra a eliminação acidental ou maliciosa.  Também pode ativar a proteção da purga para que, se um cofre ou um objeto no estado apagado, não possa ser purgado até que o período de retenção tenha passado.  

Como ativar a proteção Soft-Delete e Purga no Cofre-Chave:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Configure as chaves geridas pelo cliente utilizando C#https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configure as chaves geridas pelo cliente utilizando o modelo do Gestor de Recursos Azure:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-incident-response-guide"></a>10.1: Criar guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Criar procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Fornecer detalhes de contacto com &nbsp;incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.
    

Como definir o Contacto de Segurança do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exportar os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua para ajudar a identificar riscos para os recursos do Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade workflow Automation no Azure Security Center para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as descobertas críticas de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1que os seus Testes de Penetração não violam as políticas da Microsoft: .

Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra a Microsoft gerida por infraestruturas, serviços e aplicações em nuvem, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
