---
title: Linha de base de segurança Azure para HDInsight
description: Linha de base de segurança Azure para HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ebc0398eeb7679ffd57e0aa5aae642f6303aaa35
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484615"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Linha de base de segurança Azure para HDInsight

A Linha de Base de Segurança Azure para HDInsight contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, consulte [Controlo de Segurança: Segurança da Rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: A segurança do perímetro em Azure HDInsight é conseguida através de redes virtuais. Um administrador da empresa pode criar um cluster dentro de uma rede virtual e usar um grupo de segurança de rede (NSG) para restringir o acesso à rede virtual. Apenas os endereços IP permitidos nas regras do Grupo de Segurança da Rede de Entrada poderão comunicar com o cluster Azure HDInsight. Esta configuração proporciona segurança no perímetro. Todos os clusters implantados numa rede virtual também terão um ponto final privado que resolve um endereço IP privado dentro da Rede Virtual para acesso privado HTTP aos gateways de cluster.

Para reduzir o risco de perda de dados através da exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight utilizando a Firewall Azure.

Como implementar o Azure HDInsight dentro de uma rede virtual e garantir com um Grupo de Segurança de Rede: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Como restringir o tráfego de saída para clusters Azure HDInsight com Azure Firewall: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação**: Utilize o Azure Security Center e remedia as recomendações de proteção da rede para a rede virtual, sub-rede e grupo de segurança de rede que está a ser utilizado para proteger o seu cluster Azure HDInsight. Ativar os registos de fluxo do grupo de segurança da rede (NSG) e enviar registos para uma Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho Azure Log Analytics e utilizar o Azure Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens da Azure Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar os registos de fluxo NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como Ativar e utilizar a Azure Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Para proteções contra ataques DDoS, ative a proteção padrão Azure DDoS na rede virtual onde o seu Azure HDInsight está implantado. Use o Azure Security Center integrado em inteligência de ameaça para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Compreenda a Inteligência Integrada de Ameaças do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Ative os registos de flog do grupo de segurança da rede (NSG) para o NSG ligado à sub-rede que está a ser utilizada para proteger o seu cluster Azure HDInsight. Grave os registos de fluxo NSG numa Conta de Armazenamento Azure para gerar registos de fluxo. Se necessário para investigar atividades anómalas, ative a captura de pacotes do Azure Network Watcher.

Como ativar os registos de fluxo NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar o Observador de Rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: A exigência pode ser satisfeita O ID 1.1 do controlo de segurança da Azure; Implementar o cluster Azure HDInsight numa rede virtual e proteger-se com um grupo de segurança de rede (NSG).

Existem várias dependências para a Azure HDInsight que requerem tráfego de entrada. O tráfego de gestão de entrada não pode ser enviado através de um dispositivo de firewall. Os endereços de origem para o tráfego de gestão necessário são conhecidos e publicados. Crie regras do Grupo de Segurança de Rede com esta informação para permitir o tráfego de apenas locais fidedignos, assegurando o tráfego de entrada para os clusters.

Para reduzir o risco de perda de dados através da exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight utilizando a Firewall Azure.

Como implementar o HDInsight dentro de uma rede virtual e proteger com um Grupo de Segurança de Rede: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Compreenda as dependências hdInsight e o uso de firewall: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Endereços IP de gestão HDInsight: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede (NSG) que estejam ligados à sub-rede onde o seu cluster Azure HDInsight é implantado. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Compreender e utilizar tags de serviço para Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede relacionados com o seu cluster Azure HDInsight. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.HDInsight" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu cluster Azure HDInsight.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de gestor de recursos Azure, controlos rbac azure, e políticas, numa única definição de planta. Aplique facilmente o projeto em novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

Como visualizar pseudónimos disponíveis da Política Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar uma Planta Azure:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize Tags para grupos de segurança de rede (NSGs) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego que estejam associados ao seu cluster Azure HDInsight. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar a interface de linha de comando Azure PowerShell ou Azure (CLI) para procurar ou executar ações em recursos baseados nas suas Tags.

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG com um Config de Segurança:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as suas implementações Azure HDInsight. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos de Registo de Atividades Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os componentes do cluster Azure HDInsight, pode atualizar a sincronização de tempo para as suas implementações de computação.

Como configurar a sincronização temporal para os recursos computativos Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Pode embarcar o seu cluster Azure HDInsight para o Azure Monitor para agregar dados de segurança gerados pelo cluster. Aproveite as consultas personalizadas para detetar e responder a ameaças no ambiente. 

Como embarcar num cluster Azure HDInsight para o Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Como criar consultas personalizadas para um cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar o Monitor Azure para o cluster HDInsight, direcioná-lo para um espaço de trabalho Log Analytics. Isto registará informações relevantes do cluster e métricas de SO para todos os nós de cluster Azure HDInsight.

Como permitir a sessão de registo para o HdInsight Cluster:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Como consultar os registos HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: A bordo Azure HDInsight cluster para Azure Monitor. Certifique-se de que o espaço de trabalho Log Analytics utilizado tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.

Como embarcar num Cluster Azure HDInsight para Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Como configurar o período de retenção do espaço de trabalho do Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: A bordo Azure HDInsight cluster para Azure Monitor. Certifique-se de que o espaço de trabalho Azure Log Analytics utilizado tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.

Como embarcar num Cluster Azure HDInsight para Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Como configurar o período de retenção do espaço de trabalho do Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Utilize consultas de espaço de trabalho Azure Log Analytics para consultar os registos Azure HDInsight:

Como criar consultas personalizadas para clusters Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o espaço de trabalho Azure Log Analytics para monitorizar e alertar sobre atividades anómalas em registos de segurança e eventos relacionados com o seu cluster Azure HDInsight.

Como gerir alertas no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Como alertar os dados do registo de registos de registos de registos de registos:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Azure HDInsight vem com clamscan pré-instalado e habilitado para as imagens do nó de cluster, no entanto deve gerir o software e agregar/monitorizar manualmente quaisquer registos que o Clamscan produz.

Compreenda o Clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Implementar solução de terceiros para a sessão de registo de dns.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Configurar manualmente o registo da consola numa base por nó.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha o registo da conta administrativa local que é criada durante o a provisionamento do cluster Azure HDInsight, bem como quaisquer outras contas que crie. Além disso, se a integração AZURE AD for usada, a Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e, portanto, são questionáveis. Utilize o módulo Azure AD PowerShell para realizar consultas de adhoc para descobrir contas que são membros de grupos administrativos.

Além disso, pode utilizar recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

Como obter um papel de diretório em Azure AD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Como obter membros de um papel de diretório em Azure AD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

Como monitorizar a identidade e o acesso com o Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Ao providenciar um cluster, o Azure exige que crie novas palavras-passe para o portal web e acesso Secure Shell (SSH). Não existem senhas padrão para alterar, no entanto pode especificar diferentes palavras-passe para SSH e acesso ao portal web.

Como definir palavras-passe ao forer um cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Integrar autenticação para cluster Azure HDInsight com diretório ativo Azure. Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas.

Além disso, pode utilizar recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

Como integrar a autenticação Azure HDInsight com o Azure Ative Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Como monitorizar a identidade e o acesso com o Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Utilize o Azure HDInsight ID Broker para iniciar sedutação nos clusters do Pacote de Segurança Empresarial (ESP) utilizando a autenticação multi-factor, sem fornecer quaisquer palavras-passe. Se já assinou contrato com outros serviços Azure, como o portal Azure, pode iniciar sômposição no seu cluster Azure HDInsight com uma única experiência de sso de assinatura.

Como ativar o Corretor de ID Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar O Azure AD MFA e seguir as recomendações do Azure Security Center Identity and Access Management. Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurados podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters e executar grandes trabalhos de dados. Ao autenticar com autenticação multi-factor (MFA) ativada, os utilizadores serão desafiados a fornecer um segundo fator de autenticação.

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com autenticação multi-factor (MFA) configurada para iniciar sessão e configurar os seus clusters Azure HDInsight e recursos conexos.

Saiba mais sobre estações de acesso privilegiadas:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurados podem ser ligados a um domínio para que os utilizadores de domínio possam utilizar as suas credenciais de domínio para autenticar. Pode utilizar relatórios de segurança do Azure Ative Directory (AAD) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente AAD. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

Como identificar utilizadores da AAD sinalizados para atividade de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurados podem ser ligados a um domínio para que os utilizadores de domínio possam utilizar as suas credenciais de domínio para autenticar. Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar localizações nomeadas em Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AAD) como sistema central de autenticação e autorização. AAD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. AAD também sai, hashes e armazena seguramente credenciais de utilizador.

