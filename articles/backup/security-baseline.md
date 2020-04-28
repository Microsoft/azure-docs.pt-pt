---
title: Linha de base de segurança azure para backup
description: Linha de base de segurança azure para backup
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: acc8ceaa96f6fddafdbf4aa74ab9e43a1a60714a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195677"
---
# <a name="azure-security-baseline-for-backup"></a>Linha de base de segurança azure para backup

A Linha de Base de Segurança Azure para Backup contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Não aplicável; não é possível associar uma rede virtual, subnet ou grupo de Segurança de Rede a um cofre de Serviços de Recuperação. Ao apoiar uma máquina virtual Azure, os dados são transferidos sobre a espinha dorsal azure. Ao recuar a partir de uma máquina no local, um túnel encriptado é criado com um ponto final específico em Azure e credenciais são usadas para pré-encriptar os dados antes de serem enviados através do túnel encriptado.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Não aplicável; não é possível associar uma rede virtual, subnet ou grupo de Segurança de Rede a um cofre de Serviços de Recuperação. Ao apoiar uma máquina virtual Azure, os dados são transferidos sobre a espinha dorsal azure. Ao recuar a partir de uma máquina no local, um túnel encriptado é criado com um ponto final específico em Azure e credenciais são usadas para pré-encriptar os dados antes de serem enviados através do túnel encriptado.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Os pontos finais utilizados pelo Azure Backup (incluindo o agente dos Serviços de Recuperação do Microsoft Azure) são todos geridos pela Microsoft. É responsável por quaisquer controlos adicionais que deseje implementar para os seus sistemas no local.

