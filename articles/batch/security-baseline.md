---
title: Linha de base de segurança azure para lote
description: Linha de base de segurança azure para lote
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fef1afb9748fd6f2f59b972c451d95ae1204ef70
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011638"
---
# <a name="azure-security-baseline-for-batch"></a>Linha de base de segurança azure para lote

A Linha de Base de Segurança Azure para Lote contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Implante o conjunto de lotes azure dentro da rede virtual. Para permitir que os nós de computação de piscina saem com segurança com outras máquinas virtuais, ou com uma rede no local, você pode fornecer a piscina em uma subnet de uma rede virtual Azure. Além disso, a implementação do seu Pool dentro de uma rede virtual dá-lhe controlo sobre o grupo de segurança da rede (NSG) usado para proteger as interfaces de rede dos nós individuais (NIC), bem como a subnet. Configure o NSG para permitir o tráfego a partir de IP(s)/locais confiáveis na Internet.

Como criar um Pool de Lote Azure dentro de uma Rede Virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICS

**Orientação**: Utilize o Azure Security Center e remediar as recomendações de proteção da rede relacionadas com o grupo de segurança de rede virtual (NSG) associado ao seu pool de Lote. Ative os registos de fluxo no NSG que está a ser utilizado para proteger a sua piscina de Lote e envie registos para uma Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho Azure Log Analytics e utilizar o Azure Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Azure Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar os registos de fluxo nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e utilizar o Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Habilitar a proteção padrão do Azure DDoS (denegação de serviço distribuído) na rede virtual que protege o seu conjunto de lotes Azure para proteção contra ataques DDoS. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.

Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Compreender o Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de discos e registos de fluxo

**Orientação**: Ativar registos de fluxo no grupo de segurança da rede (NSG) que está a ser utilizado para proteger a sua piscina de Lote Azure e enviar registos para uma Conta de Armazenamento Azure para auditoria de tráfego.

Como ativar os registos de fluxo nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implementar sistemas de deteção/deteção de intrusões baseados em rede

**Orientação**: Se necessário para efeitos de conformidade, selecione um aparelho virtual de rede do Mercado Azure que suporte sistemas de deteção de intrusões (IDS) e sistemas de prevenção de intrusões (IPS) com capacidades de inspeção de carga útil.

Se a deteção e/ou prevenção de intrusões com base na inspeção da carga útil não for um requisito, o Azure Firewall com inteligência de ameaça pode ser usado. A filtragem baseada em ameaças de firewall Azure Firewall pode alertar e negar o tráfego de e para endereços e domínios ip maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence.

Implemente o Azure Firewall com um endereço IP público na mesma rede virtual que os seus nódos de Pool De Lote Azure. Configure as regras de tradução de endereços de rede (NAT) entre localizações fidedignas na Internet e os endereços IP privados dos seus nós de piscina individuais. No Firewall Azure, sob a Inteligência de Ameaça, configure "Alert and deny" para bloquear para alertar e bloquear o tráfego de/para endereços e domínios IP maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence, e apenas os registos de confiança mais elevados estão incluídos. 

Como criar um Pool de Lote Azure dentro de uma Rede Virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Como implantar o Firewall Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Mercado Azure:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gerir o tráfego para as suas aplicações web

**Orientação**: Não aplicável, o Benchmark destina-se a aplicações web em execução em casos de Azure App Service ou IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou firewalls Azure associados à sua piscina de Lotes Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Compreender e usar etiquetas de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados ao seu pool de lotes Azure com a Política Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.Batch" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede dos seus pools de Lote Azure.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de Configuração do Tráfego de Documentos

**Orientação**: Utilize etiquetas para grupos de serviços de rede (NSGs) e outros recursos relacionados com a segurança da rede e fluxo de tráfego que estejam associados aos seus conjuntos de lotes Azure. Para regras nsg individuais, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições políticas do Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e notificá-lo de recursos não marcados existentes.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou realizar ações em recursos baseados nas suas etiquetas.

Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as piscinas do Lote Azure. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.

Como visualizar e recuperar eventos de registo de atividade sinuosa:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Como criar alertas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização do tempo aprovadas

**Orientação**: Para o Lote Azure, por padrão, a Microsoft fornece sincronização de tempo. No entanto, se tiver requisitos específicos de sincronização de tempo, poderá implementar essas alterações.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a Gestão central de registos de segurança

