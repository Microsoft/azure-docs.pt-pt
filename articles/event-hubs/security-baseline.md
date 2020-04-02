---
title: Linha de Base de Segurança Azure para Centros de Eventos
description: Linha de Base de Segurança Azure para Centros de Eventos
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 10fc822f9f36512405dd4e3b5aba6270b53e163f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549034"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Linha de Base de Segurança Azure para Centros de Eventos

A Linha de Base de Segurança Azure para Centros de Eventos contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: A integração de centros de eventos com pontos finais de serviço de rede virtual permite o acesso seguro a capacidades de mensagens a partir de cargas de trabalho como máquinas virtuais que estão ligadas a redes virtuais, com a trajetória de tráfego da rede a ser protegida em ambas as extremidades.

Uma vez ligado a pelo menos um ponto final de serviço de rede virtual, o respetivo espaço de nome saque de Event Hubs já não aceita tráfego de qualquer lugar, mas subredes autorizadas em redes virtuais. Do ponto de vista da rede virtual, vincular o seu espaço de nome sem nome do Event Hubs a um ponto final de serviço configura um túnel de rede isolado da subnet da rede virtual para o serviço de mensagens. 

Também pode criar um ponto final privado, que é uma interface de rede que o liga de forma privada e segura ao serviço Azure Event Hubs utilizando o serviço Azure Private Link. O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN ou endereços IP públicos. 

Também pode proteger o seu espaço de nome Sem Nome Azure Event Hubs utilizando firewalls. O Azure Event Hubs suporta controlos de acesso baseados em IP para suporte de firewall de entrada. Pode definir regras de firewall utilizando o portal Azure, os modelos Do Gestor de Recursos Azure, ou através do Azure CLI ou Azure PowerShell.

Como utilizar pontos finais de serviço de rede virtual com Hubs de Eventos Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Para mais informações, consulte Integrate Azure Event https://docs.microsoft.com/azure/event-hubs/private-link-serviceHubs com Ligação Privada Azure: .

Ativar a integração de redes virtuais e firewalls no espaço de nome dos Hubs de Eventos:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Como configurar as regras de firewall IP para os espaços de nome do Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitore e registe a configuração e tráfego de Vnets, Subnets e NICs

**Orientação**: Utilize o Azure Security Center e siga as recomendações de proteção da rede para ajudar a proteger os seus recursos de Hubs de Eventos em Azure. Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, ative os registos de fluxo do grupo de segurança da rede (NSG) e envie registos numa conta de armazenamento para auditoria de tráfego.

Como ativar os registos de fluxo nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Compreender a Segurança da Rede fornecida pelo Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a Norma de Proteção DDoS nas redes virtuais associadas aos seus centros de eventos para se proteger contra ataques de negação de serviço distribuídos (DDoS). Utilize o Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP da Internet maliciosos ou não utilizados conhecidos.

Como configurar a proteção DDoS:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Para mais informações sobre o Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação**: Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, ative os registos de fluxo do grupo de segurança da rede (NSG) e envie registos numa conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho de Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

Se necessário para investigar atividades anómalas, ative a captura de pacotes do Observador da Rede.

Como ativar os registos de fluxo nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como ativar e utilizar o Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Como ativar o Observador da Rede:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Se utilizar máquinas virtuais Azure para aceder aos seus centros de eventos, selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil. Se não for necessária a deteção e/ou prevenção de intrusão com base na inspeção de carga útil, poderá utilizar a funcionalidade de firewall incorporada do Azure Event Hubs. Pode limitar o acesso ao seu espaço de nome sem nome do Event Hubs para uma gama limitada de endereços IP, ou um endereço IP específico utilizando regras de Firewall.

Mercado Azure:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Como adicionar uma regra de firewall em Event Hubs para um endereço IP especificado:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorização**do Azure Security Center : Ainda não está disponível

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Não aplicável, esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus espaços de nome Sem Nome Azure Event Hubs com a Política Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.EventHub" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos espaços de nome sinuosos do event hubs. Também pode utilizar definições políticas incorporadas relacionadas com hubs de eventos azure, tais como:

