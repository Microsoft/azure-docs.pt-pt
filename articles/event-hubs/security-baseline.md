---
title: Linha de base de segurança Azure para Centros de Eventos
description: A linha de base de segurança Event Hubs fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1e1a8cb24b47277b38bd2d64b733a6f28ee8acff
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742514"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Linha de base de segurança Azure para Centros de Eventos

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) para os Centros de Eventos. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável aos Centros de Eventos. Foram excluídos **os controlos** não aplicáveis aos Centros de Eventos.

 
Para ver como os Centros de Eventos mapeiam completamente o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança do Event Hubs](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A integração dos Hubs de Eventos Azure com pontos finais de serviço de rede virtual permite o acesso seguro às capacidades de mensagens a partir de cargas de trabalho, tais como máquinas virtuais que estão ligadas a redes virtuais, com a via de tráfego da rede a ser assegurada em ambas as extremidades.

Uma vez ligado a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respetivo espaço de nomes do Event Hubs já não aceita tráfego de qualquer lugar, mas sub-redes autorizadas em redes virtuais. Do ponto de vista da rede virtual, ligar o seu espaço de nomes Event Hubs a um ponto final de serviço configura um túnel de rede isolado da sub-rede de rede virtual ao serviço de mensagens. 

Também pode criar um ponto final privado, que é uma interface de rede que o liga de forma privada e segura ao serviço Event Hubs utilizando o serviço Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir da sua rede virtual, efetivamente trazendo o serviço para a sua rede virtual. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. 

Também pode proteger o seu espaço de nomeS Azure Event Hubs utilizando firewalls. O Event Hubs suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Pode definir regras de firewall utilizando o portal Azure, os modelos Azure Resource Manager ou através do Azure CLI ou Azure PowerShell.

- [Como utilizar pontos finais de serviço de rede virtual com hubs de eventos Azure](event-hubs-service-endpoints.md)

- [Integre os Hubs de Eventos Azure com Azure Private Link](private-link-service.md)

- [Como configurar as regras de firewall IP para espaços de nomes Azure Event Hubs](event-hubs-ip-filtering.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Utilize o Centro de Segurança Azure e siga as recomendações de proteção da rede para ajudar a proteger os recursos dos seus Centros de Eventos em Azure. Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, ative os registos de fluxo do grupo de segurança da rede e envie registos para uma conta de armazenamento para auditoria de tráfego.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative o DDoS Protection Standard nas redes virtuais associadas aos centros de eventos para se proteger contra ataques de negação de serviço distribuídos (DDoS). Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Para mais informações sobre o Centro de Segurança Azure Integrado De Ameaças](/azure/security-center/security-center-alerts-service-layer)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, ative os registos de fluxo do grupo de segurança da rede e envie registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo de grupos de segurança de rede para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Se necessário para investigar a atividade anómala, ative a captura de pacotes do Observador de Rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil. Se a deteção e/ou prevenção de intrusões com base na inspeção de carga útil não for necessária para a sua organização, poderá utilizar a funcionalidade de firewall incorporada do Azure Event Hubs. Pode limitar o acesso ao seu espaço de nomes de Centros de Eventos para uma gama limitada de endereços IP ou um endereço IP específico utilizando as regras de Firewall.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como adicionar uma regra de firewall em Centros de Eventos para um endereço IP especificado](event-hubs-ip-filtering.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus espaços de nome Azure Event Hubs com Azure Policy. Utilize pseudónimos da Azure Policy nos espaços de nomes **Microsoft.EventHub** e **Microsoft.Network** para criar definições de política personalizadas para auditar ou impor a configuração de rede dos espaços de nomes dos Seus Centros de Eventos. Também pode utilizar definições políticas incorporadas relacionadas com os Hubs de Eventos Azure, tais como:

- O Event Hub deve utilizar um ponto final de serviço de rede virtual.

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Política Azure Incorporada para o espaço de nomes de centros de eventos](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub)