**Orientação**: Conta de Onboard Azure Batch ao Azure Monitor para agregar dados de segurança gerados pelos dispositivos de cluster. Aproveite as consultas personalizadas para detetar e responder a ameaças no ambiente.  Para a monitorização ao nível de recursos do Lote Azure, utilize as APIs do lote para monitorizar ou consultar o estado dos seus recursos, incluindo empregos, tarefas, nós e piscinas.

Como embarcar numa conta de Lote Azure para o Monitor Azure:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos Recursos Azure

**Orientação**: Para monitorização do nível da conta do Lote Azure, monitorize cada conta do Lote utilizando as características do Monitor Azure. O Azure Monitor recolhe métricas e registos de diagnóstico opcional para recursos mapeados ao nível de uma conta de Lote, tais como piscinas, empregos e tarefas. Recolha e consuma estes dados manualmente ou programáticamente para monitorizar as atividades na sua conta Do Lote e diagnosticar problemas.

Para a monitorização do nível de recursos do Lote Azure, utilize as APIs do Lote Azure para monitorizar ou consultar o estado dos seus recursos, incluindo empregos, tarefas, nós e piscinas.

Como configurar a monitorização e o registo ao nível da conta do Lote Azure:

https://docs.microsoft.com/azure/batch/monitoring-overview

Compreender a monitorização ao nível dos recursos do lote:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Recolher registos de segurança do sistema operativo

**Orientação**: O Monitor Azure recolhe métricas e registos de diagnóstico de recursos na sua conta Azure Batch. Recolha e consuma estes dados de várias formas de monitorizar a sua conta do Lote Azure e diagnosticar problemas. Também pode configurar alertas métricos para que receba notificações quando uma métrica atinge um valor especificado.

Se necessário, talvez se ligue aos seus nós de piscina individuais através da Secured Shell (SSH) ou do Remote Desktop Protocol (RDP) para aceder aos registos do sistema operativo local.

Como recolher registos de diagnóstico da sua conta Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Como ligar remotamente aos seus nódosos de piscina do Lote Azure:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registode segurança

**Orientação**: Conta de lote de bordo Azure para o Monitor Azure. Certifique-se de que o espaço de trabalho azure Log Analytics utilizado tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização

Como configurar a monitorização e a exploração madeireira do Lote Azure:

https://docs.microsoft.com/azure/batch/monitoring-overview

Como configurar o período de retenção do espaço de trabalho Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e Registos de Revisão

**Orientação**: Criar alertas métricos do Lote Azure que disparam quando o valor de uma métrica especificada cruza um determinado limiar.

Como configurar alertas métricos do Lote Azure:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividade anómala

**Orientação**: Criar alertas métricos do Lote Azure que disparam quando o valor de uma métrica especificada cruza um determinado limiar.

Como configurar alertas métricos do Lote Azure:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Utilize o Windows Defender nos seus nós de lote individuais no caso dos sistemas operativos Windows ou forneça a sua própria solução anti-malware se estiver a utilizar o Linux.

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Implementar solução de terceiros para a exploração madeireira dns

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de registos de auditoria da linha de comando

**Orientação**: Configure manualmente o registo de consolas e a transcrição powerShell numa base por nó.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Manter o inventário das Contas Administrativas

**Orientação**: Mantenha o registo da conta administrativa local criada durante o fornecimento do pool do Lote Azure, bem como quaisquer outras contas que crie. Além disso, se a integração do Azure Ative Directory (AAD) for utilizada, a AAD tem funções incorporadas que devem ser explicitamente atribuídas e, portanto, são consultadas. Utilize o módulo AAD PowerShell para realizar consultas adhoc para descobrir contas que são membros de grupos administrativos.

Além disso, pode utilizar recomendações de Identidade e Gestão de Acesso do Azure Security Center.

Como obter um papel de diretório em AAD com powerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório em AAD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Como monitorizar a identidade e o acesso ao Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: Ao fornecer um conjunto de lotes Azure, é-lhe dada a opção de criar contas de máquinas locais. Não existem senhas padrão para alterar, no entanto pode especificar diferentes palavras-passe para acesso de Shell (SSH) e Remote Desktop Protocol (RDP). Depois de configurado o Azure Batch Pool, pode gerar um utilizador aleatório para nós individuais dentro do portal Azure, ou através da API do Gestor de Recursos Azure.

Como adicionar um utilizador ao nó de cálculo específico:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Assegurar a utilização de Contas Administrativas Dedicadas

**Orientação**: Integrar a autenticação para aplicações de lote azure com diretório ativo Azure. Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas.

Além disso, pode utilizar recomendações de Identidade e Gestão de Acesso do Azure Security Center.

Como autenticar aplicações de Lote com Diretório Ativo Azure:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Como monitorizar a identidade e o acesso ao Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilizar um único signo (SSO) com diretório ativo Azure