- O Event Hub deve utilizar um ponto final de serviço de rede virtual.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Política Azure Built-in para espaço de nome de Centros de Eventos:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Amostras da Política Azure para networking:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Como criar um Projeto Azure:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para redes virtuais e outros recursos relacionados com a segurança da rede e fluxo de tráfego que estejam associados aos seus centros de eventos.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade seleções do Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com os Hubs de Eventos Do Azure. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede.

Como visualizar e recuperar eventos de registo de atividade sinuosa:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Como criar alertas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilize fontes de sincronização do tempo aprovadas

**Orientação**: Não aplicável; A Microsoft mantém a fonte de tempo utilizada para os recursos Do Azure, como o Azure Event Hubs, para os timestamps nos registos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central de registos de segurança

**Orientação**: Dentro do Monitor Azure, configure registos relacionados com centros de eventos dentro das definições de diagnóstico do Registo de Atividades e do Event Hub para enviar registos para um espaço de trabalho de Log Analytics para ser consultado ou para uma conta de armazenamento para armazenamento de arquivo a longo prazo.

Como configurar as Definições de Diagnóstico para Centros de Eventos Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Compreender o Registo de Atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos recursos do Azure

**Orientação**: Ative as definições de diagnóstico para o seu espaço de nome Do Seu Hubs de Eventos Azure. Existem três categorias de definições de Diagnóstico para Hubs de Eventos Azure: Registos de Arquivo, Registos Operacionais e Registos de Escala Automática. Ativar registos operacionais para capturar informações sobre o que está a acontecer durante as operações do Event Hubs, especificamente, o tipo de operação, incluindo a criação de hub de eventos, recursos utilizados e o estado da operação.

Além disso, pode ativar as definições de diagnóstico de log da Atividade Azure e enviá-las para uma Conta de Armazenamento Azure, centro de eventos ou um espaço de trabalho log Analytics. Os registos de atividade fornecem informações sobre as operações que foram realizadas nos seus Hubs de Eventos Azure e outros recursos. Utilizando registos de atividade, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) tomadas nos espaços de nome do seu Azure Event Hubs.

Como ativar as Definições de Diagnóstico para Centros de Eventos Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Como ativar as Definições de Diagnóstico para o Registo de Atividade seletiva do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registos de segurança

**Orientação**: Dentro do Monitor Azure, detete o seu período de retenção no espaço de trabalho Log Analytics de acordo com os regulamentos de conformidade da sua organização para capturar e rever incidentes relacionados com o hub de eventos.

Como definir parâmetros de retenção de registos para espaços de trabalho de Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitore e reveja registos

**Orientação**: Analise e monitorize os registos para comportamentos anómalos e reveja regularmente os resultados relacionados com os seus centros de eventos. Utilize o Log Analytics do Azure Monitor para rever os registos e executar consultas nos dados de registo. Em alternativa, pode ativar e embarcar dados para o Azure Sentinel ou um SIEM de terceiros.
 

Para mais informações sobre o espaço de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Como realizar consultas personalizadas no Monitor Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividades anómalas

**Orientação**: Dentro do Monitor Azure, configure registos relacionados com os Hubs de Eventos Azure dentro do Registo de Atividades e configurações de diagnóstico de Centros de Eventos para enviar registos para um espaço de trabalho de Log Analytics para ser consultado ou para uma conta de armazenamento para armazenamento de arquivo a longo prazo. Utilize o espaço de trabalho do Log Analytics para criar alertas para atividades anómalas encontradas em registos e eventos de segurança.

Em alternativa, pode ativar e embarcar dados para o Azure Sentinel. 

Compreender o Registo de Atividades do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Como configurar as Definições de Diagnóstico para Centros de Eventos Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Como alertar sobre os dados de registo do espaço de trabalho do Log Analytics:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização**do Azure Security Center : Ainda não está disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Não aplicável; O Event Hub não processa a exploração anti-malware.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: Não aplicável; Os Centros de Eventos não processam ou produzem registos relacionados com DNS.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de auditoria da linha de comando

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Diretório Ativo azure (AD) tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

