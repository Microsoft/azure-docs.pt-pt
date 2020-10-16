---
title: Linha de base de segurança Azure para lote
description: Linha de base de segurança Azure para lote
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1eb24871817f365efe58b8e687563727df74493c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400981"
---
# <a name="azure-security-baseline-for-batch"></a>Linha de base de segurança Azure para lote

A Linha de Base de Segurança Azure para o Lote contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, consulte [Controlo de Segurança: Segurança da Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: Implementar o pool(s) Azure Batch dentro da rede virtual. Para permitir que os nós de cálculo de piscinas se comuniquem de forma segura com outras máquinas virtuais, ou com uma rede no local, pode providenciar a piscina numa sub-rede de uma rede virtual Azure. Além disso, a implementação do Pool dentro de uma rede virtual dá-lhe controlo sobre o grupo de segurança de rede (NSG) utilizado para proteger as interfaces de rede individuais dos nós (NIC), bem como a sub-rede. Configure o NSG para permitir o tráfego a partir de apenas IP(s)/locais fidedignos na Internet.

Como criar um Azure Batch Pool dentro de uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICS

**Orientação**: Utilize o Azure Security Center e remedia as recomendações de proteção da rede relacionadas com o grupo de segurança de rede virtual /rede (NSG) associado ao seu pool de lote. Ativar os registos de fluxo no NSG que está a ser utilizado para proteger a sua piscina de Lote e enviar registos numa Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho Azure Log Analytics e utilizar o Azure Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens da Azure Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Como ativar os registos de fluxo NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como Ativar e utilizar a Análise de Tráfego:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Compreender a Segurança da Rede fornecida pelo Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a proteção padrão do Azure DDoS (negação de serviço distribuído) na rede virtual que protege o seu pool Azure Batch para proteção contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

Como configurar a proteção DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Compreenda a Inteligência Integrada de Ameaças do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Ative os registos de fluxo no grupo de segurança da rede (NSG) que está a ser utilizado para proteger a sua piscina Azure Batch e envie registos para uma conta de armazenamento Azure para auditoria de tráfego.

Como ativar os registos de fluxo NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede

**Orientação**: Se necessário para efeitos de conformidade, selecione um aparelho virtual de rede do Mercado Azure que suporte sistemas de deteção de intrusões (IDS) e sistemas de prevenção de intrusões (IPS) com capacidades de inspeção de carga útil.

Se a deteção e/ou prevenção de intrusões baseada na inspeção da carga útil não for um requisito, a Azure Firewall com inteligência de ameaça pode ser usada. A filtragem baseada em ameaças de Azure Firewall pode alertar e negar tráfego de e para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

Implemente o Azure Firewall com um endereço IP público na mesma rede virtual que os seus nós Azure Batch Pool. Configure as regras de tradução de endereços de rede (NAT) entre localizações fidedignas na Internet e os endereços IP privados dos seus nós de piscina individuais. No Azure Firewall, sob a Inteligência de Ameaça, configurar "Alert and deny" para bloquear para alertar e bloquear tráfego de/para/de endereços e domínios IP maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence, e apenas os registos de maior confiança estão incluídos. 

Como criar um Azure Batch Pool dentro de uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Como implantar a Firewall Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Mercado Azure:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gerir o tráfego para as suas aplicações web

**Orientação**: Não aplicável, o Benchmark destina-se a aplicações web em execução em instâncias do Azure App Service ou iaaS.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou firewalls Azure associados à sua piscina Azure Batch. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Compreender e utilizar etiquetas de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para os recursos de rede associados ao seu(s) pool(s) Azure Batch com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.Batch" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus pools Azure Batch.

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego de documentos

**Orientação**: Utilize etiquetas para grupos de serviço de rede (NSGs) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego que estejam associados aos seus lotes Azure. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

Como criar e utilizar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma rede virtual:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Como criar um NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizar ferramentas automatizadas para monitorizar configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com os seus pools Azure Batch. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos de Registo de Atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Como criar alertas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: Para o Azure Batch, por padrão, a Microsoft fornece sincronização de tempo. No entanto, se tiver requisitos específicos de sincronização de tempo, poderá implementar essas alterações.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a Gestão central de registos de segurança

**Orientação**: Conta Azure Batch a bordo do Azure Monitor para agregar dados de segurança gerados pelos dispositivos de cluster. Aproveite as consultas personalizadas para detetar e responder a ameaças no ambiente.  Para a monitorização ao nível dos recursos do Azure Batch, utilize as APIs do lote para monitorizar ou consultar o estado dos seus recursos, incluindo empregos, tarefas, nós e piscinas.

Como embarcar uma conta Azure Batch para o Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos azure

**Orientação**: Para monitorização do nível de conta do Azure Batch, monitorize cada conta batch utilizando as características do Monitor Azure. O Azure Monitor recolhe métricas e registos de diagnóstico opcional para recursos abrangidos ao nível de uma conta Batch, tais como piscinas, empregos e tarefas. Recolher e consumir estes dados manualmente ou programáticamente para monitorizar as atividades na sua conta Batch e diagnosticar problemas.

Para monitorizar o nível de recursos do Azure Batch, utilize as APIs do Lote Azure para monitorizar ou consultar o estado dos seus recursos, incluindo empregos, tarefas, nós e piscinas.

Como configurar a monitorização e registo do nível da conta Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview

Compreender a monitorização ao nível dos recursos do Lote:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Recolher registos de segurança do Sistema Operativo

**Orientação**: O Azure Monitor recolhe métricas e registos de diagnóstico para recursos na sua conta Azure Batch. Recolher e consumir estes dados de várias formas de monitorizar a sua conta Azure Batch e diagnosticar problemas. Também pode configurar alertas métricos para que receba notificações quando uma métrica atinge um valor especificado.

Se necessário, talvez se conecte aos seus nós de piscina individuais através de Secured Shell (SSH) ou Remote Desktop Protocol (RDP) para aceder aos registos do sistema operativo local.

Como recolher registos de diagnóstico da sua conta Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Como ligar remotamente aos seus nóns de piscina Azure Batch:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registo de segurança

**Orientação**: Conta de lote Azure a bordo para o Monitor Azure. Certifique-se de que o espaço de trabalho Azure Log Analytics utilizado tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização

Como configurar a monitorização e registo do Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview

Como configurar o período de retenção do espaço de trabalho do Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Crie alertas métricos do Lote Azure que desencadeiem quando o valor de uma métrica especificada cruza um determinado limiar.

Como configurar alertas métricos do Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Crie alertas métricos do Lote Azure que desencadeiem quando o valor de uma métrica especificada cruza um determinado limiar.

Como configurar alertas métricos do Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Utilize o Windows Defender nos seus nós individuais de lote no caso dos sistemas operativos Windows ou forneça a sua própria solução anti-malware se estiver a utilizar o Linux.

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Permitir a colocação de registo de consultas de DNS

**Orientação**: Implementar solução de terceiros para a sessão de dns

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar o registo de auditoria da linha de comando

**Orientação**: Configurar manualmente a registo da consola e a transcrição powerShell numa base por nó.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Manter o Inventário das Contas Administrativas

**Orientação**: Mantenha o registo da conta administrativa local que é criada durante o provisionamento do pool do Lote Azure, bem como quaisquer outras contas que crie. Além disso, se a integração do Azure Ative Directory (AAD), a AAD tem funções incorporadas que devem ser explicitamente atribuídas e, portanto, são questionáveis. Utilize o módulo AAD PowerShell para realizar consultas de adhoc para descobrir contas que são membros de grupos administrativos.

Além disso, pode utilizar recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

Como obter um papel de diretório em AAD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório em AAD com PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Como monitorizar a identidade e o acesso com o Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Ao providenciar uma piscina Azure Batch, é-lhe dada a opção de criar contas de máquinas locais. Não existem senhas predefinidos para alterar, no entanto pode especificar diferentes palavras-passe para acesso secured Shell (SSH) e Remote Desktop Protocol (RDP). Depois de configurado o Azure Batch Pool, pode gerar um utilizador aleatório para nós individuais dentro do portal Azure ou através da AZure Resource Manager API.

Como adicionar um utilizador a um nó de computação específico:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Assegurar a utilização de Contas Administrativas Dedicadas

**Orientação**: Integrar a autenticação para aplicações de lote Azure com diretório ativo Azure. Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas.

Além disso, pode utilizar recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

Como autenticar aplicações de Lote com Diretório Ativo Azure:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Como monitorizar a identidade e o acesso com o Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilizar Sign-On individuais (SSO) com o Azure Ative Directory

**Orientação**: Não é aplicável, enquanto o Azure Batch suporta a autenticação Azure AD, a inscrição única não é suportada.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multifactor para todos os acessos baseados no Diretório Ativo Azure.

**Orientação**: Integrar a autenticação para aplicações de lote Azure com diretório ativo Azure (AAD). Ativar a autenticação multi-factor AAD (MFA) e seguir as recomendações do Azure Security Center Identity and Access Management.

 

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de acesso privilegiada) com autenticação multi-factor (MFA) configurada para iniciar sessão e configurar os seus recursos do Azure Batch.