- [Amostras da Política Azure para networking](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para redes virtuais e outros recursos relacionados com a segurança da rede e o fluxo de tráfego que estejam associados aos seus centros de eventos.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com os Hubs de Eventos Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Dentro do Azure Monitor, configurar registos relacionados com os centros de eventos dentro das definições de diagnóstico de Log e Event Hub para enviar registos para um espaço de trabalho do Log Analytics a ser consultado ou numa conta de armazenamento para armazenamento de arquivo a longo prazo.

- [Como configurar definições de diagnóstico para centros de eventos Azure](event-hubs-diagnostic-logs.md)

- [Compreender o Registo de Atividades do Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative as definições de diagnóstico para o seu espaço de nomes Azure Event Hubs. Existem três categorias de definições de Diagnóstico para Hubs de Eventos Azure: Registos de Arquivo, Registos Operacionais e Registos De Escala Automática. Permitir que os Registos Operacionais capturem informações sobre o que está a acontecer durante as operações do Event Hubs, especificamente, o tipo de operação, incluindo a criação de centros de eventos, os recursos utilizados e o estado da operação.

Além disso, pode ativar as definições de diagnóstico de registo de registo de atividade do Azure e enviá-las para uma Conta de Armazenamento Azure, centro de eventos ou um espaço de trabalho Log Analytics. Os registos de atividade fornecem informações sobre as operações que foram realizadas nos seus Hubs de Eventos Azure e outros recursos. Utilizando registos de atividade, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) tomadas nos seus espaços de nomes Azure Event Hubs.

- [Como ativar definições de diagnóstico para centros de eventos Azure](event-hubs-diagnostic-logs.md)

- [Como ativar definições de diagnóstico para registo de atividades azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.EventHub**:

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desaveja o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização para capturar e rever incidentes relacionados com o centro de eventos.

- [Como definir parâmetros de retenção de registos para espaços de trabalho do Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados relacionados com os seus centros de eventos. Utilize o Azure Monitor's Log Analytics para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para o Azure Sentinel ou uma solução de informação de sistema de terceiros e gestão de eventos.

- [Para mais informações sobre o espaço de trabalho Log Analytics](/azure/azure-monitor/log-query/get-started-portal)

- [Como realizar consultas personalizadas no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Dentro do Monitor Azure, configurar registos relacionados com os Hubs de Eventos Azure dentro do Registo de Atividades e configurações de diagnóstico de Centros de Eventos para enviar registos para um espaço de trabalho log analytics para ser consultado ou numa conta de armazenamento para armazenamento de arquivo a longo prazo. Use o espaço de trabalho do Log Analytics para criar alertas para a atividade anómala encontrada em registos de segurança e eventos.

Em alternativa, pode ativar e a bordo dados para a Azure Sentinel. 

- [Compreender o Registo de Atividades Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Como configurar definições de diagnóstico para centros de eventos Azure](event-hubs-diagnostic-logs.md)

- [Como alertar os dados do registo do espaço de trabalho do Log Analytics](/azure/azure-monitor/learn/tutorial-response)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O acesso do avião ao Event Hubs é controlado através do Azure Ative Directory (Azure AD). A Azure AD não tem o conceito de senhas padrão.

O acesso de um avião de dados aos Centros de Eventos é controlado através do Azure AD com identidades geridas ou registos de aplicações, bem como assinaturas de acesso partilhada. As assinaturas de acesso partilhado são usadas pelos clientes que se conectam aos seus centros de eventos e podem ser regeneradas a qualquer momento.

- [Compreender assinaturas de acesso compartilhado para Centros de Eventos](authenticate-shared-access-signature.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição

- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Informações adicionais estão disponíveis nos links referenciados.

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: O Microsoft Azure fornece uma gestão integrada do controlo de acessos para recursos e aplicações com base no Azure Ative Directory (Azure AD). Uma vantagem fundamental de usar a Azure AD com Azure Event Hubs é que já não precisa de armazenar as suas credenciais no código. Em vez disso, pode solicitar um token de acesso OAuth 2.0 a partir da plataforma Microsoft Identity. O nome do recurso para solicitar um token é https: \/ /eventhubs.azure.net/. A Azure AD autentica o principal de segurança (um utilizador, grupo ou principal de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve um sinal de acesso à aplicação, e a aplicação pode então utilizar o token de acesso para autorizar o pedido aos recursos do Azure Event Hubs.

- [Como autenticar uma aplicação com Azure AD para aceder aos recursos do Event Hubs](authenticate-application.md)

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactorA Azure Ative (Azure AD) e seguir as recomendações de identidade do Centro de Segurança Azure e de gestão de acessos para ajudar a proteger os seus recursos habilitados para o Centro de Eventos.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multifactor configurada para iniciar sessão e configurar recursos habilitados para o Centro de Eventos.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/security/compass/privileged-access-devices)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. Para obter registos adicionais, envie alertas de deteção de risco do Azure Security Center para o Azure Monitor e configuure alertas/notificações personalizados utilizando grupos de ação.

- [Compreender deteções de risco Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Como configurar grupos de ação para alerta e notificação personalizados](/azure/azure-monitor/platform/action-groups)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização para recursos Azure, tais como Os Centros de Eventos. Isto permite que o controlo de acesso baseado em funções (Azure RBAC) para recursos sensíveis administrativos.

- [ Como criar e configurar uma instância AD Azure](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizar o acesso aos recursos do Event Hubs utilizando a Azure AD](authorize-access-azure-active-directory.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Além disso, rode regularmente as assinaturas de acesso partilhado dos Seus Centros de Eventos.

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring/)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

- [Compreender assinaturas de acesso partilhado para Centros de Eventos](authenticate-shared-access-signature.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a atividade de login, auditoria e registo de eventos de risco da Azure Ative, que lhe permitem integrar-se com qualquer solução de informação de sistema de terceiros e de gestão de eventos ou uma ferramenta de monitorização.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Autorizar o acesso aos recursos do Event Hubs utilizando a Azure AD](authorize-access-azure-active-directory.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize funcionalidades de proteção de identidade e de deteção de riscos no Diretório Ativo Azure (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com os seus recursos ativados pelos Centros de Eventos. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

- [Como ver os inícios de sessão de risco do Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Atualmente não disponível; O Lockbox do cliente ainda não está suportado para Os Centros de Eventos.

- [Lista de serviços suportados pelo Cliente Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas em recursos relacionados com os seus Centros de Eventos para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas, opcionalmente com grupos de gestão para desenvolvimento, teste e produção. Os espaços de nomes do Event Hubs devem ser separados por rede virtual com pontos finais de serviço ativados e marcados adequadamente.

Também pode proteger o seu espaço de nomeS Azure Event Hubs utilizando firewalls. O Azure Event Hubs suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Pode definir regras de firewall utilizando o portal Azure, os modelos Azure Resource Manager ou através do Azure CLI ou Azure PowerShell.

- [Como criar subscrições adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](/azure/governance/management-groups/create)

- [Configurar as regras de firewall IP para espaços de nome do Azure Event Hubs](event-hubs-ip-filtering.md)

- [Como criar e utilizar tags](/azure/azure-resource-manager/resource-group-using-tags)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ao utilizar máquinas virtuais para aceder aos seus centros de eventos, utilize redes virtuais, pontos finais de serviço, firewall de Centros de Eventos, grupos de segurança de rede e tags de serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gere a infraestrutura subjacente aos Azure Event Hubs e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Configurar as regras de firewall IP para espaços de nome do Azure Event Hubs](event-hubs-ip-filtering.md)

- [Compreender pontos finais de serviço de rede virtual com hubs de eventos Azure](event-hubs-service-endpoints.md)

- [Integre os Hubs de Eventos Azure com Azure Private Link](private-link-service.md)

- [Compreender grupos de segurança de rede e tags de serviço](/azure/virtual-network/security-overview)

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os Hubs de Eventos Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Azure Event Hubs suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos aos recursos do Event Hubs. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (Azure RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador ou um diretor de serviço de aplicação.

- [Compreenda o Azure RBAC e os papéis disponíveis para os Azure Event Hubs](authorize-access-azure-active-directory.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: O Azure Event Hubs suporta a opção de encriptar dados em repouso com as teclas geridas pela Microsoft ou com as teclas geridas pelo cliente. Esta funcionalidade permite-lhe criar, rodar, desativar e revogar o acesso às teclas geridas pelo cliente que são utilizadas para encriptar os dados do Azure Event Hubs em repouso.

- [Como configurar chaves geridas pelo cliente para encriptar os Hubs de Eventos Azure](configure-customer-managed-key.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações nas instâncias de produção de Azure Event Hubs e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descubra todos os recursos (incluindo espaços de nomes Azure Event Hubs) dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear espaços de nomes e recursos conexos do Azure Event Hubs. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](/azure/governance/management-groups/create)

- [Como criar e usar Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Azure Resource Graph para consultar/descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para as suas implementações de Azure Event Hubs. Utilize pseudónimos da Azure Policy no espaço de nomes **Microsoft.EventHub** para criar políticas personalizadas para auditar ou impor configurações. Você também pode usar definições de política incorporadas para Azure Event Hubs tais como:

- Os registos de diagnóstico no Centro de Eventos devem ser ativados

- O Event Hub deve utilizar um ponto final de serviço de rede virtual

Informações adicionais estão disponíveis nos links referenciados.

- [Política Azure Incorporada para o espaço de nomes de centros de eventos](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub)

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] efeitos para impor definições seguras em todos os recursos ativados pelos Seus Centros de Eventos. 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

 
- [Para mais informações sobre os Efeitos políticos do Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] efeitos para impor automaticamente configurações para as suas implementações de Azure Event Hubs e recursos relacionados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usadas para aceder aos seus centros de eventos, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir a gestão de assinaturas de acesso partilhado para as suas implementações do Azure Event Hubs. Certifique-se de que o Cofre da Chave está configurado com uma eliminação suave ativada.

- [Autenticar uma identidade gerida com o Azure Ative Directory (Azure AD) para aceder aos recursos do Event Hubs](https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest)

- [Configure chaves geridas pelo cliente para centros de eventos](configure-customer-managed-key.md)

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre-Chave](/azure/key-vault/quick-create-portal)

- [Como fornecer a autenticação do Cofre-Chave com uma identidade gerida](/azure/key-vault/managed-identity)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder aos seus centros de eventos, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger os Hubs de Eventos Azure. Certifique-se de que o Cofre da Chave está configurado com uma eliminação suave ativada.

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo Azure Key Vault, sem quaisquer credenciais no seu código.

- [Autenticar uma identidade gerida com a Azure AD para aceder aos Recursos hubs de eventos](https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest)

- [Configure chaves geridas pelo cliente para centros de eventos](configure-customer-managed-key.md)

- [Como configurar identidades geridas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, etc. A Microsoft não pode aceder aos seus dados nestes casos.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Cache para Redis), no entanto não funciona com conteúdo do cliente.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Configurar a recuperação de geo-desastres para os Hubs de Eventos Azure. Quando regiões ou centros de dados inteiros do Azure (se não forem utilizadas zonas de disponibilidade) experimentam tempo de inatividade, é fundamental que o processamento de dados continue a funcionar numa região ou centro de dados diferentes. Como tal, a recuperação de geo-desastres e a geo-replicação são características importantes para qualquer empresa. O Azure Event Hubs suporta a recuperação de geo-desastres e a geo-replicação, ao nível do espaço de nome. 

- [Compreenda a recuperação de geo-desastres para os Hubs de Eventos Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Azure Event Hubs fornece encriptação de dados em repouso com Azure Storage Service Encryption (Azure SSE). O Event Hubs conta com o Azure Storage para armazenar os dados e, por padrão, todos os dados que são armazenados com o Azure Storage são encriptados utilizando as teclas geridas pela Microsoft. Se utilizar o Cofre de Chaves Azure para armazenar chaves geridas pelo cliente, certifique-se de cópias de segurança automáticas regulares das suas Chaves.

Certifique-se de cópias de segurança automáticas regulares dos seus Segredos do Cofre chave com o seguinte comando PowerShell: Backup-AzKeyVaultSecret

- [Como configurar chaves geridas pelo cliente para encriptar os dados do Azure Event Hubs em repouso](configure-customer-managed-key.md)

- [Como apoiar segredos do Cofre de Chaves](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Testar a restauração das chaves geridas pelo cliente.

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Ative a eliminação suave no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa. Os Azure Event Hubs requerem chaves geridas pelo cliente para ter Soft Delete e Não Purgar configurados.

Configure a eliminação suave para a conta de armazenamento Azure que é usada para capturar dados do Event Hubs. Note que esta funcionalidade ainda não é suportada para a Azure Data Lake Storage Gen 2.

- [Como permitir a eliminação suave no Cofre de Chaves](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Configurar um cofre com chaves](configure-customer-managed-key.md)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definam funções de pessoal, bem como fases de tratamento/gestão de incidentes.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

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

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

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

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