Como obter um papel de diretório em Azure AD com powerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de um papel de diretório em Azure AD com PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: Controle o acesso do avião aos Centros de Eventos é controlado através do Diretório Ativo Azure (AD). A Azure AD não tem o conceito de senhas padrão.

O acesso de avião de dados aos Centros de Eventos é controlado através de Azure AD com registos de Identidades Geridas ou App, bem como assinaturas de acesso partilhado. As assinaturas de acesso partilhado são utilizadas pelos clientes que se conectam aos seus centros de eventos e podem ser regeneradas a qualquer momento.

Compreenda as assinaturas de acesso partilhado para Centros de Eventos:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Identidade e Gestão de Acesso do Azure Security Center para monitorizar o número de contas administrativas.

Além disso, para o ajudar a acompanhar as contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou das Políticas Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição

- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Como utilizar o Azure Security Center para monitorizar a identidade e o acesso (Pré-visualização):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Como utilizar a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal (SSO) com diretório ativo Azure

**Orientação**: O Microsoft Azure fornece uma gestão integrada do controlo de acesso para recursos e aplicações com base no Azure Ative Directory (AD). Uma vantagem fundamental de usar o Azure AD com o Azure Event Hubs é que já não precisa de armazenar as suas credenciais no código. Em vez disso, pode solicitar um sinal de acesso OAuth 2.0 da plataforma Microsoft Identity. O nome do recurso para https://eventhubs.azure.net/pedir um símbolo é . A Azure AD autentica o diretor de segurança (utilizador, grupo ou diretor de serviço) que executa a aplicação. Se a autenticação for bem sucedida, a Azure AD devolve um sinal de acesso à aplicação, podendo então utilizar o sinal de acesso para autorizar o pedido aos recursos do Azure Event Hubs.

Como autenticar uma aplicação com a Azure AD para aceder aos recursos do Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-application

Compreensão de SSO com AD Azure:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados em Diretório Ativo Azure

**Orientação**: Enable Azure Ative Directory Multi-Factor Authentication (MFA) e siga as recomendações de identidade e gestão do Centro de Segurança Azure para ajudar a proteger os recursos habilitados para o seu Hub de Eventos.

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorizar a identidade e o acesso dentro do Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de Trabalho de Acesso Privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize estações de trabalho privilegiadas de acesso (PAW) com autenticação multi-factor (MFA) configuradas para iniciar sessão e configurar recursos ativados por Hub de Eventos.

Saiba mais sobre postos de trabalho de acesso privilegiados:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Como permitir o MFA em Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (AD) Privileged Identity Management (PIM) para a geração de registos e alertas quando ocorrer atividade suspeita ou insegura no ambiente. Utilize deteções de risco azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. Para a exploração madeireira adicional, envie alertas de deteção de risco do Azure Security Center para o Monitor Azure e configure alertas/notificações personalizados utilizando grupos de ação.