**Orientação**: Não aplicável, enquanto o Lote Azure suporta a autenticação Azure AD, não é suportado um único sinal.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados no Diretório Ativo Azure.

**Orientação**: Integrar a autenticação para aplicações de lote azure com diretório ativo Azure (AAD). Enable AAD multi-factor authentication (MFA) e siga as recomendações de Identidade e Gestão de Acesso do Azure Security Center.

 

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho de acesso privilegiados) com autenticação multifactor (MFA) configurada para iniciar sessão e configurar os seus recursos do Lote Azure.

Saiba mais sobre postos de trabalho de acesso privilegiados:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registo e Alerta sobre Atividade Suspeita a partir de Contas Administrativas

**Orientação**: Se tiver autenticação integrada para aplicações de lote azure com Diretório Ativo Azure (AAD), utilize relatórios de segurança do Diretório Ativo Azure para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente. Utilize o Azure Security Center para monitorizar a atividade de identidade e acesso.

Como identificar utilizadores de Anúncios Azure sinalizados para atividades de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Do Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gerir o recurso Azure a partir de apenas locais aprovados

**Orientação**: Se tiver autenticação integrada para aplicações de lote azure com Diretório Ativo Azure, pode utilizar localizações nomeadas de acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.

Como configurar localizações nomeadas em Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (AAD) como sistema central de autenticação e autorização e integre a Autenticação para Aplicações de Lote Azure com AAD. A AAD protege os dados utilizando encriptação forte para dados em repouso e em trânsito. A AAD também sais, hashes e armazena seguramente credenciais de utilizador.

Como criar e configurar uma instância AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Como autenticar aplicações de lote com AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Diretório Ativo Azure (AAD) fornece registos para ajudar a descobrir contas velhas. Além disso, pode utilizar as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso dos utilizadores pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

Como utilizar comentários de acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorizar tentativas de acesso a contas desativadas

**Orientação**: Criar Definições de Diagnóstico para contas de utilizadores do Diretório Ativo Azure, enviando os registos de auditoria e os registos de log-in para um espaço de trabalho Azure Log Analytics. Configure os alertas desejados dentro do espaço de trabalho azure Log Analytics.

Como integrar registos de atividade do Azure no Monitor Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o diretório ativo azure (AAD) de deteção de riscos e a proteção de identidade para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Além disso, pode ingerir dados no Azure Sentinel para mais investigação.

Como visualizar inscrições arriscadas da AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte<br></div>

**Orientação**: Não disponível; O Lockbox do cliente ainda não foi suportado para o Lote Azure.
 
Lista de serviços suportados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.

Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os Pools de Lote Azure devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos com um grupo de segurança de rede (NSG). Os dados do Lote Azure devem ser contidos numa conta de armazenamento Azure segura.

Como criar um Pool de Lote Azure dentro de uma Rede Virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Como garantir as Contas de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis.

**Orientação**: Para contas de armazenamento Azure associadas ao seu Pool azure batch pool(s) que contenham informações sensíveis, marque-as como sensíveis usando tags e prenda-as com as melhores práticas do Azure.

As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os recursos de Armazenamento de Azure ou computacionais. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Como garantir as Contas de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Os recursos do Microsoft Azure negociarão o TLS 1.2 por padrão. Certifique-se de que todos os clientes que se ligam às suas Piscinas de Lote Azure ou lojas de dados (Contas de Armazenamento Azure) são capazes de negociar TLS 1.2 ou superior.

Certifique-se de que https é necessário para aceder à Conta de Armazenamento contendo os seus dados do Lote Azure.

Compreender a encriptação da conta de armazenamento Azure em trânsito:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Para contas de armazenamento Azure associadas ao seu Pool azure batch pool(s) que contenham informações sensíveis, marque-as como sensíveis usando tags e prenda-as com as melhores práticas do Azure.

As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os recursos de Armazenamento de Azure ou computacionais. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Como garantir as Contas de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções do Azure Ative Directory (AAD) para controlar o acesso ao plano de gestão de recursos Azure, incluindo a Conta de Lote, o Pool(s) e as Contas de Armazenamento.

Compreender o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Como configurar o RBAC em Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a Prevenção da Perda de Dados baseada no hospedeiro para impor o controlo de acesso

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os recursos de Armazenamento ou cálculo do Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Para contas de armazenamento associadas à sua conta De lote Dete, recomenda-se que a Microsoft gere as chaves de encriptação, no entanto, tem a opção de gerir as suas próprias chaves, se necessário.

