---
title: Linha de base de segurança Azure para Service Bus
description: A linha de base de segurança do Service Bus fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b3a67fc06594a4782ba55c17ab36c0bad9cf2a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574931"
---
# <a name="azure-security-baseline-for-service-bus"></a>Linha de base de segurança Azure para Service Bus

A Linha de Base de Segurança Azure para o Service Bus contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação. A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview-v1.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas. Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

Para ver como o Service Bus mapeia completamente para o Azure Security Benchmark, consulte o [ficheiro de mapeamento de base de segurança do Service Bus completo.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais 

**Orientação**: A integração do Service Bus com o serviço Azure Private Link permite um acesso privado seguro às capacidades de mensagens a partir de cargas de trabalho, tais como máquinas virtuais que estão ligadas a redes virtuais. Crie uma ligação de ponto final privado ao seu espaço de nomes de Service Bus. O ponto final privado utiliza um endereço IP privado a partir da sua rede virtual, efetivamente trazendo o serviço para a sua rede virtual. Todo o tráfego para o serviço pode ser encaminhado através desse ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos.

Também pode proteger o seu espaço de nomes Azure Service Bus utilizando firewalls. A Azure Service Bus suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Pode definir regras de firewall utilizando o portal Azure, os modelos Azure Resource Manager ou através do Azure CLI ou Azure PowerShell.

- [Permitir o acesso aos espaços de nomes do Azure Service Bus através de pontos finais privados](private-link-service.md)

- [Permitir o acesso ao espaço de nomes do Azure Service Bus a partir de endereços ou intervalos IP específicos](service-bus-ip-filtering.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Se utilizar máquinas virtuais Azure para aceder às suas entidades de Service Bus, ative os registos de fluxo do grupo de segurança da rede (NSG) e envie registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede. 

Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os seus recursos de Service Bus em Azure.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative o DDoS Protection Standard nas redes virtuais associadas aos espaços de nome do seu Service Bus para se proteger contra ataques de negação de serviço distribuídos (DDoS). Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Inteligência integrada do Centro de Segurança Azure](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Se utilizar máquinas virtuais Azure para aceder às suas entidades de Service Bus, pode utilizar a captura de pacotes network Watcher para investigar atividades anómalas.

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Se utilizar máquinas virtuais Azure para aceder às suas entidades de Service Bus, selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil. Se a deteção e/ou prevenção de intrusões com base na inspeção de carga útil não for necessária para a sua organização, poderá utilizar a funcionalidade de firewall incorporada da Azure Service Bus. Pode limitar o acesso ao seu espaço de nomes de Service Bus para uma gama limitada de endereços IP ou um endereço IP específico utilizando as regras de Firewall.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como adicionar uma regra de firewall nos espaços de nome do Service Bus para um endereço IP especificado](service-bus-ip-filtering.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em Grupos de Segurança de Rede ou Firewall Azure que filtram o tráfego de e para os recursos do Service Bus. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ServiceBus) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. 

- [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus espaços de nomes do Azure Service Bus com a Azure Policy. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.ServiceBus" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos espaços de nomes do Service Bus. Você também pode fazer uso de definições políticas incorporadas relacionadas com a Azure Service Bus, tais como:

- Service Bus deve usar um ponto final de serviço de rede virtual
- Os registos de diagnóstico no Service Bus devem ser ativados

Também pode construir definições de política personalizadas se as definições incorporadas não corresponderem às necessidades da sua organização.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Política Azure Built-in para o espaço de nomes de autocarros de serviço](./policy-reference.md#azure-service-bus-messaging)

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para redes virtuais e outros recursos relacionados com a segurança da rede e o fluxo de tráfego que estejam associados aos seus espaços de nomes de Service Bus. Para regras individuais do grupo de segurança da rede, utilize o campo "Descrição" para especificar a necessidade, duração e outras informações descritivas para quaisquer regras que permitam o tráfego de ou para uma rede associada aos seus espaços de nome service Bus. 

Utilize qualquer uma das definições de política incorporadas do Azure relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados. 

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas Tags. 

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md) 

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md) 

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com o Azure Service Bus. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pelos recursos do Service Bus. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, configurar contas de Armazenamento Azure para armazenamento de longo prazo ou arquivo. Também pode configurar registos relacionados com o Service Bus para serem enviados para a Azure Sentinel ou para um SIEM de terceiros.

