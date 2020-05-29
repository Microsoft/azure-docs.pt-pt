---
title: Linha de segurança Azure para Azure Databricks
description: Linha de segurança Azure para Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e6c51e80a0a9a68dc6ec11e03c133f3480eb4329
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170482"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Linha de segurança Azure para Azure Databricks

A Linha de Base de Segurança Azure para Azure Databricks contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte a visão geral das [linhas de base de segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte [o controlo de segurança: segurança da rede.](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: Implemente a Azure Databricks na sua própria rede virtual Azure (VNet). A implementação padrão do Azure Databricks é um serviço totalmente gerido no Azure: todos os recursos de data plane, incluindo um VNet com o qual todos os clusters serão associados, são implantados num grupo de recursos bloqueados. No entanto, se necessitar de personalização da rede, pode implantar recursos de planos de dados Azure Databricks na sua própria rede virtual (injeção VNet), permitindo-lhe implementar configurações de rede personalizadas. Pode aplicar o seu próprio grupo de segurança de rede (NSG) com regras personalizadas para restrições específicas de tráfego de saídas.

Além disso, pode configurar as regras NSG para especificar as restrições de tráfego na sub-rede para as quais a sua instância Azure Databricks é implantada. O Azure Firewall pode ser configurado para espaços de trabalho injetados em VNET.

* [Como implantar dados do Azure na sua própria Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como gerir os NSGs](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICS

**Orientação**: Implemente a Azure Databricks na sua própria rede virtual Azure (VNet). Um grupo de segurança de rede (NSG) não é criado automaticamente. É-lhe exigido que crie um NSG de base apenas com regras Azure predefinidas. Quando implementa um espaço de trabalho, são adicionadas as regras exigidas pelos Databricks. Também pode começar com um NSG vazio e as regras apropriadas serão adicionadas automaticamente. Ativar os registos de fluxo nSG e enviar registos para uma conta de armazenamento para auditorias de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

* [Como implantar dados do Azure na sua própria Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como ativar registos de fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como Ativar e utilizar a Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Como ativar o Observador de Redes](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a Norma de Proteção DDoS do Azure nas Redes Virtuais Azure associadas aos seus casos de databricks Azure para proteção contra ataques de negação de serviço distribuídos. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP públicos maliciosos ou não utilizados conhecidos.

* [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Compreenda a proteção contra ameaças para a camada de rede Azure no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Implemente a Azure Databricks na sua própria rede virtual Azure (VNet). Um Grupo de Segurança de Rede (NSG) não é criado automaticamente. É-lhe exigido que crie um NSG de base apenas com regras Azure predefinidas. Quando implementa um espaço de trabalho, são adicionadas as regras exigidas pelos Databricks. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

* [Como implantar dados do Azure na sua própria Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como ativar registos de fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como Ativar e utilizar a Análise de Tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Como ativar o Observador de Redes](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Implementar um aparelho virtual de rede (NVA) capaz de criar um dispositivo de trabalho integrado em rede virtual no qual todos os clusters Azure Databricks têm um único endereço de saída IP. O único endereço IP pode ser usado como uma camada de segurança adicional com outros serviços e aplicações Azure que permitem o acesso com base em endereços IP específicos. Você pode usar uma firewall Azure, ou outras ferramentas de terceiros como nVA, para gerir o tráfego de entradas e saídas.

Se a deteção e/ou prevenção de intrusões baseada na inspeção de carga útil não for um requisito, a Azure Firewall com Threat Intelligence pode ser usada. A filtragem baseada em inteligência Azure Firewall Threat pode alertar e negar tráfego de e para endereços ip maliciosos conhecidos e domínios. Os endereços e domínios IP são obtidos a partir do feed de Inteligência de Ameaça da Microsoft.

* [How to Assign a Single Public IP for VNet-Injected Workspaces Using Azure Firewall](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip) (Como Atribuir um IP Público Único para Áreas de Trabalho Injetadas da VNet com o Azure Firewall)

* [Como criar um NVA](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço para definir controlos de acesso à rede em grupos de segurança de rede que estejam ligados às sub-redes com que a sua instância Azure Databricks está associada. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. As etiquetas de serviço não são suportadas com espaços de trabalho VNET não injetados.

* [Compreender tags de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança de rede para os seus casos de Azure Databricks com a Política Azure. Pode utilizar pseudónimos da Azure Policy no espaço de nomes "Microsoft.Databricks" para definir definições de política personalizadas. As políticas não se aplicarão aos recursos dentro do grupo de recursos geridos.

Também pode utilizar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de gestão de recursos Azure, controlo de acesso baseado em funções (RBAC) e políticas, numa única definição de planta. Aplique facilmente o projeto em novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender pseudónimos da Política azul e estrutura de definição](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Como criar uma Planta Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize Tags para NSGs e outros recursos relacionados com a segurança da rede e o fluxo de tráfego que estejam associados à sua instância Azure Databricks. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com as suas instâncias Azure Databricks. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

* [Como visualizar e recuperar eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Como criar alertas no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para recursos Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para os seus recursos de computação. Uma vez que o Azure Databricks é um serviço PaaS, não tem acesso direto aos VMs num cluster Azure Databricks e não consegue configurar configurações de sincronização de tempo.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pela Azure Databricks. Dentro do Azure Monitor, é possível consultar o espaço de trabalho Log Analytics que está configurado para receber os seus Databricks e registos de diagnóstico. Utilize contas de armazenamento Azure para armazenamento de registos de longo prazo/arquivo ou centros de eventos para exportar dados para outros sistemas. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um terceiro Security Incident and Event Management (SIEM).

Nota: Os registos de diagnóstico Azure Databricks requerem o Plano Premium Azure Databricks

* [Como configurar definições de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho log Analytics, centro de eventos Azure ou conta de armazenamento Azure para o arquivo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Para o registo de auditoria, a Azure Databricks fornece registos de diagnóstico abrangentes de atividades realizadas pelos utilizadores da Azure Databricks, permitindo à sua empresa monitorizar padrões de utilização detalhados do Azure Databricks.

Nota: Os registos de diagnóstico Azure Databricks requerem o Plano Premium Azure Databricks

* [Como ativar definições de diagnóstico para registo de atividades azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como ativar definições de diagnóstico para databricks Azure](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: A Azure Databricks fornece registos de diagnóstico abrangentes de atividades realizadas pelos utilizadores da Azure Databricks, permitindo à sua empresa monitorizar padrões de utilização detalhados do Azure Databricks.

Nota: Os registos de diagnóstico Azure Databricks requerem o Plano Premium Azure Databricks. O registo de segurança do SO não está disponível.

* [Como ativar definições de diagnóstico para registo de atividades azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como ativar definições de diagnóstico para databricks Azure](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Ative as definições de diagnóstico para Azure Databricks. Se optar por armazenar registos num espaço de trabalho do Log Analytics, desapasse o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo. As atividades relacionadas com a segurança são rastreadas nos registos de auditoria da Databricks.

Nota: Os registos de diagnóstico Azure Databricks requerem o Plano Premium Azure Databricks

* [Como permitir configurações de diagnóstico em Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Como definir parâmetros de retenção de registos para log analytics workspaces](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Ative as definições de diagnóstico para Azure Databricks e envie registos para um espaço de trabalho Log Analytics. Utilize o espaço de trabalho Do Log Analytics para analisar e monitorizar os registos do Azure Databricks para um comportamento anómalo e rever regularmente os resultados.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Nota: Os registos de diagnóstico Azure Databricks requerem o Plano Premium Azure Databricks

* [Como permitir configurações de diagnóstico em Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Como consultar os registos Azure Databricks enviados para o Log Analytics Workspace](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Configure as definições de diagnóstico para a sua instância Azure Databricks e envie registos para o espaço de trabalho do Log Analytics. Dentro do seu espaço de trabalho Log Analytics, os alertas de configuração devem ocorrer para quando um conjunto de condições pré-definidos ocorrer.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Nota: Os registos de diagnóstico Azure Databricks requerem o Plano Premium Azure Databricks

* [Como enviar registos de dados do Azure para log analytics workspace](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [Como configurar alertas no Log Analytics Workspace](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Permitir a colocação de registo de consultas de DNS

**Orientação**: Não aplicável; A Azure Databricks não processa ou produz registos DNS acessíveis ao utilizador.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Pode utilizar a Azure Databricks SCIM APIs para gerir os utilizadores num espaço de trabalho Azure Databricks e conceder privilégios administrativos aos utilizadores designados. Também pode geri-los através da UI Azure Databricks.

* [Como utilizar as APIs do SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como adicionar e remover utilizadores em Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: A Azure Databricks utiliza o Azure Ative Directory (AD) para fornecer acesso ao portal Azure, bem como à consola de administração Azure Databricks. O Azure AD não tem o conceito de senhas padrão, no entanto, é responsável por alterar ou não permitir senhas padrão para quaisquer aplicações personalizadas ou de terceiros.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Pode utilizar Azure Databricks SCIM APIs para adicionar utilizadores com privilégios de administração num Azure Databricks. Também pode geri-los através da UI Azure Databricks.

* [Como utilizar as APIs do SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como adicionar e remover utilizadores em Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: A azure Databricks é configurado automaticamente para utilizar o Azure Ative Directory single sign-on para autenticar os utilizadores. Os utilizadores fora da sua organização devem completar o processo de convite e ser adicionados ao seu inquilino Ative Directory antes de poderem iniciar sessão no Azure Databricks através de um único login. Pode implementar o SCIM para automatizar o provisionamento e desavisionamento de utilizadores a partir de espaços de trabalho.

* [Como utilizar as APIs do SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Compreenda um único sinal para Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Como convidar os utilizadores para o seu inquilino AZURE AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todo o acesso baseado no Azure Ative Directory.

**Orientação**: Ativar O Azure AD MFA e seguir as recomendações do Azure Security Center Identity and Access Management.

* [Como permitir o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com MFA configurados para iniciar sessão e configurar recursos Azure.

* [Saiba mais sobre estações de trabalho de acesso privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como permitir o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso. Além disso, pode aproveitar os registos de diagnóstico Azure Databricks.

* [Como identificar utilizadores de AD Azure sinalizados para atividade de risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: A azure Databricks é configurado automaticamente para utilizar o Azure Ative Directory single sign-on para autenticar os utilizadores. Os utilizadores fora da sua organização devem completar o processo de convite e ser adicionados ao seu inquilino Ative Directory antes de poderem iniciar sessão no Azure Databricks através de um único login.

* [Compreenda um único sinal para Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Como convidar os utilizadores para o seu inquilino AZURE AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: A Azure AD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize revisões de acesso à identidade Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. Também pode implementar registos de diagnóstico SCIM APIs e Azure Databricks para rever o acesso do utilizador. Também pode utilizar os registos de diagnóstico SCIM APIs e Azure Databricks para rever o acesso do utilizador.

Além disso, reveja e gere regularmente o acesso do utilizador dentro da consola de administração Azure Databricks.

* [Relatório ad Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Como utilizar comentários de acesso à identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Como gerir o acesso do utilizador dentro da consola de administração Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Tem acesso a fontes de registo de registo de registos de registos, auditorias e eventos de risco Azure, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring. Além disso, pode utilizar registos de diagnóstico Azure Databricks para rever a atividade de acesso ao utilizador.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores do Azure Ative Directory e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

* [Como utilizar as APIs do SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como integrar os Registos de Atividades do Azure no Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de Proteção de Risco e Identidade Azure AD para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação. Além disso, pode utilizar registos de diagnóstico Azure Databricks para rever a atividade de acesso ao utilizador.

* [Como ver a Azure AD a entrar em risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Como configurar e permitir políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Como embarcar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Quando os bilhetes de apoio estiverem abertos, os engenheiros de Atendimento ao Cliente e apoio pedirão consentimento para aceder aos dados relevantes do cliente.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize tags para ajudar a rastrear casos de Azure Databricks que processam informações sensíveis.

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. A implementação padrão do Azure Databricks é um serviço totalmente gerido que é implantado dentro da sua própria rede virtual. Se necessitar de personalização da rede, pode implementar Azure Databricks na sua própria rede virtual. É uma das melhores práticas criar espaços de trabalho separados da Azure Databricks para diferentes equipas ou departamentos de negócios.

* [Como implantar dados do Azure na sua própria Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorize e bloqueie a transferência não autorizada de informações sensíveis.

**Orientação**: Siga a arquitetura de proteção de exfiltração de Databricks para atenuar a possibilidade de exfiltração de dados.

* [Proteção de Exfiltração de Dados com Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

A Microsoft gere a infraestrutura subjacente à Azure Databricks e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

* [Compreender a proteção de dados do cliente em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: A Microsoft negociará tLS 1.2 por padrão ao administrar a sua instância Azure Databricks através do portal Azure ou da consola Azure Databricks. Databricks Web App suporta TLS 1.3.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Atualmente não disponível; as funcionalidades de identificação, classificação e prevenção de perdas não estão atualmente disponíveis para a Azure Databricks. Marque a Azure Databricks casos e recursos relacionados que possam estar a processar informações sensíveis como tal e implementar solução de terceiros se necessário para fins de conformidade.

A plataforma Databricks é apenas computacional, e todos os dados são armazenados em outros serviços de dados Azure. Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Compreender a proteção de dados do cliente em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Em Azure Databricks, pode utilizar listas de controlo de acesso (ACLs) para configurar permissão para aceder a tabelas de dados, agrupamentos, piscinas, empregos e objetos do espaço de trabalho como cadernos, experiências e pastas. Todos os utilizadores administrativos podem gerir listas de controlo de acesso, assim como os utilizadores que receberam permissões delegadas para gerir listas de controlo de acesso. Pode utilizar o Azure RBAC para definir permissões no espaço de trabalho Azure Databricks.

Nota: O controlo de acesso à mesa, cluster, piscina, trabalho e espaço de trabalho só está disponível no Plano Premium Azure Databricks

* [Como gerir o controlo de acessos em Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo de acesso

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

A Microsoft gere a infraestrutura subjacente à Azure Databricks e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

* [Compreender a proteção de dados do cliente em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Um espaço de trabalho Azure Databricks é composto por um plano de gestão que está hospedado numa rede virtual gerida pela Azure Databricks e num plano de dados que é implantado numa rede virtual gerida pelo cliente. O avião de controlo armazena todos os cadernos dos utilizadores e o caderno associado resulta numa base de dados. Por padrão, todos os cadernos e resultados são encriptados em repouso com uma chave de encriptação diferente.

Databricks File System (DBFS) é um sistema de ficheiros distribuído montado num espaço de trabalho Azure Databricks e disponível em clusters Azure Databricks. O DBFS é implementado como uma Conta de Armazenamento no grupo de recursos geridos do seu espaço de trabalho Azure Databricks. Por predefinição, a conta de armazenamento é encriptada com as teclas geridas pela Microsoft. Os seus dados são armazenados nos serviços de dados Azure que possui, e tem a opção de os encriptar. Não é recomendada a utilização de DBFS para armazenar dados de produção

Nota: Estas funcionalidades não estão disponíveis para todas as subscrições do Azure Databricks. Contacte o seu representante da conta Microsoft ou Databricks para solicitar o acesso.

* [Como permitir chaves geridas pelo cliente para os cadernos Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Como ativar chaves geridas pelo cliente para o Sistema de Ficheiros Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nos espaços de trabalho críticos do Azure Databricks.

* [Como criar alertas para eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte [o controlo de segurança: Gestão de vulnerabilidades.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros.

Se tiver uma subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar scripts de inicialização do Azure Databricks para instalar agentes de avaliação de vulnerabilidade nos seus nós de cluster Azure Databricks e gerir os nós através do respetivo portal. Note que cada solução de terceiros funciona de forma diferente.

* [Como instalar o agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o Agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: A Microsoft mantém as imagens base do nó de base do azure Databricks, no entanto, é responsável por garantir que os seus nós de cluster permanecem remendados. Para adicionar uma atualização de manutenção a um cluster de execução existente, tem de reiniciar o cluster.

* [Compreender as atualizações de manutenção do tempo de execução para Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: A Microsoft mantém as imagens base do nó de nó de nó de azure Databricks, no entanto é responsável por garantir que quaisquer aplicações de terceiros que instale permaneçam corrigidas.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros que tenha a capacidade de comparar digitalizações de vulnerabilidades ao longo do tempo. Se tiver uma subscrição de gestão de vulnerabilidades, poderá utilizar o portal desse fornecedor para visualizar e comparar as verificações de vulnerabilidades de trás para a costa. Note que cada solução de terceiros funciona de forma diferente.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas.

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, sistema comum de pontuação de vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros.

**Monitorização do Centro de Segurança Azure**: N/A

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua(s) subscrição( s). Certifique-se de que existem permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

* [Como criar consultas com gráfico de recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Compreender Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a política da Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos aprovados da Azure e dos títulos de software.

**Orientação**: Defina recursos Azure aprovados e software aprovado para recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar consultas com Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; A azure Databricks é um serviço PaaS, os clientes não têm acesso direto aos VMs num cluster Azure Databricks.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.), incluindo instâncias Azure Databricks, dentro da sua subscrição(s). Remova quaisquer recursos não aprovados do Azure que descobrir. Para os nós de cluster Azure Databricks, implemente uma solução de terceiros para remover ou alertar em software não aprovado.

* [Como criar consultas com Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; Azure Databricks é um serviço PaaS, você não tem acesso direto aos VMs em um cluster Azure Databricks.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; Azure Databricks é um serviço PaaS, você não tem acesso direto aos VMs em um cluster Azure Databricks.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure através de scripts</div>

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; isto não é aplicável à Azure Databricks uma vez que os utilizadores (não administradores) do cluster não precisam de acesso aos nós individuais para executar empregos. O administrador de cluster tem acesso de raiz a todos os nós de cluster por padrão.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou a computar recursos que hospedam aplicações web.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os seus casos de Azure Databricks com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Dados Azure. Note que quaisquer políticas aplicadas não funcionam no Grupo de Recursos Geridos databricks.

* [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; Azure Databricks é um serviço PaaS, você não tem acesso direto aos VMs em um cluster Azure Databricks.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os seus casos de Azure Databricks com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Dados Azure. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender efeitos da política do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Sistema operativo Azure Databricks Imagens geridas e mantidas pela Microsoft. É responsável pela implementação da configuração do estado de nível oss.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições de política Azure personalizadas, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

* [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação de Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Se utilizar imagens personalizadas para os seus nóns de cluster Azure Databricks, empurre a sua imagem de base personalizada para um registo Docker, como o Registo do Contentor Azure (ACR).

* [Como personalizar contentores com serviços de contentores databricks](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Compreender o Registo do Contentor de Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Defina e implemente configurações de segurança padrão para os seus casos de Azure Databricks com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Dados Azure.

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; Azure Databricks é um serviço PaaS, você não tem acesso direto aos VMs em um cluster Azure Databricks.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização de configuração automatizada para serviços Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os seus casos de Azure Databricks com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Dados Azure.

* [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para monitorizar o estado dos seus sistemas operativos de nó de cluster Azure Databricks.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize o Cofre da Chave Azure com um âmbito secreto Azure Databricks para gerir e utilizar segredos de forma segura.

* [Como usar o Cofre da Chave Azure com dados Azure](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Atualmente não disponível; Identidades geridas não estão atualmente disponíveis para Azure Databricks

* [Serviços que suportam identidades geridas para recursos da Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Se tiver uma subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar scripts de inicialização do Azure Databricks para instalar agentes de avaliação de vulnerabilidade nos seus nós de cluster Azure Databricks e gerir os nós através do respetivo portal. Note que cada solução de terceiros funciona de forma diferente.

* [Como instalar o agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o Agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure App Service), no entanto não funciona com o seu conteúdo.

Pré-digitalize quaisquer ficheiros que sejam enviados para os nós do cluster Azure Databricks ou recursos relacionados.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Se tiver uma subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar scripts de inicialização do Azure Databricks para instalar agentes de avaliação de vulnerabilidade nos seus nós de cluster Azure Databricks e gerir os nós através do respetivo portal. Note que cada solução de terceiros funciona de forma diferente.

* [Como instalar o agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o Agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [o controlo de segurança: recuperação de dados.](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Para as fontes de dados do Azure Databricks, certifique-se de que configura um nível adequado de redundância de dados para o seu caso de utilização. Por exemplo, se utilizar uma conta de Armazenamento Azure para a sua loja de dados Azure Databricks, escolha a opção de redundância adequada (LRS, ZRS, GRS, RA-GRS).

* [Fontes de dados para Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Recuperação regional de desastres para aglomerados Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Cópia de segurança de qualquer cliente que gere chaves relacionadas com as implementações do Azure Databricks dentro do Cofre da Chave Azure. Também pode utilizar a API rest e/ou CLI para criar uma cópia de segurança diária das configurações do Databricks.

* [Como backup chaves chave cofre em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Teste a restauração de chaves geridas pelo cliente com suporte relacionado com as suas implementações Azure Databricks.

* [Como restaurar chaves chave do cofre em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Certifique-se de que a eliminação suave está ativada no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

* [Como permitir o Soft-Delete no Cofre da Chave](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente.

* [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Você também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

* [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas Sentinel.

* [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Como transmitir alertas para o Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

* [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias.

**Orientação**: * [Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximos passos

- Consulte a [referência de segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