Os clusters Azure HDInsight com Pacote de Segurança Empresarial (ESP) configurados podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters.

Como criar e configurar um caso AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Como configurar o Pacote de Segurança Empresarial com os Serviços de Domínio do Diretório Ativo Azure em Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Utilize a autenticação do Azure Ative Directory (AAD) com o seu cluster Azure HDInsight. AAD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso do utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 

Como utilizar comentários sobre o acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Utilize registos de inscrição e auditoria do Azure Ative Para monitorizar as tentativas de acesso a contas desativadas; estes registos podem ser integrados em qualquer ferramenta siem/monitorização de terceiros.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores AAD, enviando os registos de auditoria e registos de login para um espaço de trabalho Azure Log Analytics. Configure os alertas desejados dentro do espaço de trabalho Azure Log Analytics.

Como integrar os Registos de Atividade do Azure no Monitor Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Os clusters Azure HDInsight com Pacote de Segurança Empresarial (ESP) configurados podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters.  Utilize o recurso Azure Ative Directory (AAD) deteções de riscos e proteção de identidade para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Além disso, você pode ingerir dados em Azure Sentinel para mais investigação.

Como visualizar as entradas de risco da AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e permitir políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não disponível; O Lockbox do cliente ainda não suportado para a Azure HDInsight.

