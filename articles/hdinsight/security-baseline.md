---
title: Linha de base de segurança Azure para HDInsight
description: A linha de base de segurança HDInsight fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c5ffdecf768be0962950bb3691dbb11fb0e70120
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565015"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Linha de base de segurança Azure para HDInsight

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) a HDInsight. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao HDInsight. Foram excluídos **os controlos** não aplicáveis ao HDInsight.

 
Para ver como o HDInsight mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança HDInsight completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A segurança do perímetro em Azure HDInsight é conseguida através de redes virtuais. Um administrador da empresa pode criar um cluster dentro de uma rede virtual e usar um grupo de segurança de rede (NSG) para restringir o acesso à rede virtual. Apenas os endereços IP permitidos nas regras do Grupo de Segurança da Rede de Entrada poderão comunicar com o cluster Azure HDInsight. Esta configuração proporciona segurança no perímetro. Todos os clusters implantados numa rede virtual também terão um ponto final privado que resolve um endereço IP privado dentro da Rede Virtual para acesso privado HTTP aos gateways de cluster.

Para reduzir o risco de perda de dados através da exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight utilizando a Firewall Azure.

- [Como implementar o Azure HDInsight dentro de uma rede virtual e garantir com um Grupo de Segurança de Rede](hdinsight-create-virtual-network.md)

