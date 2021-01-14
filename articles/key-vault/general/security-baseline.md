---
title: Linha de Base de Segurança do Azure para o Key Vault
description: Linha de Base de Segurança do Azure para o Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad47ac7d51de6ab497d7061961c14b5324fab931
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202458"
---
# <a name="azure-security-baseline-for-key-vault"></a>Linha de Base de Segurança do Azure para o Key Vault

A Linha de Base de Segurança Azure para o Cofre de Chaves contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, consulte [Controlo de Segurança: Segurança da Rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: Integre o cofre da chave Azure com a Ligação Privada Azure. 

O Azure Private Link Service permite-lhe aceder aos Serviços Azure (por exemplo, Azure Key Vault) e ao Azure hospedados serviços de cliente/parceiro sobre um Ponto Final Privado na sua rede virtual.

Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

Como integrar o Key Vault com Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação**: Utilize o Centro de Segurança Azure e siga as recomendações de proteção da rede para ajudar a proteger os recursos configurados do Cofre chave em Azure. 

Para mais informações sobre a Segurança da Rede fornecida pelo Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar o Padrão de Proteção Azure DDoS nas Redes Virtuais Azure associadas aos seus casos de Cofre-Chave para proteção contra ataques de negação de serviço distribuídos. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

 
Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Deteção de ameaças para a camada de serviço Azure no Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: O Azure Key Vault não utiliza grupos de segurança de rede (NSG) e os registos de fluxo para o Cofre da Chave Azure não são capturados. Em vez disso, utilize o Azure Private Link para proteger as suas instâncias do Azure Key Vault e permita configurações de diagnóstico para registar métricas e eventos de auditoria.

Integre o Cofre de Chaves com Ligação Privada Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Registo do cofre da chave Azure: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Este requisito pode ser cumprido configurando uma proteção avançada de ameaças (ATP) para o Cofre da Chave Azure. O ATP fornece uma camada adicional de inteligência de segurança. Esta ferramenta deteta tentativas potencialmente nocivas de aceder ou explorar contas do Azure Key Vault.

Quando o Azure Security Center deteta atividade anómala, apresenta alertas. Envia também um e-mail ao administrador de subscrição com detalhes da atividade suspeita e recomendações para como investigar e remediar as ameaças identificadas.

Confiúdo de proteção de ameaças avançadas para o Cofre da Chave Azure:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso às suas instâncias Azure Key Vault, utilize etiquetas de serviço Azure para o Cofre da Chave Azure para definir controlos de acesso à rede em grupos de segurança de rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Visão geral das etiquetas de serviço Azure: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus casos de Azure Key Vault com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.KeyVault" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das suas instâncias Azure Key Vault. Você também pode fazer uso de definições políticas incorporadas relacionadas com Azure Key Vault, tais como:

O Key Vault deve usar um ponto final de serviço de rede virtual

Tutorial: Criar e gerir políticas para impor o cumprimento:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras da política de Azure:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Início rápido: Definir e atribuir uma planta no portal:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos relacionados com a segurança da rede e fluxo de tráfego para as suas instâncias Azure Key Vault para fornecer metadados e organização lógica.

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

Use etiquetas para organizar os seus recursos Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as suas instâncias do Azure Key Vault. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

Ver e recuperar eventos de Registo de Atividades Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Criar, visualizar e gerir alertas de registo de atividade utilizando o Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: Não aplicável; A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como o Azure Key Vault, para os timetamps nos registos.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pelo Azure Key Vault. Dentro do Azure Monitor, utilize o espaço de trabalho Azure Log Analytics para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

Registo do cofre da chave Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Quickstart: Como embarcar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative as definições de diagnóstico nos seus casos de Azure Key Vault para acesso a registos de auditoria, segurança e diagnóstico. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis.

Registo do cofre da chave Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Âmbito do Monitor Azure, para o espaço de trabalho Log Analytics utilizado para manter os registos do Cofre da Chave Azure, desagure o período de retenção de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Alterar o período de retenção de dados: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados dos seus recursos protegidos pelo Cofre da Chave Azure. Utilize o espaço de trabalho do Log Analytics do Azure Monitor para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

Quickstart: A bordo Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Começa com o Log Analytics no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Começa com consultas de registo no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: No Centro de Segurança Azure, ative a proteção avançada de ameaças (ATP) para o Cofre de Chaves. Ativar as definições de diagnóstico no Cofre de Chaves Azure e enviar registos para um espaço de trabalho do Log Analytics. A bordo do seu espaço de trabalho Log Analytics para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança.

Quickstart: A bordo Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Gerir e responder aos alertas de segurança no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Responda aos eventos com alertas do Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; O Azure Key Vault não processa nem produz registos relacionados com anti-malware.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; O Azure Key Vault não processa nem produz registos relacionados com DNS.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha um inventário das suas aplicações registadas no Azure Ative Directory, bem como quaisquer contas de utilizador que tenham acesso às chaves, segredos e certificados do Azure Key Vault. Pode utilizar o portal Azure ou o PowerShell para consultar e conciliar o acesso ao Cofre de Chaves. Para visualizar o acesso no PowerShell, utilize o seguinte comando:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Propriedades.AccessPolicies

Registo de um requerimento no Azure Ative Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Acesso seguro a um cofre de chaves:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Não aplicável; O Azure Key Vault não tem o conceito de palavras-passe padrão, uma vez que a autenticação é fornecida pelo Ative Directory e assegurada com o controlo de acesso baseado em funções Azure (Azure RBAC).


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso às suas instâncias Azure Key Vault. Utilize a Azure Security Center Identity and Access Management (atualmente em pré-visualização) para monitorizar o número de contas administrativas ativas.

Monitorizar a identidade e o acesso (pré-visualização):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Utilize um principal de serviço Azure em conjunto com o AppId, TenantID e ClientSecret, para autenticar perfeitamente a sua aplicação e recuperar o token que será usado para aceder aos seus segredos do Cofre da Chave Azure.

Autenticação de serviço-a-serviço para Azure Key Vault utilizando .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ative Directory Multi-Factor Authentication e siga as recomendações do Azure Security Center Identity and Access Management (atualmente em pré-visualização) para ajudar a proteger os recursos ativados pelo Centro de Eventos.

Planejando uma implementação de autenticação multi-factor Azure Ad baseada na nuvem:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitorizar a identidade e o acesso (pré-visualização):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiada (PAW) com a autenticação multi-factor AD (MFA) configurada para iniciar sessão e configurar recursos ativados do Cofre da Chave. 

Estações de trabalho de acesso privilegiada: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/ 

Planejando uma implementação de autenticação multi-factor Azure Ad baseada na nuvem: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. Para obter registos adicionais, envie alertas de deteção de risco do Azure Security Center para o Azure Monitor e configuure alertas/notificações personalizados utilizando grupos de ação.

Ativar uma proteção avançada de ameaças (ATP) para o Azure Key Vault para gerar alertas para atividades suspeitas.

Implementar gestão de identidade privilegiada da Azure AD (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Confiúdo de proteção de ameaças avançadas para o Cofre da Chave Azure (pré-visualização): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Alertas para Azure Key Vault (Pré-visualização): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Deteções de risco do Azure Ative Directory: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Criar e gerir grupos de ação no portal Azure: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Configure a condição de localização de uma política de acesso condicional e gerencie as suas localizações nomeadas. Com localizações nomeadas, pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões. Pode restringir o acesso a recursos sensíveis, como os seus segredos key vault, aos seus locais com nomes configurados.

Qual é a condição de localização no Azure Ative Directy Conditional Access?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização para recursos Azure, como o Key Vault. Isto permite que o controlo de acesso baseado em funções (Azure RBAC) administrar recursos sensíveis.

 

Quickstart: Criar um novo inquilino no Azure Ative Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Rever os registos do Azure Ative Directory (Azure AD) para ajudar a descobrir contas velhas com funções administrativas do Azure Key Vault. Além disso, utilize avaliações de acesso AZure AD para gerir eficientemente os membros do grupo, acesso a aplicações empresariais que possam ser usadas para aceder ao Azure Key Vault e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os utilizadores certos têm acesso continuado.

Relatórios do Azure Ative Directory e documentação de monitorização:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

O que são comentários de acesso a AD AZure?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Ative as definições de diagnóstico para Azure Key Vault e Azure Ative Directory, enviando todos os registos para um espaço de trabalho Log Analytics. Configure os alertas desejados (tais como tentativas de acesso a segredos desativados) dentro do Log Analytics.

Integre os registos AD do Azure com registos do Monitor Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrando da antiga solução Key Vault: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize as funcionalidades de Proteção de Identidade do Azure Ative Directory e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com os recursos protegidos do seu Cofre de Chaves Azure. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização. 

Relatório de inscrições arriscados no portal Azure Ative Directory: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Como: Configurar e permitir políticas de risco: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar Azure Sentinel:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não aplicável; O Lockbox do cliente não suportado para o Cofre da Chave Azure.

Serviços e cenários apoiados em disponibilidade geral: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Proteção de Dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis sobre o Azure Key Vault habilitados recursos. 

Use etiquetas para organizar os seus recursos Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Pode garantir o acesso ao Cofre da Chave Azure utilizando pontos finais de serviço de rede virtuais configurados para restringir o acesso a sub-redes específicas.

Após as regras de firewall estarem em vigor, só pode efetuar operações de plano de dados Azure Key Vault quando o seu pedido tiver origem em sub-redes permitidas ou intervalos de endereços IP. Isto também se aplica ao acesso a Azure Key Vault no portal Azure. Embora possa navegar por um cofre chave a partir do portal Azure, poderá não conseguir listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Azure Key Vault Picker e outros serviços Azure. Pode ver listas de Cofres-Chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes de o fazer.

Configure firewalls E redes virtuais do Cofre de Chaves Azure: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Pontos finais de serviço de rede virtual para Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Todos os dados armazenados dentro do Cofre da Chave Azure são considerados sensíveis. Use os controlos de acesso do avião de acesso do Azure Key Vault para controlar o acesso aos segredos do Cofre da Chave Azure. Também pode utilizar a firewall incorporada do Key Vault para controlar o acesso na camada de rede. Para monitorizar o acesso ao Cofre da Chave Azure, ative as Definições de Diagnóstico do Cofre de Chaves e envie registos para uma conta de armazenamento Azure ou espaço de trabalho Log Analytics.

Acesso seguro a um cofre de chaves: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Configure firewalls E redes virtuais do Cofre de Chaves Azure: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Registo do cofre da chave Azure: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Todo o tráfego para Azure Key Vault para autenticação, gestão e acesso a plano de dados, é encriptado e passa por HTTPS: porta 443. (No entanto, haverá ocasionalmente tráfego HTTP [porta 80] para CRL.) O Azure Key Vault continua a permitir que os dados TLS 1.1 e TLS 1.0 sejam ingeridos. Os dados podem ser restringidos ao TLS 1.2 através da configuração do lado do cliente.



Acesso Azure Key Vault atrás de uma firewall: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não aplicável; todos os dados dentro do Cofre da Chave Azure (segredos, chaves e certificados) são considerados sensíveis.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Acesso seguro à gestão e plano de dados das suas instâncias Azure Key Vault.

Acesso seguro a um cofre de chaves:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: A Microsoft gere a infraestrutura subjacente ao Azure Key Vault e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

O que é o Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Proteção de dados do cliente Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Todos os objetos geridos (chave, certificados e segredos) são encriptados em repouso no Cofre da Chave Azure.

Documentação de apoio:

- [Modelo de encriptação e tabela de gestão chave](../../security/fundamentals/encryption-atrest.md)


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize a solução Azure Key Vault Analytics no Azure Monitor para rever os registos de eventos de auditoria do Azure Key Vault.

Solução Azure Key Vault Analytics no Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: A Microsoft executa a gestão de vulnerabilidades nos sistemas subjacentes que suportam o Azure Key Vault.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: N/A; A Microsoft realiza a gestão de patchs nos sistemas subjacentes que suportam o Key Vault.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: A Microsoft executa a gestão de vulnerabilidades nos sistemas subjacentes que suportam o Key Vault.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center.

Melhore a sua pontuação segura no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar e descubra todos os recursos (incluindo instâncias do Cofre chave Azure) dentro da sua subscrição. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Quickstart: Execute a sua primeira consulta de gráfico de recurso utilizando o Azure Resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Obtenha subscrições a que a conta corrente pode aceder.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure Key Vault, dando metadados para organizá-los logicamente numa taxonomia.

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize etiquetas, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear instâncias e recursos relacionados do Azure Key Vault. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Criar uma subscrição adicional do Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Criar grupos de gestão para organização e gestão de recursos:

https://docs.microsoft.com/azure/governance/management-groups/create

Use etiquetas para organizar os seus recursos Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação**: Defina a lista de recursos aprovados da Azure e software aprovado para os seus recursos de computação

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize as políticas Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s) subscrição.

Tutorial: Criar e gerir políticas para impor o cumprimento: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Quickstart: Execute a sua primeira consulta de gráfico de recurso utilizando o Azure Resource Graph Explorer: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure como um todo, bem como aos recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize as políticas Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Tutorial: Criar e gerir políticas para impor o cumprimento: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras da política de Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azureresources através de scripts

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager (ARM) configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações nos recursos dentro de um ambiente de alta segurança, como aqueles com configuração Key Vault.

Gerir o acesso à gestão Azure com Acesso Condicional:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


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

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte [Controlo de Segurança: Configuração Segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.KeyVault" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias do Azure Key Vault. Também pode utilizar definições de Política Azure incorporadas para a Azure Key Vault, tais como:

Os objetos do Cofre chave devem ser recuperáveis

Implementar definições de diagnóstico para cofre de chaves para registar espaço de trabalho

Os registos de diagnóstico no Cofre de Chaves devem ser ativados

O Key Vault deve usar um ponto final de serviço de rede virtual

Implementar definições de diagnóstico para cofre de chave para o centro de eventos

Utilize recomendações do Azure Security Center como base de configuração segura para as suas instâncias Azure Key Vault.

Como visualizar pseudónimos disponíveis da Política Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Tutorial: Criar e gerir políticas para impor o cumprimento:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Utilize a Política Azure [negar] e [implementar se não existir] para impor definições seguras através dos recursos ativados pelo Cofre da Chave Azure. 

Tutorial: Criar e gerir políticas para impor o cumprimento:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Compreender os efeitos da Política Azure: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para o seu Cofre de Chaves Azure, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

Como armazenar código em Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Documentação Azure Repos: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.KeyVault" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

Como configurar e gerir a Política de Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Utilize o Azure Security Center para realizar análises de base para os seus recursos protegidos por Azure Key Vault 

  

Como remediar recomendações no Centro de Segurança Azure: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; este referencial destina-se a recursos computacional.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem. Certifique-se de que o cofre da chave Azure está ativado.

Como integrar-se com identidades geridas aZure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Como autenticar o Cofre de Chaves:

https://docs.microsoft.com/azure/key-vault/general/authentication

Como atribuir uma política de acesso ao Cofre chave:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem. 

  

* [Como integrar-se com identidades geridas aZure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Como criar um Cofre-Chave](quick-create-portal.md)

* [Como autenticar para o Cofre de Chaves](authentication.md)

* [Como atribuir uma política de acesso ao Cofre de Chaves](assign-access-policy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.  
  
 Como configurar o Scanner Credencial: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Controlo de Segurança: Defesa de Malware.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Key Vault), no entanto, não funciona com conteúdo do cliente.

Pré-digitalize qualquer conteúdo que seja carregado ou enviado para recursos Azure não computados, como Azure Key Vault. A Microsoft não pode aceder aos seus dados nestes casos.

Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Recuperação de Dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Certifique-se de cópias de segurança automáticas regulares dos seus certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com os seguintes comandos PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcionalmente, pode armazenar as cópias de segurança do Key Vault no suporte de backup Azure.

Como fazer backup dos certificados de cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Como apoiar chaves do cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Como fazer backup contas de armazenamento gerido do cofre do cofre: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Como apoiar os Segredos do Cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Como ativar a cópia de segurança do Azure: https://docs.microsoft.com/azure/backup



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Execute cópias de segurança dos certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com os seguintes comandos PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcionalmente, pode armazenar as cópias de segurança do Key Vault no suporte de backup Azure.

Como fazer backup dos certificados de cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Como apoiar chaves do cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Como fazer backup contas de armazenamento gerido do cofre do cofre: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Como apoiar os Segredos do Cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Como ativar a cópia de segurança do Azure: https://docs.microsoft.com/azure/backup



**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Execute periodicamente a restauração de dados dos seus certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com os seguintes comandos PowerShell:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Como restaurar os certificados de cofre chave:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Como restaurar chaves do cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Como restaurar as contas de armazenamento gerido do cofre chave: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Como restaurar os segredos do cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Certifique-se de que a eliminação suave está ativada para o Cofre da Chave Azure. A eliminação suave permite a recuperação de cofres e objetos de abóbada apagados, tais como chaves, segredos e certificados. 

Como utilizar o Soft Delete do Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](../../security/benchmarks/security-control-incident-response.md)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente. Estes processos devem ter como foco a proteção de sistemas sensíveis, como os que utilizam segredos do Key Vault.

Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Orientação para a construção do seu próprio processo de resposta a incidentes de segurança:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente aqueles que processam dados sensíveis como os segredos do Azure Key Vault.


**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger as instâncias do Cofre da Chave Azure e recursos relacionados. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

Consulte a publicação do NIST: Guide to Test, Training e Exercise Programs for IT Plans and Capabilities: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos ativados pelo Cofre da Chave Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua.  Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel. 

 

Como configurar a exportação contínua: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Como transmitir alertas para Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações lógicas" em alertas de segurança e recomendações para proteger os recursos protegidos pelo cofre da chave Azure. 

 

Como configurar a automatização do fluxo de trabalho e as aplicações lógicas: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte [Controlo de Segurança: Testes de penetração e exercícios da equipa vermelha.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Não deve realizar testes de canetas diretamente no serviço Azure Key Vault, no entanto é encorajado a testar os seus recursos Azure que estão a usar o Key Vault para garantir a segurança dos segredos.

Terá de seguir as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft em nuvem, aqui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Azure Security Benchmark](../../security/benchmarks/overview.md)
- Saiba mais sobre [as Linhas de Base de Segurança Azure](../../security/benchmarks/security-baselines-overview.md)