Lista de serviços suportados pelo Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas em recursos relacionados com as suas implementações Azure HDInsight para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

Como criar e utilizar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os clusters Azure HDInsight e quaisquer contas de armazenamento associadas devem ser separados por rede/sub-rede virtuais, marcados adequadamente e protegidos dentro de um grupo de segurança de rede (NSG) ou Azure Firewall. Os dados do cluster devem ser contidos numa conta de armazenamento Azure ou no Azure Data Lake Storage (Gen1 ou Gen2).

Escolha opções de armazenamento para o seu cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Como garantir o armazenamento do Lago de Dados Azure:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Como garantir contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Para clusters Azure HDInsight armazenando ou processando informações sensíveis, marque o cluster e recursos relacionados como sensíveis usando tags. Para reduzir o risco de perda de dados através da exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight utilizando a Firewall Azure.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Como restringir o tráfego de saída para clusters Azure HDInsight com Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Compreender a proteção de dados do cliente em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Certifique-se de que quaisquer clientes que se conectem ao seu cluster Azure HDInsight ou lojas de dados de cluster (Contas de Armazenamento Azure ou Azure Data Lake Storage Gen1/Gen2) são capazes de negociar TLS 1.2 ou maior. Os recursos da Microsoft Azure negociarão tLS 1.2 por padrão. 

Compreenda a encriptação de armazenamento do Lago de Dados Azure em trânsito:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Compreenda a encriptação da conta de armazenamento Azure em trânsito:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Com o Azure HDInsight Enterprise Security Package (ESP), pode utilizar o Apache Ranger para criar e gerir políticas de controlo de acessos finos e de obfuscção de dados para os seus dados armazenados em ficheiros, pastas, bases de dados, tabelas e linhas/colunas. O administrador hadoop pode configurar o controlo de acesso baseado em funções (RBAC) para garantir a Hive Apache, HBase, Kafka e Spark usando esses plugins em Apache Ranger.

Configurar as políticas do RBAC com o Apache Ranger permite-lhe associar permissões com um papel na organização. Esta camada de abstração facilita a garantia de que as pessoas têm apenas as permissões necessárias para desempenhar as suas responsabilidades de trabalho.

Configurações do Pacote de Segurança Empresarial com Serviços de Domínio do Diretório Ativo Azure em HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Visão geral da segurança da empresa em Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Para clusters Azure HDInsight armazenando ou processando informações sensíveis, marque o cluster e recursos relacionados como sensíveis usando tags. As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Se utilizar a Base de Dados Azure SQL para armazenar metadados Apache Hive e Apache Oozie, certifique-se de que os dados SQL permanecem sempre encriptados. Para contas de armazenamento Azure e armazenamento de data lake (Gen1 ou Gen2), é aconselhável permitir que a Microsoft gere as suas chaves de encriptação, no entanto, tem a opção de gerir as suas próprias chaves.