- [Compreender o suporte de networking e acesso ao agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#networking-and-access-support)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Não aplicável; não é possível associar uma rede virtual, subnet ou grupo de Segurança de Rede a um cofre de Serviços de Recuperação. Ao apoiar uma máquina virtual Azure, os dados são transferidos sobre a espinha dorsal azure. Ao recuar a partir de uma máquina no local, um túnel encriptado é criado com um ponto final específico em Azure e credenciais são usadas para pré-encriptar os dados antes de serem enviados através do túnel encriptado.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Os pontos finais utilizados pelo Azure Backup (incluindo o agente dos Serviços de Recuperação do Microsoft Azure) são todos geridos pela Microsoft. É responsável por quaisquer controlos adicionais que deseje implementar para os seus sistemas no local.

- [Compreender o suporte de networking e acesso ao agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#networking-and-access-support)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Se estiver a utilizar o agente MARS numa Máquina Virtual Azure, utilize a etiqueta de serviço AzureBackup no seu NSG ou Azure Firewall para permitir o acesso de saída à Cópia de Segurança Azure.

- [Bases de dados de backup SQL Server em VMs Azure](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Não aplicável; os pontos finais utilizados pelo Azure Backup (incluindo o agente dos Serviços de Recuperação do Microsoft Azure) são todos geridos pela Microsoft.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Se estiver a utilizar o agente MARS numa Máquina Virtual Azure, associe que a VM com um grupo de segurança de rede utilize a descrição para especificar a necessidade de negócio para a regra

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Se estiver a utilizar o agente MARS numa Máquina Virtual Azure que está a ser protegida por uma NSG ou uma Firewall Azure, utilize o Registo de Atividade sinuoso para monitorizar a configuração do NSG ou firewall. Poderá criar alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações a estes recursos.

- [Ver e recuperar eventos de registo de atividade seleções do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Criar, visualizar e gerir alertas de registo de atividade utilizando o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: Não aplicável; A Microsoft mantém a fonte de tempo utilizada para os recursos Do Azure, como o Azure Backup, para os timestamps nos registos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Para controlar a exploração de registos de auditoria de planos, ative as definições de diagnóstico do Azure Activity Log e envie os registos para um espaço de trabalho de Log Analytics, hub de eventos Azure ou conta de armazenamento Azure para arquivo. Utilizando dados do Registo de Atividade sinuoso do Azure, pode determinar o "quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Além disso, ingerir registos via Azure Monitor para agregar dados de segurança gerados pela Azure Backup. Dentro do Monitor Azure, utilize o (s) espaço de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de armazenamento para armazenamento a longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para o Azure Sentinel ou para um incidente de segurança e gestão de eventos de terceiros (SIEM).

- [Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Utilização de configurações de diagnóstico para cofres de serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Para controlar a exploração de registos de auditoria de planos, ative as definições de diagnóstico do Azure Activity Log e envie os registos para um espaço de trabalho de Log Analytics, hub de eventos Azure ou conta de armazenamento Azure para arquivo. Utilizando dados do Registo de Atividade sinuoso do Azure, pode determinar o "quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para os seus recursos Azure.

Além disso, o Azure Backup envia eventos de diagnóstico que podem ser recolhidos e utilizados para efeitos de análise, alerta e reporte. Pode configurar as definições de diagnóstico para um Cofre de Serviços de Recuperação através do portal Azure. Pode enviar um ou mais eventos de diagnóstico para uma Conta de Armazenamento, Centro de Eventos ou um espaço de trabalho log Analytics.

- [Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Utilização de configurações de diagnóstico para cofres de serviços de recuperação](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: No Monitor Azure, detete o período de retenção de registos para os espaços de trabalho do Log Analytics associados aos cofres dos serviços de recuperação do Azure de acordo com as regras de conformidade da sua organização.

- [Como definir parâmetros de retenção de troncos](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: A Azure Backup fornece capacidades de monitorização e alerta incorporadas num cofre dos Serviços de Recuperação. Estas capacidades estão disponíveis sem qualquer infraestrutura de gestão adicional. Também pode aumentar a escala da sua monitorização e reportagem utilizando o Monitor Azure.

Ative as definições de diagnóstico do Registo de Atividade si e envie os registos para um espaço de trabalho de Log Analytics. Execute consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos Dados de Registo de Atividade que podem ter sido recolhidos para cofres de Serviços de Recuperação.

- [Monitorização de cargas de trabalho de backup azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)

- [Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como recolher e analisar registos de atividade do Azure no espaço de trabalho do Log Analytics no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: A Azure Backup fornece capacidades de monitorização e alerta incorporadas num cofre dos Serviços de Recuperação. Estas capacidades estão disponíveis sem qualquer infraestrutura de gestão adicional. Também pode aumentar a escala da sua monitorização e reportagem utilizando o Monitor Azure.

Os alertas são principalmente cenários em que os utilizadores são notificados para que possam tomar medidas relevantes. A secção Backup Alerts mostra alertas gerados pelo serviço de backup Azure. Estes alertas são definidos pelo serviço e não é possível criar quaisquer alertas personalizados.

Também pode embarcar num espaço de trabalho de Log Analytics para o Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar e utilizar os livros de jogadas (soluções automatizadas) para remediar problemas de segurança. Além disso, pode criar alertas de registo personalizados no seu espaço de trabalho Log Analytics utilizando o Monitor Azure.

- [Monitorização de cargas de trabalho de backup azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Criar, visualizar e gerir alertas de registo usando o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável; O Azure Backup não processa nem produz registos relacionados com anti-malware.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável; A Azure Backup não processa nem produz registos relacionados com DNS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Diretório Ativo azure (AD) tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Documentação de apoio:

- [Como obter um papel de diretório em Azure AD com powerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: A AD Azure não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma palavra-passe obrigam a criar uma palavra-passe com requisitos de complexidade e um comprimento mínimo de senha, que difere consoante o serviço. Você é responsável por aplicações de terceiros e serviços de mercado que podem usar senhas padrão.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Identidade e Gestão de Acesso do Azure Security Center para monitorizar o número de contas administrativas.

Além disso, para o ajudar a acompanhar as contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou das Políticas Azure incorporadas, tais como: Deve haver mais de um proprietário atribuído à sua subscrição Contas Deprecated com permissões do proprietário devem ser removidas da sua subscrição As contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Como utilizar o Azure Security Center para monitorizar a identidade e o acesso (Pré-visualização)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Como usar a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: Utilize um registo de aplicações Azure (diretor de serviço) para recuperar um símbolo que possa ser usado para interagir com os seus cofres de Serviços de Recuperação através de chamadas API.

- [Como chamar APIs de REST Azure](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registar a sua aplicação de cliente (diretor de serviço) com a Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informação da API dos Serviços de Recuperação do Azure](https://docs.microsoft.com/rest/api/recoveryservices/)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Quando realiza operações críticas em Backup Azure, tem de introduzir um PIN de segurança, disponível no portal Azure. Ativar a autenticação multi-factor Azure adiciona uma camada de segurança. Apenas utilizadores autorizados com credenciais Azure válidas, e autenticados a partir de um segundo dispositivo, podem aceder ao portal Azure.

- [Autenticação multi-factor em backup azure](https://docs.microsoft.com/azure/backup/backup-azure-security-feature)

- [Planear a implementação da Multi-Factor Authentication do Azure baseada na cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiado (PAW) com autenticação de multi-factores Azure (MFA) configurada para iniciar sessão e configurar os seus recursos ativados por backup Azure.

- [Estações de Trabalho de Acesso Privilegiado](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Planear a implementação da Multi-Factor Authentication do Azure baseada na cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AD) Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente.

Além disso, utilize deteções de risco azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como implementar a Gestão de Identidade Privilegiada (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Compreender as deteções de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Utilizar locais de acesso condicional nomeados para permitir o acesso ao portal Azure a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (AD) como sistema central de autenticação e autorização para as suas instâncias de Backup Azure. A Azure AD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A Azure AD também sais, hashes e armazena de forma segura credenciais de utilizador.

- [Como configurar o Backup Azure para utilizar o login Azure AD](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Como criar e configurar uma instância AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Diretório Ativo Azure (AD) fornece registos para ajudá-lo a descobrir contas velhas. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Compreender relatórios da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Como utilizar avaliações de acesso à identidade do Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Utilize o Diretório Ativo Azure (AD) como sistema central de autenticação e autorização para as suas instâncias de Backup Azure. A Azure AD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A Azure AD também sais, hashes e armazena de forma segura credenciais de utilizador.

Tem acesso a fontes de registo de registo de eventos de acesso à Azure AD, auditoria e registo de eventos de risco, que lhe permitem integrar-se com o Azure Sentinel ou com um SIEM de terceiros.

Pode simplificar este processo criando definições de diagnóstico para contas de utilizadores da AD Azure e enviando os registos de auditoria e registos de log-in para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo desejados dentro do Log Analytics.

- [Como integrar registos de atividade do Azure no Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o Diretório Ativo Azure (AD) como sistema central de autenticação e autorização para os seus cofres de Serviços de Recuperação. Para desviar o comportamento de login de conta no plano de controlo (portal Azure), utilize funcionalidades de Proteção de Identidade Azure AD e deteção de riscos para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Também pode ingerir dados no Azure Sentinel para mais investigação.

- [Como configurar o Backup Azure para utilizar o login Azure AD](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Como ver o sign-in de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e ativar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Atualmente não disponível; O Bloqueio do Cliente ainda não é suportado para o Azure Backup.

- [Lista de serviços apoiados pelo Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Ao apoiar os VMs Azure IaaS, o Azure Backup fornece cópias de segurança independentes e isoladas para se proteger contra a destruição acidental de dados originais. As cópias de segurança são armazenadas num cofre dos Serviços de Recuperação com gestão incorporada dos pontos de recuperação.

Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e serviços de recuperação de produção Vaults. Os recursos devem ser separados por VNet/Subnet, marcados adequadamente e protegidos por um NSG ou Firewall Azure. Os recursos que armazenam ou processam dados sensíveis devem ser suficientemente isolados. Para máquinas virtuais armazenando ou processando dados sensíveis, implemente a política e o procedimento(s) para desligá-los quando não estão a ser utilizados.

Documentação de apoio:

- [Visão geral do Backup Azure](https://docs.microsoft.com/azure/backup/backup-overview)

- [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Atualmente não disponível; As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Backup.

A Microsoft gere a infraestrutura subjacente ao Azure Backup e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: O tráfego de backup dos servidores para o cofre dos Serviços de Recuperação é transferido através de um link HTTPS seguro e encriptado utilizando advanced Encryption Standard (AES) 256 quando armazenado no cofre.

- [Compreender a encriptação em repouso em Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Atualmente não disponível; As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Backup.

A Microsoft gere a infraestrutura subjacente ao Azure Backup e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: O Controlo de Acesso baseado em funções azure (RBAC) permite uma gestão de acesso de grãos finos para o Azure. Ao utilizar o RBAC, pode segregar funções na sua equipa e conceder apenas a quantidade de acesso a utilizadores que precisam para desempenhar as suas funções.

A Azure Backup fornece três funções incorporadas para controlar as operações de gestão de backup: Backup Contributor, Backup Operator e Backup Reader. Você pode mapear papéis de backup incorporados para várias ações de gestão de backup.

- [Como configurar o RBAC em Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

- [Utilize o Controlo de Acesso baseado em funções para gerir pontos de recuperação de backup do Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos. A Microsoft gere a infraestrutura subjacente ao Azure Backup e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

- [Proteção de dados dos clientes azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: O Azure Backup suporta encriptação para dados de repouso. Para cópias de segurança no local, a encriptação inativa de dados é fornecida ao utilizar a frase de acesso que forneceu ao fazer cópias de segurança no Azure. Para cargas de trabalho na nuvem, os dados são encriptados em repouso utilizando encriptação do serviço de armazenamento (SSE). A Microsoft não desencripta os dados da cópia de segurança em momento algum.

Ao fazer backup com o agente MARS ou utilizar um cofre de Serviços de Recuperação encriptado com uma chave gerida pelo cliente, apenas tem acesso à chave de encriptação. A Microsoft nunca mantém uma cópia e não tem acesso à chave. Se a chave estiver deslocada, a Microsoft não pode recuperar os dados de backup.

- [Compreender a encriptação em repouso para o Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo da Atividade Azure para criar alertas para quando ocorrerem alterações na produção de cofres dos Serviços de Recuperação Azure, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Log de Atividade do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Ainda não disponível; A avaliação de vulnerabilidade no Azure Security Center ainda não está disponível para o Azure Backup.

Plataforma subjacente digitalizada e corrigida pela Microsoft. Reveja os controlos de segurança disponíveis para o Azure Backup para reduzir as vulnerabilidades relacionadas com a configuração do serviço.

- [Compreender os controlos de segurança disponíveis para o Backup Azure](https://docs.microsoft.com/azure/backup/backup-security-controls)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Atualmente não disponível; As configurações de segurança para o Azure Backup ainda não são suportadas no Azure Security Center.

- [Lista de serviços de segurança do Azure suportados pela PaaS](https://docs.microsoft.com/azure/security-center/features-paas)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro da sua subscrição(s).  Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Gestor de Recursos Azure para avançar.

- [Como criar consultas com o Gráfico de Recursos Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Como visualizar as suas Assinaturas Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.

- [Como criar e usar tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Além disso, utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas: Tipos de recursos não autorizados

- [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Definir recursos azure aprovados e software aprovado para recursos computacionais.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na sua subscrição.

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a Política Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas: Tipos de recursos não autorizados

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limite a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts</div>

**Orientação**: Configure O Acesso Condicional azure para limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure configurando o "Block access" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar fisicamente ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Defina e implemente configurações de segurança padrão para o seu cofre de Serviços de Recuperação com a Política Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.RecoveryServices" para criar políticas personalizadas para auditar ou impor a configuração dos seus cofres de Serviços de Recuperação.

- [Como ver os Aliases políticos do Azure disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Utilize a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Compreender efeitos políticos do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições de política azure personalizadas, utilize O Azure DevOps ou o Azure Repos para armazenar e gerir de forma segura o seu código.

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize definições de política azure incorporadas, bem como pseudónimos da Política Azure no espaço de nome "Microsoft.RecoveryServices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize definições de política azure incorporadas, bem como pseudónimos da Política Azure no espaço de nome "Microsoft.RecoveryServices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente as configurações para os seus recursos Azure.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Ao configurar o agente MARS, guarde a sua palavra-passe de encriptação dentro do Cofre de Chaves Azure.

- [Como criar um Cofre chave](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Como fornecer a autenticação do Cofre Chave com uma identidade gerida](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Não aplicável; Identidades geridas não suportadas para o Azure Backup.

- [Serviços que suportam identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos. O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Do Azure (por exemplo, Azure Backup), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O Microsoft Antimalware está ativado no hospedeiro subjacente que suporta os serviços Azure (por exemplo, Azure Backup), no entanto não funciona com o seu conteúdo.

Pré-digitalização de quaisquer ficheiros que sejam enviados para recursos não computacionais do Azure, tais como Serviço de Aplicações, Armazenamento de Data Lake, Armazenamento blob, etc.

Utilize a deteção de ameaças do Azure Security Center para os serviços de dados para detetar malware enviado para contas de armazenamento.

- [Compreenda o Microsoft Antimalware para Serviços Azure Cloud e Máquinas Virtuais](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

- [Compreender a deteção de ameaças do Azure Security Center para serviços de dados](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Não aplicável; esta recomendação destina-se a que os recursos sejam apoiados e não o próprio Azure Backup.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: Armazenamento redundante localmente (LRS) replica os seus dados três vezes (cria três cópias dos seus dados) numa unidade de escala de armazenamento num datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger os seus dados de falhas de hardware locais. O armazenamento geo-redundante (GRS) é a opção de replicação predefinida e recomendada. O GRS replica os dados para uma região secundária (a centenas de quilómetros da localização primária da origem de dados). GrS custa mais do que LRS, mas GRS fornece um nível mais alto de durabilidade para os seus dados, mesmo que haja uma paragem regional.

O cliente de reserva geria as chaves dentro do Cofre de Chaves Azure.

- [Visão geral do Backup Azure](https://docs.microsoft.com/azure/backup/backup-overview)

- [Como apoiar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Compreender a encriptação em Backup Azure](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste a restauração das chaves geridas pelo cliente.

- [Como restaurar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Para cópia de segurança no local, a encriptação em repouso é fornecida utilizando a frase de passe que fornece ao apoiar o Azure. Nas VMs do Azure, os dados são encriptados inativamente com a Encriptação do Serviço de Armazenamento (SSE). Pode ativar a eliminação suave no Cofre-Chave para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Como permitir a eliminação suave no Cofre-Chave](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.

- [Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure.

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas Sentinel.

- [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Como transmitir alertas para o Sentinela Azure](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

- [Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: - Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de [Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