Como implementar a Gestão de Identidade Privilegiada (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Compreender as deteções de risco da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Como configurar grupos de ação para alerta e notificação personalizados:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados

**Orientação**: Utilizar locais de acesso condicional nomeados para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões.



Como configurar localizações nomeadas em Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (AD) como sistema central de autenticação e autorização para recursos Azure, como Centros de Eventos. Isto permite o controlo de acesso baseado em papéis (RBAC) a recursos sensíveis administrativos.

 Como criar e configurar uma instância azure ad:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Para saber como o Azure Event Hubs se integra com o Azure Ative Directory (AAD), consulte autorizar o acesso aos recursos do Event Hubs utilizando o Diretório Ativo azure:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: O Diretório Ativo Azure (AD) fornece registos para ajudá-lo a descobrir contas velhas. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de papéis. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.

Em outros, rode regularmente as assinaturas de acesso partilhado dos seus Centros de Eventos.

Compreender relatórios da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Como utilizar comentários de acesso à identidade do Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Compreender as assinaturas de acesso partilhado para Centros de Eventos:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tenta aceder a contas desativadas

**Orientação**: Tem acesso à atividade de registo de registo sessão do Azure Ative Directory (AD), fontes de auditoria e registo de eventos de risco, que lhe permitem integrar-se com qualquer ferramenta de registo/monitorização do SIEM/Monitor.

Pode simplificar este processo criando definições de diagnóstico para contas de utilizadores da AD Azure e enviando os registos de auditoria e registos de log-in para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo desejados dentro do Log Analytics.

Como integrar registos de atividade do Azure no Monitor Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorizar o acesso aos recursos do Event Hubs utilizando o Diretório Ativo azure:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize as funcionalidades de Proteção de Identidade e Deteção de Riscos do Azure Ative Directory para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com os recursos ativados pelo Seu Evento Hubs. Deve permitir que as respostas automatizadas através do Azure Sentinel implementem as respostas de segurança da sua organização.

Como ver os sign-ins de risco da AD Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e ativar políticas de risco de proteção de identidade:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Como embarcar no Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Atualmente não disponível; O Bloqueio do Cliente ainda não é suportado para os Hubs de Eventos.

Lista de serviços apoiados pelo Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Atualmente não disponível

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas sobre recursos relacionados com os seus Centros de Eventos para ajudar a rastrear os recursos do Azure que armazenam ou processam informações sensíveis.

Como criar e utilizar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Os espaços de nome do Event Hubs devem ser separados por rede virtual com pontos finais de serviço ativados e marcados adequadamente.

Também pode proteger o seu espaço de nome Sem Nome Do Azure Event Hubs utilizando firewalls. O Azure Event Hubs suporta controlos de acesso baseados em IP para suporte de firewall de entrada. Pode definir regras de firewall utilizando o portal Azure, os modelos Do Gestor de Recursos Azure, ou através do Azure CLI ou Azure PowerShell.

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Configure as regras de firewall IP para os espaços de nome do Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Como criar e utilizar tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como criar uma Rede Virtual:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ao utilizar máquinas virtuais para aceder aos seus centros de eventos, utilize redes virtuais, pontos finais de serviço, firewall Event Hubs, grupos de segurança de rede e etiquetas de serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gere a infraestrutura subjacente aos Hubs de Eventos Azure e implementou controlos rigorosos para evitar a perda ou exposição de dados dos clientes.

Configure as regras de firewall IP para os espaços de nome do Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Compreenda os pontos finais do serviço de rede virtual com hubs de eventos Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrar hubs de eventos Azure com Ligação Privada Azure:https://docs.microsoft.com/azure/event-hubs/private-link-service

Compreender os Grupos de Segurança da Rede e etiquetas de serviço:https://docs.microsoft.com/azure/virtual-network/security-overview

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: O Azure Event Hubs impõe por defeito comunicações encriptadas por TLS. As versões TLS 1.0, 1.1 e 1.2 são atualmente suportadas. No entanto, os TLS 1.0 e 1.1 estão no caminho da depreciação em toda a indústria, por isso use TLS 1.2 se possível.

Para compreender as funcionalidades de segurança dos Centros de Eventos, consulte a segurança da rede:https://docs.microsoft.com/azure/event-hubs/network-security

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para os Hubs de Eventos Azure. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Partilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: O Azure Event Hubs apoia a utilização do Azure Ative Directory (AD) para autorizar pedidos de recursos do Event Hubs. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (RBAC) para conceder permissões a um diretor de segurança, que pode ser um utilizador, ou um diretor de serviço de aplicação.

Compreenda o Azure AD RBAC e as funções disponíveis para o Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável; esta orientação destina-se a recursos de computação.

A Microsoft gere a infraestrutura subjacente aos Hubs de Eventos e implementou controlos rigorosos para evitar a perda ou exposição dos dados dos clientes.

Compreender a proteção de dados dos clientes em Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: O Azure Event Hubs suporta a opção de encriptar dados em repouso com chaves geridas pela Microsoft ou chaves geridas pelo cliente. Esta funcionalidade permite-lhe criar, rodar, desativar e revogar o acesso às chaves geridas pelo cliente que são usadas para encriptar dados do Azure Event Hubs em repouso.

Como configurar as chaves geridas pelo cliente para encriptar os Hubs de Eventos Azure:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo da Atividade Azure para criar alertas para quando ocorrerem alterações em casos de produção de Hubs de Eventos Azure e outros recursos críticos ou conexos.

Como criar alertas para eventos de Registo de Atividade seleções do Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam os Event Hubs.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patch do sistema operativo

**Orientação**: Não aplicável; A Microsoft realiza a gestão de patch nos sistemas subjacentes que suportam os Event Hubs.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação**: Não aplicável; o benchmark destina-se aos recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar as varreduras de vulnerabilidade consecutivas

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam os Event Hubs.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam os Event Hubs.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar e descobrir todos os recursos (incluindo os espaços de nome do Azure Event Hubs) dentro da sua subscrição(s). Certifique-se de que tem permissões adequadas (ler) no seu inquilino e que é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições.

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como visualizar as suas Assinaturas Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Compreender o Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure, dando metadados para logicamente organizá-los numa taxonomia.

Como criar e utilizar etiquetas:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos Azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear os espaços de nome sem nome do Azure Event Hubs e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente.

Como criar subscrições adicionais do Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gestão:https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e utilizar Tags:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter um inventário dos títulos aprovados de recursos e software do Azure

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação e ao Azure como um todo.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para os recursos Azure não aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Azure Resource Graph para consultar/descobrir recursos dentro da(s subscrição)."

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos do cálculo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos não aprovados do Azure e aplicações de software

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos de computação e ao Azure como um todo.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo específico de recurso com a Política Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Limite a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de scripts</div>

**Orientação**: Configure O Acesso Condicional azure para limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure configurando o "Block access" para a App "Microsoft Azure Management".

Como configurar o Acesso Condicional ao acesso ao portal Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Orientação**: Defina e implemente configurações de segurança padrão para as implementações dos Hubs de Eventos Azure. Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.EventHub" para criar políticas personalizadas para auditar ou impor configurações. Também pode utilizar definições políticas incorporadas para hubs de eventos azure tais como:

- Os registos de diagnóstico no Event Hub devem ser ativados

- O Event Hub deve usar um ponto final do serviço de rede virtual

Política Azure Built-in para espaço de nome de Centros de Eventos:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Como visualizar os aliases de política do Azure disponíveis:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação:** Utilize a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos ativados por Hubs de Eventos. 

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Para mais informações sobre os Efeitos políticos do Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

**Orientação**: Se utilizar definições de Política Azure personalizadas para os seus Hubs de Eventos ou recursos conexos, utilize o Azure Repos para armazenar e gerir de forma segura o seu código.

Como armazenar código em Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.EventHub" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Utilize a política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente as configurações para as implementações dos seus Hubs de Eventos Azure e recursos conexos.

Como configurar e gerir a Política Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service que estão a ser utilizados para aceder aos seus centros de eventos, utilize a Identidade de Serviço Gerida em conjunto com o Azure Key Vault para simplificar e garantir a gestão de assinaturas de acesso partilhado para as suas implementações do Azure Event Hubs. Certifique-se de que o soft-delete do Key Vault está ativado.

Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos recursos do Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Configure as chaves geridas pelo cliente para Os Centros de Eventos:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Como integrar-se com identidades geridas pelo Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do Cofre Chave com uma identidade gerida:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Para máquinas virtuais Azure ou aplicações web em funcionamento no Azure App Service que estão a ser utilizados para aceder aos seus centros de eventos, utilize a Identidade de Serviço Gerida em conjunto com o Azure Key Vault para simplificar e proteger os Hubs de Eventos Azure. Certifique-se de que o soft-delete do Key Vault está ativado.

Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Diretório Ativo Azure (AD). Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação Azure AD, incluindo o Cofre chave Azure, sem qualquer credencial no seu código.

Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos Recursos hubs de eventos:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Configure as chaves geridas pelo cliente para Os Centros de Eventos:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Como configurar identidades geridas:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Como integrar-se com identidades geridas pelo Azure:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure.

Como configurar o Scanner Credencial:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, o Azure App Service), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: Pré-digitalização de qualquer conteúdo que seja enviado para recursos não computacionais do Azure, tais como Hubs de Eventos Azure, Serviço de Aplicações, Armazenamento de Data Lake, Armazenamento Blob, Base de Dados Azure para PostgreSQL, etc. A Microsoft não pode aceder aos seus dados nestes casos.

