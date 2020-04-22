---
title: Linha de Base de Segurança do Azure para o Key Vault
description: Linha de Base de Segurança do Azure para o Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6e660c1244dd5566fbfb45a6da37d39294354ccb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756999"
---
# <a name="azure-security-baseline-for-key-vault"></a>Linha de Base de Segurança do Azure para o Key Vault

A Linha de Base de Segurança Azure para o Cofre de Chaves contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Integrar o cofre de chaves Azure com ligação privada Azure. 

O Azure Private Link Service permite-lhe aceder aos Serviços Azure (por exemplo, Azure Key Vault) e o Azure acolheu serviços de clientes/parceiros sobre um Ponto Final Privado na sua rede virtual.

Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado por Azure Private Link. O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a uma instância de um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acesso.

Como integrar o Cofre chave com ligação privada Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os seus recursos configurados por cofre chave em Azure. 

Para mais informações sobre a Segurança da Rede fornecida pelo Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a Norma de Proteção De DDoS Azure nas Redes Virtuais Azure associadas às instâncias do Cofre Chave para proteção contra ataques de negação de serviço distribuídos. Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.

 
Gerir a Norma de Proteção Azure DDoS utilizando o portal Azure:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Deteção de ameaças para a camada de serviço Azure no Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: O Cofre de Chaves Azure não utiliza grupos de segurança de rede (NSG) e os registos de fluxo para o Cofre de Chaves Azure não são capturados. Em vez disso, utilize o Azure Private Link para proteger as instâncias do Cofre de Chaves Azure e permitir configurações de diagnóstico para registar métricas e eventos de auditoria.

Integrar o cofre chave com ligação privada Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Exploração madeireira do cofre de chaves azure:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Este requisito pode ser satisfeito configurando uma proteção avançada contra ameaças (ATP) para o Cofre chave Azure. A ATP fornece uma camada adicional de inteligência de segurança. Esta ferramenta deteta tentativas potencialmente nocivas de acesso ou exploração de contas do Cofre chave do Azure.

Quando o Azure Security Center deteta atividade anómala, apresenta alertas. Também envia um e-mail ao administrador de subscrição com detalhes sobre a atividade suspeita e recomendações para como investigar e remediar as ameaças identificadas.

Criar uma proteção avançada contra ameaças para o Cofre chave Azure:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso às instâncias do Cofre chave Azure, utilize etiquetas de serviço Azure para o Cofre chave Azure para definir controlos de acesso à rede em grupos de segurança de rede ou Firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Visão geral das etiquetas de serviço Azure:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus casos de Cofre chave Azure com a Política Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.KeyVault" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede das suas instâncias do Cofre chave Azure. Também pode utilizar definições políticas incorporadas relacionadas com o Cofre chave Azure, tais como:

O Key Vault deve utilizar um ponto final de serviço de rede virtual

Tutorial: Criar e gerir políticas para impor o cumprimento:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras de política azure:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Arranque rápido: Defina e atribua uma planta no portal:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos relacionados com a segurança da rede e fluxo de tráfego para as instâncias do Cofre chave Azure para fornecer metadados e organização lógica.

Utilize qualquer uma das definições políticas do Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e notificá-lo de recursos não marcados existentes.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou realizar ações em recursos baseados nas suas etiquetas.

Utilize etiquetas para organizar os seus recursos Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as instâncias do Cofre de Chaves Do Azure. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.

Ver e recuperar eventos de registo de atividades do Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Criar, visualizar e gerir alertas de registo de atividade utilizando o Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: Não aplicável; A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como o Azure Key Vault, para os carimbos de tempo nos registos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pelo Cofre chave Azure. Dentro do Monitor Azure, utilize o espaço de trabalho Azure Log Analytics para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo. Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. 

Exploração madeireira do cofre de chaves azure:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Quickstart: Como embarcar No Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Ative as definições de diagnóstico no seu Cofre de Chaves Azure para acesso a registos de auditoria, segurança e diagnóstico. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, carimbo de tempo, endereços de origem, endereços de destino e outros elementos úteis.