Como gerir chaves de encriptação para contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Como criar o Azure Data Lake Storage utilizando chaves de encriptação geridas pelo cliente:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Compreender a encriptação para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Como configurar encriptação de dados transparentes para base de dados SQL utilizando chaves geridas pelo cliente:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Configurações de diagnóstico configuradas para contas de armazenamento Azure associadas a clusters Azure HDInsight para monitorizar e registar todas as operações CRUD contra dados de cluster. Ativar a auditoria de quaisquer Contas de Armazenamento ou Lojas data lake associadas ao cluster Azure HDInsight.

Como permitir a registo/auditoria adicional de uma Conta de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Como permitir a exploração/auditoria adicional para o armazenamento do Lago de Dados Azure:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros.

Opcionalmente, se tiver uma subscrição de Plataforma De Gestão de Vulnerabilidades Rapid7, Qualys ou qualquer outra subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar ações de script para instalar agentes de avaliação de vulnerabilidade nos seus nós de cluster Azure HDInsight e gerir os nós através do respetivo portal.

Como instalar manualmente o Agente Rapid7:

https://insightvm.help.rapid7.com/docs/install

Como instalar manualmente o Agente Qualys:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Como utilizar as ações de script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Foram ativadas atualizações automáticas do sistema para imagens de nó de cluster, no entanto deve reiniciar periodicamente os nós de cluster para garantir que as atualizações são aplicadas.

Microsoft para manter e atualizar as imagens do nó de azure HDInsight base.

Como configurar o calendário de remendos de SISTEMAS para clusters HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: Utilize ações de script ou outros mecanismos para corrigir os seus clusters Azure HDInsight. Os clusters recém-criados terão sempre as últimas atualizações disponíveis, incluindo os mais recentes patches de segurança.

Como configurar o calendário de remendos de SISTEMAS para clusters Azure HDInsight baseados em Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Como utilizar as ações de script:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros que tenha a capacidade de comparar digitalizações de vulnerabilidades ao longo do tempo. Se tiver uma subscrição Rapid7 ou Qualys, poderá utilizar o portal desse fornecedor para visualizar e comparar as verificações de vulnerabilidades de trás para a outra.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, sistema comum de pontuação de vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters Azure HDInsight, dentro da sua subscrição(s).  Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

Como criar consultas com gráfico de recursos Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver as suas Subscrições Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Compreenda Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

Como criar e utilizar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação**: Defina a lista de recursos aprovados da Azure e software aprovado para os seus recursos de computação

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.s. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com O Gráfico Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Implementar uma solução de terceiros para monitorizar os nós de cluster para aplicações de software não aprovadas.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters Azure HDInsight, dentro da sua subscrição(s).  Remova quaisquer recursos não aprovados do Azure que descobrir. Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para remover ou alertar em software não aprovado.

