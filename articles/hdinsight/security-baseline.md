---
title: Linha de base de segurança azure para HDInsight
description: Linha de base de segurança azure para HDInsight
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: eddfcacd01a67fffa8e3e992e021ed1771d25944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471341"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Linha de base de segurança azure para HDInsight

A Linha de Base de Segurança Azure para HDInsight contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para estes serviços é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: A segurança do perímetro no Azure HDInsight é alcançada através de redes virtuais. Um administrador da empresa pode criar um cluster dentro de uma rede virtual e usar um grupo de segurança de rede (NSG) para restringir o acesso à rede virtual. Apenas os endereços IP permitidos nas regras do Grupo de Segurança da Rede de Entrada poderão comunicar com o cluster Azure HDInsight. Esta configuração proporciona segurança no perímetro. Todos os clusters implantados numa rede virtual também terão um ponto final privado que se resolve com um endereço IP privado dentro da Rede Virtual para acesso privado http aos gateways do cluster.


Como implantar o Azure HDInsight dentro de uma Rede Virtual e Seguro com um Grupo de Segurança de Rede:

https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Utilize o Azure Security Center e remediar as recomendações de proteção da rede para a rede virtual, subnet e grupo de segurança da rede que está a ser utilizado para proteger o seu cluster Azure HDInsight. Ative os registos de fluxo do grupo de segurança da rede (NSG) e envie registos numa Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um Espaço de Trabalho de Analytics de Log Azure e utilizar o Azure Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Azure Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar configurações erradas da rede.


Como ativar os registos de fluxo nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Como ativar e utilizar o Azure Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicações web.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Para proteções contra ataques DDoS, ative a proteção Padrão Azure DDoS na rede virtual onde o seu Azure HDInsight está implantado. Utilize o Azure Security Center integrado em inteligência de ameaça para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.


Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Compreender o Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Ativar os registos de fluxo do grupo de segurança da rede (NSG) para o NSG ligado à subnet que está a ser utilizado para proteger o seu cluster Azure HDInsight. Grave os registos de fluxo da NSG numa Conta de Armazenamento Azure para gerar registos de fluxo. Se necessário para investigar atividades anómalas, ative a captura de pacotes do Azure Network Watcher.


Como ativar os registos de fluxo nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Como ativar o Observador da Rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: A exigência pode ser satisfeita id 1.1 de controlo de segurança azure; Implemente o cluster Azure HDInsight numa rede virtual e proteja com um grupo de segurança de rede (NSG).

Existem várias dependências para o Azure HDInsight que requerem tráfego de entrada. O tráfego de gestão de entrada não pode ser enviado através de um dispositivo de firewall. Os endereços de origem para o tráfego de gestão exigido são conhecidos e publicados. Crie regras do Grupo de Segurança da Rede com esta informação para permitir o tráfego de apenas locais confiáveis, garantindo o tráfego de entrada para os clusters.

Como implantar o HDInsight dentro de uma Rede Virtual e Proteger com um Grupo de Segurança de Rede:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Compreender as dependências do HDInsight e o uso da firewall:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Endereços IP de gestão HDInsight:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicações web.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede (NSG) que estão ligados à sub-rede em que o seu cluster Azure HDInsight está implantado. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.


Compreender e utilizar etiquetas de serviço para Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede relacionados com o seu cluster Azure HDInsight. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.HDInsight" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede do seu cluster Azure HDInsight.


Também pode utilizar plantas Azure para simplificar as implementações azure em larga escala, embalagendo artefactos ambientais chave, tais como modelos de Gestor de Recursos Azure, controlos RBAC e políticas, numa única definição de planta. Aplique facilmente o projeto a novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.


Como visualizar os aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Como criar um Projeto Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para o grupo de segurança da rede (NSGs) e outros recursos relacionados com a segurança da rede e fluxo de tráfego que estejam associados ao seu cluster Azure HDInsight. Para regras nsg individuais, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.


Utilize qualquer uma das definições políticas do Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e notificá-lo de recursos não marcados existentes.


Pode utilizar a interface de linha de comando Azure PowerShell ou Azure (CLI) para procurar ou realizar ações em recursos baseados nas suas Tags.


Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


Como criar um NSG com um Config de Segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as suas implementações Azure HDInsight. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.


Como visualizar e recuperar eventos de registo de atividade sinuosa:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Como criar alertas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para componentes de cluster Azure HDInsight, pode atualizar a sincronização do tempo para as suas implementações computacionais.


Como configurar a sincronização do tempo para os recursos computacionais do Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação:** Pode embarcar no seu cluster Azure HDInsight para o Azure Monitor para agregar dados de segurança gerados pelo cluster. Aproveite as consultas personalizadas para detetar e responder a ameaças no ambiente. 