Exploração madeireira do cofre de chaves azure:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: Dentro do Monitor Azure, para o espaço de trabalho do Log Analytics utilizado para manter os seus registos do Cofre de Chaves Azure, deteta o período de retenção de acordo com as regras de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Alterar o período de retenção de dados:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: Analise e monitorize os registos para comportamentos anómalos e reveja regularmente os resultados dos seus recursos protegidos pelo Cofre de Chaves Azure. Utilize o espaço de trabalho do Monitor de Log Analytics do Azure Monitor para rever os registos e executar consultas nos dados de registo. Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros. 

Quickstart: A bordo do Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Inicie-se com Log Analytics no Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Começar com consultas de log no Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: No Centro de Segurança Azure, ative a proteção avançada de ameaças (ATP) para o Cofre chave. Ative as definições de diagnóstico no Cofre de Chaves Azure e envie registos para um espaço de trabalho de Log Analytics. A bordo do seu espaço de trabalho Log Analytics para o Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar e utilizar os livros de jogadas (soluções automatizadas) para remediar problemas de segurança.

Quickstart: A bordo do Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Gerir e responder a alertas de segurança no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Responda a eventos com alertas do Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável; O Azure Key Vault não processa nem produz registos relacionados com anti-malware.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável; O Cofre de Chaves Azure não processa nem produz registos relacionados com DNS.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha um inventário das suas aplicações registadas no Diretório Ativo Azure, bem como quaisquer contas de utilizador que tenham acesso às chaves, segredos e certificados do Cofre de Chaves Azure. Pode utilizar o portal Azure ou o PowerShell para consultar e conciliar o acesso ao Key Vault. Para ver o acesso no PowerShell, utilize o seguinte comando:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Propriedades.AccessPolicies

Registar uma candidatura com o Diretório Ativo azure:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Acesso seguro a um cofre chave:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: Não aplicável; O Azure Key Vault não tem o conceito de senhas padrão, uma vez que a autenticação é fornecida pelo Ative Directory e protegida com controlo de acesso baseado em Funções.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso às instâncias do Cofre chave Azure. Utilize a Identidade e Gestão de Acesso do Centro de Segurança Azure (atualmente em pré-visualização) para monitorizar o número de contas administrativas ativas.

Monitorizar a identidade e o acesso (pré-visualização):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: Utilize um diretor de serviço Azure em conjunto com o AppId, TenantID e ClientSecret, para autenticar perfeitamente a sua aplicação e recuperar o símbolo que será usado para aceder aos seus segredos do Cofre chave Azure.

Autenticação serviço-a-serviço para o Cofre chave Azure utilizando .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Ativar a autenticação multi-factor de diretório ativo do Azure e seguir as recomendações de Identidade e Acesso do Azure Security Center (atualmente em pré-visualização) para ajudar a proteger os recursos ativados pelo Hub do evento.

Planejando uma implementação de autenticação azure multi-factor baseada na nuvem:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitorizar a identidade e o acesso (pré-visualização):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiado (PAW) com autenticação de multi-factores Azure (MFA) configurada para iniciar sessão e configurar os recursos habilitados para o Cofre chave. 

Postos de trabalho privilegiados de acesso:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Planejando uma implementação de autenticação azure multi-factor baseada na nuvem:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AAD) Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente. Utilize deteções de risco AAD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. Para a exploração madeireira adicional, envie alertas de deteção de risco do Azure Security Center para o Monitor Azure e configure alertas/notificações personalizados utilizando grupos de ação.

Permitir que a proteção avançada de ameaças (ATP) para o Cofre chave Azure gere alertas para atividades suspeitas.

