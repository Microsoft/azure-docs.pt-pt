---
title: Linha de base de segurança Azure para centros de eventos
description: Linha de base de segurança Azure para centros de eventos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f47e9cb93b90c8a2401d1dfe6c0ff7800b0bcf27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401134"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Linha de base de segurança Azure para centros de eventos

A Linha de Base de Segurança Azure para Centros de Eventos contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, consulte [Controlo de Segurança: Segurança da Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação**: A integração de centros de eventos com pontos finais de serviço de rede virtual permite o acesso seguro às capacidades de mensagens a partir de cargas de trabalho, tais como máquinas virtuais que estão ligadas a redes virtuais, com o caminho de tráfego da rede a ser assegurado em ambas as extremidades.

Uma vez ligado a pelo menos um ponto final de serviço de sub-rede de rede virtual, o respetivo espaço de nomes do Event Hubs já não aceita tráfego de qualquer lugar, mas sub-redes autorizadas em redes virtuais. Do ponto de vista da rede virtual, ligar o seu espaço de nomes Event Hubs a um ponto final de serviço configura um túnel de rede isolado da sub-rede de rede virtual ao serviço de mensagens. 

Também pode criar um ponto final privado, que é uma interface de rede que o liga de forma privada e segura ao serviço Azure Event Hubs utilizando o serviço Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. 

Também pode proteger o seu espaço de nomeS Azure Event Hubs utilizando firewalls. O Azure Event Hubs suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Pode definir regras de firewall utilizando o portal Azure, os modelos Azure Resource Manager ou através do Azure CLI ou Azure PowerShell.

Como utilizar pontos finais de serviço de rede virtual com hubs de eventos Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Para mais informações, consulte Integrate Azure Event Hubs com Azure Private Link: https://docs.microsoft.com/azure/event-hubs/private-link-service .

Ativar a integração de redes virtuais e firewalls no espaço de nomes de Centros de Eventos: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Como configurar as regras de firewall IP para espaços de nomes Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorar e registar a configuração e o tráfego de Vnets, Subnetas e NICs

**Orientação**: Utilize o Centro de Segurança Azure e siga as recomendações de proteção da rede para ajudar a proteger os recursos dos seus Centros de Eventos em Azure. Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, ative os registos de fluxo do grupo de segurança da rede (NSG) e envie registos para uma conta de armazenamento para auditoria de tráfego.

Como ativar os registos de fluxo NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Compreender a Segurança da Rede fornecida pelo Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative o DDoS Protection Standard nas redes virtuais associadas aos centros de eventos para se proteger contra ataques de negação de serviço distribuídos (DDoS). Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

Como configurar a proteção DDoS: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../virtual-network/manage-ddos-protection.md)

Para mais informações sobre o Centro de Segurança Azure Integrado De Ameaças: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, ative os registos de fluxo do grupo de segurança da rede (NSG) e envie registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Se necessário para investigar a atividade anómala, ative a captura de pacotes do Observador de Rede.

Como ativar os registos de fluxo NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como Ativar e utilizar a Análise de Tráfego: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Como ativar o Observador de Rede: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil. Se a deteção e/ou prevenção de intrusões com base na inspeção de carga útil não for necessária para a sua organização, poderá utilizar a funcionalidade de firewall incorporada do Azure Event Hubs. Pode limitar o acesso ao seu espaço de nomes de Centros de Eventos para uma gama limitada de endereços IP ou um endereço IP específico utilizando as regras de Firewall.

Mercado Azure:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Como adicionar uma regra de firewall nos Centros de Eventos para um endereço IP especificado:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorização do Centro de Segurança Azure**: Ainda não disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Não aplicável, esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus espaços de nome Azure Event Hubs com Azure Policy. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.EventHub" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos espaços de nomes dos Seus Centros de Eventos. Você também pode fazer uso de definições políticas incorporadas relacionadas com Azure Event Hubs, tais como:

