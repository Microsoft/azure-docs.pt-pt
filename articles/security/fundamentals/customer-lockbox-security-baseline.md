---
title: Linha de segurança Azure para o Lockbox do Cliente para o Microsoft Azure
description: Linha de segurança Azure para o Lockbox do Cliente para o Microsoft Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: fb0b976c8759eb77aa2240f95fadbd41e5d9596a
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504921"
---
# <a name="azure-security-baseline-for-customer-lockbox-for-microsoft-azure"></a>Linha de segurança Azure para o Lockbox do Cliente para o Microsoft Azure

A Linha de Base de Segurança Azure para o Bloqueio do Cliente para o Microsoft Azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte a visão geral das [linhas de base de segurança Azure](../benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](../benchmarks/security-control-network-security.md)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede ao Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede ao Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede ao Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede ao Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede ao Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede ao Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede com o Azure Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede ao Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Não aplicável; não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede ao Customer Lockbox. Não existem configurações de rede para fazer ou monitorizar.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: Não aplicável; A Microsoft mantém a fonte de tempo utilizada para recursos como o Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Os registos de auditoria do Bloqueio do Cliente são automaticamente ativados e mantidos em Registos de Atividade Azure. Pode visualizar estes dados transmitindo-os a partir do login da Atividade Azure para um espaço de trabalho log Analytic onde poderá então realizar pesquisas e análises sobre os mesmos.

A bordo dos registos de atividade gerados pelo Customer Lockbox para a Azure Sentinel ou outro SIEM para permitir a agregação e gestão de registos centrais.

