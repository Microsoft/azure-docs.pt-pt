---
title: Linha de segurança Azure para Azure DevTest Labs
description: A linha de base de segurança Azure DevTest Labs fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 27a0d5b809480b2ce4aff36c5acd43c149ed5bb3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562839"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Linha de segurança Azure para Azure DevTest Labs

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para a Azure DevTest Labs. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Azure DevTest Labs. Foram excluídos **controlos** não aplicáveis à Azure DevTest Labs.

Para ver como a Azure DevTest Labs mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança da Azure DevTest Labs](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Quando implementar recursos da Azure DevTest Labs, deve criar ou utilizar uma rede virtual existente. Certifique-se de que a rede virtual escolhida tem um grupo de segurança de rede aplicado às suas sub-redes e controlos de acesso à rede configurados específicos das portas e fontes fidedignas da sua aplicação. Quando os recursos de laboratório são configurados com uma rede virtual, não são publicamente endereçados e só podem ser acedidos a partir da rede virtual. Também pode optar por criar um laboratório isolado em rede, onde, a par de ambientes de laboratório, recursos laboratoriais como a Conta de Armazenamento e Cofres-Chave também serão completamente isolados e apenas acessíveis através de pontos finais especificados. 

Dependendo das suas necessidades organizacionais, utilize o serviço Azure Firewall para criar, impor e registar políticas de aplicação e conectividade de rede em subscrições e redes virtuais.

- [Como configurar uma rede virtual para a Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Como criar uma rede isolada de devTest Labs exemplo](network-isolation.md)

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: Implementar um grupo de segurança de rede na rede para a que os seus recursos Azure DevTest Labs são implantados. Ativar os registos de fluxo do grupo de segurança da rede nos seus grupos de segurança de rede para auditoria de tráfego.

Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Implementar firewall de aplicação web Azure (WAF) em frente a aplicações web críticas implementadas usando modelos de Gestor de Recursos Azure para inspeção adicional do tráfego de entrada. Ativar a definição de diagnóstico para WAF e ingerir registos numa conta de armazenamento, no Centro de Eventos ou no espaço de trabalho do Log Analytics.

- [Como implantar a Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: A implantação de uma Rede Virtual Azure (VNet) para um laboratório aumenta a segurança e o isolamento para o seu laboratório. Sub-redes, políticas de controlo de acessos e outras funcionalidades também ajudam a restringir o acesso. Quando implantado num VNet, o Azure DevTest Labs não é publicamente endereçado e só pode ser acedido a partir de máquinas virtuais e aplicações dentro do VNet.

Ativar a proteção DDoS Standard nas suas Redes Virtuais Azure para proteger contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP maliciosos conhecidos.

Implemente o Azure Firewall em cada um dos limites de rede da organização com a Threat Intelligence habilitada e configurada a "alertar e negar" para tráfego de rede malicioso. Utilize o Centro de Segurança Azure Apenas no Tempo O acesso à rede de configuração de NSGs para limitar a exposição dos pontos finais a endereços IP aprovados por um período limitado. Use o Hardening de Rede Adaptável do Centro de Segurança Azure para recomendar configurações NSG que limitem portas e IPs de origem com base na inteligência real de tráfego e ameaça.

- [Como configurar uma rede virtual para a Azure DevTest Labs](devtest-lab-configure-vnet.md)

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

- [Compreender o Hardenive de Rede Adaptável do Centro de Segurança Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Compreender o Centro de Segurança Azure Mesmo no Tempo Controlo de Acesso à Rede](../security-center/security-center-just-in-time.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Permitir a captura de pacotes do Network Watcher na sua rede virtual de laboratório para investigar atividades anómalas. 

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Utilize uma Firewall Azure implantada na sua rede virtual com a Threat Intelligence ativada. A filtragem baseada em inteligência Azure Firewall Threat pode alertar e negar tráfego de e para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. Se a sua organização necessitar de funcionalidades adicionais para além do que o Azure Firewall pode fornecer, selecione uma oferta apropriada do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil.

Implemente a solução de firewall à sua escolha em cada um dos limites de rede da sua organização para detetar e/ou negar tráfego malicioso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Ao trabalhar com ambientes baseados em recursos da DevTest Labs Azure Resource Manager que incluem aplicações web, implemente um Gateway de aplicação Azure com HTTPS/TLS habilitado para certificados fidedignos.

- [Como implementar o Gateway de Aplicações](../application-gateway/quick-create-portal.md)

- [Como configurar o Gateway de aplicações para utilizar HTTPS](../application-gateway/create-ssl-portal.md)

- [Compreenda o equilíbrio de carga da camada 7 com os gateways de aplicações web Azure](../application-gateway/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure configurados para os seus recursos DevTest Labs. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Também pode utilizar grupos de segurança de aplicações para ajudar a simplificar a configuração complexa de segurança. Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos.

- [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

- [Compreender e utilizar grupos de segurança de aplicações](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Definir e implementar configurações de segurança padrão para recursos de rede com a Política Azure.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlos de RBAC e políticas, numa única definição de planta. Pode aplicar o projeto a novas subscrições e afinar o controlo e a gestão através da versão.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos de rede associados à sua implantação Azure DevTest Labs de forma a organizá-las logicamente numa taxonomia. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas Tags.

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações dos recursos e detetar alterações nos seus recursos Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os recursos Azure. No entanto, pode gerir as definições de sincronização de tempo para os seus recursos de computação.

- [Sincronização de tempo para VMs do Windows em Azure](../virtual-machines/windows/time-sync.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho log Analytics, centro de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram feitas nas suas instâncias Azure DevTest Labs ao nível do avião de gestão. Utilizando dados de registo de atividades Azure, pode determinar "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) feitas ao nível do plano de gestão para as suas instâncias DevTest Labs.

- [Criar definições de diagnóstico para enviar registos e métricas da plataforma para destinos diferentes](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho log Analytics, centro de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram feitas nas suas instâncias Azure DevTest Labs ao nível do avião de gestão. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) feitas ao nível do plano de gestão para as suas instâncias DevTest Labs.

- [Criar definições de diagnóstico para enviar registos e métricas da plataforma para destinos diferentes](../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: As máquinas virtuais Azure DevTest Labs (VMs) são criadas e propriedade do cliente. Então, é responsabilidade da organização monitorizá-lo. Pode utilizar o Azure Security Center para monitorizar o sistema operativo computacional. Os dados recolhidos pelo Security Center do sistema operativo incluem o tipo e versão de SISTEMA, OS (Registos de Eventos do Windows), processos de execução, nome da máquina, endereços IP e registados no utilizador. O Agente Descodudo de Log também recolhe ficheiros de despejo de acidentes.

Para obter mais informações, veja os seguintes artigos:

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Compreender a recolha de dados do Centro de Segurança Azure](../security-center/security-center-enable-data-collection.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados aos seus casos Azure DevTest Labs de acordo com os regulamentos de conformidade da sua organização.

- [Para mais informações, consulte o seguinte artigo](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho do Log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados de registo de atividade que podem ter sido recolhidos para Azure DevTest Labs.

Para obter mais informações, veja os seguintes artigos:

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/essentials/diagnostic-settings.md)

- [Como recolher e analisar registos de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o espaço de trabalho Azure Log Analytics para monitorizar e alertar sobre atividades anómalas em registos de segurança e eventos relacionados com os seus Laboratórios Azure DevTest.

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para executar consultas ad-hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Funções de Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O Azure Ative Directory (Azure AD) não tem o conceito de palavras-passe padrão. Outros recursos Azure que requerem uma palavra-passe forçam uma senha a ser criada com requisitos de complexidade e um comprimento mínimo de senha, que diferem consoante o serviço. Você é responsável por aplicações de terceiros e serviços marketplace que podem usar senhas padrão.

A DevTest Labs não tem o conceito de senhas padrão.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição

- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

- [Funções de Azure DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: A DevTest Labs utiliza o serviço Azure Ative Directory (Azure AD) para gestão de identidade. Considere estes dois aspetos fundamentais quando dá aos utilizadores acesso a um ambiente baseado em DevTest Labs:
- Gestão de recursos: Fornece acesso ao portal Azure para gerir recursos (criar VMs, criar ambientes, iniciar, parar, reiniciar, excluir e aplicar artefactos, e assim por diante). A gestão de recursos é feita em Azure utilizando o controlo de acesso baseado em funções Azure (Azure RBAC). Atribui funções aos utilizadores e define permissões de recursos e de nível de acesso.
- Máquinas virtuais (nível de rede): Na configuração predefinida, os VMs utilizam uma conta de administração local. Se houver um domínio disponível (Azure Ative Directory Domain Services (Azure AD DS), um domínio no local ou um domínio baseado na nuvem), as máquinas podem ser unidas ao domínio. Os utilizadores podem então utilizar as suas identidades baseadas em domínios utilizando o artefacto de união de domínios para se conectarem às máquinas.

- [Arquitetura de referência para DevTest Labs](./devtest-lab-reference-architecture.md#architecture)

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAWs) com autenticação multifactor configurada para iniciar sessão e configurar recursos Azure. 

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

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerir os recursos do Azure apenas a partir de locais aprovados

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize revisões de acesso à identidade Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/overview-reports.md)

- [Como utilizar comentários de acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso ao Azure Ative Directory (Azure AD) em fontes de registo de Atividade, Auditoria e Evento de Risco, que lhe permitem integrar-se com qualquer ferramenta de Gestão de Informações de Segurança e Gestão de Eventos (SIEM) /Ferramenta de monitorização.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar alertas dentro do Log Analytics Workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de Proteção de Risco e Identidade Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Use o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso a laboratórios em Azure DevTest Labs.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Compreender papéis em DevTest Labs](devtest-lab-add-devtest-user.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações para instâncias da DevTest Labs e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Como criar alertas para eventos de registo de atividades da DevTest Labs](create-alerts.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (incluindo recursos da DevTest Labs) dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e pode enumerar todas as subscrições e recursos da Azure dentro das suas subscrições.

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizá-los logicamente de acordo com uma taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Etiquetas de configuração para DevTest Labs](devtest-lab-add-tag.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize etiquetas, grupos de gestão e assinaturas separadas, e laboratórios separados, se for caso disso, para organizar e rastrear laboratórios e recursos relacionados com o laboratório. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados rapidamente da subscrição.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar um laboratório usando devTest Labs](devtest-lab-create-lab.md)

- [Como criar e usar tags](devtest-lab-add-tag.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Criar um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as necessidades organizacionais. Como administrador de subscrição, também pode utilizar controlos de aplicações adaptativos, uma funcionalidade do Azure Security Center para o ajudar a definir um conjunto de aplicações que podem ser executadas em grupos configurados de máquinas de laboratório. Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux.

- [Como permitir o Controlo adaptativo da aplicação](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro das subscrições. Pode ajudar em ambientes de alta segurança, como os que têm contas de Armazenamento.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: A Azure Automation proporciona controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos. Como administrador de subscrição, pode utilizar o inventário Azure Virtual Machine para automatizar a recolha de informações sobre todo o software em VMs da DevTest Labs na sua subscrição. O nome do software, versão, editor e propriedades de tempo de atualização estão disponíveis a partir do portal Azure. Para ter acesso à data de instalação e outras informações, o cliente necessário para ativar o diagnóstico ao nível do hóspede e trazer os registos do Evento do Windows para um espaço de trabalho do Log Analytics.

Além de utilizar o Change Tracking para monitorização de aplicações de software, os controlos de aplicações adaptativos no Azure Security Center utilizam machine learning para analisar as aplicações em execução nas suas máquinas e criar uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo evitar que software indesejado seja utilizado no seu ambiente. Pode configurar o modo de auditoria ou impor o modo. O modo de auditoria apenas audita a atividade nos VM protegidos. O modo de aplicação aplica as regras e garante que as aplicações que não são permitidas a correr estão bloqueadas. 

- [Uma introdução à Automatização do Azure](../automation/automation-intro.md)

- [Como ativar o inventário Azure VM](../automation/automation-tutorial-installed-software.md)

- [Compreender controlos de aplicações adaptativos](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: A Azure Automation proporciona controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos. Como administrador de subscrição, poderá utilizar o Change Tracking para identificar todos os softwares instalados em VMs hospedados em DevTest Labs. Pode implementar o seu próprio processo ou utilizar a Configuração do Estado da Automação Azure para remover software não autorizado.

- [Uma introdução à Automatização do Azure](../automation/automation-intro.md)

- [Acompanhe as mudanças no seu ambiente com a solução Change Tracking](../automation/change-tracking/overview.md)

- [Visão geral da configuração do estado da Azure Automation](../automation/automation-dsc-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Como administrador de subscrição, pode utilizar os Controlos de Aplicação Adaptativa do Centro de Segurança Azure para garantir que apenas o software autorizado executa, e todo o software não autorizado está bloqueado de executar em VMs Azure hospedados em DevTest Labs.

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Material de referência:

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: O controlo de aplicações adaptativas é uma solução inteligente, automatizada e de ponta a ponta do Azure Security Center, que o ajuda a controlar quais aplicações podem ser executadas nas suas máquinas Azure e não-Azure (Windows e Linux), hospedadas na DevTest Labs. Note que precisa de ser um administrador de subscrição para configurar esta definição para os recursos computacional subjacentes hospedados na DevTest Labs. Implemente a solução de terceiros se esta definição não corresponder aos requisitos da sua organização.

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o acesso ao **Bloco** para a App de Gestão do **Azure** da Microsoft.

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts em recursos computacional

**Orientação**: Dependendo do tipo de scripts, pode utilizar configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts dentro dos VMs hospedados em DevTest Labs. Também pode utilizar controlos de aplicação adaptativos do Azure Security Center para garantir que apenas o software autorizado executa, e todo o software não autorizado está bloqueado de executar nos VMs Azure subjacentes.

- [Como controlar a execução do script PowerShell em Ambientes windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&view=powershell-7)

- [Como utilizar os controlos de aplicações adaptativos do Centro de Segurança Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: As aplicações de alto risco implementadas no seu ambiente Azure podem ser isoladas utilizando rede virtual, sub-rede, subscrições, grupos de gestão, e assim por diante. e suficientemente seguro com um Azure Firewall, Web Application Firewall (WAF) ou um grupo de segurança de rede (NSG).

- [Configure a rede virtual para DevTest Labs](devtest-lab-configure-vnet.md)

- [Visão geral do Azure Firewall](../web-application-firewall/overview.md)

- [Visão geral do Firewall de Aplicação Web](../virtual-network/network-security-groups-overview.md)

- [Descrição geral da segurança de rede](security-baseline.md)

- [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)

- [Guia de decisão de subscrição](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Use pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos Azure criados como parte da DevTest Labs. Também pode utilizar definições de Política Azure incorporadas.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo na Notação de Objetos JavaScript (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua organização.

Também pode utilizar recomendações do Azure Security Center como uma base de configuração segura para os seus recursos Azure.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Utilize recomendações do Azure Security Center para manter as configurações de segurança em todos os recursos computacional subjacentes criados como parte da DevTest Labs. Além disso, pode utilizar imagens personalizadas do sistema operativo ou configuração do Estado da Automação Azure ou artefactos da DevTest Labs para estabelecer a configuração de segurança do sistema operativo exigido pela sua organização.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

- [Visão geral da configuração do estado da Azure Automation](../automation/automation-dsc-overview.md)

- [Faça upload de um VHD e use-o para criar novos VMs windows em Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Criar um Linux VM a partir de um disco personalizado com o Azure CLI](../virtual-machines/linux/upload-vhd.md)

- [Criar e distribuir imagens personalizadas para vários Laboratórios DevTest](image-factory-save-distribute-custom-images.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Azure Policy **negar** e implementar regras **se não existirem** para impor definições seguras em todos os seus recursos Azure criados como parte da DevTest Labs. Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização.

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos recursos de computação Azure subjacentes criados como parte de um laboratório. Além disso, pode utilizar modelos de Gestor de Recursos Azure, imagens personalizadas do sistema operativo ou configuração do Estado de Automação Azure para manter a configuração de segurança do sistema operativo exigido pela sua organização. Também pode utilizar a solução de fábrica de imagem, que é uma solução de configuração como código que constrói e distribui automaticamente as imagens numa base regular com todas as configurações desejadas.

Além disso, as imagens de máquina virtual do Azure Marketplace publicadas pela Microsoft são geridas e mantidas pela Microsoft.

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

- [Visão geral da configuração do estado da Azure Automation](../automation/automation-dsc-overview.md)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [Como criar uma fábrica de imagem em DevTest Labs](image-factory-create.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize devOps Azure para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure e scripts de Configuração do Estado Desejado. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se estiverem integrados com Azure DevOps.

- [Tutorial de Azure Repos Git](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos](/azure/devops/organizations/security/about-permissions?preserve-view=true&tabs=preview-page&view=azure-devops)

- [Integração entre Azure DevTest Labs e fluxo de trabalho Azure DevOps](devtest-lab-dev-ops.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Se utilizar imagens personalizadas, utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para garantir que apenas os utilizadores autorizados possam aceder às imagens. Utilizando uma Galeria de Imagens Partilhadas, pode partilhar as suas imagens em laboratórios específicos que dela necessitem. Para imagens de contentores, guarde-as no Registo do Contentor Azure e utilize o Azure RBAC para garantir que apenas os utilizadores autorizados possam aceder às imagens.

- [Compreender Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Como configurar o Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Configurar uma Galeria de Imagem Partilhada para um DevTest Labs](configure-shared-image-gallery.md)

- [Compreender O RBAC de Azure para o Registo de Contentores](../container-registry/container-registry-roles.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para recursos Azure utilizando a Política Azure. Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure criados no âmbito da DevTest Labs. Você também pode fazer uso de definições políticas incorporadas relacionadas com os seus recursos específicos. Além disso, pode utilizar a Azure Automation para implementar alterações de configuração.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](../governance/policy/concepts/definition-structure.md#aliases)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Azure Automation State Configuration é um serviço de gestão de configuração para nós de configuração estatal desejada (DSC) em qualquer datacenter de nuvem ou no local. Pode facilmente embarcar máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado. Também pode escrever um artefacto personalizado que pode ser instalado em todas as máquinas de laboratório para garantir que seguem as políticas organizacionais. 

- [Máquinas de embarque para gestão por Azure Automation State Configuration](../automation/automation-dsc-onboarding.md)

- [Criação de artefactos personalizados para máquinas virtuais DavTest Labs](devtest-lab-artifact-author.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Azure Security Center para realizar análises de base para os seus Recursos Azure criados no âmbito da DevTest Labs. Além disso, utilize a Azure Policy para alertar e auditar as configurações de recursos do Azure.

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Utilize o Azure Security Center para efetuar as configurações de base para as definições de OS e Docker para contentores que são utilizados no seu laboratório.

- [Compreender as recomendações do contentor do Centro de Segurança do Azure](../security-center/container-security.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem.

- [Configurar identidade gerida para implantar ambientes do Gestor de Recursos Azure em Laboratórios DevTest](use-managed-identities-environments.md)

- [Configurar identidade gerida para implantar máquinas virtuais em Laboratórios DevTest](enable-managed-identities-lab-vms.md)

- [Como criar um cofre chave](../key-vault/general/quick-create-portal.md)

- [Como fornecer a autenticação do Cofre-Chave com uma identidade gerida](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Configurar identidade gerida para implantar ambientes do Gestor de Recursos Azure em Laboratórios DevTest](use-managed-identities-environments.md)

- [Configurar identidade gerida para implantar máquinas virtuais em Laboratórios DevTest](enable-managed-identities-lab-vms.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Utilizar software antimalware gerido centralmente

**Orientação**: Utilize o Microsoft Antimalware para o Azure monitorizar e defender continuamente os seus recursos. Para o Linux, utilize a solução antimalware de terceiros.  Além disso, utilize a deteção de ameaças do Azure Security Center para detetar malware enviado para contas de armazenamento. 

- [Como configurar o Microsoft Antimalware para o Azure](../security/fundamentals/antimalware.md) 

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, O Serviço de Aplicações Azure hospedado num laboratório), no entanto, não funciona com o seu conteúdo. 

Ficheiros pré-digitalização enviados para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, e assim por diante. 

Utilize a deteção de ameaças do Azure Security Center para os serviços de dados para detetar malware enviado para contas de armazenamento. 

- [Compreenda o Microsoft Antimalware para O Azure](../security/fundamentals/antimalware.md) 

- [Compreenda a deteção de ameaças do Azure Security Center para serviços de dados](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Assegurar que o software e assinaturas antimalware sejam atualizados

**Orientação**: Quando implementado, o Microsoft Antimalware for Azure instalará automaticamente as atualizações mais recentes de assinatura, plataforma e motor por predefinição. Siga as recomendações no Azure Security Center: "Compute &amp; Apps" para garantir que todos os pontos finais para os recursos de computação subjacentes da DevTest Labs estão atualizados com as assinaturas mais recentes. O Sistema operativo Windows pode ser ainda protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de Proteção avançada de ameaças do Microsoft Defender que se integra com o Azure Security Center.

- [Como implementar o Microsoft Antimalware para o Azure](../security/fundamentals/antimalware.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Atualmente, a Azure DevTest Labs não suporta cópias de segurança e instantâneos VM. No entanto, pode ativar e configurar o Azure Backup nos VMs Azure subjacentes hospedados na DevTest Labs. E também pode configurar o período de frequência e retenção desejado para backups automáticos, desde que tenha acesso adequado aos recursos computatórios subjacentes. 

- [Uma visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)

- [Apoie um VM Azure a partir das definições de VM](../backup/backup-azure-vms-first-look-arm.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Atualmente, a Azure DevTest Labs não suporta cópias de segurança e instantâneos VM. No entanto, pode criar instantâneos dos seus VMs Azure subjacentes alojados nos Laboratórios DevTest ou nos discos geridos ligados a essas instâncias usando PowerShell ou REST APIs desde que tenha acesso adequado aos recursos computacional subjacentes. Também pode fazer o back-up de quaisquer chaves geridas pelo cliente dentro do Azure Key Vault.

Ativar o Azure Backup nos VMs Azure alvo e nos períodos de frequência e retenção desejados. Inclui cópia de segurança completa do estado do sistema. Se estiver a utilizar a encriptação do disco Azure, a cópia de segurança do Azure VM lida automaticamente com a cópia de segurança das teclas geridas pelo cliente.

- [Recue em VMs Azure que usam encriptação](../backup/backup-azure-vms-encryption.md)

- [Visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)

- [Uma visão geral do backup Azure VM](../backup/backup-azure-vms-introduction.md)

- [Como apoiar as chaves do Cofre chave em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Garantir a capacidade de efetuar periodicamente a restauração de dados de conteúdos dentro da Cópia de Segurança Azure. Se necessário, teste a restauração do conteúdo para uma rede virtual isolada ou subscrição. Além disso, teste restauração de chaves geridas pelo cliente.

Se estiver a utilizar a encriptação do disco Azure, pode restaurar o Azure VM com as teclas de encriptação do disco. Ao utilizar a encriptação do disco, pode restaurar o Azure VM com as teclas de encriptação do disco.

- [Recue em VMs Azure que usam encriptação](../backup/backup-azure-vms-encryption.md)

- [Como recuperar ficheiros da cópia de segurança da Azure VM](../backup/backup-azure-restore-files-from-vm.md)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Como fazer o back up e restaurar um VM encriptado](../backup/backup-azure-vms-encryption.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Quando faz cópia de segurança gerida com backup Azure, os VMs são encriptados em repouso com encriptação do serviço de armazenamento (SSE). O Azure Backup também pode fazer backup de VMs Azure que são encriptados usando Azure Disk Encryption. A encriptação do disco Azure integra-se com chaves de encriptação BitLocker (BEKs), que são salvaguardadas num cofre-chave como segredos. A encriptação do disco Azure também se integra com chaves de encriptação chave Azure Key Vault (KEKs). Ativar Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Excluir suave para VMs](../backup/soft-delete-virtual-machines.md)

- [Descrição geral da eliminação recuperável do Azure Key Vault](../key-vault/general/soft-delete-overview.md)

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

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque subscrições usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  É sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde o incidente ocorreu. 

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e, em seguida, reveja o seu plano de resposta conforme necessário. 

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação contínua para ajudar a identificar riscos para os recursos da Azure. A exportação contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização de fluxos de trabalho Azure Security Center para acionar automaticamente respostas a alertas de segurança e recomendações para proteger os seus recursos Azure. 

- [Como configurar a automatização do fluxo de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

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