Saiba mais sobre estações de acesso privilegiadas:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registo e alerta sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Se tiver autenticação integrada para aplicações de lote Azure com Diretório Ativo Azure (AAD), utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrerem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

Como identificar utilizadores de AD Azure sinalizados para atividade de risco:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gerir o Recurso Azure a partir de apenas locais aprovados

**Orientação**: Se tiver autenticação integrada para aplicações de lote Azure com diretório ativo Azure, pode utilizar locais com indicação de acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar localizações nomeadas em Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AAD) como sistema central de autenticação e autorização e integre a autenticação para aplicações de lote Azure com AAD. AAD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. AAD também sai, hashes e armazena seguramente credenciais de utilizador.

Como criar e configurar um caso AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Como autenticar aplicações de lote com AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever e conciliar regularmente o acesso ao utilizador

**Orientação**: O Azure Ative Directory (AAD) fornece registos para ajudar a descobrir contas velhas. Além disso, pode utilizar a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso dos utilizadores pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

Como utilizar comentários sobre o acesso à identidade do Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tentativas de acesso a contas desativadas

**Orientação**: Criar Definições de diagnóstico para contas de utilizadores do Azure Ative Directory, enviar os registos de auditoria e registos de login para um espaço de trabalho Azure Log Analytics. Configure os alertas desejados dentro do espaço de trabalho Azure Log Analytics.