- O Event Hub deve utilizar um ponto final de serviço de rede virtual.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Política Azure Built-in para o espaço de nomes de Centros de Eventos:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Amostras da Política Azure para networking: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Como criar uma Planta Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para redes virtuais e outros recursos relacionados com a segurança da rede e o fluxo de tráfego que estejam associados aos seus centros de eventos.

Como criar e utilizar tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com os Hubs de Eventos Azure. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

Como visualizar e recuperar eventos de Registo de Atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: Não aplicável; A Microsoft mantém a fonte de tempo utilizada para os recursos Azure, como o Azure Event Hubs, para os timetamps nos registos.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Dentro do Azure Monitor, configurar registos relacionados com os centros de eventos dentro das definições de diagnóstico de Log e Event Hub para enviar registos para um espaço de trabalho do Log Analytics a ser consultado ou numa conta de armazenamento para armazenamento de arquivo a longo prazo.

Como configurar definições de diagnóstico para centros de eventos Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Compreensão do Registo de Atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative as definições de diagnóstico para o seu espaço de nomes Azure Event Hubs. Existem três categorias de definições de Diagnóstico para Hubs de Eventos Azure: Registos de Arquivo, Registos Operacionais e Registos De Escala Automática. Permitir que os Registos Operacionais capturem informações sobre o que está a acontecer durante as operações do Event Hubs, especificamente, o tipo de operação, incluindo a criação de centros de eventos, os recursos utilizados e o estado da operação.

Além disso, pode ativar as definições de diagnóstico de registo de registo de atividade do Azure e enviá-las para uma Conta de Armazenamento Azure, centro de eventos ou um espaço de trabalho Log Analytics. Os registos de atividade fornecem informações sobre as operações que foram realizadas nos seus Hubs de Eventos Azure e outros recursos. Utilizando registos de atividade, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) tomadas nos seus espaços de nomes Azure Event Hubs.

Como ativar as definições de diagnóstico para centros de eventos Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Como ativar as definições de diagnóstico para o registo de atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Dentro do Azure Monitor, desaveja o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização para capturar e rever incidentes relacionados com o centro de eventos.

Como definir parâmetros de retenção de registos para espaços de trabalho do Log Analytics: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados relacionados com os seus centros de eventos. Utilize o Azure Monitor's Log Analytics para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.
 

Para mais informações sobre o espaço de trabalho Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Como embarcar Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Dentro do Monitor Azure, configurar registos relacionados com os Hubs de Eventos Azure dentro do Registo de Atividades e configurações de diagnóstico de Centros de Eventos para enviar registos para um espaço de trabalho log analytics para ser consultado ou numa conta de armazenamento para armazenamento de arquivo a longo prazo. Use o espaço de trabalho do Log Analytics para criar alertas para a atividade anómala encontrada em registos de segurança e eventos.

Em alternativa, pode ativar e a bordo dados para a Azure Sentinel. 

Compreender o Registo de Atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Como configurar definições de diagnóstico para centros de eventos Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Como alertar os dados do registo do espaço de trabalho do Log Analytics: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Como embarcar Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização do Centro de Segurança Azure**: Ainda não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Não aplicável; O Event Hub não processa a exploração anti-malware.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; Os Centros de Eventos não processam nem produzem registos relacionados com DNS.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

Como obter um papel de diretório em Azure AD com PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório em Azure AD com PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O acesso do avião ao Event Hubs é controlado através do Azure Ative Directory (AD). A Azure AD não tem o conceito de senhas padrão.

O acesso de um avião de dados aos Centros de Eventos é controlado através do Azure AD com identidades geridas ou registos de aplicações, bem como assinaturas de acesso partilhada. As assinaturas de acesso partilhado são usadas pelos clientes que se conectam aos seus centros de eventos e podem ser regeneradas a qualquer momento.

Compreender assinaturas de acesso partilhado para Centros de Eventos: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição

- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Como utilizar a Política Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: O Microsoft Azure fornece uma gestão integrada do controlo de acessos para recursos e aplicações com base no Azure Ative Directory (AD). Uma vantagem fundamental de usar a Azure AD com Azure Event Hubs é que já não precisa de armazenar as suas credenciais no código. Em vez disso, pode solicitar um token de acesso OAuth 2.0 a partir da plataforma Microsoft Identity. O nome do recurso para solicitar um token é https: \/ /eventhubs.azure.net/. A Azure AD autentica o principal de segurança (um utilizador, grupo ou principal de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve um sinal de acesso à aplicação, e a aplicação pode então utilizar o token de acesso para autorizar o pedido aos recursos do Azure Event Hubs.

Como autenticar uma aplicação com Azure AD para aceder aos recursos do Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Compreensão de SSO com Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ative Directory Multi-Factor Authentication (MFA) e siga as recomendações de identidade do Centro de Segurança Azure e de gestão de acessos para ajudar a proteger os recursos ativados pelo Centro de Eventos.

Como permitir o MFA em Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho de acesso privilegiada (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos ativados pelo Centro de Eventos.

Saiba mais sobre estações de acesso privilegiadas: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. Para obter registos adicionais, envie alertas de deteção de risco do Azure Security Center para o Azure Monitor e configuure alertas/notificações personalizados utilizando grupos de ação.

Como implementar a Gestão de Identidade Privilegiada (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Compreenda as deteções de risco da AD Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Como configurar grupos de ação para alerta e notificação personalizados: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.



Como configurar localizações nomeadas em Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (AD) como o sistema central de autenticação e autorização para recursos Azure, tais como Centros de Eventos. Isto permite que o controlo de acesso baseado em funções (RBAC) a recursos sensíveis administrativos.

 Como criar e configurar um exemplo AD Azure: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Para saber como o Azure Event Hubs se integra com o Azure Ative Directory (AAD), consulte acesso autorizado aos recursos do Event Hubs utilizando o Diretório Ativo Azure: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (AD) fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Adicionalmente, rode regularmente as assinaturas de acesso partilhado dos Seus Centros de Eventos.

Compreenda a Azure AD reportando: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como utilizar comentários sobre o acesso à identidade do Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Compreender assinaturas de acesso partilhado para Centros de Eventos: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização das tentativas de acesso a contas desativadas

**Orientação**: Tem acesso a atividade de login do Azure Ative Directory (AD), fontes de registo de eventos de auditoria e risco, que lhe permitem integrar-se com qualquer ferramenta SIEM/Monitoring.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

Como integrar os Registos de Atividade do Azure no Monitor Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize as funcionalidades de Proteção de Identidade do Azure Ative Directory e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com os seus recursos habilitados para o Evento Hubs. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

Como ver a Azure AD a iniciar súmis arriscados: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e permitir políticas de risco de proteção de identidade: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Atualmente não disponível; O Lockbox do cliente ainda não está suportado para Os Centros de Eventos.

Lista de serviços suportados pelo Cliente Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Proteção de Dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas em recursos relacionados com os seus Centros de Eventos para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

Como criar e utilizar Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os espaços de nomes do Event Hubs devem ser separados por rede virtual com pontos finais de serviço ativados e marcados adequadamente.

Também pode proteger o seu espaço de nomeS Azure Event Hubs utilizando firewalls. O Azure Event Hubs suporta controlos de acesso baseados em IP para suporte a firewall de entrada. Pode definir regras de firewall utilizando o portal Azure, os modelos Azure Resource Manager ou através do Azure CLI ou Azure PowerShell.

Como criar subscrições adicionais do Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão: https://docs.microsoft.com/azure/governance/management-groups/create

Configure as regras de firewall IP para espaços de nome Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Como criar e utilizar tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma Rede Virtual: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ao utilizar máquinas virtuais para aceder aos seus centros de eventos, utilize redes virtuais, pontos finais de serviço, firewall de Centros de Eventos, grupos de segurança de rede e tags de serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gere a infraestrutura subjacente aos Azure Event Hubs e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

Configure as regras de firewall IP para espaços de nome Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Compreender pontos finais de serviço de rede virtual com hubs de eventos Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integre os Hubs de Eventos Azure com Azure Private Link: https://docs.microsoft.com/azure/event-hubs/private-link-service

Compreender grupos de segurança de rede e tags de serviço: https://docs.microsoft.com/azure/virtual-network/security-overview

Compreender a proteção de dados do cliente em Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Azure Event Hubs aplica por padrão comunicações encriptadas TLS. As versões TLS 1.0, 1.1 e 1.2 estão atualmente suportadas. No entanto, os TLS 1.0 e 1.1 estão num caminho para a depreciação em toda a indústria, pelo que utilize o TLS 1.2, se possível.

Para compreender as funcionalidades de segurança dos Centros de Eventos, consulte a segurança da rede:  https://docs.microsoft.com/azure/event-hubs/network-security

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os Hubs de Eventos Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados do cliente em Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Azure Event Hubs suporta a utilização do Azure Ative Directory (AD) para autorizar pedidos aos recursos do Event Hubs. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador ou um diretor de serviço de aplicação.

Compreenda o Azure AD RBAC e as funções disponíveis para os Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

A Microsoft gere a infraestrutura subjacente aos Centros de Eventos e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

Compreender a proteção de dados do cliente em Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: O Azure Event Hubs suporta a opção de encriptar dados em repouso com as teclas geridas pela Microsoft ou com as teclas geridas pelo cliente. Esta funcionalidade permite-lhe criar, rodar, desativar e revogar o acesso às teclas geridas pelo cliente que são utilizadas para encriptar os dados do Azure Event Hubs em repouso.

Como configurar chaves geridas pelo cliente para encriptar os Hubs de Eventos Azure: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações nas instâncias de produção de Azure Event Hubs e outros recursos críticos ou relacionados.

Como criar alertas para eventos de Registo de Atividades Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam os Centros de Eventos.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Não aplicável; A Microsoft realiza a gestão de patchs nos sistemas subjacentes que suportam os Centros de Eventos.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patchs de software de terceiros

**Orientação**: Não aplicável; referência destina-se a recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam os Centros de Eventos.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam os Centros de Eventos.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação**: Utilize o Azure Resource Graph para consultar e descubra todos os recursos (incluindo espaços de nomes Azure Event Hubs) dentro da sua subscrição( s). Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Como criar consultas com gráfico de recursos Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como ver as suas Subscrições Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreenda Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

Como criar e utilizar tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear espaços de nomes e recursos conexos do Azure Event Hubs. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Como criar subscrições adicionais do Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão: https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos recursos aprovados da Azure e dos títulos de software

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional e a Azure como um todo.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Azure Resource Graph para consultar/descobrir recursos dentro da subscrição.s.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com O Gráfico Azure: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional e a Azure como um todo.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure através de scripts</div>

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte [Controlo de Segurança: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para as suas implementações de Azure Event Hubs. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.EventHub" para criar políticas personalizadas para auditar ou impor configurações. Você também pode usar definições de política incorporadas para Azure Event Hubs tais como:

- Os registos de diagnóstico no Centro de Eventos devem ser ativados

- O Event Hub deve utilizar um ponto final de serviço de rede virtual

Política Azure Built-in para o espaço de nomes de Centros de Eventos: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Como visualizar pseudónimos disponíveis da Política Azure: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor definições seguras em todos os recursos ativados pelos Seus Centros de Eventos. 

Como configurar e gerir a Política de Azure:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Para mais informações sobre os Efeitos políticos do Azure:  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para os seus Centros de Eventos ou recursos relacionados, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

Como armazenar código em Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para serviços Azure

**Orientação**: Use pseudónimos da Política Azure no espaço de nomes "Microsoft.EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para as suas implementações de Azure Event Hubs e recursos relacionados.

Como configurar e gerir a Política de Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usadas para aceder aos seus centros de eventos, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir a gestão de assinaturas de acesso partilhado para as suas implementações do Azure Event Hubs. Certifique-se de que o cofre de chaves está ativado.

Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos recursos do Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Configure as chaves geridas pelo cliente para os Centros de Eventos: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Como integrar-se com identidades geridas aZure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Como autenticar o Cofre de Chaves: https://docs.microsoft.com/azure/key-vault/general/authentication

Como atribuir uma política de acesso ao Cofre chave: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder aos seus centros de eventos, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger os Hubs de Eventos Azure. Certifique-se de que o cofre de chaves está ativado.

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente no Azure Ative Directory (AD). Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo Azure Key Vault, sem quaisquer credenciais no seu código.

Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos do Centro de Eventos: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Configure as chaves geridas pelo cliente para os Centros de Eventos: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Como configurar identidades geridas: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como integrar-se com identidades geridas aZure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Como configurar o Scanner Credencial: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Controlo de Segurança: Defesa de Malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure App Service), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, etc. A Microsoft não pode aceder aos seus dados nestes casos.

O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Cache para Redis), no entanto não funciona com conteúdo do cliente.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Configurar a recuperação de geo-desastres para os Hubs de Eventos Azure. Quando regiões ou centros de dados inteiros do Azure (se não forem utilizadas zonas de disponibilidade) experimentam tempo de inatividade, é fundamental que o processamento de dados continue a funcionar numa região ou centro de dados diferentes. Como tal, a recuperação de geo-desastres e a geo-replicação são características importantes para qualquer empresa. O Azure Event Hubs suporta a recuperação de geo-desastres e a geo-replicação, ao nível do espaço de nome. 

Compreenda a recuperação de geo-desastres para os Hubs de Eventos Azure: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Azure Event Hubs fornece encriptação de dados em repouso com Azure Storage Service Encryption (Azure SSE). O Event Hubs conta com o Azure Storage para armazenar os dados e, por padrão, todos os dados que são armazenados com o Azure Storage são encriptados utilizando as teclas geridas pela Microsoft. Se utilizar o Cofre de Chaves Azure para armazenar chaves geridas pelo cliente, certifique-se de cópias de segurança automáticas regulares das suas Chaves.

Certifique-se de cópias de segurança automáticas regulares dos seus Segredos do Cofre chave com o seguinte comando PowerShell: Backup-AzKeyVaultSecret

Como configurar chaves geridas pelo cliente para encriptar os dados do Azure Event Hubs em repouso: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Como apoiar os Segredos do Cofre chave: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Testar a restauração das chaves geridas pelo cliente.

 

Como restaurar as chaves do cofre chave em Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Ative a eliminação suave no Cofre da Chave para proteger as chaves contra a eliminação acidental ou maliciosa. Os Azure Event Hubs requerem chaves geridas pelo cliente para ter Soft Delete e Não Purgar configurados.

Configure a eliminação suave para a conta de Armazenamento Azure que é usada para capturar dados do Event Hubs. Note que esta funcionalidade ainda não é suportada para a Azure Data Lake Storage Gen 2.

Como permitir a eliminação suave no Cofre de Chaves: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Crie um cofre com chaves: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Excluir suavemente para bolhas de armazenamento Azure: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](../security/benchmarks/security-control-incident-response.md)*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definem funções de pessoal, bem como fases de tratamento/gestão de incidentes.

Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que quando um recurso estiver comprometido, possa chegar imediatamente ao mesmo. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Consulte a publicação do NIST: Guide to Test, Training e Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

Como definir o Contacto de Segurança do Centro de Segurança Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas Sentinel.

Como configurar a exportação contínua: https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a automatização do fluxo de trabalho e as aplicações lógicas: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte [Controlo de Segurança: Testes de penetração e exercícios da equipa vermelha.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .
Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridos pela Microsoft na nuvem, aqui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre [as Linhas de Base de Segurança Azure](../security/benchmarks/security-baselines-overview.md)