Como gerir as chaves de encriptação das Contas de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades do Azure para criar alertas para quando ocorrerem alterações aos recursos críticos do Azure relacionados ou associados às suas contas/piscinas do Lote Azure.

Configure as Definições de Diagnóstico para Contas de Armazenamento associadas ao Azure Batch Pool para monitorizar e registar todas as operações crud contra dados da piscina.

Como criar alertas para eventos de Registo de Atividade seleções do Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como permitir a exploração/auditoria adicional para uma conta de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Para os nós do Pool De Lote Azure, é responsável pela gestão da solução de gestão de vulnerabilidades.

Opcionalmente, se tiver uma assinatura rápida 7, Qualys ou qualquer outra subscrição da plataforma de gestão de vulnerabilidades, poderá instalar manualmente agentes de avaliação de vulnerabilidades nos nós do Pool Batch e gerir nós através do respetivo portal.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Microsoft para manter e atualizar imagens do nó do Azure Batch Pool. Certifique-se de que o sistema operativo dos nós do Pool Azure Batch permanece remendado durante a duração do tempo de vida do cluster, o que pode exigir atualizações automáticas, monitorizar os nós ou realizar reboots periódicos.


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Certifique-se de que as aplicações de terceiros do Azure Batch Pool permanecem corrigidas durante a duração do tempo de vida do cluster, o que pode exigir atualizações automáticas, monitorizar os nós ou realizar reboots periódicos.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Se tiver uma assinatura rápida 7, Qualys ou qualquer outra subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar o portal do fornecedor para visualizar e comparar scans de vulnerabilidade seletiva.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas.

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, Sistema Comum de Pontuação de Vulnerabilidades) ou as classificações de risco predefinidas fornecidas pela sua ferramenta de digitalização de terceiros.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, etc.) dentro da sua subscrição. Certifique-se de que tem permissões adequadas (ler) no seu inquilino e que é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos do Azure Resource Manager (ARM) em curso.

Como criar consultas com o Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como visualizar as suas Assinaturas Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreender o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.

Como criar e utilizar etiquetas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear os ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e user Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos e títulos de software aprovados do Azure.

**Orientação**: Definir lista de recursos azure aprovados e software aprovado para recursos de computação


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos azure não aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos Recursos Compute

**Orientação**: Para os nós do Pool Azure, implemente uma solução de terceiros para monitorizar os nós de cluster para aplicações de software não aprovadas.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Para os nós do Pool Azure, implemente uma solução de terceiros para monitorizar os nós de cluster para aplicações de software não aprovadas.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Para os nós do Pool Azure, implemente uma solução de terceiros para impedir a execução de software não autorizado.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Para os nós do Pool Azure, implemente uma solução de terceiros para impedir a execução de tipos de ficheiros não autorizados.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limite a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de Scripts</div>

**Orientação**: Utilize o Acesso Condicional azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure configurando o "Block access" para a app "Microsoft Azure Management".

Como configurar o Acesso Condicional ao acesso ao portal Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável,

Isto não é aplicável ao Lote Azure, uma vez que os utilizadores (não administradores) das piscinas do Lote Azure não precisam de ter acesso aos nós individuais para executar postos de trabalho. O administrador do cluster já tem acesso de raiz a todos os nós.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco fisicamente ou logicamente segregadas

**Orientação**: Não aplicável, o Benchmark destina-se a aplicações web em execução em casos de Azure App Service ou IaaS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração das suas contas e piscinas do Lote Azure.

Como visualizar os aliases de política do Azure disponíveis:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Estabelecer configurações seguras para o seu sistema operativo

**Orientação**: Estabeleça configurações seguras para o sistema operativo dos nódos do Pool.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Manter configurações seguras para todos os recursos azure

**Orientação**: Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras para os recursos Azure relacionados com a sua conta de Lote e piscinas (tais como redes virtuais, subredes, Firewalls Azure, Contas de Armazenamento Azure, etc.). Pode utilizar aliases de política azure a partir dos seguintes espaços de nome para criar políticas personalizadas:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender efeitos políticos do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Manter configurações seguras para sistemas operativos

**Orientação**: Imagens do sistema operativo Azure Batch Pool geridas e mantidas pela Microsoft. É responsável pela implementação da configuração do estado de nível osso.

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos azure

**Orientação**: Se utilizar definições políticas personalizadas do Azure para as suas contas, piscinas ou recursos relacionados, utilize o Azure Repos para armazenar e gerir de forma segura o seu código.

Como armazenar código em Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Se utilizar imagens personalizadas para as suas piscinas de Lote Azure, utilize o controlo de acesso baseado em funções (RBAC) para garantir que apenas os utilizadores autorizados possam aceder às imagens.

