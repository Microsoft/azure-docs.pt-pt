---
title: Linha de segurança Azure para Automação
description: A linha de base de segurança automation fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ea75b9c1e8c7a1a242406b5a7f73db1b05c70276
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562210"
---
# <a name="azure-security-baseline-for-automation"></a>Linha de segurança Azure para Automação

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para a Azure Automation. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Azure Automation. Foram excluídos **os controlos** não aplicáveis à Azure Automation.

 
Para ver como a Azure Automation mapeia completamente para o Azure Security Benchmark, consulte o [ficheiro de mapeamento de base de segurança Azure Automation](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A conta Azure Automation ainda não suporta a Azure Private Link para restringir o acesso ao serviço através de pontos finais privados. Os runbooks que autenticam e funcionam contra recursos em Azure funcionam numa caixa de areia Azure, e alavancam recursos de backend partilhados, que a Microsoft é responsável por isolar uns dos outros; a sua ligação em rede é ilimitada e pode aceder aos recursos públicos. A Azure Automation não possui atualmente integração de rede virtual para redes privadas para além do suporte para trabalhadores híbridos runbook. Este controlo não é aplicável se estiver a utilizar o serviço fora da caixa sem trabalhadores híbridos runbook.

Para obter um maior isolamento para os seus runbooks, pode utilizar trabalhadores híbridos runningbook em máquinas virtuais Azure. Quando criar uma máquina virtual Azure, tem de criar uma rede virtual (VNet) ou utilizar um VNet existente e configurar os seus VMs com uma sub-rede. Certifique-se de que todas as sub-redes implementadas têm um Grupo de Segurança de Rede aplicado com controlos de acesso à rede específicos das suas aplicações portas e fontes fidedignas. Para requisitos específicos do serviço, consulte a recomendação de segurança para esse serviço específico. Em alternativa, se tiver um requisito específico, o Azure Firewall também pode ser utilizado para o cumprir.

- [Redes virtuais e máquinas virtuais em Azure](../virtual-machines/network-overview.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Ambiente de execução de runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: A Azure Automation não tem atualmente integração de rede virtual para redes privadas para além do suporte para trabalhadores híbridos runbook. Este controlo não é aplicável se estiver a utilizar o serviço fora da caixa sem trabalhadores híbridos runbook.

Se estiver a utilizar trabalhadores híbridos com suporte de máquinas virtuais Azure, certifique-se de que a sub-rede que contém esses trabalhadores está ativada com um Grupo de Segurança de Rede (NSG) e configurar registos de fluxo para encaminhar registos para uma Conta de Armazenamento para auditoria de tráfego. Também pode encaminhar os registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Embora as regras nSG e as rotas definidas pelo utilizador não se apliquem ao ponto final privado, os registos de fluxo NSG e as informações de monitorização das ligações de saída ainda são suportados e podem ser utilizados.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: A Azure Automation não possui atualmente integração de rede virtual para redes privadas para além do suporte para trabalhadores híbridos runbook. Este controlo não é aplicável se estiver a utilizar o serviço fora da caixa sem trabalhadores híbridos runbook.

Se estiver a utilizar trabalhadores híbridos com apoio de máquinas virtuais Azure, ative a proteção padrão de Negação de Serviço Distribuída (DDoS) nas suas Redes Virtuais que hospedam os seus Trabalhadores De Runbook Híbridos para se proteger contra ataques DDoS. Ao utilizar a Azure Security Center Integrated Threat Intelligence, pode negar comunicações com endereços IP maliciosos conhecidos.  Configure Azure Firewall em cada um dos segmentos da Rede Virtual, com a Inteligência de Ameaça ativada, e configurar para **alertar e negar** para tráfego de rede malicioso.

Pode utilizar o acesso da Rede Just In Time do Azure Security Center para limitar a exposição das máquinas virtuais do Windows aos endereços IP aprovados por um período limitado de tempo.  Além disso, utilize recomendações de endurecimento de rede adaptativa do Azure Security Center para configurações de NSG para limitar portas e IPs de origem com base na inteligência real de tráfego e ameaça.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

- [Compreender o Hardenive de Rede Adaptável do Centro de Segurança Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Compreender o Centro de Segurança Azure Mesmo no Tempo Controlo de Acesso à Rede](../security-center/security-center-just-in-time.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: A Azure Automation não tem atualmente integração de rede virtual para redes privadas para além do suporte para trabalhadores de runbook híbridos, este controlo não é aplicável se estiver a utilizar o serviço fora da caixa sem trabalhadores híbridos.

Se estiver a utilizar trabalhadores híbridos com o apoio de máquinas virtuais Azure, então pode gravar registos de fluxo NSG numa conta de armazenamento para gerar registos de fluxo para as suas Máquinas Virtuais Azure que estão a agir como trabalhadores de runbook. Ao investigar uma atividade anómala, pode ativar a captura de pacotes do Network Watcher para que o tráfego de rede possa ser revisto para uma atividade invulgar e inesperada.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar o Observador de Redes](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: A Azure Automation não possui atualmente integração de rede virtual para redes privadas para além do suporte para trabalhadores híbridos runbook. Este controlo não é aplicável se estiver a utilizar o serviço fora da caixa sem trabalhadores híbridos runbook.

Se estiver a utilizar os Trabalhadores de Runbook Híbridos alojados em máquinas virtuais Azure, pode combinar capturas de pacotes fornecidas pelo Network Watcher e ferramentas IDS de código aberto para executar a deteção de intrusões em rede para uma ampla gama de ameaças a essas máquinas operárias. Além disso, pode implementar o Azure Firewall para os segmentos de Rede Virtual conforme apropriado, com a Threat Intelligence ativada e configurada para "alertar e negar" para tráfego de rede malicioso.

- [Realize deteção de intrusão de rede com o Network Watcher e ferramentas open source](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em Grupos de Segurança de Rede ou firewall Azure configurados em Azure que requerem acesso aos seus Recursos de Automação. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, GuestAndHybridManagement) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Definir e implementar configurações de segurança padrão para os recursos de rede utilizados pela Azure Automation com Azure Policy.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, controlos e políticas do Azure, e políticas, numa única definição de planta. Pode aplicar o projeto a novas subscrições e afinar o controlo e a gestão através da versão.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para NSGs e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas Tags.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações dos recursos e detetar alterações nos recursos da sua rede. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Encaminhar dados de registo para Azure Monitor Logs para agregar dados de segurança gerados pelos recursos da Azure Automation. Dentro do Azure Monitor, utilize consultas de registo para pesquisar e realizar análises, e utilize contas de armazenamento Azure para armazenamento de longo prazo/arquivo. A Azure Automation pode enviar o estado de trabalho do runbook, streams de trabalho, dados de configuração do estado da automação, gestão de atualização e alterar registos de rastreio ou inventário para o seu espaço de trabalho Log Analytics. Esta informação é visível a partir do portal Azure PowerShell e AZure Monitor Logs API, que permite realizar investigações simples.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Reencaminhar dados de tarefa da Automatização do Azure para os registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md)

- [Integre o DSC com registos do Monitor Azure](automation-dsc-diagnostics.md)

- [Regiões suportadas para a área de trabalho do Log Analytics ligada](how-to/region-mappings.md)

- [Registos de gestão de atualização de consulta](update-management/query-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative o Monitor Azure para acesso aos seus registos de auditoria e atividade, que incluem fonte de eventos, data, utilizador, marca de tempo, endereços de origem, endereços de destino e outros elementos úteis. 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Ver e recuperar eventos de log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desa um ponto final no seu período de retenção do espaço de trabalho Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Detalhes da retenção de dados para Contas de Automação](./automation-managing-data.md#data-retention)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados. Utilize consultas de registo do Azure Monitor para rever registos e efetuar consultas nos dados de registo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Compreenda as consultas de registo no Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Azure Security Center com o Azure Monitor para monitorizar e alertar sobre a atividade anómala encontrada em registos e eventos de segurança.

Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar os dados de registo do Azure Monitor](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Implementar uma solução de terceiros do Azure Marketplace para a solução de registo de DNS conforme as suas organizações precisam.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Utilize funções de administrador incorporado do Azure Ative (Azure AD) que podem ser explicitamente atribuídas e podem ser consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos. Sempre que utilizar a Conta Demôm automação Como conta para os seus runbooks, certifique-se de que estes principais serviços também são rastreados no seu inventário, uma vez que muitas vezes têm permissões elevadas. Elimine qualquer execução não-tousada Como contas para minimizar a superfície de ataque exposta.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Eliminar uma conta Run As ou Run As Clássica](delete-run-as-account.md)

- [Gerir uma execução de automação Azure Como conta](manage-runas-account.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: A Azure Automation Account não tem o conceito de senhas padrão.  Os clientes são responsáveis por aplicações de terceiros e serviços de marketplace que podem usar senhas padrão que funcionam em cima do serviço ou seus Trabalhadores De Runbook Híbridos.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas. Sempre que utilizar a Conta Demôm automação Como conta para os seus runbooks, certifique-se de que estes principais serviços também são rastreados no seu inventário, uma vez que muitas vezes têm permissões elevadas. Coloque estas identidades com as permissões menos privilegiadas de que necessitam para que os seus runbooks realizem com sucesso o seu processo automatizado. Elimine qualquer execução não-tousada Como contas para minimizar a superfície de ataque exposta.

Também pode ativar um Acesso Rápido / Justo-Suficiente utilizando o Azure Ative Directory (Azure AD) Funções privilegiadas de gestão de identidade privilegiada para serviços da Microsoft e gestor de recursos Azure.

- [Saiba mais sobre Gestão de Identidade Privilegiada](../active-directory/privileged-identity-management/index.yml)

- [Eliminar uma conta Run As ou Run As Clássica](delete-run-as-account.md)

- [Gerir uma execução de automação Azure Como conta](manage-runas-account.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Sempre que possível, utilize SSO com Azure Ative Directory (Azure AD) em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

- [Inscrição única para aplicações em Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

- [Utilizar o Azure AD para autenticar no Azure](automation-use-azure-ad.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs com autenticação multifactor configurado para iniciar sessão e configurar recursos da Conta de Automação Azure em ambientes de produção. 

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas 

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Deteções de Riscos para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. Opcionalmente, o cliente pode encaminhar alertas de Deteção de Risco do Centro de Segurança Azure para o Azure Monitor e configurar alertas/notificações personalizadas usando Grupos de Ação.

- [Compreender deteções de risco do Centro de Segurança Azure (atividade suspeita)](../active-directory/identity-protection/overview-identity-protection.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como configurar grupos de ação para alerta e notificação personalizados](../azure-monitor/alerts/action-groups.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas 

**Orientação**: Recomenda-se a utilização de locais nomeados de Acesso Condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores. Se utilizar trabalhadores híbridos de runbook, poderá aproveitar as identidades geridas em vez de Executar Como Contas para permitir permissões de segurança mais perfeitas.

- [Como criar e configurar instâncias do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Utilize a autenticação de runbook com identidades geridas](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize revisões de acesso à identidade Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado. Sempre que utilizar a Conta Demôm automação Como conta para os seus runbooks, certifique-se de que estes principais serviços também são rastreados no seu inventário, uma vez que muitas vezes têm permissões elevadas. Elimine qualquer execução não-tousada Como contas para minimizar a superfície de ataque exposta.

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários de acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

- [Eliminar uma conta Run As ou Run As Clássica](delete-run-as-account.md)

- [Gerir uma execução de automação Azure Como conta](manage-runas-account.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a Fontes de registo de registo de eventos de inscrição, de auditoria e de eventos de risco da Azure Ative, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring.

Pode agilizar este processo criando Definições de Diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize funcionalidades de Proteção de Risco e Identidade do Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador para o seu recurso de rede. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Para contas de automação Azure, o suporte da Microsoft pode aceder aos metadados de recursos da plataforma durante um caso de suporte aberto sem a utilização de outra ferramenta.

No entanto, ao utilizar os Trabalhadores de Runbook Híbridos apoiados por máquinas virtuais Azure e um terceiro precisa de aceder aos dados dos clientes (como durante um pedido de suporte), utilize o Customer Lockbox (Preview) para que as máquinas virtuais Azure revejam e aprovem ou rejeitem os pedidos de acesso aos dados dos clientes.

- [Compreender o bloqueio do cliente](../security/fundamentals/customer-lockbox-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure Automation que armazenam ou processam informações sensíveis. 

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Isolar ambientes utilizando recursos separados da Conta de Automação. Recursos como os Trabalhadores De Runbook Híbridos devem ser separados por rede/sub-rede virtuais, marcados adequadamente e protegidos dentro de um grupo de segurança de rede (NSG) ou Azure Firewall. Para as máquinas virtuais armazenarem ou processarem dados sensíveis, implemente políticas e procedimentos para os desligar quando não estiverem a ser utilizados.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alerta ou alerta e negar com a Azure Firewall](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ao utilizar a função Trabalhador do Runbook Híbrido, aproveite uma solução de terceiros do Azure Marketplace em perímetros de rede que monitorize para transferência não autorizada de informações sensíveis e bloqueie tais transferências enquanto alerta os profissionais de segurança da informação.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure nas redes virtuais Azure seja capaz de negociar TLS 1.2 ou superior. A Azure Automation suporta e aplica totalmente a camada de transporte (TLS) 1.2 e todas as chamadas de clientes ou versões posteriores para todos os pontos finais HTPS externos (através de webhooks, nós DSC, trabalhador de runbook híbrido).

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, sempre que aplicável.

- [Compreender a encriptação em trânsito com Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Aplicação da Azure Automation TLS 1.2](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Utilize uma ferramenta de descoberta ativa de terceiros para identificar todas as informações sensíveis armazenadas, processadas ou transmitidas pelos sistemas tecnológicos da organização, incluindo as localizadas no local ou num prestador de serviços remotos e atualize o inventário de informações sensíveis da organização.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para controlar o acesso aos recursos da Azure Automation utilizando as definições de funções incorporadas, atribua acesso aos utilizadores que acedam aos seus recursos de automação seguindo um modelo de acesso menos privilegiado ou "justo". Ao utilizar os Trabalhadores De Runbook Híbridos, aproveite as identidades geridas para que essas máquinas virtuais evitem a utilização de princípios de serviço, quando utilizar tanto o multi-inquilino como os Trabalhadores de Runbook Híbridos certifique-se de aplicar permissões Azure RBAC adequadamente scopeed sobre a identidade dos trabalhadores do runbook.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Permissões de runbook para um trabalhador de runbook híbrido](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Gerir permissões e segurança de funções](automation-role-based-access-control.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: A Azure Automation não expõe atualmente as máquinas virtuais do trabalhador de vários inquilinos subjacentes, e isso é tratado pela plataforma. Este controlo não é aplicável se estiver a utilizar o serviço fora da caixa sem trabalhadores híbridos runbook.

Se estiver a utilizar trabalhadores híbridos com o apoio de máquinas virtuais Azure, então precisa de utilizar uma solução de prevenção de perda de dados baseada em terceiros para impor controlos de acesso às máquinas virtuais do Trabalhador De Runbook Híbrido.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Utilize chaves geridas pelo cliente com a Azure Automation. A Azure Automation suporta o uso de chaves geridas pelo cliente para encriptar todos os "ativos seguros" utilizados, tais como: credenciais, certificados, ligações e variáveis encriptadas. Aproveite as variáveis encriptadas com os seus runbooks para todas as suas necessidades de procura variável persistentes para evitar exposição não intencional.

Ao utilizar os Trabalhadores de Runbook Híbridos, os discos virtuais nas máquinas virtuais são encriptados em repouso utilizando a encriptação do lado do servidor ou a encriptação do disco Azure (ADE). A encriptação do disco Azure aproveita a funcionalidade BitLocker do Windows para encriptar discos geridos com chaves geridas pelo cliente dentro do VM do hóspede. A encriptação do lado do servidor com as teclas geridas pelo cliente melhora no ADE, permitindo-lhe utilizar quaisquer tipos e imagens de OS para os seus VMs encriptando dados no serviço de Armazenamento.

- [Encriptação lateral do servidor dos discos geridos pelo Azure](../virtual-machines/disk-encryption.md)

- [Encriptação do disco Azure para VMs windows](../virtual-machines/windows/disk-encryption-overview.md)

- [Utilização de chaves geridas pelo cliente para uma conta de Automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Variáveis geridas na Azure Automation](shared-resources/variables.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Automation**:

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com Log de atividade azure para criar alertas para quando ocorrerem alterações para recursos críticos do Azure, como componentes de rede, contas de Automação Azure e runbooks. 

- [Registo de diagnóstico para um grupo de segurança de rede](../private-link/private-link-overview.md#logging-and-monitoring)

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus recursos Azure

- [Recomendações de segurança no Centro de Segurança do Azure](../security-center/security-center-recommendations.md)

- [Referência de recomendação do Centro de Segurança](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Exportar resultados de digitalização a intervalos consistentes e comparar os resultados para verificar se as vulnerabilidades foram remediadas. Ao utilizar a recomendação de gestão de vulnerabilidades sugerida pelo Azure Security Center, o cliente pode entrar no portal da solução selecionada para visualizar dados históricos de digitalização.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center para ajudar a priorizar a remediação de vulnerabilidades descobertas.

- [Compreenda a pontuação segura do Centro de Segurança Azure](../security-center/secure-score-security-controls.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descubra todos os recursos da Azure Automation dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

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

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos da Azure Automation. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil. Elimine qualquer execução não-tousada Como contas para minimizar a superfície de ataque exposta.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Eliminar uma conta Run As ou Run As Clássica](delete-run-as-account.md)

- [Gerir uma execução de automação Azure Como conta](manage-runas-account.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Terá de criar um inventário dos recursos Azure aprovados e software aprovado para obter recursos de computação de acordo com as suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada: 

- Tipos de recursos não permitidos 
- Tipos de recursos permitidos 

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro de subscrições. Isto pode ajudar em ambientes de alta segurança, como aqueles com contas de Armazenamento. 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Amostra de política Azure incorporada para a Azure Automation](policy-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: O cliente pode impedir a criação ou utilização de recursos com a Azure Policy, conforme exigido pelas diretrizes da empresa do cliente. Pode implementar o seu próprio processo de remoção de recursos não autorizados. Dentro da Azure Automation oferecendo que é possível instalar, remover e gerir os módulos PowerShell ou Python que os livros de execução podem aceder através do Portal ou cmdlets. O módulo não aprovado ou antigo deve ser removido ou atualizado para os livros de execução.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Gerir módulo em Azure Automation](shared-resources/modules.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize políticas de acesso condicionado Azure para limitar a capacidade de interação dos utilizadores com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management" a partir de locais ou dispositivos não protegidos ou não aprovados. 

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração da sua Automatização Azure e recursos conexos. Também pode utilizar definições de Política Azure incorporadas.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo na Notação de Objetos JavaScript (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua organização.

Também pode utilizar recomendações do Azure Security Center como uma base de configuração segura para os seus recursos Azure.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Amostra de política Azure incorporada para a Azure Automation](policy-reference.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Utilize modelos de Gestor de Recursos Azure e Política Azure para configurar de forma segura os recursos Azure associados à Azure Automation. Os modelos do Gestor de Recursos Azure são ficheiros baseados em JSON utilizados para implementar recursos Azure, e quaisquer modelos personalizados terão de ser armazenados e mantidos de forma segura num repositório de código. Utilize a função de integração do controlo de fonte para manter os seus livros na sua conta Automation até à data com scripts no seu repositório de controlo de origem. Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Utilizar a integração do controlo de código fonte](source-control-integration.md)

- [Informação sobre a criação de modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

- [Implementar uma Conta de Automação utilizando um modelo de Gestor de Recursos Azure](/azure/automation/quickstart-create-automation-account-template)

- [Amostra de política Azure incorporada para a Azure Automation](policy-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize devOps Azure para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure e scripts de Configuração do Estado Desejado. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS. Utilize a função de integração do controlo de fonte para manter os seus livros na sua conta Automation até à data com scripts no seu repositório de controlo de origem.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Utilizar a integração do controlo de código fonte](source-control-integration.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para recursos Azure utilizando a Política Azure. Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure. Você também pode fazer uso de definições políticas incorporadas relacionadas com os seus recursos específicos. 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](../governance/policy/concepts/definition-structure.md#aliases)

- [Amostra de política Azure incorporada para a Azure Automation](policy-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize a Política Azure para alertar e auditar configurações de recursos Azure, a política pode ser usada para detetar determinados recursos não configurados com um ponto final privado.

Ao utilizar a função Trabalhador do Runbook Híbrido, aproveite o Azure Security Center para realizar verificações de linha de base para as suas máquinas Azure Virtual.  Os métodos adicionais para configuração automatizada incluem: Azure Automation State Configuration.

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

- [Começando com a configuração do Estado da Automação Azure](automation-dsc-getting-started.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostra de política Azure incorporada para a Azure Automation](policy-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Utilize o Gestor de Recursos Azure para implementar contas de Automação Azure e recursos conexos. O Azure Resource Manager fornece a capacidade de exportar modelos que podem ser usados como backups para restaurar contas de Automação Azure e recursos relacionados. Utilize a Azure Automation para chamar regularmente o modelo de Azure Resource Manager para exportar API.

Utilize a função de integração do controlo de fonte para manter os seus livros na sua conta Automation até à data com scripts no seu repositório de controlo de origem.

- [Descrição geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Referência do modelo do Gestor de Recursos Azure para os recursos da Azure Automation](/azure/templates/microsoft.automation/allversions)

- [Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure](quickstart-create-automation-account-template.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos - Modelo de exportação](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à Azure Automation](automation-intro.md)

- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Utilização de chaves geridas pelo cliente para uma conta de Automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Utilizar a integração do controlo de código fonte](source-control-integration.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Utilize o Gestor de Recursos Azure para implementar contas de Automação Azure e recursos conexos. O Azure Resource Manager fornece a capacidade de exportar modelos que podem ser usados como backups para restaurar contas de Automação Azure e recursos relacionados. Utilize a Azure Automation para chamar regularmente o modelo de Azure Resource Manager para exportar API. Faça o reforço das chaves geridas pelo cliente dentro do Cofre da Chave Azure. Pode exportar os seus livros para scripts através do portal Azure ou do PowerShell.

- [Descrição geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Referência do modelo do Gestor de Recursos Azure para os recursos da Azure Automation](/azure/templates/microsoft.automation/allversions)

- [Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure](quickstart-create-automation-account-template.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos - Modelo de exportação](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à Azure Automation](automation-intro.md)

- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Utilização de chaves geridas pelo cliente para uma conta de Automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Backup de dados Azure para Contas de Automação](./automation-managing-data.md#data-backup)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Garantir a capacidade de executar periodicamente a implementação de modelos do Gestor de Recursos Azure regularmente para uma subscrição isolada, se necessário. Teste de restauração de chaves geridas pelo cliente.

- [Implementar recursos com modelos ARM e portal Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Utilização de chaves geridas pelo cliente para uma conta de Automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como modelos do Azure Resource Manager. Para proteger os recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se estiverem integrados com Azure DevOps ou Ative Directory se integrados com TFS.

Utilize a função de integração do controlo de fonte para manter os seus livros na sua conta Automation até à data com scripts no seu repositório de controlo de origem.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Utilizar a integração do controlo de código fonte](source-control-integration.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança 

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