O anti-malware da Microsoft está ativado no hospedeiro subjacente que suporta os serviços Do Azure (por exemplo, Azure Cache for Redis), no entanto não funciona com conteúdo de clientes.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software anti-malware e assinaturas são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

**Orientação**: Configure a recuperação geo-desastre para os Hubs de Eventos Azure. Quando regiões ou centros de dados inteiros do Azure (se não forem utilizadas zonas de disponibilidade) experimentam tempo de inatividade, é fundamental que o processamento de dados continue a operar numa região ou datacenter diferente. Como tal, a recuperação geo-desastre e a geo-replicação são características importantes para qualquer empresa. O Azure Event Hubs apoia a recuperação geo-desastre e a geo-replicação, ao nível do espaço de nome. 

Compreenda a recuperação geo-desastre dos Hubs de Eventos Azure:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

**Orientação**: O Azure Event Hubs fornece encriptação de dados em repouso com encriptação do Serviço de Armazenamento Azure (Azure SSE). O Event Hubs conta com o Armazenamento Azure para armazenar os dados e, por padrão, todos os dados armazenados com o Armazenamento Azure são encriptados utilizando chaves geridas pela Microsoft. Se utilizar o Cofre de Chaves Azure para armazenar chaves geridas pelo cliente, certifique-se de cópias de segurança automáticas regulares das suas Keys.