Como criar consultas com O Gráfico Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para impedir a execução de software não autorizado.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para impedir a execução de tipos de ficheiros não autorizados.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure através de scripts

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; Isto não é aplicável ao Azure HDInsight, uma vez que os utilizadores (não administradores) do cluster não precisam de acesso aos nós individuais para executarem empregos. O administrador do cluster tem acesso de raiz a todos os nós de cluster.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte [Controlo de Segurança: Configuração Segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.HDInsight" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu cluster HDInsight.

Como visualizar pseudónimos disponíveis da Política Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Imagens do sistema operativo Azure HDInsight geridas e mantidas pela Microsoft. Cliente responsável pela implementação de configurações seguras para o sistema operativo dos seus nós de cluster. 


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor definições seguras para os seus clusters Azure HDInsight e recursos relacionados.

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreenda os efeitos da política do Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Imagens do sistema operativo Azure HDInsight geridas e mantidas pela Microsoft. Cliente responsável pela implementação da configuração do estado de nível oss.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

[Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; imagens personalizadas não aplicáveis ao Azure HDInsight.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.HDInsight" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para manter o estado desejado para os sistemas operativos do nó de cluster.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.HDInsight" para criar políticas personalizadas para auditar ou impor a configuração do seu cluster HDInsight.

[Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

[Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para monitorizar o estado dos seus sistemas operativos de nó de cluster.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Azure HDInsight inclui suporte Bring Your Own Key (BYOK) para Apache Kafka. Esta capacidade permite-lhe possuir e gerir as chaves usadas para encriptar dados em repouso.

Todos os discos geridos em Azure HDInsight estão protegidos com encriptação do serviço de armazenamento Azure (SSE). Por predefinição, os dados desses discos são encriptados utilizando as teclas geridas pela Microsoft. Se ativar a BYOK, fornece a chave de encriptação para o Azure HDInsight usá-lo e geri-lo usando o Azure Key Vault.

O Key Vault também pode ser utilizado com implementações Azure HDInsight para gerir chaves para armazenamento de clusters (Contas de Armazenamento Azure e Armazenamento de Dados Azure)

Como trazer a sua própria chave para Apache Kafka em Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Como gerir chaves de encriptação para contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Identidades geridas podem ser usadas no Azure HDInsight para permitir que os seus clusters acedam aos serviços de domínio do Azure Ative Directory, acedam ao Cofre da Chave Azure ou acedam a ficheiros no Azure Data Lake Storage Gen2.

Compreender identidades geridas com Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Se utilizar qualquer código relacionado com a sua implementação Azure HDInsight, poderá implementar o Scanner Credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

Como configurar o Scanner Credencial:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Controlo de Segurança: Defesa de Malware.](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Azure HDInsight vem com clamscan pré-instalado e habilitado para as imagens do nó de cluster, no entanto deve gerir o software e agregar/monitorizar manualmente quaisquer registos que o Clamscan produz.

Compreenda o Clamscan para Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure, no entanto não funciona com conteúdo do cliente.

Pré-digitalize quaisquer ficheiros que sejam enviados para quaisquer recursos Azure relacionados com a sua implementação do cluster Azure HDInsight, tais como Armazenamento de Data Lake, Armazenamento de Blob, etc. A Microsoft não pode aceder aos dados dos clientes nestes casos.

Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Azure HDInsight vem com clamscan pré-instalado e ativado para as imagens do nó de cluster. O Clamscan realizará atualizações de motor e definição automaticamente, no entanto, a agregação e gestão de troncos terá de ser executada manualmente.

Compreenda o Clamscan para Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Quando utilizar uma Conta de Armazenamento Azure para a loja de dados do cluster HDInsight, escolha a opção de despedimento adequada (LRS, ZRS, GRS, RA-GRS).  Ao utilizar uma Base de Dados Azure SQL para a loja de dados do cluster Azure HDInsight, configuure a replicação de geo-replicação ativa.

Como configurar a redundância de armazenamento para as contas de armazenamento da Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Como configurar a redundância para a Base de Dados Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Quando utilizar uma conta de armazenamento Azure para a loja de dados do cluster Azure HDInsight, escolha a opção de redundância adequada (LRS, ZRS, GRS, RA-GRS). Se utilizar o Azure Key Vault para qualquer parte da sua implantação Azure HDInsight, certifique-se de que as suas chaves estão apoiadas.

Escolha opções de armazenamento para o seu cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Como configurar a redundância de armazenamento para as contas de armazenamento da Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Como apoiar as chaves do Cofre chave em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Se o Azure Key Vault estiver a ser utilizado com a sua implantação Azure HDInsight, teste a restauração das teclas geridas pelo cliente.

Como trazer a sua própria chave para Apache Kafka em Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Como restaurar as chaves do cofre chave em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Se o Cofre da Chave Azure estiver a ser utilizado com a sua implantação Azure HDInsight, ative Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

Como permitir Soft-Delete no Cofre da Chave Azure:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definem funções de pessoal, bem como fases de tratamento/gestão de incidentes.

Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que quando um recurso estiver comprometido, possa chegar imediatamente ao mesmo. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário. Consulte a publicação do NIST: Guide to Test, Training e Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.

Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a automatização do fluxo de trabalho e as aplicações lógicas:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte [Controlo de Segurança: Testes de penetração e exercícios da equipa vermelha.](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridos pela Microsoft na nuvem, aqui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as Linhas de Base de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