Como integrar os Registos de Atividade do Azure no Monitor Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o Azure Ative Directory (AAD) Deteções de Riscos e Proteção de Identidade para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Além disso, você pode ingerir dados em Azure Sentinel para mais investigação.

Como visualizar as entradas de risco da AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e permitir políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte<br></div>

**Orientação**: Não disponível; Lockbox do cliente ainda não suportado para Azure Batch.
 
Lista de serviços suportados pelo Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Proteção de Dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

Como criar e utilizar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As piscinas de lote Azure devem ser separadas por rede/sub-rede virtuais, marcadas adequadamente e protegidas com um grupo de segurança de rede (NSG). Os dados do Azure Batch devem ser contidos numa conta de armazenamento Azure segura.

Como criar um Azure Batch Pool dentro de uma rede virtual:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Como garantir contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorize e bloqueie a transferência não autorizada de informações sensíveis.

**Orientação**: Para contas de armazenamento Azure associadas ao seu(s) Azure Batch Pool(s) que contenham informações sensíveis, marque-as como sensíveis usando Tags e as fixe com as melhores práticas do Azure.

As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Como garantir contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Os recursos da Microsoft Azure negociarão tLS 1.2 por padrão. Certifique-se de que quaisquer clientes que se conectem aos seus Pools de Lote Azure ou lojas de dados (Contas de Armazenamento Azure) sejam capazes de negociar TLS 1.2 ou superior.

Certifique-se de que o HTTPS é necessário para aceder à Conta de Armazenamento que contém os seus dados do Azure Batch.

Compreenda a encriptação da conta de armazenamento Azure em Trânsito:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Para contas de armazenamento Azure associadas ao seu(s) Azure Batch Pool(s) que contenham informações sensíveis, marque-as como sensíveis usando tags e prenda-as com as melhores práticas do Azure.

As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Como garantir contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para controlar o acesso ao plano de gestão de recursos Azure, incluindo a Conta de Lote, Pool de Lotes e Contas de Armazenamento.

Compreenda Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Como configurar o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo de acesso

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Para contas de armazenamento associadas à sua conta Azure Batch, é aconselhável permitir à Microsoft gerir as chaves de encriptação, no entanto, tem a opção de gerir as suas próprias chaves, se necessário.

Como gerir chaves de encriptação para contas de armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações para recursos críticos do Azure relacionados ou associados às suas contas/piscinas do Azure Batch.

Configure as definições de diagnóstico para contas de armazenamento associadas ao Azure Batch Pool para monitorizar e registar todas as operações CRUD contra dados do pool.

