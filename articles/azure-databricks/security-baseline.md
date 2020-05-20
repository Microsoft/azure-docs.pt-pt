---
title: Linha de base de segurança Azure para Tijolos de Dados Azure
description: Linha de base de segurança Azure para Tijolos de Dados Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681707"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Linha de base de segurança Azure para Tijolos de Dados Azure

A Linha de Base de Segurança Azure para Os Tijolos de Dados Azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a visão geral das linhas de [segurança do Azure.](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

## <a name="network-security"></a>Segurança da rede

*Para mais informações, consulte Controlo de [Segurança: Segurança da rede](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Implante os Tijolos de Dados Azure na sua própria rede virtual Azure (VNet). A implementação padrão de Azure Databricks é um serviço totalmente gerido no Azure: todos os recursos de avião de dados, incluindo um VNet com o quais todos os clusters estarão associados, são implantados a um grupo de recursos bloqueados. No entanto, se necessitar de personalização da rede, pode implementar os recursos de plano de dados de Dados Azure Databricks na sua própria rede virtual (injeção VNet), permitindo-lhe implementar configurações de rede personalizadas. Pode aplicar o seu próprio grupo de segurança de rede (NSG) com regras personalizadas a restrições de tráfego específicas.

Além disso, pode configurar as regras do NSG para especificar as restrições de tráfego na subnet a que a sua instância de Azure Databricks está implantada. O Azure Firewall pode ser configurado para espaços de trabalho injetados VNET.

* [Como implantar os Tijolos de Dados Azure na sua própria Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como gerir nsgs](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICS

**Orientação**: Implante os Tijolos de Dados Azure na sua própria rede virtual Azure (VNet). Um grupo de segurança de rede (NSG) não é criado automaticamente. É-lhe exigido que crie um NSG de base apenas com regras de Azure padrão. Quando se implementa um espaço de trabalho, são adicionadas regras exigidas pelos Databricks. Também pode começar com um NSG vazio e as regras apropriadas serão adicionadas automaticamente. Ative os registos de fluxo do NSG e envie registos numa conta de armazenamento para auditorias de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

* [Como implantar os Tijolos de Dados Azure na sua própria Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como ativar os registos de fluxo nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como ativar e utilizar o Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Como ativar o Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a Norma de Proteção De DDoS Azure nas Redes Virtuais Azure associadas aos seus dados azure casos de proteção contra ataques de negação de serviço distribuídos. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP públicos conhecidos ou não utilizados.

* [Gerir a Norma de Proteção DDoS Azure utilizando o portal Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Compreender a proteção contra ameaças para a camada de rede Azure no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Implante os Tijolos de Dados Azure na sua própria rede virtual Azure (VNet). Um Grupo de Segurança de Rede (NSG) não é criado automaticamente. É-lhe exigido que crie um NSG de base apenas com regras de Azure padrão. Quando se implementa um espaço de trabalho, são adicionadas regras exigidas pelos Databricks. Ative os registos de fluxo do NSG e envie registos numa conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

* [Como implantar os Tijolos de Dados Azure na sua própria Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como ativar os registos de fluxo nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Como ativar e utilizar o Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Como ativar o Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Implementar um aparelho virtual de rede idp/IPS (NVA) capaz de rede do Mercado Azure para criar um espaço de trabalho integrado em rede virtual no qual todos os clusters de Databricks Azure tenham um único endereço de saída IP. O único endereço IP pode ser usado como uma camada de segurança adicional com outros serviços e aplicações Azure que permitem o acesso com base em endereços IP específicos. Você pode usar uma firewall Azure, ou outras ferramentas de terceiros como a NVA, para gerir ingresse e esgress tráfego.

Se a deteção e/ou prevenção de intrusões com base na inspeção da carga útil não for um requisito, o Azure Firewall com a Threat Intelligence pode ser utilizado. A filtragem baseada na inteligência da Ameaça de Firewall Azure pode alertar e negar o tráfego de e para endereços e domínios ip maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence.

* [How to Assign a Single Public IP for VNet-Injected Workspaces Using Azure Firewall](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip) (Como Atribuir um IP Público Único para Áreas de Trabalho Injetadas da VNet com o Azure Firewall)

* [Como criar um NVA](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço para definir controlos de acesso à rede em Grupos de Segurança da Rede que estão ligados às subredes a que a sua instância de Databricks Azure está associada. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. As etiquetas de serviço não são suportadas com espaços de trabalho VNET não injetados.

* [Compreender etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança de rede para os casos de Azure Databricks com a Política Azure. Pode utilizar pseudónimos da Política Azure no espaço de nome "Microsoft.Databricks" para definir definições de políticas personalizadas. As políticas não se aplicarão aos recursos dentro do grupo de recursos geridos.

Também pode utilizar plantas Azure para simplificar as implementações azure em larga escala através da embalagem de artefactos ambientais chave, tais como modelos de Gestão de Recursos Azure, controlo de acesso baseado em funções (RBAC) e políticas, numa única definição de blueprint. Aplique facilmente o projeto a novas subscrições e ambientes, e afinar o controlo e a gestão através da versão.

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender pseudónimos da Política Azure e estrutura de definição](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Como criar uma Planta Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para NSGs e outros recursos relacionados com a segurança da rede e fluxo de tráfego associados à sua instância DeBricks Azure. Para regras nsg individuais, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as instâncias dos seus Databricks Azure. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.

* [Como visualizar e recuperar eventos de Registo de Atividade sinuosa do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Como criar alertas no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para mais informações, consulte Controlo de [Segurança: Registo e monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para os recursos Do Azure, no entanto, tem a opção de gerir as definições de sincronização de tempo para os seus recursos computacionais. Uma vez que o Azure Databricks é um serviço PaaS, não tem acesso direto aos VMs num cluster de Databricks Azure e não consegue configurar definições de sincronização temporal.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados por Bricks De dados Azure. Dentro do Monitor Azure, é possível consultar o espaço de trabalho log Analytics que está configurado para receber os seus Databricks e registos de diagnóstico. Utilize contas de armazenamento Azure para armazenamento de registos de longo prazo/arquivo ou centros de eventos para exportar dados para outros sistemas. Em alternativa, pode ativar e a bordo dados para o Azure Sentinel ou para um incidente de segurança e gestão de eventos de terceiros (SIEM).

Nota: Os registos de diagnóstico de Tijolos de Dados Azure requerem o Plano Premium de Tijolos de Dados Azure

* [Como configurar as definições de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Como começar com o Azure Monitor e a integração siem de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Ative as definições de diagnóstico do Registo de Atividade si e envie os registos para um espaço de trabalho de Log Analytics, hub de eventos Azure ou conta de armazenamento Azure para arquivo. Utilizando dados do Registo de Atividade sinuoso do Azure, pode determinar o "quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Para a exploração de auditoria, a Azure Databricks fornece registos de diagnóstico de ponta a ponta abrangentes de atividades realizadas pelos utilizadores de Azure Databricks, permitindo à sua empresa monitorizar padrões de utilização detalhados dos Azure Databricks.

Nota: Os registos de diagnóstico de Tijolos de Dados Azure requerem o Plano Premium de Tijolos de Dados Azure

* [Como ativar as Definições de Diagnóstico para o Registo de Atividade do Azure)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como ativar as Definições de Diagnóstico para Os Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Os Bricks Azure fornecem registos de diagnóstico completos de atividades realizadas pelos utilizadores de Azure Databricks, permitindo à sua empresa monitorizar padrões de utilização detalhados dos Tijolos de Dados Azure.

Nota: Os registos de diagnóstico dos Tijolos de Dados Azure requerem o Plano Premium de Tijolos de Dados Azure. O registo de segurança do OS não está disponível.

* [Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Como ativar as Definições de Diagnóstico para Os Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: Ativar as definições de diagnóstico dos tijolos de dados do Azure. Se optar por armazenar registos num espaço de trabalho de Log Analytics, detete o período de retenção do Espaço de Trabalho de Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo. As atividades relacionadas com a segurança são rastreadas nos registos de auditoria da Databricks.

Nota: Os registos de diagnóstico de Tijolos de Dados Azure requerem o Plano Premium de Tijolos de Dados Azure

* [Como ativar as definições de diagnóstico em Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Como definir parâmetros de retenção de registos para espaços de trabalho de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: Ative as definições de diagnóstico dos Databrick sinuosos e envie registos para um espaço de trabalho de Log Analytics. Utilize o espaço de trabalho do Log Analytics para analisar e monitorizar os registos dos Seus Dados Azure para comportamentos anómalos e rever regularmente os resultados.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros.

Nota: Os registos de diagnóstico de Tijolos de Dados Azure requerem o Plano Premium de Tijolos de Dados Azure

* [Como ativar as definições de diagnóstico em Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Como consultar os registos de databrick saqueados para log analytics workspace](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: Configure as definições de diagnóstico para a sua instância de Databricks Azure e envie registos para o espaço de trabalho do Log Analytics. Dentro do seu espaço de trabalho Log Analytics, configure alertas para quando ocorrer um conjunto de condições pré-definidas.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros.

Nota: Os registos de diagnóstico de Tijolos de Dados Azure requerem o Plano Premium de Tijolos de Dados Azure

* [Como enviar registos de databrick sintetmente para log analytics workspace](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Como configurar alertas no Espaço de Trabalho de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável; Os Azure Databricks não processam nem produzem registos relacionados com dNS acessíveis ao utilizador.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte Controlo de [Segurança: Controlo de identidade e acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Pode utilizar a Azure Databricks SCIM APIs para gerir os utilizadores num espaço de trabalho do Azure Databricks e conceder privilégios administrativos aos utilizadores designados. Também pode geri-los através do Azure Databricks UI.

* [Como utilizar as APIs SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como adicionar e remover utilizadores em Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: A Azure Databricks utiliza o Azure Ative Directory (AD) para fornecer acesso ao portal Azure, bem como à consola de administração Azure Databricks. A Azure AD não tem o conceito de senhas padrão, no entanto, é responsável por alterar ou não permitir senhas padrão para quaisquer aplicações personalizadas ou de terceiros.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Pode utilizar a Azure Databricks SCIM APIs para adicionar aos utilizadores privilégios de administração num Azure Databricks. Também pode geri-los através do Azure Databricks UI.

* [Como utilizar as APIs SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como adicionar e remover utilizadores em Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: Os Tijolos de Dados Azure são configurados automaticamente para utilizar o único sinal de entrada do Azure Ative Directory para autenticar os utilizadores. Os utilizadores fora da sua organização devem completar o processo de convite e ser adicionados ao seu inquilino de Diretório Ativo antes de poderem iniciar sessão no Azure Databricks através de um único login. Pode implementar o SCIM para automatizar o fornecimento e desprovisionamento dos utilizadores a partir de espaços de trabalho.

* [Como utilizar as APIs SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Compreender um único sinal para os Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Como convidar os utilizadores para o seu inquilino Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multifactor para todos os acessos baseados no Diretório Ativo azure.

**Orientação**: Habilitar o Azure AD MFA e seguir as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

* [Como ativar o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Como monitorizar a identidade e o acesso dentro do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho de acesso privilegiados) com MFA configurado para iniciar sessão e configurar os recursos Azure.

* [Saiba mais sobre postos de trabalho de acesso privilegiados](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Como ativar o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Diretório Ativo Azure para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente. Utilize o Azure Security Center para monitorizar a atividade de identidade e acesso. Além disso, pode alavancar os registos de diagnóstico dos Databricks Azure.

* [Como identificar utilizadores de Anúncios Azure sinalizados para atividades de risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Utilizar locais de acesso condicional nomeados para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas em Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Os Tijolos de Dados Azure são configurados automaticamente para utilizar o único sinal de entrada do Azure Ative Directory para autenticar os utilizadores. Os utilizadores fora da sua organização devem completar o processo de convite e ser adicionados ao seu inquilino de Diretório Ativo antes de poderem iniciar sessão no Azure Databricks através de um único login.

* [Compreender um único sinal para os Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Como convidar os utilizadores para o seu inquilino Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: A AD Azure fornece registos para ajudar a descobrir contas velhas. Além disso, utilize avaliações de acesso à identidade azure para gerir eficientemente os membros do grupo, acesso a aplicações empresariais e atribuições de papéis. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. Também pode implementar registos de diagnóstico sCIM APIs e Azure Databricks para rever o acesso ao utilizador. Também pode utilizar registos de diagnóstico SCIM APIs e Azure Databricks para rever o acesso ao utilizador.

Além disso, reveja e gere regularmente o acesso ao utilizador dentro da consola de administração Azure Databricks.

* [Relatórios da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Como utilizar avaliações de acesso à identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Como gerir o acesso dos utilizadores dentro da consola de administração Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Tem acesso a fontes de registo de registo de registo de registo sionantes de Eventos De Acesso AD, auditoria e risco, que lhe permitem integrar-se com qualquer ferramenta de registo SIEM/Monitor. Além disso, pode utilizar registos de diagnóstico azure Databricks para rever a atividade de acesso ao utilizador.

Pode simplificar este processo criando Definições de Diagnóstico para contas de utilizadores do Diretório Ativo Azure e enviando os registos de auditoria e registos de log-in para um espaço de trabalho de Log Analytics. Pode configurar os alertas desejados dentro do espaço de trabalho de Log Analytics.

* [Como utilizar APIs SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Como integrar registos de atividade do Azure no Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de risco e proteção de identidade azure AD para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Também pode ingerir dados no Azure Sentinel para mais investigação. Além disso, pode utilizar registos de diagnóstico azure Databricks para rever a atividade de acesso ao utilizador.

* [Como ver os sign-ins de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Como configurar e ativar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Quando os bilhetes de apoio estiverem abertos, os engenheiros do Serviço ao Cliente e de Apoio pedirão consentimento para aceder aos dados relevantes do cliente.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

## <a name="data-protection"></a>Proteção de dados

*Para mais informações, consulte Controlo de [Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os casos de Azure Databricks que processam informações sensíveis.

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. A implementação padrão de Azure Databricks é um serviço totalmente gerido que é implementado dentro da sua própria rede virtual. Se necessitar de personalização de rede, pode implementar os Databricks Azure na sua própria rede virtual. É uma boa prática criar espaços de trabalho separados da Azure Databricks para diferentes equipas de negócioou departamentos.

* [Como implantar os Tijolos de Dados Azure na sua própria Rede Virtual](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis.

**Orientação**: Siga a arquitetura de proteção de exfiltração de Databricks para mitigar a possibilidade de exfiltração de dados.

* [Proteção de Exfiltração de Dados com Tijolos de Dados Azure](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

A Microsoft gere a infraestrutura subjacente aos Bricks Azure e implementou controlos rigorosos para evitar a perda ou exposição de dados dos clientes.

* [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: A Microsoft negociará o TLS 1.2 por padrão ao administrar a sua instância de Databricks Azure através do portal Azure ou da consola Azure Databricks. Databricks Web App suporta TLS 1.3.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Atualmente não disponível; As funcionalidades de identificação, classificação e prevenção de perdas de dados não estão atualmente disponíveis para os Tijolos de Dados Do Azure. Tag Azure Databricks exemplos e recursos conexos que podem estar a processar informações sensíveis como tal e implementar solução de terceiros, se necessário para fins de conformidade.

A plataforma Databricks é apenas computada, e todos os dados são armazenados em outros serviços de dados do Azure. Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

* [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Em Azure Databricks, pode utilizar listas de controlo de acesso (ACLs) para configurar permissão para aceder a tabelas de dados, clusters, piscinas, empregos e objetos do espaço de trabalho, como cadernos, experiências e pastas. Todos os utilizadores de administração podem gerir as listas de controlo de acesso, assim como os utilizadores que receberam permissões delegadas para gerir as listas de controlo de acesso. Pode utilizar o Azure RBAC para definir permissões no espaço de trabalho azure Databricks.

Nota: Tabela, cluster, pool, job e workspace access control estão disponíveis apenas no Plano Premium Azure Databricks

* [Como gerir o controlo de acesso sintetizar em Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a Prevenção da Perda de Dados baseada no hospedeiro para impor o controlo de acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

A Microsoft gere a infraestrutura subjacente aos Bricks Azure e implementou controlos rigorosos para evitar a perda ou exposição de dados dos clientes.

* [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Um espaço de trabalho Azure Databricks compreende um plano de gestão que está hospedado numa rede virtual gerida pela Azure Databricks e num avião de dados que é implantado numa rede virtual gerida pelo cliente. O avião de controlo armazena todos os cadernos dos utilizadores e o caderno associado resulta numa base de dados. Por padrão, todos os cadernos e resultados são encriptados em repouso com uma chave de encriptação diferente.

Databricks File System (DBFS) é um sistema de ficheiros distribuído montado num espaço de trabalho azure Databricks e disponível em clusters De Dados Azure. O DBFS é implementado como uma Conta de Armazenamento no grupo de recursos geridos do seu espaço de trabalho Azure Databricks. Por predefinição, a conta de armazenamento é encriptada com chaves geridas pela Microsoft. Os seus dados são armazenados nos serviços de dados Do Azure que possui, e tem a opção de os encriptar. Não é recomendada a utilização do DBFS para armazenar dados de produção

Nota: Estas funcionalidades não estão disponíveis para todas as subscrições do Azure Databricks. Contacte o seu representante da conta Microsoft ou Databricks para solicitar o acesso.

* [Como ativar as chaves geridas pelo cliente para os cadernos Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Como ativar as chaves geridas pelo cliente para o Sistema de Ficheiros De Ficheiros Azure Databricks](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividade seleções do Azure para criar alertas para quando ocorrerem alterações nos espaços de trabalho críticos dos Tijolos de Dados do Azure.

* [Como criar alertas para eventos de Log de Atividade do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para mais informações, consulte Controlo de [Segurança: Gestão de vulnerabilidades.](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros.

Se tiver uma subscrição de plataforma de gestão de vulnerabilidades, poderá utilizar scripts de inicialização de Dados Azure para instalar agentes de avaliação de vulnerabilidades nos seus nós de cluster Azure Databricks e gerir os nós através do respetivo portal. Note que cada solução de terceiros funciona de forma diferente.

* [Como instalar o agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o Agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização de Databricks Azure](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: A Microsoft mantém as imagens de base do nó de cluster Azure Databricks, no entanto, é responsável por garantir que os seus nós de cluster permanecem remendados. Para adicionar uma atualização de manutenção a um cluster de execução existente, tem de reiniciar o cluster.

* [Compreender atualizações de manutenção de Tempo de Funcionar para Os Tijolos de Dados Azure](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: A Microsoft mantém as imagens de base do nó de cluster Azure Databricks, no entanto é responsável por garantir que quaisquer aplicações de terceiros que instale permanecem corrigidas.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Implementar uma solução de gestão de vulnerabilidades de terceiros que tenha a capacidade de comparar scans de vulnerabilidade ao longo do tempo. Se tiver uma subscrição de gestão de vulnerabilidades, poderá utilizar o portal do fornecedor para visualizar e comparar os exames de vulnerabilidade consecutivos. Note que cada solução de terceiros funciona de forma diferente.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas.

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, Sistema Comum de Pontuação de Vulnerabilidades) ou as classificações de risco predefinidas fornecidas pela sua ferramenta de digitalização de terceiros.

**Monitorização**do Centro de Segurança Azure : N/A

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte Controlo de [Segurança: Inventário e gestão de ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua subscrição(s). Certifique-se de que existem permissões adequadas (ler) no seu inquilino e enumera todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Gestor de Recursos Azure para avançar.

* [Como criar consultas com o Gráfico de Recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Como visualizar as suas Assinaturas Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Compreender o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Além disso, utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos e títulos de software aprovados do Azure.

**Orientação**: Definir recursos azure aprovados e software aprovado para recursos computacionais.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; O Azure Databricks é um serviço PaaS, os clientes não têm acesso direto aos VMs num cluster de Databricks Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.), incluindo instâncias de Azure Databricks, dentro da sua subscrição(s). Remova quaisquer recursos Azure não aprovados que descubra. Para os nós de cluster Azure Databricks, implemente uma solução de terceiros para remover ou alertar em software não aprovado.

* [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; O Azure Databricks é um serviço PaaS, não tem acesso direto aos VMs num cluster de Databricks Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; O Azure Databricks é um serviço PaaS, não tem acesso direto aos VMs num cluster de Databricks Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limite a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts</div>

**Orientação**: Utilize o Acesso Condicional azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure configurando o "Block access" para a app "Microsoft Azure Management".

* [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; isto não é aplicável aos Bricks Azure, uma vez que os utilizadores (não administradores) do cluster não necessitam de acesso aos nós individuais para executar postos de trabalho. O administrador do cluster tem acesso raiz a todos os nós do cluster por padrão.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; o benchmark destina-se ao Azure Apps Service ou aos recursos de computação que hospedam aplicações web.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para mais informações, consulte [O controlo de segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os casos de Tijolos de Dados Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Databricks Azure. Note que quaisquer políticas aplicadas não funcionam no Grupo de Recursos Geridos de Databricks.

* [Como ver os Aliases políticos do Azure disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; O Azure Databricks é um serviço PaaS, não tem acesso direto aos VMs num cluster de Databricks Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os casos de Tijolos de Dados Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Databricks Azure. Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Compreender efeitos políticos do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Sistema operativo Azure Databricks Imagens geridas e mantidas pela Microsoft. É responsável pela implementação da configuração do estado de nível osso.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições políticas personalizadas do Azure, utilize o Azure DevOps ou o Azure Repos para armazenar e gerir de forma segura o seu código.

* [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Se utilizar imagens personalizadas para os seus nós de cluster Azure Databricks, empurre a sua imagem de base personalizada para um registo de Docker, como o Registo de Contentores Azure (ACR).

* [Como personalizar contentores com serviços de contentores de databricks](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Compreender o Registo de Contentores De Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Defina e implemente configurações de segurança padrão para os casos de Tijolos de Dados Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Databricks Azure.

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Como ver os Aliases políticos do Azure disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; O Azure Databricks é um serviço PaaS, não tem acesso direto aos VMs num cluster de Databricks Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Defina e implemente configurações de segurança padrão para os casos de Tijolos de Dados Azure com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Databricks" para criar políticas personalizadas para auditar ou impor a configuração dos seus casos de Databricks Azure.

* [Como ver os Aliases políticos do Azure disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Implemente uma solução de terceiros para monitorizar o estado dos seus sistemas operativos de nó de cluster Azure Databricks.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize o Cofre de Chaves Azure com um âmbito secreto de Databricks Azure para gerir e utilizar de forma segura segredos.

* [Como usar o Cofre chave Azure com tijolos de dados Azure](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Atualmente não disponível; Identidades geridas não estão atualmente disponíveis para os Tijolos de Dados Do Azure

* [Serviços que suportam identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure.

* [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte Controlo de [Segurança: Defesa de Malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Se tiver uma subscrição de plataforma de gestão de vulnerabilidades, poderá utilizar scripts de inicialização de Dados Azure para instalar agentes de avaliação de vulnerabilidadenos nós de cluster do Seu Azure Databricks e gerir os nós através do respetivo portal. Note que cada solução de terceiros funciona de forma diferente.

* [Como instalar o agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o Agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização de Databricks Azure](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O Microsoft Antimalware está ativado no hospedeiro subjacente que suporta os serviços Azure (por exemplo, O Serviço de Aplicações Azure), no entanto não funciona com o seu conteúdo.

Pré-digitalização de quaisquer ficheiros que sejam enviados para os seus nós de cluster Azure Databricks ou recursos relacionados.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Se tiver uma subscrição de plataforma de gestão de vulnerabilidades, poderá utilizar scripts de inicialização de Dados Azure para instalar agentes de avaliação de vulnerabilidadenos nós de cluster do Seu Azure Databricks e gerir os nós através do respetivo portal. Note que cada solução de terceiros funciona de forma diferente.

* [Como instalar o agente Rapid7 manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Como instalar o Agente Qualys manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Scripts de inicialização de Databricks Azure](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para mais informações, consulte Controlo de [Segurança: Recuperação de dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Para as fontes de dados do Seu Azure Databricks, certifique-se de que configura um nível adequado de redundância de dados para o seu caso de utilização. Por exemplo, se utilizar uma conta de Armazenamento Azure para a sua loja de dados Azure Databricks, escolha a opção de redundância adequada (LRS, ZRS, GRS, RA-GRS).

* [Fontes de dados de Tijolos de Dados Azure](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Recuperação regional de desastres para clusters de Databricks Azure](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: Backup de chaves geridas pelo cliente relacionadas com as implementações dos seus Tijolos de Dados Azure dentro do Cofre chave Azure. Também pode utilizar rest API e CLI para criar uma cópia de segurança diária das configurações de Databricks. Também pode utilizar o REST API/CLI para criar uma cópia de segurança diária das configurações dos Databricks.

* [Como apoiar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste a restauração de chaves geridas por clientes apoiados relacionadas com as implementações dos seus Tijolos de Dados Azure.

* [Como restaurar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Certifique-se de que o Soft-Delete está ativado no Cofre da Chave para proteger as chaves contra a eliminação acidental ou maliciosa.

* [Como ativar o Soft-Delete no Cofre de Chaves](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte Controlo de [Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.

* [Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

* [Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

* [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas Sentinel.

* [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Como transmitir alertas para o Sentinela Azure](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

* [Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para mais informações, consulte Controlo de [Segurança: Testes de penetração e exercícios vermelhos da equipa.](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realize testes regulares de penetração dos seus recursos Azure e garanta a reparação de todos os resultados críticos de segurança no prazo de 60 dias.

**Orientação**: * Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de [Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte o referencial de [segurança azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