Implemente a Gestão de Identidade Privilegiada da Azure AD (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Criar proteção avançada contra ameaças para o Cofre chave Azure (pré-visualização):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Alertas para cofre de chaves Azure (Pré-visualização):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Deteções de risco do Diretório Ativo Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Criar e gerir grupos de ação no portal Azure:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Configure o estado de localização de uma política de acesso condicional e gerencie os seus locais nomeados. Com localizações nomeadas, pode criar agrupamentos lógicos de gamas de endereços IP ou países e regiões. Pode restringir o acesso a recursos sensíveis, como os seus segredos do Cofre chave, aos seus locais configurados.

Qual é a condição de localização no Acesso Condicional do Diretório Ativo Azure?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (AAD) como sistema central de autenticação e autorização para recursos Azure, como o Key Vault. Isto permite que o controlo de acesso baseado em funções (RBAC) adite recursos sensíveis.

 

Quickstart: Criar um novo inquilino no Azure Ative Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: Rever os registos de Diretório Ativo azure (AAD) para ajudar a descobrir contas velhas com funções administrativas do Cofre chave Azure. Além disso, utilize avaliações de acesso AAD para gerir eficientemente os membros do grupo, acesso a aplicações empresariais que possam ser usadas para aceder ao Azure Key Vault e atribuições de papéis. O acesso ao utilizador deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os utilizadores certos tenham acesso continuado.

Relatórios de Diretório Ativo Azure e documentação de monitorização:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

O que são as avaliações de acesso da Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Ative as definições de diagnóstico para o Cofre de Chaves Azure e para o Diretório Ativo Azure, enviando todos os registos para um espaço de trabalho de Log Analytics. Configure os alertas desejados (como tentativas de aceder a segredos desativados) dentro do Log Analytics.

Integre os registos de AD Azure com registos do Monitor Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrando da antiga solução Key Vault:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize as funcionalidades de Proteção de Identidade e Deteção de Riscos do Azure Ative Directory para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com os recursos protegidos do seu Cofre chave Azure. Deve permitir que as respostas automatizadas através do Azure Sentinel implementem as respostas de segurança da sua organização. 

Relatório de inscrição arriscado no portal azure ative diretório:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Como: Configurar e ativar políticas de risco:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Não aplicável; Caixa de bloqueio de cliente não suportada para cofre de chaves Azure.

Serviços e cenários suportados em disponibilidade geral:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis sobre o Cofre chave Azure habilitados recursos. 

Utilize etiquetas para organizar os seus recursos Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Pode garantir o acesso ao Cofre chave Azure através da utilização de pontos finais de serviço de rede virtual configurados para restringir o acesso a subredes específicas.

Depois de as regras de firewall estarem em vigor, só é possível executar operações de avião de dados Azure Key Vault quando o seu pedido tiver origem em subredes permitidas ou intervalos de endereçoip. Isto também se aplica ao acesso azure key vault no portal Azure. Embora possa navegar para um cofre chave a partir do portal Azure, pode não ser capaz de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Azure Key Vault Picker e outros serviços Azure. Pode ver listas de Cofres Chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de cliente de o fazer.

Configure firewalls e redes virtuais do Cofre de Chaves Do Azure:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Pontos finais de serviço de rede virtual para o Cofre chave Azure:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Todos os dados armazenados no Cofre de Chaves Azure são considerados sensíveis. Utilize os controlos de acesso ao avião de dados Azure Key Vault para controlar o acesso aos segredos do Cofre chave Azure. Também pode utilizar a firewall incorporada do Key Vault para controlar o acesso na camada de rede. Para monitorizar o acesso ao Cofre de Chaves Azure, ative as definições de diagnóstico do cofre de chaves e envie registos para uma conta de armazenamento Azure ou espaço de trabalho de Log Analytics.

Acesso seguro a um cofre chave:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Configure firewalls e redes virtuais do Cofre de Chaves Do Azure:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Exploração madeireira do cofre de chaves azure:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Todo o tráfego para o Cofre chave Azure para autenticação, gestão e acesso a avião de dados, é encriptado e passa por HTTPS: porta 443. (No entanto, ocasionalmente haverá tráfego HTTP [porta 80] para a CRL.) 

Aceda ao Cofre chave Azure atrás de uma firewall:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Não aplicável; todos os dados dentro do Cofre chave Azure (segredos, chaves e certificados) são considerados sensíveis.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Acesso seguro à gestão e plano de dados das instâncias do Cofre chave Azure.

Acesso seguro a um cofre chave:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorização**do Centro de Segurança Azure : Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: A Microsoft gere a infraestrutura subjacente ao Cofre de Chaves Azure e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

O que é o cofre de chave do Azure?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Proteção de dados dos clientes da Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: Todos os objetos geridos (chave, certificados e segredos) são encriptados em repouso no Cofre chave Azure.

Documentação de apoio:

- [Modelo de encriptação e tabela de gestão de chaves](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize a solução Azure Key Vault Analytics no Azure Monitor para rever os registos de eventos de auditoria do Cofre de Chaves do Azure.

Solução azure key vault Analytics no Monitor Azure:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: A Microsoft realiza uma gestão de vulnerabilidade nos sistemas subjacentes que suportam o Cofre chave Azure.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: N/A; A Microsoft realiza a gestão de patch nos sistemas subjacentes que suportam o Key Vault.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: A Microsoft realiza uma gestão de vulnerabilidade nos sistemas subjacentes que suportam o Key Vault.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Utilize as classificações de risco padrão (Pontuação Segura) fornecidas pelo Azure Security Center.

Melhore a sua pontuação segura no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar e descubra todos os recursos (incluindo as instâncias do Cofre Chave Azure) dentro da sua subscrição. Certifique-se de que tem permissões adequadas (ler) no seu inquilino e que é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Quickstart: Execute a sua primeira consulta de gráfico de recursos usando o Explorador de Gráficos de Recursos Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Obtenha subscrições a que a conta corrente pode aceder.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

O que é o controlo de acesso baseado em funções (RBAC) dos recursos do Azure?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Cofre chave Azure, dando metadados para logicamente organizá-los numa taxonomia.

Como criar e utilizar Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear as instâncias do Cofre chave azure e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Crie uma subscrição azure adicional:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Criar grupos de gestão para organização e gestão de recursos:

https://docs.microsoft.com/azure/governance/management-groups/create

Utilize etiquetas para organizar os seus recursos Azure:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Definir lista de recursos azure aprovados e software aprovado para os seus recursos computacionais

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize as políticas do Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou subscrição do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s subscrição)."

Tutorial: Criar e gerir políticas para impor o cumprimento:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Quickstart: Execute a sua primeira consulta de gráfico de recursos usando o Explorador de Gráficos de Recursos Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Não aplicável; esta recomendação destina-se ao Azure como um todo, bem como aos recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize as políticas do Azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou subscrição do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Tutorial: Criar e gerir políticas para impor o cumprimento:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Amostras de política azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos AzureResources através de scripts

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure (ARM) configurando o "block access" para a App "Microsoft Azure Management". Isto pode impedir a criação e alterações nos recursos dentro de um ambiente de alta segurança, como aqueles com configuração key vault.

Gerir o acesso à gestão azure com acesso condicional:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


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

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.KeyVault" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias do Cofre chave Azure. Também pode utilizar definições de política azure incorporadas para o Cofre chave Azure, tais como:

Objetos do cofre da chave devem ser recuperáveis

Implementar definições de diagnóstico para o cofre de chaves para registar espaço de trabalho de Análise de Registo

Os registos de diagnóstico no Cofre-Chave devem ser ativados

O Key Vault deve utilizar um ponto final de serviço de rede virtual

Implementar definições de diagnóstico para o cofre de chaves para o centro de eventos

Utilize recomendações do Azure Security Center como base de configuração segura para as instâncias do Cofre de Chaves Azure.

Como visualizar os pseudónimos disponíveis da Política Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Tutorial: Criar e gerir políticas para impor o cumprimento:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação:** Utilize a política azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos ativados por cofre seletiva azure. 

Tutorial: Criar e gerir políticas para impor o cumprimento:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Compreender os efeitos da política azure: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições de política azure personalizadas para o seu Cofre de Chave Azure ativou recursos, utilize o Azure Repos para armazenar e gerir de forma segura o seu código.

Como armazenar código em Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Documentação Azure Repos: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.KeyVault" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize o Centro de Segurança Azure para realizar digitalizações de base para os seus recursos protegidos por cofre sinuoso 

  

Como remediar recomendações no Centro de Segurança Azure: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta referência destina-se a recursos de computação.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerida em conjunto com o Cofre de Chaves Azure para simplificar e proteger a gestão secreta das suas aplicações na nuvem. Certifique-se de que o cofre de teclas Azure está ativado.

Como integrar-se com identidades geridas pelo Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do Cofre Chave com uma identidade gerida: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize a Identidade de Serviço Gerida em conjunto com o Cofre de Chaves Azure para simplificar e proteger a gestão secreta das suas aplicações na nuvem. 

  

Como integrar-se com identidades geridas pelo Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Como criar um Cofre chave: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Como fornecer a autenticação do Cofre Chave com uma identidade gerida:  
https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure.  
  
 Como configurar o Scanner Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Azure (por exemplo, Azure Key Vault), no entanto, não funciona com conteúdo do cliente.

Pré-digitalização de qualquer conteúdo que seja carregado ou enviado para recursos não computacionais do Azure Key Vault, como o Azure Key Vault. A Microsoft não pode aceder aos seus dados nestes casos.

Compreenda o Microsoft Antimalware para Serviços Azure Cloud e Máquinas Virtuais:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos. A Microsoft lida com anti-malware para a plataforma subjacente.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Certifique-se de cópias de segurança automáticas regulares dos certificados, chaves, contas de armazenamento geridos e segredos, com os seguintes comandos PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcionalmente, pode armazenar as suas cópias de segurança do Key Vault dentro do Azure Backup.

Como fazer backup dos certificados do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Como fazer backup das chaves do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Como fazer backup nas Contas de Armazenamento Geridas pelo Cofre de Chaves:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Como apoiar os segredos do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Como ativar o Backup Azure:https://docs.microsoft.com/azure/backup



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: Execute cópias de segurança dos certificados, chaves, contas de armazenamento geridos e segredos, com os seguintes comandos PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcionalmente, pode armazenar as suas cópias de segurança do Key Vault dentro do Azure Backup.

Como fazer backup dos certificados do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Como fazer backup das chaves do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Como fazer backup nas Contas de Armazenamento Geridas pelo Cofre de Chaves:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Como apoiar os segredos do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Como ativar o Backup Azure:https://docs.microsoft.com/azure/backup



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Realizar periodicamente a restauração de dados dos seus Certificados de Cofre chave, chaves, contas de armazenamento geridas e segredos, com os seguintes comandos PowerShell:

- Restore-AzKeyVaultCertificate

- Restaurar-AzKeyVaultKey

- Restaurar-AzKeyVaultManagedStorageAccount

- Restaurar-AzKeyVaultSecret

Como restaurar os certificados do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Como restaurar as chaves do cofre:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Como restaurar as Contas de Armazenamento Geridas pelo Cofre chave:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Como restaurar os segredos do cofre:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Certifique-se de que o soft-delete está ativado para o Cofre de Chaves Azure. O soft-delete permite a recuperação de cofres de chaves apagados e objetos de cofre, tais como chaves, segredos e certificados. 

Como utilizar o Soft Delete do Cofre de Chaves Azure: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente. Estes processos devem ter um foco na proteção de sistemas sensíveis, como aqueles que usam segredos do Cofre Chave.

Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia de um incidente do Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. Além disso, marque claramente as assinaturas (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis, como os segredos do Cofre Chave Azure.


**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger as instâncias do Cofre chave Azure e recursos conexos. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

Como definir o Contacto de Segurança do Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua para ajudar a identificar riscos para os recursos ativados pelo Cofre de Chaves azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua.  Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Azure Sentinel. 

 

Como configurar a exportação contínua: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Como transmitir alertas para O Sentinela Azul: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os recursos protegidos pelo cofre de chaves Azure. 

 

Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Não deve realizar testes de caneta saqueia diretamente no serviço Azure Key Vault, no entanto é encorajado a testar os seus recursos Azure que estão a usar o Key Vault para garantir a segurança dos segredos.

Terá de seguir as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft, aqui: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