* [Registos de auditoria para o Bloqueio do Cliente](./customer-lockbox-overview.md#auditing-logs)

* [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Os registos de auditoria do Bloqueio do Cliente são automaticamente ativados e mantidos em Registos de Atividade Azure. Pode visualizar estes dados transmitindo-os a partir do login da Atividade Azure para um espaço de trabalho log Analytic onde poderá então realizar pesquisas e análises sobre os mesmos.

* [Registos de auditoria para o Bloqueio do Cliente](./customer-lockbox-overview.md#auditing-logs)

* [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados ao seu Lockbox de Clientes de acordo com os regulamentos de conformidade da sua organização.

* [Como definir parâmetros de retenção de registos](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Os registos de auditoria do Bloqueio do Cliente são automaticamente ativados e mantidos em Registos de Atividade Azure. Pode visualizar estes dados transmitindo-os a partir do login da Atividade Azure para um espaço de trabalho log Analytic onde poderá então realizar pesquisas e análises sobre os mesmos. Analise e monitorize os registos dos seus pedidos de Bloqueio de Clientes para um comportamento anómalo. Utilize a secção "Logs" no seu espaço de trabalho Azure Sentinel para realizar consultas ou criar alertas com base nos registos do Seu Bloqueio ao Cliente.

* [Registos de auditoria no Bloqueio do Cliente](./customer-lockbox-overview.md#auditing-logs)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Os registos de auditoria do Bloqueio do Cliente são automaticamente ativados e mantidos em Registos de Atividade Azure. Pode visualizar estes dados transmitindo-os a partir do login da Atividade Azure para um espaço de trabalho log Analytic onde poderá então realizar pesquisas e análises sobre os mesmos. Analise e monitorize os registos dos seus pedidos de Bloqueio de Clientes para um comportamento anómalo. Utilize a secção "Logs" no seu espaço de trabalho Azure Sentinel para realizar consultas ou criar alertas com base nos registos do Seu Bloqueio ao Cliente.

* [Registos de auditoria no Bloqueio do Cliente](./customer-lockbox-overview.md#auditing-logs)

* [Como alertar nos dados de registo de registo de registos de registos](../../azure-monitor/alerts/tutorial-response.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; O Lockbox do cliente não processa nem produz registos relacionados com malware.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; O Lockbox do cliente não processa nem produz registos relacionados com DNS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Manter um inventário das contas de utilizador que tenham acesso administrativo aos seus pedidos de Bloqueio de Clientes. Pode utilizar o painel de controlo de identidade e acesso (IAM) no portal Azure para a sua subscrição para configurar o controlo de acesso baseado em funções (Azure RBAC). As funções são aplicadas aos utilizadores, grupos, diretores de serviços e identidades geridas no Azure Ative Directory.

Na organização do cliente, o utilizador que tem o papel de Proprietário para a subscrição do Azure recebe um e-mail da Microsoft, para os notificar sobre quaisquer pedidos de acesso pendentes. Para pedidos de bloqueio de clientes, esta pessoa é o aprovador designado.

* [Compreender papéis personalizados](../../role-based-access-control/custom-roles.md)

* [Como configurar o Azure RBAC para livros](../../sentinel/quickstart-get-visibility.md)

* [Compreender permissões de pedido de acesso no Lockbox do Cliente](./customer-lockbox-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O Azure Ative Directory não tem o conceito de palavras-passe padrão. Outros recursos Azure que requerem uma palavra-passe forçam a criação de uma palavra-passe com requisitos de complexidade e um comprimento mínimo de senha, que difere consoante o serviço. É responsável por aplicações de terceiros e serviços de marketplace que possam utilizar senhas padrão.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:
- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

* [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../../security-center/security-center-identity-access.md)

* [Como usar a Política Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Não aplicável; acesso ao Customer Lockbox é através do portal Azure e reservado para contas com a função de inquilino do Proprietário. A inscrição não é suportada.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a autenticação multi-factor do Diretório Ativo Azure e seguir as recomendações do Azure Security Center Identity and Access Management.

* [Como ativar o MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiada (PAW) com autenticação multi-factor AD (MFA) ativada para iniciar sessão e configurar os seus pedidos de Bloqueio ao Cliente.

* [Estações de Trabalho de Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Planear a implementação da Multi-Factor Authentication do Azure AD com base na cloud](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize a Azure Ative Directory Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Além disso, utilize deteções de risco do Azure Ative Directory para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

* [Como implementar gestão de identidade privilegiada (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Compreender deteções de risco Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados para acesso condicional para permitir o acesso ao portal Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory como sistema central de autenticação e autorização, sempre que aplicável. O Azure Ative Directory protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. O Azure Ative Directory também sai, hashes e armazena de forma segura credenciais de utilizador.

* [Como criar e configurar um exemplo de Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize avaliações de acesso ao Azure Ative Directory para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

* [Compreenda o relatório do Diretório Ativo do Azure](../../active-directory/reports-monitoring/index.yml)

* [Como utilizar comentários de acesso ao Azure Ative Directory](../../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Utilize o Azure Ative Directory como sistema central de autenticação e autorização, sempre que aplicável. O Azure Ative Directory protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. O Azure Ative Directory também sai, hashes e armazena de forma segura credenciais de utilizador.

Tem acesso a atividade de login, auditoria e registo de eventos de risco, que lhe permitem integrar-se com o Azure Sentinel ou com um SIEM de terceiros.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores do Azure Ative Directory e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

* [Como integrar os registos de atividade do Azure no Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Para desvio de comportamento de login de conta no plano de controlo (por exemplo, portal Azure), utilize recursos de proteção de identidade do Azure Ative Directory e funcionalidades de deteção de riscos para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

* [Como ver o Azure Ative Directory a iniciar súmis de risco](../../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e permitir políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Esta recomendação não é aplicável ao Cofre do Cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Esta recomendação não é aplicável; as tags não são suportadas para o Bloqueio do Cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Não aplicável; O Lockbox do Cliente será a provisionado na mesma subscrição que os recursos a que está a conceder acesso. Não há nenhum ponto final público para proteger ou isolar. O pedido de bloqueio do cliente é concedido ao utilizador que detém a função de Proprietário ao nível do arrendatário.

* [Compreender o fluxo de trabalho do Lockbox do Cliente](./customer-lockbox-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: A Microsoft gere a infraestrutura subjacente ao Bloqueio do Cliente e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

* [Compreender a proteção dos dados dos clientes no Azure](./protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Por padrão, a Microsoft utiliza o protocolo de Segurança da Camada de Transporte (TLS) para proteger os dados quando viaja entre os serviços na nuvem e os clientes. Os datacenters da Microsoft negoceiam uma ligação TLS com sistemas de clientes que se ligam aos serviços Azure. O TLS proporciona uma autenticação forte, privacidade e integridade da mensagem (permitindo a deteção de adulteração de mensagens, interceção e falsificação), interoperabilidade, flexibilidade de algoritmos e facilidade de implantação e utilização.

* [Compreender a encriptação em trânsito com Azure](./encryption-overview.md#encryption-of-data-in-transit)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não aplicável; O lockbox do cliente em si não detém quaisquer dados do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: A aprovação do pedido de bloqueio do cliente é concedida ao utilizador que detém a função de Proprietário ao nível do arrendatário.

* [Compreender o fluxo de trabalho do Lockbox do Cliente](./customer-lockbox-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft gere a infraestrutura subjacente ao Customer Lockbox e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

* [Proteção de dados do cliente Azure](./protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Não aplicável; O bloqueio do cliente em si não possui dados do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Os registos de auditoria do Bloqueio do Cliente são automaticamente ativados e mantidos em Registos de Atividade Azure. Utilize o Registo de Atividades Azure para monitorizar e detetar alterações nos recursos do Lockbox do cliente Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos.

* [Como permitir a auditoria no Customer Lockbox](./customer-lockbox-overview.md)

* [Como visualizar e recuperar eventos de Registo de Atividades Azure](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](../benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam o Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam o Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam o Customer Lockbox.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph, é altamente recomendado que crie e utilize recursos do Azure Resource Manager.

* [Como criar consultas com gráfico de recursos Azure](../../governance/resource-graph/first-query-portal.md)

* [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

* [Compreenda o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: As etiquetas não são suportadas para o Lockbox do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar subscrições adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gestão](../../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar tags](../../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com gráfico de recursos Azure](../../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição(s) utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo específico de recurso com a Política Azure](../../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azureresources através de scripts

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](../benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Não aplicável, o Lockbox do cliente não tem configurações de segurança configuráveis.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Não aplicável, o Lockbox do cliente não tem configurações de segurança configuráveis.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Não aplicável; O Lockbox do cliente não tem configurações de segurança configuráveis.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Não aplicável; O Lockbox do cliente não tem configurações de segurança configuráveis.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Não aplicável; O Lockbox do cliente não tem configurações de segurança configuráveis.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Não aplicável; o acesso aos pedidos do Customer Lockbox está limitado ao proprietário da subscrição Azure que alberga o recurso. Não existem senhas, segredos ou chaves necessárias para aceder ao Bloqueio do Cliente fora do login como proprietário do inquilino.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Não aplicável; O Lockbox do cliente não utiliza identidades geridas.

* [Serviços Azure que suportam identidades geridas](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](../benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional. O Microsoft Antimalware está ativado no anfitrião subjacente que suporta a solução Lockbox do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta serviços Azure como o Customer Lockbox.

É da sua responsabilidade pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados. A Microsoft não pode aceder aos dados dos clientes e, portanto, não pode realizar análises anti-malware do conteúdo do cliente em seu nome.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional. O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure, no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](../benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Não aplicável; O bloqueio do cliente em si não armazena os dados do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Não aplicável; O bloqueio do cliente em si não armazena os dados do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Não aplicável; O bloqueio do cliente em si não armazena os dados do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Não aplicável; O Bloqueio do Cliente em si não armazena os dados do cliente, também não utiliza chaves ou senhas de acesso.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](../benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

* [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../../security-center/security-center-planning-and-operations-guide.md)

* [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

* [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

* [Como configurar a exportação contínua](../../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

* [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](../benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação:** 

* [Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../benchmarks/security-baselines-overview.md)