Como embarcar num cluster Azure HDInsight para o Monitor Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Como criar consultas personalizadas para um cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Ativar o Monitor Azure para o cluster HDInsight, direcioná-lo para um espaço de trabalho de Log Analytics. Isto irá registar informações relevantes do cluster e métricas de SPara todos os nós de cluster Azure HDInsight.


Como ativar o registo do Cluster HDInsight:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Como consultar os registos do HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Cluster Onboard Azure HDInsight para o Monitor Azure. Certifique-se de que o espaço de trabalho de Log Analytics utilizado tem o período de retenção de registo definido de acordo com as regras de conformidade da sua organização.


Como embarcar num Cluster Azure HDInsight para o Monitor Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Como configurar o período de retenção do espaço de trabalho de Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: Cluster Onboard Azure HDInsight para o Monitor Azure. Certifique-se de que o espaço de trabalho Azure Log Analytics utilizado tem o período de retenção de registo definido de acordo com as regras de conformidade da sua organização.


Como embarcar num Cluster Azure HDInsight para o Monitor Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Como configurar o período de retenção do espaço de trabalho de Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: Utilize consultas de espaço de trabalho Azure Log Analytics para consulta de registos Azure HDInsight:


Como criar consultas personalizadas para clusters Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: Utilize o espaço de trabalho Azure Log Analytics para monitorizar e alertar sobre atividades anómalas em registos de segurança e eventos relacionados com o seu cluster Azure HDInsight.


Como gerir alertas no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


Como alertar sobre os dados de registo de análise de registo:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: O Azure HDInsight vem com a Clamscan pré-instalada e ativada para as imagens do nó de cluster, no entanto deve gerir o software e agregar/monitorizar manualmente quaisquer registos que a Amêijoa produz.


Compreender a Amêijoa:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Implementar solução de terceiros para a exploração madeireira dns.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Configure manualmente o registo da consola numa base por nó.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha o registo da conta administrativa local criada durante o fornecimento de cluster do cluster Azure HDInsight, bem como quaisquer outras contas que criar. Além disso, se a integração da Azure AD for utilizada, a Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e, portanto, são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas adhoc para descobrir contas que são membros de grupos administrativos.


Além disso, pode utilizar recomendações de Identidade e Gestão de Acesso do Azure Security Center.


Como obter um papel de diretório em Azure AD com powerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Como obter membros de um papel de diretório em Azure AD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Como monitorizar a identidade e o acesso ao Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: Ao fornecer um cluster, o Azure exige que crie novas senhas para o portal web e acesso secure Shell (SSH). Não existem senhas padrão para alterar, no entanto pode especificar diferentes palavras-passe para acesso ao SSH e portal web.


Como definir palavras-passe ao fornecer um cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Integrar a autenticação para o cluster Azure HDInsight com o Diretório Ativo Azure. Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas.


Além disso, pode utilizar recomendações de Identidade e Gestão de Acesso do Azure Security Center.


Como integrar a autenticação Azure HDInsight com o Diretório Ativo Azure:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds


Como monitorizar a identidade e o acesso ao Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: Utilize o Corretor de ID Azure HDInsight para iniciar sessão nos clusters do Pacote de Segurança Empresarial (ESP) utilizando a autenticação multi-factor, sem fornecer quaisquer palavras-passe. Se já assinou outros serviços azure, como o portal Azure, pode iniciar sessão no seu cluster Azure HDInsight com uma única experiência de inscrição (SSO).


Como ativar o Corretor de ID Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Habilitar o Azure AD MFA e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center. Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurado podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters e executar grandes trabalhos de dados. Ao autenticar com a autenticação multifactor (MFA) ativada, os utilizadores serão desafiados a fornecer um segundo fator de autenticação.


Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Como monitorizar a identidade e o acesso dentro do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho de acesso privilegiado) com autenticação multifactor (MFA) configurada para iniciar sessão e configurar os seus clusters Azure HDInsight e recursos conexos.


Saiba mais sobre postos de trabalho de acesso privilegiados:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurado podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar. Pode utilizar relatórios de segurança do Azure Ative Directory (AAD) para a geração de registos e alertas quando ocorrer atividade suspeite ou insegura no ambiente AAD. Utilize o Azure Security Center para monitorizar a atividade de identidade e acesso.


Como identificar utilizadores aAD sinalizados para atividades de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Do Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurado podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar. Utilizar locais nomeados para acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.