Compreender o RBAC em Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Como configurar o RBAC em Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize definições de política azure incorporadas para alertar, auditar e impor configurações de recursos relacionados com o Lote Azure.  Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Batch" para criar políticas personalizadas para as suas contas e piscinas do Lote Azure. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Implemente uma solução de terceiros para manter o estado desejado para os sistemas operativos dos seus nódos de lote Azure.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços do Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração da sua instância de Lote Azure. Também pode utilizar quaisquer políticas incorporadas criadas especificamente para o Lote Azure ou os recursos utilizados pelo Lote Azure, tais como:

- As subredes devem ser associadas a um Grupo de Segurança da Rede

-As Contas de Armazenamento devem utilizar um ponto final do serviço de rede virtual

- Os registos de diagnóstico nas contas do Lote devem ser ativados

Como visualizar os aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Implemente uma solução de terceiros para monitorizar o estado dos sistemas operativos dos nódos do seu Pool Azure Batch Pool.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gerir de forma segura os segredos do Azure

**Orientação**: O Cofre de Chaves Azure pode ser utilizado com implementações do Lote Azure para gerir as chaves para armazenamento de piscinas dentro das Contas de Armazenamento Azure.

Como integrar-se com identidades geridas pelo Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave Azure:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do Cofre Chave com uma identidade gerida:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gerir de forma segura e automática as identidades

**Orientação**: Não disponível, Identidade de Serviço Gerida não suportada por Lote Azure


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 

Como configurar o Scanner Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Utilize o Windows Defender nos seus nós individuais de piscina do Lote Azure no caso dos sistemas operativos Windows ou forneça a sua própria solução anti-malware se estiver a utilizar o Linux.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O Microsoft Antimalware está ativado no hospedeiro subjacente que suporta os serviços Azure (por exemplo, O Lote Azure), no entanto não funciona com o conteúdo do cliente.

Pré-digitalização de quaisquer ficheiros que sejam enviados para recursos não computacionais do Azure, tais como Serviço de Aplicações, Armazenamento de Data Lake, Armazenamento blob, etc. Nestes casos, a Microsoft não pode aceder aos dados dos clientes.

Compreenda o Microsoft Antimalware para Serviços Azure Cloud e Máquinas Virtuais:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software e assinaturas anti-malware são atualizados

**Orientação**: Utilize o Windows Defender nos seus nódos os seus nódos os conjuntos de conjuntos do Lote Azure individuais no caso dos sistemas operativos Windows e certifique-se de que a atualização automática está ativada. Forneça a sua própria solução anti-malware se estiver a utilizar o Linux.


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Ao utilizar uma conta de armazenamento Azure para a loja de dados Azure Batch Pool, escolha a opção de despedimento adequada (LRS,ZRS, GRS, RA-GRS). 

Como configurar o despedimento de armazenamento para contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e backup de quaisquer chaves geridas pelo cliente

**Orientação**: Ao utilizar uma conta de armazenamento Azure para a loja de dados Azure Batch Pool, escolha a opção de despedimento adequada (LRS,ZRS, GRS, RA-GRS).  Se utilizar o Cofre de Chaves Azure para qualquer parte da sua implantação do Lote Azure, certifique-se de que as suas chaves estão apoiadas.

Como configurar o despedimento de armazenamento para contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Como apoiar as chaves do cofre em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Se estiver a gerir as suas próprias chaves para contas de armazenamento Azure ou qualquer outro recurso relacionado com a implementação do Lote Azure, teste periodicamente a restauração das chaves apoiadas.

Como apoiar as chaves do cofre em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Como restaurar uma chave gerida pelo cliente com powerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Se o Cofre de Chaves Azure estiver a ser utilizado para conter quaisquer chaves relacionadas com as Contas de Armazenamento de Piscinas de Lote Azure, ative o Soft-Delete no Cofre de Chaves Azure para proteger as chaves contra a eliminação acidental ou maliciosa.

Como ativar o Soft Delete no Cofre de Chaves Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-incident-response-guide"></a>10.1: Criar guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definam funções de pessoal, bem como fases de tratamento/gestão de incidentes.

Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Criar procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que, quando um recurso estiver comprometido, possa chegar ao mesmo imediatamente. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Fornecer detalhes de contacto com &nbsp;incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.

Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Azure Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para O Sentinela Azul:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as descobertas críticas de segurança no prazo de 60 dias.

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra a Microsoft gerida por infraestruturas, serviços e aplicações em nuvem, aqui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