Certifique-se de cópias de segurança automatizadas regulares dos seus Segredos de Cofre chave com o seguinte comando PowerShell: Backup-AzKeyVaultSecret

Como configurar as chaves geridas pelo cliente para encriptar os dados do Azure Event Hubs em repouso:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Como apoiar os segredos do cofre de chaves:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Teste a restauração das chaves geridas pelo cliente.

 

Como restaurar as chaves do cofre chave em Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Ativar a eliminação suave no Cofre-Chave para proteger as chaves contra a eliminação acidental ou maliciosa. O Azure Event Hubs requer chaves geridas pelo cliente para ter Soft Delete e Não Purgar configurados.

Configure o soft delete para a conta de Armazenamento Azure que é usada para capturar dados do Event Hubs. Note que esta funcionalidade ainda não é suportada para o Azure Data Lake Storage Gen 2 2.

Como permitir a eliminação suave no Cofre-Chave:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Instale um cofre com chaves:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Eliminação suave para bolhas de armazenamento Azure:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definam funções de pessoal, bem como fases de tratamento/gestão de incidentes.

Como configurar as automatizações de fluxo de trabalho dentro do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma severidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que, quando um recurso estiver comprometido, possa chegar ao mesmo imediatamente. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Consulte a publicação do NIST: Guia para programas de teste, formação e exercício para planos e capacidades de TI:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

Como definir o Contacto de Segurança do Centro de Segurança Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas Sentinel.

Como configurar a exportação contínua:https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para O Sentinela Azul:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todos os resultados críticos de segurança no prazo de 60 dias

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1que os seus Testes de Penetração não violam as políticas da Microsoft: .
Pode encontrar mais informações sobre a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra a Microsoft gerida por infraestruturas, serviços e aplicações em nuvem, aqui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