Como configurar localizações nomeadas em Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (AAD) como sistema central de autenticação e autorização. A AAD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A AAD também sais, hashes e armazena seguramente credenciais de utilizador.

Os clusters Azure HDInsight com pacote de segurança empresarial (ESP) configurados podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters.


Como criar e configurar uma instância AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


Como configurar o Pacote de Segurança Empresarial com os Serviços de Domínio de Diretório Ativo Azure em Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: Utilize a autenticação do Diretório Ativo Azure (AAD) com o seu cluster Azure HDInsight. A AAD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso do utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 


Como utilizar comentários de acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Utilize registos de registos de sessão e auditoria do Azure Ative Directory (AAD) para monitorizar as tentativas de acesso a contas desativadas; estes registos podem ser integrados em qualquer ferramenta de monitorização/siEM de terceiros.


Pode simplificar este processo criando Definições de Diagnóstico para contas de utilizadores AAD, enviando os registos de auditoria e registos de log-in para um espaço de trabalho do Azure Log Analytics. Configure os alertas desejados dentro do espaço de trabalho azure Log Analytics.


Como integrar registos de atividade do Azure no Monitor Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Os clusters Azure HDInsight com pacote de segurança empresarial (ESP) configurados podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters.  Utilize o diretório ativo azure (AAD) deteção de riscos e a proteção de identidade para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Além disso, pode ingerir dados no Azure Sentinel para mais investigação.


Como visualizar inscrições arriscadas da AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Como configurar e ativar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Não disponível; O Lockbox do cliente ainda não foi suportado para o Azure HDInsight.

Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas sobre recursos relacionados com as suas implementações Azure HDInsight para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.


Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os clusters Azure HDInsight e quaisquer contas de armazenamento associadas devem ser separados por rede/sub-rede virtual, marcados adequadamente e protegidos dentro de um grupo de segurança de rede (NSG) ou Azure Firewall. Os dados do cluster devem ser contidos numa conta de armazenamento azure segura ou no armazenamento de lagos de dados azure (Gen1 ou Gen2).


Escolha opções de armazenamento para o seu cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Como garantir o armazenamento do Lago Azure Data:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Como garantir as Contas de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Para os clusters Azure HDInsight que armazenam ou processam informações sensíveis, marquem o cluster e os recursos conexos como sensíveis utilizando etiquetas. Para reduzir o risco de perda de dados através da exfiltração, restrinja o tráfego de rede de saída para os clusters Azure HDInsight utilizando o Firewall Azure.


Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.


Como restringir o tráfego de saída para clusters Azure HDInsight com Firewall Azure:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic


Compreender a proteção de dados dos clientes em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Certifique-se de que todos os clientes que se ligam às suas lojas de dados de cluster Azure HDInsight ou cluster (Contas de Armazenamento Azure ou Azure Data Lake Storage Gen1/Gen2) são capazes de negociar TLS 1.2 ou superior. Os recursos do Microsoft Azure negociarão o TLS 1.2 por padrão. 


Compreender a encriptação de armazenamento de lagos de dados azure em trânsito:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Compreender a encriptação da Conta de Armazenamento Azure em trânsito:

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os recursos de Armazenamento ou cálculo do Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.



Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.



Compreender a proteção de dados dos clientes em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Com o Pacote de Segurança Empresarial Azure HDInsight (ESP), pode utilizar o Apache Ranger para criar e gerir as políticas de controlo de acesso e obfuscção de dados dos seus dados armazenados em ficheiros, pastas, bases de dados, tabelas e linhas/colunas. A administração hadoop pode configurar o controlo de acesso baseado em funções (RBAC) para proteger apache Hive, HBase, Kafka e Spark usando esses plugins em Apache Ranger.

Configurar as políticas de RBAC com o Apache Ranger permite-lhe associar permissões com um papel na organização. Esta camada de abstração facilita a garantia de que as pessoas têm apenas as permissões necessárias para desempenhar as suas responsabilidades de trabalho.

Configurações do Pacote de Segurança Empresarial com Serviços de Domínio de Diretório Ativo Azure em HDInsight:https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Visão geral da segurança da empresa em Azure HDInsight:https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Para os clusters Azure HDInsight que armazenam ou processam informações sensíveis, marquem o cluster e os recursos conexos como sensíveis utilizando etiquetas. As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os recursos de Armazenamento de Azure ou computacionais. Implementar solução de terceiros, se necessário para efeitos de conformidade.


Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.


Compreender a proteção de dados dos clientes em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Se utilizar a Base de Dados Azure SQL para armazenar metadados Apache Hive e Apache Oozie, certifique-se de que os dados do SQL permanecem encriptados em todos os momentos. Para contas de armazenamento Azure e armazenamento de data lake (Gen1 ou Gen2), é aconselhável permitir que a Microsoft gere as suas chaves de encriptação, no entanto, tem a opção de gerir as suas próprias chaves.