Como criar alertas para eventos de Registo de Atividades Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Como permitir a registo/auditoria adicional de uma Conta de Armazenamento Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização de vulnerabilidade automatizada

**Orientação**: Para os nós do Azure Batch Pool, é responsável pela gestão da solução de gestão de vulnerabilidades.

Opcionalmente, se tiver uma subscrição de Rapid7, Qualys ou qualquer outra plataforma de gestão de vulnerabilidades, poderá instalar manualmente agentes de avaliação de vulnerabilidades nos nós da piscina Batch e gerir os nós através do respetivo portal.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patchs de sistema operativo

**Orientação**: Microsoft para manter e atualizar as imagens do nó do conjunto Azure Batch Pool. Certifique-se de que o sistema operativo dos nós do Azure Batch Pool permanece corrigido durante o período de vida útil do cluster, o que pode exigir atualizações automáticas, monitorização dos nós ou reinicialização periódica.


**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Certifique-se de que as aplicações de terceiros dos nós de Azure Batch Pool permanecem corrigidas durante o período de vida útil do cluster, o que pode exigir atualizações automáticas, monitorização dos nós ou reinicialização periódica.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as análises de vulnerabilidade de trás para a outra

**Orientação**: Se tiver uma subscrição de rapid7, Qualys ou qualquer outra subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar o portal desse fornecedor para visualizar e comparar as verificações de vulnerabilidades de trás para a saturação.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas.

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, sistema comum de pontuação de vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como cálculo, armazenamento, rede, etc.) dentro da sua subscrição(s). Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e que é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager (ARM) em curso.

Como criar consultas com gráfico de recursos Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver as suas Subscrições Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreenda Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

Como criar e utilizar tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos de azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Como criar subscrições adicionais do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizadores Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos aprovados da Azure e dos títulos de software.

**Orientação**: Definir lista de recursos Azure aprovados e software aprovado para recursos computacional


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor de Recursos Azure Não Aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.s. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com O Gráfico Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor de aplicações de software não aprovadas dentro de Recursos Compute

**Orientação**: Para os nós do Azure Batch Pool, implemente uma solução de terceiros para monitorizar os nós do cluster para aplicações de software não aprovadas.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Para os nós do Azure Batch Pool, implemente uma solução de terceiros para monitorizar os nós do cluster para aplicações de software não aprovadas.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Para os nós do Azure Batch Pool, implemente uma solução de terceiros para impedir a execução de software não autorizado.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Para os nós do Pool de Lote Azure, implemente uma solução de terceiros para evitar a execução de tipos de ficheiros não autorizados.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de Scripts</div>

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro de recursos computacional

**Orientação**: Não aplicável,

Isto não é aplicável ao Azure Batch, uma vez que os utilizadores (não administradores) dos pools Azure Batch não precisam de acesso aos nós individuais para executarem empregos. O administrador do cluster já tem acesso de raiz a todos os nós.


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável, o Benchmark destina-se a aplicações web em execução em instâncias do Azure App Service ou iaaS.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte [Controlo de Segurança: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos azuis

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração das suas contas e piscinas do Azure Batch.

Como visualizar pseudónimos disponíveis da Política Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Estabelecer configurações seguras para o seu Sistema Operativo

**Orientação**: Estabeleça configurações seguras para o sistema operativo dos nós do Pool de Lote.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Manter configurações seguras para todos os recursos azuis

**Orientação**: Utilize a Política Azure [negar] e [implementar se não existir] para impor definições seguras para os recursos Azure relacionados com a sua conta Batch e piscinas (tais como redes virtuais, sub-redes, Firewalls Azure, Contas de Armazenamento Azure, etc.). Pode utilizar pseudónimos da Azure Policy a partir dos seguintes espaços de nome para criar políticas personalizadas:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreenda os efeitos da política do Azure: https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Manter configurações seguras para sistemas operativos

**Orientação**: Imagens do sistema operativo Azure Batch Pool geridas e mantidas pela Microsoft. É responsável pela implementação da configuração do estado de nível oss.

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração de loja segura de recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para as suas contas, piscinas ou recursos relacionados do Azure Batch, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

Como armazenar código em Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Se utilizar imagens personalizadas para as suas piscinas Azure Batch, utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para garantir que apenas os utilizadores autorizados possam aceder às imagens.

Compreenda Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Como configurar o Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize definições de Política Azure incorporadas para alertar, auditar e impor configurações de recursos relacionadas com o Azure Batch.  Use pseudónimos da Azure Policy no espaço de nomes "Microsoft.Batch" para criar políticas personalizadas para as suas contas e piscinas do Azure Batch. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para manter o estado desejado para os sistemas operativos dos nós do Azure Batch Pool.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização de configuração automatizada para serviços de Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração da sua instância do Azure Batch. Também pode utilizar quaisquer políticas incorporadas criadas especificamente para o Azure Batch ou os recursos utilizados pela Azure Batch, tais como:

- As subnetas devem ser associadas a um Grupo de Segurança de Rede

-As contas de armazenamento devem utilizar um ponto final de serviço de rede virtual

- Os registos de diagnóstico nas contas do Lote devem ser ativados

Como visualizar pseudónimos disponíveis da Política Azure: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização de configuração automatizada para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para monitorizar o estado dos sistemas operativos dos seus nós Azure Batch Pool.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gerir de forma segura os segredos do Azure

**Orientação**: O Cofre da Chave Azul pode ser utilizado com implementações do Azure Batch para gerir as chaves para armazenamento de piscinas dentro das contas de armazenamento do Azure.

Como integrar-se com identidades geridas aZure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Como autenticar o Cofre de Chaves:

https://docs.microsoft.com/azure/key-vault/general/authentication

Como atribuir uma política de acesso ao Cofre chave:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gerir de forma segura e automática as identidades

**Orientação**: Não disponível, Identidade de Serviço Gerido não suportada pelo Azure Batch


**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

Como configurar o Scanner Credencial: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Controlo de Segurança: Defesa de Malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação**: Utilize o Windows Defender nos seus nós individuais de piscina Azure Batch no caso dos sistemas operativos Windows ou forneça a sua própria solução anti-malware se estiver a utilizar o Linux.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Batch), no entanto não funciona com conteúdo do cliente.

Pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, etc. A Microsoft não pode aceder aos dados dos clientes nestes casos.

Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software e assinaturas anti-malware são atualizados

**Orientação**: Utilize o Windows Defender nos seus nós individuais de piscina Azure Batch no caso dos sistemas operativos Windows e certifique-se de que a atualização automática está ativada. Forneça a sua própria solução anti-malware se estiver a utilizar o Linux.


**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Quando utilizar uma conta de armazenamento Azure para a loja de dados Azure Batch Pool, escolha a opção de despedimento adequada (LRS, ZRS, GRS, RA-GRS). 

Como configurar a redundância de armazenamento para as contas de armazenamento da Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer backup de qualquer tecla gerida pelo cliente

**Orientação**: Quando utilizar uma conta de armazenamento Azure para a loja de dados Azure Batch Pool, escolha a opção de despedimento adequada (LRS, ZRS, GRS, RA-GRS).  Se utilizar o Cofre da Chave Azure para qualquer parte da sua implantação do Azure Batch, certifique-se de que as suas chaves estão apoiadas.

Como configurar a redundância de armazenamento para as contas de armazenamento da Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Como fazer backup chaves do cofre em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Se estiver a gerir as suas próprias chaves para contas de armazenamento Azure ou qualquer outro recurso relacionado com a sua implementação do Azure Batch, teste periodicamente a restauração das teclas de apoio.

Como fazer backup chaves do cofre em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Como restaurar uma chave gerida pelo cliente com PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves geridas pelo cliente

**Orientação**: Se o Cofre da Chave Azure estiver a ser utilizado para conter quaisquer teclas relacionadas com as contas de armazenamento de piscinas do Azure Batch, ative Soft-Delete no Cofre da Chave Azure para proteger as chaves contra a eliminação acidental ou maliciosa.

Como permitir a eliminação suave no cofre da chave Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](../security/benchmarks/security-control-incident-response.md)*

### <a name="101-create-incident-response-guide"></a>10.1: Criar guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definam funções de pessoal, bem como fases de tratamento/gestão de incidentes.

Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Criar procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que quando um recurso estiver comprometido, possa chegar imediatamente ao mesmo. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Consulte a publicação do NIST: Guide to Test, Training e Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta &nbsp; para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.

Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a automatização do fluxo de trabalho e as aplicações lógicas:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte [Controlo de Segurança: Testes de penetração e exercícios da equipa vermelha.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir que remedia todas as conclusões críticas de segurança no prazo de 60 dias.

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridos pela Microsoft na nuvem, aqui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre [as Linhas de Base de Segurança Azure](../security/benchmarks/security-baselines-overview.md)