- [Como configurar definições de diagnóstico para o autocarro de serviço da Azure](service-bus-diagnostic-logs.md)

- [Compreender o Registo de Atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative as definições de diagnóstico para o seu espaço de nomes Azure Service Bus. A Azure Service Bus suporta atualmente registos de atividade e de diagnóstico ou operacionais. Os registos de atividade têm informações sobre operações feitas num trabalho. Os registos de diagnóstico fornecem informações mais ricas sobre operações e ações que são feitas contra o seu espaço de nome utilizando a API, ou através de clientes de gestão usando o Idioma SDK. Especificamente, estes registos captam o tipo de operação, incluindo a criação de filas, os recursos utilizados e o estado da operação.

- [Como ativar definições de diagnóstico para ônibus de serviço Azure](service-bus-diagnostic-logs.md)

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/essentials/activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desaveja o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização para capturar e rever incidentes relacionados com o Service Bus.

- [Como definir parâmetros de retenção de registos para espaços de trabalho do Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados relacionados com as suas entidades de Service Bus. Utilize o Azure Monitor para rever registos e efetuar consultas em dados de registo relacionados com o Service Bus.

- [Para mais informações sobre o espaço de trabalho Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança Azure com o log analytics espaço de trabalho para monitorizar e alertar sobre a atividade anómala encontrada em registos de segurança e eventos. Em alternativa, também pode ativar e a bordo dados para a Azure Sentinel.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O controlo de acesso baseado em funções (Azure RBAC) permite-lhe gerir o acesso aos recursos Azure através de atribuições de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções pré-definidas para Service Bus, estas funções podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure.

- [Papéis incorporados para a Azure Service Bus](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole) 

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O acesso do avião ao Service Bus é controlado através do Azure Ative Directory (Azure AD). A Azure AD não tem o conceito de senhas padrão.

O acesso de um avião de dados ao Service Bus é controlado através do Azure AD utilizando identidades geridas, registos de aplicações ou assinaturas de acesso partilhado. As assinaturas de acesso partilhado são usadas pelos clientes que se conectam ao seu espaço de nomes do Service Bus e podem ser regeneradas a qualquer momento.

- [Compreender assinaturas de acesso compartilhado para Service Bus](service-bus-sas.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Também pode construir definições de política personalizadas se as definições incorporadas não corresponderem às necessidades da sua organização.

- [Como usar o Centro de Segurança Azure para monitorizar a identidade e o acesso](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: O Microsoft Azure fornece uma gestão integrada do controlo de acessos para recursos e aplicações com base no Azure Ative Directory (Azure AD). Uma vantagem fundamental de usar o Azure AD com a Azure Service Bus é que já não precisa de armazenar as suas credenciais no código. Em vez disso, pode solicitar um token de acesso OAuth 2.0 a partir da plataforma Microsoft Identity. O nome do recurso para solicitar um token é https://azure.microsoft.com/services/service-bus/ . A Azure AD autentica o principal de segurança (um utilizador, grupo ou principal de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve um sinal de acesso à aplicação, e a aplicação pode então utilizar o token de acesso para autorizar o pedido aos recursos da Azure Service Bus.

- [Como autenticar uma aplicação com Azure AD para aceder aos recursos do Service Bus](authenticate-application.md)

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ative Directory Multi-Factor Authentication (MFA) e siga as recomendações de identidade e gestão do Centro de Segurança Azure para ajudar a proteger os recursos habilitados para o Service Bus.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos habilitados para o Serviço bus.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory e monitoriem para detetar quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize localizações nomeadas a Azure AD para permitir o acesso apenas a agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar a Azure AD nomeou localizações](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para recursos Azure, como o Service Bus. Isto permite que o controlo de acesso baseado em funções (Azure RBAC) para recursos sensíveis administrativos.

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizar o acesso aos recursos do Service Bus utilizando o Azure Ative Directory](authenticate-application.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Adicionalmente, rode regularmente a assinatura de acesso partilhado do seu service bus.

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

- [Compreender assinaturas de acesso compartilhado para service bus namespace](service-bus-sas.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a atividade de login, auditoria e log de eventos de risco da Azure Ative, que lhe permitem integrar-se com a Azure Sentinel ou com uma ferramenta SIEM de terceiros.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Em seguida, no Azure Monitor pode configurar os alertas de registo desejados para determinadas ações que ocorrem nos registos.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Autorizar o acesso aos recursos do Service Bus utilizando o Azure Ative Directory](authenticate-application.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize as funcionalidades de Proteção de Identidade do Azure Ative Directory e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com os recursos ativados pelo seu Service Bus. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Atualmente não disponível; O Lockbox do cliente ainda não está suportado para o Service Bus.

- [Lista de serviços suportados pelo Cliente Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas em recursos relacionados com o seu Service Bus para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar assinaturas separadas e grupos de gestão para desenvolvimento, teste e produção. Os espaços de nome do Service Bus devem ser separados por redes virtuais com pontos finais privados configurados e marcados adequadamente.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e utilizar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ao utilizar máquinas virtuais para aceder às suas entidades de Service Bus, utilize redes virtuais, pontos finais privados, firewall de Service Bus, grupos de segurança de rede e tags de serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gere a infraestrutura subjacente à Azure Service Bus e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Configure as regras de firewall IP para espaços de nomes de autocarros de serviço Azure](service-bus-ip-filtering.md)

- [Permitir o acesso ao espaço de nomes do Azure Service Bus a partir de redes virtuais específicas](private-link-service.md)

- [Permitir o acesso aos espaços de nomes do Azure Service Bus através de pontos finais privados](private-link-service.md)

- [Compreender grupos de segurança de rede e tags de serviço](../virtual-network/network-security-groups-overview.md)

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: A Azure Service Bus aplica por defeito as comunicações encriptadas TLS. As versões TLS 1.0, 1.1 e 1.2 estão atualmente suportadas. No entanto, os TLS 1.0 e 1.1 estão num caminho para a depreciação em toda a indústria, por isso utilize o TLS 1.2 ou mais recente, sempre que possível.

- [Para compreender as funcionalidades de segurança do Service Bus, consulte a segurança da Rede](network-security.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Service Bus. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: A Azure Service Bus suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos a entidades de Service Bus. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (Azure RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador ou um diretor de serviço de aplicação.

- [Compreenda a Azure RBAC e as funções disponíveis para a Azure Service Bus](authenticate-application.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: A Azure Service Bus suporta a opção de encriptar dados em repouso com as teclas geridas pela Microsoft ou com as teclas geridas pelo cliente. Esta funcionalidade permite-lhe criar, rodar, desativar e revogar o acesso às chaves geridas pelo cliente que são usadas para encriptar os dados do Azure Service Bus em repouso.

- [Como configurar chaves geridas pelo cliente para encriptar o Azure Service Bus](configure-customer-managed-key.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações nas instâncias de produção da Azure Service Bus e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descubra todos os recursos (incluindo espaços de nomes do Azure Service Bus) dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize etiquetas, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear espaços de nomes do Azure Service Bus e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Azure Resource Graph para consultar e descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Também pode construir definições de política personalizadas se as definições incorporadas não corresponderem às necessidades da sua organização.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para as suas implementações de Ônibus de serviço Azure. Você também pode usar definições de política incorporada para Azure Service Bus, tais como:

- Os registos de diagnóstico no Service Bus devem ser ativados
- O Service Bus deve utilizar um ponto final de serviço de rede virtual para limitar o tráfego de rede às suas redes privadas.

Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.ServiceBus" para criar políticas personalizadas para auditar ou impor configurações.

- [Políticas Azure Built-in para Service Bus ](./policy-reference.md)

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor definições seguras em todos os recursos ou aplicações ativados pelo Service Bus.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Para mais informações sobre os Efeitos políticos do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.ServiceBus" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.ServiceBus" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Azure Policy [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para as suas implementações de Azure Service Bus e recursos relacionados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usadas para aceder às suas entidades de Service Bus, utilize uma Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir a gestão de assinaturas de acesso partilhado para as suas implementações do Azure Service Bus. Certifique-se de que o cofre de chaves está ativado.

- [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos recursos do Service Bus](service-bus-managed-service-identity.md)

- [Configure chaves geridas pelo cliente para o Service Bus](configure-customer-managed-key.md)

- [Como criar um Cofre-Chave](../key-vault/general/quick-create-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder às suas entidades de Service Bus, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger o Azure Service Bus. Certifique-se de que o cofre de chaves está ativado.

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo Azure Key Vault, sem quaisquer credenciais no seu código.

- [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos de Autocarros de Serviço](service-bus-managed-service-identity.md)

- [Configure chaves geridas pelo cliente para o Service Bus](configure-customer-managed-key.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte o [Azure Security Benchmark: Malware defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como Azure Service Bus, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, etc. A Microsoft não pode aceder aos seus dados nestes casos.

O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure, no entanto não é executado em conteúdo sonoro do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Configurar a recuperação de geo-desastres para o Azure Service Bus. Quando regiões ou centros de dados inteiros do Azure (se não forem utilizadas zonas de disponibilidade) experimentam tempo de inatividade, é fundamental que o processamento de dados continue a funcionar numa região ou centro de dados diferentes. Como tal, a recuperação de geo-desastres e a geo-replicação são características importantes para qualquer empresa. A Azure Service Bus suporta a recuperação de geo-desastres e a geo-replicação, ao nível do espaço de nome.

- [Compreenda a recuperação de geo-desastres para a Azure Service Bus](service-bus-geo-dr.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: A azure Service Bus fornece encriptação de dados em repouso com Azure Storage Service Encryption (Azure SSE). O Service Bus conta com o Azure Storage para armazenar os dados e, por padrão, todos os dados que são armazenados com o Azure Storage são encriptados utilizando as teclas geridas pela Microsoft. Se utilizar o Cofre de Chaves Azure para armazenar chaves geridas pelo cliente, certifique-se de cópias de segurança automáticas regulares das suas Chaves.

Certifique-se de cópias de segurança automáticas regulares dos seus Segredos do Cofre chave com o seguinte comando PowerShell: Backup-AzKeyVaultSecret

- [Como configurar chaves geridas pelo cliente para encriptar os dados do Azure Service Bus em repouso](configure-customer-managed-key.md)

- [Como apoiar segredos do Cofre de Chaves](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste a restauração de chaves geridas pelo cliente com suporte para encriptar os dados do Service Bus.

- [Como configurar chaves geridas pelo cliente para encriptar os dados do Azure Service Bus em repouso](configure-customer-managed-key.md)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Ative a eliminação suave no Cofre da Chave para proteger as chaves contra a eliminação acidental ou maliciosa. O Azure Service Bus requer chaves geridas pelo cliente para ter Soft Delete e Não Purgar configurados.

- [Como permitir a eliminação suave no Cofre de Chaves](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Configurar um cofre com chaves](../event-hubs/configure-customer-managed-key.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Desenvolva um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções do pessoal, bem como as fases de tratamento e gestão de incidentes, desde a deteção até à revisão pós-incidente. 

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Utilize o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque subscrições usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis. É sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde o incidente ocorreu. 

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Publicação do NIST: Guia para Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação contínua para ajudar a identificar riscos para os recursos da Azure. A exportação contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação:** 

Utilize a funcionalidade de automatização de fluxos de trabalho Azure Security Center para desencadear automaticamente respostas a alertas de segurança e recomendações para proteger os seus recursos Azure. 

- [Como configurar a automatização do fluxo de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)