- [Como restringir o tráfego de saída para clusters Azure HDInsight com Azure Firewall](hdinsight-restrict-outbound-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Utilize o Azure Security Center e remedia as recomendações de proteção da rede para a rede virtual, sub-rede e grupo de segurança de rede que está a ser utilizado para proteger o seu cluster Azure HDInsight. Ativar os registos de fluxo do grupo de segurança da rede (NSG) e enviar registos numa Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho Azure Log Analytics e utilizar o Azure Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens da Azure Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Azure Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Para proteções contra ataques DDoS, ative a proteção padrão Azure DDoS na rede virtual onde o seu Azure HDInsight está implantado. Use o Azure Security Center integrado em inteligência de ameaça para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Ative os registos de fluxo do grupo de segurança da rede (NSG) para o NSG ligado à sub-rede que está a ser utilizada para proteger o seu cluster Azure HDInsight. Grave os registos de fluxo NSG numa Conta de Armazenamento Azure para gerar registos de fluxo. Se necessário para investigar atividades anómalas, ative a captura de pacotes do Azure Network Watcher.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: A exigência pode ser satisfeita O ID 1.1 do controlo de segurança da Azure; Implementar o cluster Azure HDInsight numa rede virtual e proteger-se com um grupo de segurança de rede (NSG).

Existem várias dependências para a Azure HDInsight que requerem tráfego de entrada. O tráfego de gestão de entrada não pode ser enviado através de um dispositivo de firewall. Os endereços de origem para o tráfego de gestão necessário são conhecidos e publicados. Crie regras do Grupo de Segurança de Rede com esta informação para permitir o tráfego de apenas locais fidedignos, assegurando o tráfego de entrada para os clusters.

Para reduzir o risco de perda de dados através da exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight utilizando a Firewall Azure.

- [Como implementar o HDInsight dentro de uma rede virtual e garantir com um Grupo de Segurança de Rede](hdinsight-create-virtual-network.md)

- [Compreender as dependências de HDInsight e o uso de firewall](hdinsight-restrict-outbound-traffic.md)

- [Gestão de endereços IP do HDInsight](hdinsight-management-ip-addresses.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede (NSG) que estejam ligados à sub-rede onde o seu cluster Azure HDInsight é implantado. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Compreender e utilizar tags de serviço para Azure HDInsight](../virtual-network/network-security-groups-overview.md#service-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede relacionados com o seu cluster Azure HDInsight. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.HDInsight" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu cluster Azure HDInsight.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlos e políticas do Azure RBAC, e políticas, numa única definição de planta. Aplique facilmente o projeto em novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize Tags para grupos de segurança de rede (NSGs) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego que estejam associados ao seu cluster Azure HDInsight. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar a interface de linha de comando Azure PowerShell ou Azure (CLI) para procurar ou executar ações em recursos baseados nas suas Tags.

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as suas implementações Azure HDInsight. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Pode embarcar o seu cluster Azure HDInsight para o Azure Monitor para agregar dados de segurança gerados pelo cluster. Aproveite as consultas personalizadas para detetar e responder a ameaças no ambiente. 

- [Como embarcar um cluster Azure HDInsight para O Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Como criar consultas personalizadas para um cluster Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar o Monitor Azure para o cluster HDInsight, direcioná-lo para um espaço de trabalho Log Analytics. Isto registará informações relevantes do cluster e métricas de SO para todos os nós de cluster Azure HDInsight.

- [Como permitir a sessão de registo para o HDInsight Cluster](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Como consultar os registos HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: A bordo Azure HDInsight cluster para Azure Monitor. Certifique-se de que o espaço de trabalho Log Analytics utilizado tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.

- [Como embarcar num Cluster Azure HDInsight para Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: A bordo Azure HDInsight cluster para Azure Monitor. Certifique-se de que o espaço de trabalho Azure Log Analytics utilizado tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.

- [Como embarcar num Cluster Azure HDInsight para Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Utilize consultas de espaço de trabalho Azure Log Analytics para consultar os registos Azure HDInsight:

- [Como criar consultas personalizadas para clusters Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o espaço de trabalho Azure Log Analytics para monitorizar e alertar sobre atividades anómalas em registos de segurança e eventos relacionados com o seu cluster Azure HDInsight.

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Azure HDInsight vem com clamscan pré-instalado e habilitado para as imagens do nó de cluster, no entanto deve gerir o software e agregar/monitorizar manualmente quaisquer registos que o Clamscan produz.

- [Compreenda o Clamscan](./hdinsight-faq.md#security-and-certificates)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Implementar solução de terceiros para a sessão de registo de DNS.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Configurar manualmente o registo da consola numa base por nó.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha o registo da conta administrativa local que é criada durante o a provisionamento do cluster Azure HDInsight, bem como quaisquer outras contas que crie. Além disso, se a integração do Azure Ative Directory (Azure AD), a Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e, portanto, são questionáveis. Utilize o módulo Azure AD PowerShell para realizar consultas de adhoc para descobrir contas que são membros de grupos administrativos.

Além disso, pode utilizar recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como monitorizar a identidade e o acesso com o Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Ao providenciar um cluster, o Azure exige que crie novas palavras-passe para o portal web e acesso Secure Shell (SSH). Não existem senhas padrão para alterar, no entanto pode especificar diferentes palavras-passe para SSH e acesso ao portal web.

- [Como definir palavras-passe ao forer um cluster Azure HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Integrar a autenticação para o cluster Azure HDInsight com o Azure Ative Directory (Azure AD). Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas.

Além disso, pode utilizar recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

- [Como integrar a autenticação Azure HDInsight com Azure AD](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Como monitorizar a identidade e o acesso com o Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Utilize o Azure HDInsight ID Broker para iniciar sedutação nos clusters do Pacote de Segurança Empresarial (ESP) utilizando a autenticação multifactor, sem fornecer quaisquer palavras-passe. Se já assinou contrato com outros serviços Azure, como o portal Azure, pode iniciar sômposição no seu cluster Azure HDInsight com uma única experiência de sso de assinatura.

- [Como ativar o Corretor de ID Azure HDInsight](./domain-joined/identity-broker.md#enable-hdinsight-id-broker)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity and Access Management. Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurados podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters e executar grandes trabalhos de dados. Ao autenticar com autenticação multifactor ativada, os utilizadores serão desafiados a fornecer um segundo fator de autenticação.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com autenticação multifactor configurada para iniciar sessão e configurar os seus clusters Azure HDInsight e recursos conexos.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurados podem ser ligados a um domínio para que os utilizadores de domínio possam utilizar as suas credenciais de domínio para autenticar. Pode utilizar relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente Azure AD. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Azure Security Center](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Os clusters Azure HDInsight com o Pacote de Segurança Empresarial configurados podem ser ligados a um domínio para que os utilizadores de domínio possam utilizar as suas credenciais de domínio para autenticar. Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Os clusters Azure HDInsight com Pacote de Segurança Empresarial (ESP) configurados podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters.

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Como configurar pacote de segurança empresarial com serviços de domínio Ad AD Azure em Azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Utilize a autenticação do Azure Ative Directory (Azure AD) com o seu cluster Azure HDInsight. A Azure AD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso do utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Utilize registos de inscrição e auditoria do Azure Ative (Azure AD) para monitorizar as tentativas de acesso a contas desativadas; estes registos podem ser integrados em qualquer ferramenta siem/monitorização de terceiros.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores AZure, enviando os registos de auditoria e registos de login para um espaço de trabalho Azure Log Analytics. Configure os alertas desejados dentro do espaço de trabalho Azure Log Analytics.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Os clusters Azure HDInsight com Pacote de Segurança Empresarial (ESP) configurados podem ser ligados a um domínio para que os utilizadores de domínio possam usar as suas credenciais de domínio para autenticar com os clusters. Utilize o Azure Ative Directory (Azure AD) Deteções de Riscos e Proteção de Identidade para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores. Além disso, você pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não disponível; O Lockbox do cliente ainda não suportado para a Azure HDInsight.

- [Lista de serviços suportados pelo Bloqueio do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas em recursos relacionados com as suas implementações Azure HDInsight para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os clusters Azure HDInsight e quaisquer contas de armazenamento associadas devem ser separados por rede/sub-rede virtuais, marcados adequadamente e protegidos dentro de um grupo de segurança de rede (NSG) ou Azure Firewall. Os dados do cluster devem ser contidos numa conta de armazenamento Azure ou no Azure Data Lake Storage (Gen1 ou Gen2).

- [Escolha opções de armazenamento para o seu cluster Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Como garantir o armazenamento do Lago de Dados Azure](../data-lake-store/data-lake-store-security-overview.md)

- [Como garantir contas de armazenamento Azure](../storage/blobs/security-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Para clusters Azure HDInsight armazenando ou processando informações sensíveis, marque o cluster e recursos relacionados como sensíveis usando tags. Para reduzir o risco de perda de dados através da exfiltração, restringir o tráfego de rede de saída para clusters Azure HDInsight utilizando a Firewall Azure.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Como restringir o tráfego de saída para clusters Azure HDInsight com Azure Firewall](hdinsight-restrict-outbound-traffic.md)

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Certifique-se de que quaisquer clientes que se conectem ao seu cluster Azure HDInsight ou lojas de dados de cluster (Contas de Armazenamento Azure ou Azure Data Lake Storage Gen1/Gen2) são capazes de negociar TLS 1.2 ou maior. Os recursos da Microsoft Azure negociarão tLS 1.2 por padrão. 

- [Compreenda a encriptação de armazenamento do Lago de Dados Azure em trânsito](../data-lake-store/data-lake-store-security-overview.md)

- [Compreender encriptação da conta de armazenamento Azure em trânsito](../storage/blobs/security-recommendations.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos 

**Orientação**: Com o Azure HDInsight Enterprise Security Package (ESP), pode utilizar o Apache Ranger para criar e gerir políticas de controlo de acessos finos e de obfuscção de dados para os seus dados armazenados em ficheiros, pastas, bases de dados, tabelas e linhas/colunas. O administrador hadoop pode configurar o controlo de acesso baseado em funções (RBAC) para garantir a Hive Apache, HBase, Kafka e Spark usando esses plugins em Apache Ranger.

Configurar as políticas do RBAC com o Apache Ranger permite-lhe associar permissões com um papel na organização. Esta camada de abstração facilita a garantia de que as pessoas têm apenas as permissões necessárias para desempenhar as suas responsabilidades de trabalho.

- [Configurações de pacotes de segurança da empresa com Azure Ative Directory (Azure AD) Serviços de Domínio em HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Visão geral da segurança da empresa em Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Para clusters Azure HDInsight armazenando ou processando informações sensíveis, marque o cluster e recursos relacionados como sensíveis usando tags. As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Se utilizar a Base de Dados Azure SQL para armazenar metadados Apache Hive e Apache Oozie, certifique-se de que os dados SQL permanecem sempre encriptados. Para contas de armazenamento Azure e armazenamento de data lake (Gen1 ou Gen2), é aconselhável permitir que a Microsoft gere as suas chaves de encriptação, no entanto, tem a opção de gerir as suas próprias chaves.

- [Como gerir chaves de encriptação para contas de armazenamento Azure](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Como criar o Azure Data Lake Storage utilizando chaves de encriptação geridas pelo cliente](../data-lake-store/data-lake-store-get-started-portal.md)

- [Compreender encriptação para Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md#data-encryption)

- [Como configurar encriptação de dados transparentes para base de dados SQL utilizando chaves geridas pelo cliente](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Configurações de diagnóstico configuradas para contas de armazenamento Azure associadas a clusters Azure HDInsight para monitorizar e registar todas as operações CRUD contra dados de cluster. Ativar a auditoria de quaisquer Contas de Armazenamento ou Lojas data lake associadas ao cluster Azure HDInsight.

- [Como permitir a registo/auditoria adicional de uma Conta de Armazenamento Azure](../storage/common/manage-storage-analytics-logs.md)

- [Como permitir a exploração/auditoria adicional para o armazenamento do Lago de Dados Azure](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros.

Opcionalmente, se tiver uma subscrição de Plataforma De Gestão de Vulnerabilidades Rapid7, Qualys ou qualquer outra subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar ações de script para instalar agentes de avaliação de vulnerabilidade nos seus nós de cluster Azure HDInsight e gerir os nós através do respetivo portal.

- [Como instalar o agente Rapid7 manualmente](https://insightvm.help.rapid7.com/docs/install)

- [Como instalar o Agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Como usar as ações de script](hdinsight-hadoop-customize-cluster-linux.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Foram ativadas atualizações automáticas do sistema para imagens de nó de cluster, no entanto deve reiniciar periodicamente os nós de cluster para garantir que as atualizações são aplicadas.

Microsoft para manter e atualizar as imagens do nó de azure HDInsight base.

- [Como configurar o calendário de remendos de SISTEMAS para clusters HDInsight](hdinsight-os-patching.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Utilize ações de script ou outros mecanismos para corrigir os seus clusters Azure HDInsight. Os clusters recém-criados terão sempre as últimas atualizações disponíveis, incluindo os mais recentes patches de segurança.

- [Como configurar o calendário de remendos de SISTEMA para clusters Azure HDInsight baseados em Linux](hdinsight-os-patching.md)

- [Como usar as ações de script](hdinsight-hadoop-customize-cluster-linux.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros que tenha a capacidade de comparar digitalizações de vulnerabilidades ao longo do tempo. Se tiver uma subscrição Rapid7 ou Qualys, poderá utilizar o portal desse fornecedor para visualizar e comparar as verificações de vulnerabilidades de trás para a outra.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, sistema comum de pontuação de vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters Azure HDInsight, dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Defina a lista de recursos aprovados da Azure e software aprovado para os seus recursos de computação

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das suas subscrições. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Implementar uma solução de terceiros para monitorizar os nós de cluster para aplicações de software não aprovadas.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters Azure HDInsight, dentro das suas subscrições.  Remova quaisquer recursos não aprovados do Azure que descobrir. Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para remover ou alertar em software não aprovado.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para impedir a execução de software não autorizado.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Para obter mais informações, veja as seguintes referências:

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Para os nós de cluster Azure HDInsight, implemente uma solução de terceiros para impedir a execução de tipos de ficheiros não autorizados.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.HDInsight" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu cluster HDInsight.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Imagens do sistema operativo Azure HDInsight geridas e mantidas pela Microsoft. Cliente responsável pela implementação de configurações seguras para o sistema operativo dos seus nós de cluster.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor definições seguras para os seus clusters Azure HDInsight e recursos relacionados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Imagens do sistema operativo Azure HDInsight geridas e mantidas pela Microsoft. Cliente responsável pela implementação da configuração do estado de nível oss.

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

- [Tutorial de Azure Repos Git](/azure/devops/repos/git/gitworkflow)

- [Documentação de Azure Repos](/azure/devops/repos/index)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.HDInsight" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para manter o estado desejado para os sistemas operativos do nó de cluster.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.HDInsight" para criar políticas personalizadas para auditar ou impor a configuração do seu cluster HDInsight.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para monitorizar o estado dos seus sistemas operativos de nó de cluster.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Azure HDInsight inclui suporte Bring Your Own Key (BYOK) para Apache Kafka. Esta capacidade permite-lhe possuir e gerir as chaves usadas para encriptar dados em repouso.

Todos os discos geridos em Azure HDInsight estão protegidos com encriptação do serviço de armazenamento Azure (SSE). Por predefinição, os dados desses discos são encriptados utilizando as teclas geridas pela Microsoft. Se ativar a BYOK, fornece a chave de encriptação para o Azure HDInsight usá-lo e geri-lo usando o Azure Key Vault.

O Key Vault também pode ser utilizado com implementações Azure HDInsight para gerir chaves para armazenamento de clusters (Contas de Armazenamento Azure e Armazenamento de Dados Azure)

- [Como trazer a sua própria chave para Apache Kafka em Azure HDInsight](./disk-encryption.md)

- [Como gerir chaves de encriptação para contas de armazenamento Azure](../storage/common/customer-managed-keys-configure-key-vault.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Identidades geridas podem ser usadas em Azure HDInsight para permitir que os seus clusters acedam aos serviços de domínio do Azure Ative Directory (Azure AD), acedam ao Cofre da Chave Azure ou acedam a ficheiros em Azure Data Lake Storage Gen2.

- [Compreender identidades geridas com Azure HDInsight](hdinsight-managed-identities.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Se utilizar qualquer código relacionado com a sua implementação Azure HDInsight, poderá implementar o Scanner Credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação**: Azure HDInsight vem com clamscan pré-instalado e habilitado para as imagens do nó de cluster, no entanto deve gerir o software e agregar/monitorizar manualmente quaisquer registos que o Clamscan produz.

- [Compreenda o Clamscan para Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure, no entanto não funciona com conteúdo do cliente.

Pré-digitalize quaisquer ficheiros que sejam enviados para quaisquer recursos Azure relacionados com a sua implementação do cluster Azure HDInsight, tais como Armazenamento de Data Lake, Armazenamento de Blob, etc. A Microsoft não pode aceder aos dados dos clientes nestes casos.

- [Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Azure HDInsight vem com clamscan pré-instalado e ativado para as imagens do nó de cluster. O Clamscan realizará atualizações de motor e definição automaticamente, no entanto, a agregação e gestão de troncos terá de ser executada manualmente.

- [Compreenda o Clamscan para Azure Azure HDInsight](./hdinsight-faq.md#security-and-certificates)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Quando utilizar uma Conta de Armazenamento Azure para a loja de dados do cluster HDInsight, escolha a opção de despedimento adequada (LRS, ZRS, GRS, RA-GRS).  Ao utilizar uma Base de Dados Azure SQL para a loja de dados do cluster Azure HDInsight, configuure a replicação de geo-replicação ativa.

- [Como configurar a redundância de armazenamento para contas de armazenamento Azure](../storage/common/storage-redundancy.md)

- [Como configurar a redundância para a Base de Dados Azure SQL](../azure-sql/database/active-geo-replication-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Quando utilizar uma conta de armazenamento Azure para a loja de dados do cluster Azure HDInsight, escolha a opção de redundância adequada (LRS, ZRS, GRS, RA-GRS). Se utilizar o Azure Key Vault para qualquer parte da sua implantação Azure HDInsight, certifique-se de que as suas chaves estão apoiadas.

- [Escolha opções de armazenamento para o seu cluster Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Como configurar a redundância de armazenamento para contas de armazenamento Azure](../storage/common/storage-redundancy.md)

- [Como apoiar chaves do Cofre chave em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Se o Azure Key Vault estiver a ser utilizado com a sua implantação Azure HDInsight, teste a restauração de chaves geridas pelo cliente.

- [Como trazer a sua própria chave para Apache Kafka em Azure HDInsight](./disk-encryption.md)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Se o Cofre da Chave Azure estiver a ser utilizado com a sua implementação Azure HDInsight, ative a eliminação suave no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Como permitir o soft delete Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Desenvolva um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções do pessoal, bem como as fases de tratamento e gestão de incidentes, desde a deteção até à revisão pós-incidente. 

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Utilize o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que quando um recurso estiver comprometido, possa chegar imediatamente ao mesmo. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

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

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