Como gerir as chaves de encriptação das Contas de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal



Como criar o Armazenamento de Lagos de Dados Azure utilizando chaves de encriptação geridas pelo cliente:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal



Compreender a encriptação para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption



Como configurar a encriptação transparente de dados para a Base de Dados SQL utilizando chaves geridas pelo cliente:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Configure as definições de diagnóstico para contas de armazenamento Azure associadas aos clusters Azure HDInsight para monitorizar e registar todas as operações de CRUD contra dados de cluster. Ative a auditoria de quaisquer Contas de Armazenamento ou Lojas data Lake associadas ao cluster Azure HDInsight.


Como permitir a exploração/auditoria adicional para uma conta de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account


Como permitir a exploração/auditoria adicional para o armazenamento de lagos de dados azure:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros.


Opcionalmente, se tiver uma subscrição rápida 7, Qualys ou qualquer outra subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar ações de script para instalar agentes de avaliação de vulnerabilidadenos nós de cluster Do IADInsight e gerir os nós através do respetivo portal.


Como instalar o agente Rapid7 manualmente:

https://insightvm.help.rapid7.com/docs/azure-security-center


Como instalar o Agente Qualys manualmente:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf


Como usar as ações do script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Foram ativadas atualizações automáticas do sistema para imagens do nó do cluster, no entanto deve reiniciar periodicamente os nós do cluster para garantir que as atualizações são aplicadas.


Microsoft para manter e atualizar imagens de nó azure HDInsight base.


Como configurar o calendário de correção do OS para os clusters HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Utilize ações de script ou outros mecanismos para remendar os seus clusters Azure HDInsight. Os clusters recém-criados terão sempre as mais recentes atualizações disponíveis, incluindo as mais recentes correções de segurança.


Como configurar o calendário de correção do OS para clusters Azure HDInsight baseados em Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching


Como usar as ações do script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros que tenha a capacidade de comparar os exames de vulnerabilidade ao longo do tempo. Se tiver uma subscrição Rapid7 ou Qualys, poderá utilizar o portal do fornecedor para visualizar e comparar as análises de vulnerabilidade seletivas.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, Sistema Comum de Pontuação de Vulnerabilidades) ou as classificações de risco predefinidas fornecidas pela sua ferramenta de digitalização de terceiros.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters Azure HDInsight, dentro da sua subscrição(s).  Certifique-se de que tem permissões adequadas (ler) no seu inquilino e que é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.


Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.


Como criar consultas com o Gráfico de Recursos Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Como visualizar as suas Assinaturas Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Compreender o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.


Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear os ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.


Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription


Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create


Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Definir lista de recursos azure aprovados e software aprovado para os seus recursos computacionais

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Gráfico de Recursos Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Implementar uma solução de terceiros para monitorizar os nós de cluster para aplicações de software não aprovadas.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters Azure HDInsight, dentro da sua subscrição(s).  Remova quaisquer recursos Azure não aprovados que descubra. Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para remover ou alertar em software não aprovado.


Como criar consultas com o Gráfico de Recursos Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para impedir a execução de software não autorizado.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos


Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Como negar um tipo específico de recurso com a Política Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para impedir a execução de tipos de ficheiros não autorizados.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts

**Orientação**: Utilize o Acesso Condicional azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure configurando o "Block access" para a app "Microsoft Azure Management".


Como configurar o Acesso Condicional ao acesso ao portal Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; Isto não é aplicável ao Azure HDInsight, uma vez que os utilizadores (não administradores) do cluster não precisam de ter acesso aos nós individuais para executar postos de trabalho. O administrador do cluster tem acesso de raiz a todos os nós do cluster.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicações web.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.HDInsight" para criar políticas personalizadas para auditar ou impor a configuração da rede do seu cluster HDInsight.


Como visualizar os aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Imagens do Sistema Operativo Azure HDInsight geridas e mantidas pela Microsoft. Cliente responsável pela implementação de configurações seguras para o sistema operativo dos seus nós de cluster. 

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação:** Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras para os seus clusters Azure HDInsight e recursos conexos.


Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Compreender efeitos políticos do Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Imagens do Sistema Operativo Azure HDInsight geridas e mantidas pela Microsoft. Cliente responsável pela implementação da configuração do estado de nível os.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições políticas personalizadas do Azure, utilize o Azure DevOps ou o Azure Repos para armazenar e gerir de forma segura o seu código.



Como armazenar código em Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Documentação Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; imagens personalizadas não aplicáveis ao Azure HDInsight.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.HDInsight" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.



Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Implemente uma solução de terceiros para manter o estado desejado para os sistemas operativos do nó cluster.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.HDInsight" para criar políticas personalizadas para auditar ou impor a configuração do seu cluster HDInsight.


Como visualizar os aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Implemente uma solução de terceiros para monitorizar o estado dos sistemas operativos do nó de cluster.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Azure HDInsight inclui suporte Bring Your Own Key (BYOK) para Apache Kafka. Esta capacidade permite-lhe possuir e gerir as chaves utilizadas para encriptar dados em repouso.


Todos os discos geridos no Azure HDInsight estão protegidos com encriptação do serviço de armazenamento Azure (SSE). Por predefinição, os dados desses discos são encriptados utilizando chaves geridas pela Microsoft. Se ativar o BYOK, fornece a chave de encriptação para o Azure HDInsight utilizar e geri-la utilizando o Cofre de Chaves Azure.


O Cofre chave também pode ser usado com implementações Azure HDInsight para gerir chaves para armazenamento de cluster (Contas de Armazenamento Azure e Armazenamento de Lagos De Dados Azure)


Como trazer a sua própria chave para Apache Kafka no Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Como gerir as chaves de encriptação das Contas de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Identidades geridas podem ser usadas no Azure HDInsight para permitir que os seus clusters acedam aos serviços de domínio do Diretório Ativo Azure, acedam ao Cofre de Chaves Azure ou ficheiros de acesso em Azure Data Lake Storage Gen2.


Compreender identidades geridas com Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Se utilizar qualquer código relacionado com a sua implementação Azure HDInsight, poderá implementar o Credential Scanner para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 


Como configurar o Scanner Credencial:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: O Azure HDInsight vem com a Clamscan pré-instalada e ativada para as imagens do nó de cluster, no entanto deve gerir o software e agregar/monitorizar manualmente quaisquer registos que a Amêijoa produz.


Compreenda o Clamscan para Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O Microsoft Antimalware está ativado no hospedeiro subjacente que suporta os serviços Do Azure, no entanto não funciona com conteúdo do cliente.


Pré-digitalização de quaisquer ficheiros que sejam enviados para quaisquer recursos Azure relacionados com a sua implementação do cluster Azure HDInsight, tais como Armazenamento de Data Lake, Blob Storage, etc. Nestes casos, a Microsoft não pode aceder aos dados dos clientes.


Compreenda o Microsoft Antimalware para Serviços Azure Cloud e Máquinas Virtuais:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: O Azure HDInsight vem com a Amêijoa pré-instalada e ativada para as imagens do nó de cluster. A Amêijoa realizará automaticamente atualizações de motor e definição, no entanto, a agregação e a gestão dos registos terão de ser realizadas manualmente.


Compreenda a Amêijoa para Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Ao utilizar uma conta de armazenamento Azure para o armazém de dados do cluster HDInsight, escolha a opção de despedimento adequada (LRS,ZRS, GRS, RA-GRS).  Ao utilizar uma base de dados Azure SQL para a loja de dados do cluster Azure HDInsight, configure a geo-replicação ativa.


Como configurar o despedimento de armazenamento para contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Como configurar o despedimento para as bases de dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: Ao utilizar uma conta de armazenamento Azure para o armazém de dados do cluster Azure HDInsight, escolha a opção de redundância adequada (LRS,ZRS, GRS, RA-GRS). Se utilizar o Cofre de Chaves Azure para qualquer parte da sua implementação Azure HDInsight, certifique-se de que as suas chaves estão apoiadas.


Escolha opções de armazenamento para o seu cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Como configurar o despedimento de armazenamento para contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Como fazer backup das chaves do cofre chave em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Se o Cofre de Chaves Azure estiver a ser utilizado com a sua implementação Azure HDInsight, teste a restauração das chaves geridas pelo cliente.


Como trazer a sua própria chave para Apache Kafka no Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Como restaurar as chaves do cofre chave em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Se o Cofre de Chaves Azure estiver a ser utilizado com a sua implementação Azure HDInsight, ative o Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.


Como ativar o Soft-Delete no Cofre de Chaves Azure:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definam funções de pessoal, bem como fases de tratamento/gestão de incidentes.



Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que, quando um recurso estiver comprometido, possa chegar ao mesmo imediatamente. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário. Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.



Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas Sentinel.



Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export



Como transmitir alertas para O Sentinela Azul:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.



Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra a Microsoft gerida por infraestruturas, serviços e aplicações em nuvem, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
