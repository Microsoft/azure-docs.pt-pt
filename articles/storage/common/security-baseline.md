---
title: Linha de base de segurança azure para armazenamento azure
description: Linha de base de segurança azure para armazenamento azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d083ba757004fa477038750ad6210e4bb30659ee
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120759"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Linha de base de segurança azure para armazenamento azure

A Linha de Base de Segurança Azure para armazenamento azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implementação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](https://docs.microsoft.com/azure/security/benchmarks/overview)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com as nossas melhores práticas de orientação.

Para mais informações, consulte a [visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteja os recursos utilizando grupos de segurança de rede ou firewall azure na sua Rede Virtual

**Orientação**: Configure o Firewall da sua Conta de Armazenamento limitando o acesso aos clientes de gamas específicas de endereços IP públicos, selecione redes virtuais (VNets) no Azure ou para recursos específicos do Azure. Também pode configurar Private Endpoints para que o tráfego para o serviço de armazenamento da sua empresa viaje exclusivamente por redes privadas.

Nota: As contas de armazenamento clássicas não suportam firewalls e redes virtuais.

- [Como configurar a Firewall de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Como configurar pontos finais privados para armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1.2: Monitorizar e registar configuração e tráfego vnet, subnet e NIC

**Orientação**: O Armazenamento Azure fornece um modelo de segurança em camadas. Pode limitar o acesso à sua conta de armazenamento a pedidos originários de endereços IP especificados, intervalos IP ou de uma lista de subredes numa Rede Virtual Azure (VNet). Pode utilizar o Azure Security Center e seguir recomendações de proteção de rede para ajudar a proteger os recursos da sua rede em Azure. Além disso, ative os registos de fluxo NSG para redes virtuais /subnet configurados para as contas de Armazenamento através da firewall da conta de armazenamento e envie registos para uma Conta de Armazenamento para auditoria de tráfego. 

Note que se tiver pontos finais privados anexados à sua conta de armazenamento, não pode configurar as regras do Network Security Group (NSG) para as subredes. 

- [Configure firewalls de armazenamento Azure e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [Como ativar os registos de fluxo nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Compreender pontos finais privados para armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar proteção avançada contra ameaças para a sua conta de Armazenamento Azure. A proteção avançada contra ameaças para o Armazenamento Azure fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Os alertas integrados do Azure Security Center baseiam-se em atividades para as quais a comunicação de rede foi associada a um endereço IP que foi resolvido com sucesso, se o endereço IP é ou não um endereço IP de risco conhecido (por exemplo, um criptomineiro conhecido) ou um endereço IP que não é reconhecido anteriormente como arriscado. Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. 

- [Como ativar a Proteção Avançada de Ameaças](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

- [Compreender o Centro de Segurança Azure Inteligência Integrada de Ameaças](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de discos e registos de fluxo

**Orientação**: A captura de pacotes do Network Watcher permite-lhe criar sessões de captura para rastrear o tráfego entre a conta de Armazenamento e uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede, tanto reactivamente como proactivamente. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar a comunicação cliente-servidor, e muito mais. Ser capaz de disparar remotamente capturas de pacotes, alivia o fardo de executar uma captura de pacote manualmente numa máquina virtual desejada, o que poupa tempo valioso. 

- [Gerir capturas de pacotes com o Azure Network Watcher usando o portal](https://docs.microsoft.comazure/network-watcher/network-watcher-packet-capture-manage-portal)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implementar sistemas de deteção/deteção de intrusões baseados em rede

**Orientação**: A proteção avançada contra ameaças para o Armazenamento Azure fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no Azure Security Center, e são também enviados por e-mail para administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças. 

- [Configure proteção avançada contra ameaças para armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gerir o tráfego para as suas aplicações web

**Orientação**: Não aplicável; recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos em Redes Virtuais que necessitem de acesso à sua conta de Armazenamento, utilize etiquetas de serviço de rede virtual para a rede Virtual configurada para definir controlos de acesso à rede em Grupos de Segurança de Rede ou Firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, Armazenamento) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. 

Quando o acesso à rede precisa de ser acessível a contas de armazenamento específicas, utilize as políticas de ponto final do serviço da Rede Virtual.

- [Para mais informações sobre a utilização de Etiquetas de Serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Para mais informações sobre as políticas finais do serviço de rede virtual para o Armazenamento Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Serviço

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados à sua Conta de Armazenamento Azure com a Política Azure. Utilize pseudónimos da Política Azure nos espaços de nome "Microsoft.Storage" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração da rede dos recursos da sua conta de Armazenamento. 

Também pode utilizar definições de políticas incorporadas relacionadas com a conta de Armazenamento, tais como: Contas de armazenamento devem usar um ponto final de serviço de rede virtual 

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Amostras de política azure para armazenamento](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage)

- [Amostras de política azure para rede](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Como criar uma Planta Azure](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de Configuração do Tráfego de Documentos

**Orientação**: Etiquetas de utilização para grupos de segurança de rede (NSG) e outros recursos relacionados com a segurança da rede e fluxo de tráfego. Para regras nsg individuais, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede. Utilize qualquer uma das definições políticas do Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e notificá-lo de recursos não marcados existentes. Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou realizar ações em recursos baseados nas suas etiquetas. 

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Como criar uma Rede Virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Como criar um NSG com um Config de Segurança](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize a Política Azure para registar alterações de configuração para recursos de rede. Crie alertas dentro do Monitor Azure que irão desencadear quando ocorrerem alterações aos recursos críticos da rede. 

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como criar alertas no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para mais informações, consulte [Controlo de Segurança: Registo e Monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-resource"></a>2.1: Utilizar recurso de sincronização do tempo aprovado

**Orientação**: Não aplicável; A Microsoft mantém fontes de tempo para as contas de Armazenamento Azure.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a Gestão central de registos de segurança

**Orientação**: Ingerir logs via Azure Monitor para agregar dados de segurança gerados por dispositivos de pontos finais, recursos de rede e outros sistemas de segurança. Dentro do Monitor Azure, utilize log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo, opcionalmente com funcionalidades de segurança como armazenamento imutável e retenção forçada.

- [Como recolher registos e métricas da plataforma com o Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditoria aos Recursos Azure

**Orientação**: A Azure Storage Analytics fornece registos para bolhas, filas e mesas. Pode utilizar o portal Azure para configurar quais os registos registados para a sua conta. 

- [Como configurar a monitorização da sua conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-monitoring-for-a-storage-account)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de registode segurança

**Orientação**: Ao armazenar registos de eventos de segurança na conta de Armazenamento Azure ou no espaço de trabalho do Log Analytics, pode definir a política de retenção de acordo com os requisitos da sua organização. 

- [Como configurar a política de retenção para registos de conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

- [Alterar o período de retenção de dados no Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e Registos de Revisão

**Orientação**: Para rever os registos de Armazenamento Azure, existem as opções habituais, tais como consultas através da oferta de Log Analytics, bem como uma opção única de visualizar diretamente os ficheiros de registo. No Armazenamento Azure, os registos são armazenados em bolhas que http://accountname.blob.core.windows.net/devem ser acedidas diretamente em $logs (A pasta de registo está escondida por padrão, pelo que terá de navegar diretamente. Não exibirá nos comandos da Lista) 

Além disso, ative a proteção avançada de ameaças para a sua conta de Armazenamento Azure. A proteção avançada contra ameaças para o Armazenamento Azure fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no Azure Security Center, e são também enviados por e-mail para administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças. 

- [Registar e rever dados](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored)

- [Como ativar a Proteção Avançada de Ameaças](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Ativar alertas para atividade anómala

**Orientação**: No Centro de Segurança Azure, ative a proteção avançada de ameaças para armazenamento. Ative as Definições de Diagnóstico da conta de Armazenamento e envie registos para um espaço de trabalho de Log Analytics. A bordo do log analytics workspace para o Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar e utilizar os livros de jogadas (soluções automatizadas) para remediar problemas de segurança. 

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Como gerir alertas no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Como alertar sobre os dados de registo de análise de registo](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Registo da análise de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação**: Utilize o Centro de Segurança Azure e permita a proteção contra ameaças para o Armazenamento Azure para detetar uploads de malware para o Armazenamento Azure utilizando análise de reputação hash e acesso suspeito a partir de um nó de saída tor ativo (um proxy anoonizador). 

- [Configure proteção avançada contra ameaças para armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a exploração de consultas dNS

**Orientação**: A solução Azure DNS Analytics (Preview) no Azure Monitor reúne informações sobre a infraestrutura de DNS sobre segurança, desempenho e operações. Atualmente, isto não suporta contas de Armazenamento Azure no entanto pode utilizar solução de registo dns de terceiros. 

- [Recolha informações sobre a sua infraestrutura DNS com a solução de pré-visualização DNS Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar a exploração de registos de auditoria da linha de comando

**Orientação**: Não aplicável; o benchmark destina-se aos recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Controlo de Identidade e Acesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Manter o inventário das Contas Administrativas

**Orientação**: A Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

- [Como obter um papel de diretório em Azure AD com powerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar as palavras-passe por defeito sempre que aplicável

**Orientação**: As contas de Armazenamento Azure nem o Diretório Ativo Azure têm o conceito de senhas por defeito ou em branco. O Azure Storage implementa um modelo de controlo de acesso que suporta o controlo de acesso baseado em funções azure (RBAC), bem como as assinaturas de chave partilhada e de acesso partilhado (SAS). Uma característica da chave partilhada e da autenticação SAS é que nenhuma identidade está associada ao chamador e, portanto, a autorização baseada na permissão principal de segurança não pode ser realizada. 

- [Autorizar o acesso a dados no Armazenamento Do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Compreender os diretores de segurança e o controlo de acesso para a conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar Contas Administrativas Dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso à sua conta de Armazenamento. Utilize a identidade do Azure Security Center Identidade e gestão de acesso para monitorizar o número de contas administrativas. 

Também pode ativar um Acesso Just-In-Time / Just-Enough utilizando papéis privilegiados de gestão de identidade privilegiada seletivas azure AD para serviços da Microsoft e Arm Azure. 

- [Compreender identidade e acesso do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Visão geral privilegiada da gestão da identidade](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilizar o diretório ativo Azure single sign-on (SSO)

**Orientação**: Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Identidade e Gestão de Acesso do Centro de Segurança Azure. 

- [Compreensão de SSO com AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Autorizar o acesso a dados no Armazenamento Do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Autorizar acesso a blobs e filas utilizando o Diretório Ativo Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação multifactor para todos os acessos baseados no Diretório Ativo Azure.

**Orientação**: Enable Azure Ative Directory Multifactor Authentication and follow Azure Security Center Identity and access management recommendations to help protect your Storage account resources. 

- [Como ativar o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Como monitorizar a identidade e o acesso dentro do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Postos de trabalho de acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho de acesso privilegiados) com MFA configurado para iniciar sessão e configurar os recursos da conta de armazenamento. 

- [Saiba mais sobre postos de trabalho de acesso privilegiados](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Como ativar o MFA em Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registo e Alerta sobre Atividade Suspeita a partir de Contas Administrativas

**Orientação**: Envie alertas de deteção de riscos do Centro de Segurança Azure para o Monitor Azure e configure alertas/notificações personalizados utilizando grupos de ação. Ativar a Proteção Avançada de Ameaças para a conta de Armazenamento Azure para gerar alertas para atividades suspeitas. Além disso, utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. 

- [Como configurar a Proteção Avançada de Ameaças para a conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

- [Compreender as deteções de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [Como configurar grupos de ação para alerta e notificação personalizados](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gerir o recurso Azure a partir de apenas locais aprovados

**Orientação**: Utilizar locais nomeados para o Acesso Condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de gamas de endereços IP ou países/regiões. 

- [Como configurar localizações nomeadas em Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Diretório Ativo Azure (Azure AD) como sistema central de autenticação e autorização. A Azure AD fornece um controlo de acesso baseado em funções (RBAC) para um controlo fino sobre o acesso de um cliente aos recursos numa conta de armazenamento.  Utilize credenciais de AD Azure sempre que possível como uma boa prática de segurança, em vez de usar a chave da conta, que pode ser mais facilmente comprometida. Quando o design da sua aplicação necessitar de assinaturas de acesso partilhado para acesso ao armazenamento Blob, utilize credenciais de AD Azure para criar uma delegação de utilizadores com partilha de assinaturas de acesso (SAS) sempre que possível para uma segurança superior.

- [Como criar e configurar uma instância azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Como configurar o acesso aos dados do Azure Blob e da Fila com o RBAC no portal Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Autorizar o acesso a dados no Armazenamento Do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Conceder acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever regularmente e conciliar o acesso ao utilizador

**Orientação**: Reveja os registos do Diretório Ativo Azure para ajudar a descobrir contas velhas que podem incluir as que têm funções administrativas de conta de armazenamento. Além disso, utilize as Análises de Acesso à Identidade do Azure para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais que possam ser usadas para aceder aos recursos da conta de Armazenamento e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.  

Também pode utilizar a assinatura de acesso partilhado (SAS) para fornecer acesso seguro aos recursos delegados na sua conta de armazenamento sem comprometer a segurança dos seus dados. Pode controlar os recursos a que o cliente pode aceder, quais as permissões que tem sobre esses recursos, e quanto tempo o SAS é válido, entre outros parâmetros.

Além disso, consulte o acesso anónimo a recipientes e bolhas. Por predefinição, um contentor e quaisquer blobs dentro do mesmo podem apenas ser acedidos por um utilizador ao qual foram dadas as permissões adequadas. Pode utilizar o Monitor Azure para alertar sobre o acesso anónimo a contas de Armazenamento utilizando condição de autenticação anónima.

Uma forma eficaz de reduzir o risco de acesso insuspeito à conta do utilizador é limitar a duração de acesso que concede aos utilizadores. Os URIs SAS limitados no tempo são uma forma eficaz de caducar automaticamente o acesso do utilizador a uma conta de Armazenamento. Além disso, a rotação das Chaves da Conta de Armazenamento numa base frequente é uma forma de garantir que o acesso inesperado através das chaves da Conta de Armazenamento seja de duração limitada.

- [Compreender relatórios da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Como visualizar e alterar o acesso ao nível da conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Conceder acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

- [Gerir o acesso de leitura anónimo a contentores e blobs](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)

- [Monitorizar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

- [Gerir as chaves de acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorizar tentativas de acesso a contas desativadas

**Orientação**: Utilize o Storage Analytics para registar informações detalhadas sobre pedidos bem sucedidos e falhados a um serviço de armazenamento. Todos os registos são armazenados em blocos num recipiente chamado $logs, que é automaticamente criado quando o Storage Analytics está ativado para uma conta de armazenamento.

Crie Definições de Diagnóstico para contas de utilizadores do Diretório Ativo Azure, enviando os registos de auditoria e os registos de log-in para um espaço de trabalho de Log Analytics. Pode configurar os alertas desejados dentro do espaço de trabalho de Log Analytics. Para monitorizar falhas de autenticação contra contas de armazenamento azure, pode criar alertas para notificá-lo quando determinados limiares tiverem sido atingidos para métricas de recursos de armazenamento. Além disso, utilize o Monitor Azure para alertar sobre o acesso anónimo às contas de Armazenamento utilizando condição de autenticação anónima.

- [Registo da análise de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Como integrar registos de atividade do Azure no Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Como configurar alertas de métricas para contas de armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize as funcionalidades de risco e proteção de identidade do Azure Ative Directory para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com os recursos da sua conta de Armazenamento. Deve permitir que as respostas automatizadas através do Azure Sentinel implementem as respostas de segurança da sua organização. 

- [Como ver os sign-ins de risco da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e ativar políticas de risco de proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Como embarcar no Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante os cenários de suporte

**Orientação**: Em cenários de suporte em que a Microsoft precisa de aceder aos dados dos clientes, o Customer Lockbox (Conta 'Preview for Storage' fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. A Microsoft não necessitará, nem solicitará o acesso aos segredos da sua organização armazenados na conta de Armazenamento.

- [Compreender o lockbox do cliente](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte [Controlo de Segurança: Proteção de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da conta de armazenamento que armazenam ou processam informações sensíveis. 

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou processamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando assinaturas separadas, grupos de gestão e contas de armazenamento para domínios de segurança individuais, tais como ambiente, sensibilidade a dados.  Pode restringir a sua Conta de Armazenamento para controlar o nível de acesso às suas contas de armazenamento que as suas aplicações e ambientes empresariais exigem, com base no tipo e subconjunto de redes utilizadas. Quando as regras de rede são configuradas, apenas as aplicações que solicitam dados sobre o conjunto especificado de redes podem aceder a uma conta de armazenamento. Pode controlar o acesso ao Azure Storage via Azure AD RBAC. Também pode configurar Private Endpoints para melhorar a segurança, uma vez que o tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública. 

- [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Configure firewalls de armazenamento Azure e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [Pontos finais do serviço de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorizar e bloquear transferência não autorizada de informações sensíveis.

**Orientação**: Para os recursos da conta de armazenamento armazenando ou processando informações sensíveis, marque os recursos como sensíveis utilizando tags. Para reduzir o risco de perda de dados através da exfiltração, restrinja o tráfego de rede de saída para contas de Armazenamento Azure utilizando o Firewall Azure. 

Além disso, utilize políticas de ponto final de serviço de rede virtual para filtrar o tráfego de rede virtual para contas de Armazenamento Azure sobre o ponto final do serviço, e permitir a exfiltração de dados apenas contas específicas de Armazenamento Azure.

- [Configure firewalls de armazenamento Azure e redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

- [Políticas de endpoint de serviço de rede virtual para armazenamento Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-storage-portal)

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Pode impor a utilização do HTTPS, permitindo a transferência segura necessária para a conta de armazenamento. As ligações através de HTTP serão recusadas assim que esta opção estiver ativada. Além disso, utilize o Azure Security Center e a Azure Policy para impor a transferência segura para a sua conta de armazenamento.

- [Como exigir transferência segura no Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)

- [Políticas de segurança azure monitorizadas pelo Centro de Segurança](https://docs.microsoft.com/azure/security-center/security-center-policy-definitions)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Partilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação de dados ainda não estão disponíveis para a conta de Armazenamento Azure e recursos conexos. Implementar solução de terceiros, se necessário para efeitos de conformidade. 

- [Compreender a proteção de dados dos clientes em Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos garantidos através do controlo de acesso baseado em funções (RBAC). O Azure Storage define um conjunto de funções RBAC incorporadas que englobam conjuntos comuns de permissões usadas para aceder a dados blob ou fila. 

- [Como atribuir funções RBAC para conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)

- [Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Como configurar o acesso aos dados do Azure Blob e da Fila com o RBAC no portal Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Como criar e configurar uma instância AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Autorizar o acesso a dados no Armazenamento Do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a Prevenção da Perda de Dados baseada no hospedeiro para impor o controlo de acesso

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação**: A encriptação do Armazenamento Azure está ativada para todas as contas de armazenamento e não pode ser desativada. O Azure Storage encripta automaticamente os seus dados quando é persistido na nuvem. Quando lê os dados a partir do Armazenamento do Azure, estes são desencriptados pelo Armazenamento do Azure antes de serem devolvidos. A encriptação azure Storage permite-lhe proteger os seus dados em repouso sem ter de modificar código ou adicionar código a quaisquer aplicações. 

- [Compreender a encriptação do armazenamento azure em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividade seleções do Azure para criar alertas para quando as alterações ocorrerem nos recursos da conta de armazenamento. Também pode permitir que a exploração de armazenamento azure rastreie como cada pedido feito contra o Armazenamento Azure foi autorizado. Os registos indicam se um pedido foi feito de forma anónima, utilizando um token OAuth 2.0, utilizando a Chave Partilhada, ou utilizando uma assinatura de acesso partilhado (SAS). Além disso, utilize o Monitor Azure para alertar sobre o acesso anónimo às contas de Armazenamento utilizando condição de autenticação anónima.

- [Como criar alertas para eventos de Log de Atividade do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Registo da análise de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Como configurar alertas de métricas para contas de armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização automática de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center para auditar e monitorizar continuamente a configuração das suas contas de armazenamento. 

- [Recomendações de segurança - um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitorização**do Azure Security Center : Não aplicável

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

**Orientação**: Não aplicável; A Microsoft realiza a gestão de vulnerabilidades nos sistemas subjacentes que suportam as contas de Armazenamento.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a remediação de vulnerabilidades descobertas.

**Orientação**: Utilize as classificações de risco padrão (Pontuação Segura) fornecidas pelo Azure Security Center. 

- [Compreender a pontuação segura do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Utilizar a Descoberta de Ativos Azure

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar e descobrir todos os recursos (incluindo contas de armazenamento) dentro da sua subscrição. Certifique-se de que tem permissões adequadas (ler) no seu inquilino e que é capaz de enumerar todas as subscrições do Azure, bem como recursos dentro das suas subscrições. 

- [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Como visualizar as suas Assinaturas Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos da conta de armazenamento, dando metadados para logicamente organizá-los numa taxonomia. 

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, os grupos de gestão e as assinaturas separadas, se for caso disso, para organizar e rastrear contas de armazenamento e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição atempadamente. 

Além disso, utilize proteção avançada de ameaças para armazenamento azure para detetar recursos azure não autorizados. 

- [Como criar subscrições adicionais do Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Como criar e usar Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Configure proteção avançada contra ameaças para armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos e títulos de software aprovados do Azure.

**Orientação**: Terá de criar um inventário dos recursos Azure aprovados de acordo com as suas necessidades organizacionais. 


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos azure não aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas: 

 - Tipos de recursos não permitidos 
 - Tipos de recursos permitidos 

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s subscrição)." Isto pode ajudar em ambientes baseados em alta segurança, como aqueles com contas de Armazenamento. 

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como criar consultas com o Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos Recursos Compute

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: O cliente pode impedir a criação de recursos ou a utilização com a Política Azure, conforme exigido pelas políticas da empresa do cliente. 

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilize apenas pedidos aprovados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas os serviços Azure aprovados

**Orientação**: Utilize a política azure para colocar restrições ao tipo de recursos que podem ser criados na subscrição ou s do cliente utilizando as seguintes definições políticas incorporadas: 

- Tipos de recursos não permitidos 
- Tipos de recursos permitidos 

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagir com o Gestor de Recursos Azure através de Scripts

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure configurando o "Block access" para a App "Microsoft Azure Management". Isto pode impedir a criação e alterações de recursos num ambiente de alta segurança, como os que têm contas de Armazenamento. 

- [Como configurar o Acesso Condicional ao bloqueio de acesso à ARM](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro dos recursos computacionais

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Aplicações de alto risco fisicamente ou logicamente segregadas

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Serviço de Aplicações Azure ou recursos de computação.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para mais informações, consulte [Controlo de Segurança: Configuração segura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nome "Microsoft.Storage" para criar políticas personalizadas para auditar ou impor a configuração das instâncias da sua conta de Armazenamento. Também pode utilizar definições de política azure incorporadas para a conta de Armazenamento Azure, tais como: 

Audit unrestricted network access to storage accounts Deploy Advanced Threat Protection on Storage Accounts Storage accounts should be migrated to new Azure Resource Manager resources Secure transfer to storage accounts should be enabled 

Utilize recomendações do Azure Security Center como base de configuração segura para as suas contas de Armazenamento. 

- [Como ver os Aliases políticos do Azure disponíveis](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Estabelecer configurações seguras para o seu sistema operativo

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Manter configurações seguras para todos os recursos azure

**Orientação:** Utilize a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os recursos da sua conta de Armazenamento. 

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Compreender efeitos políticos do Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Manter configurações seguras para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos azure

**Orientação**: Utilize o Azure Repos para armazenar e gerir de forma segura o seu código, como políticas personalizadas do Azure, modelos do Gestor de Recursos Azure, scripts de Configuração do Estado Desejados, etc. Para aceder aos recursos que gere no Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com o Azure DevOps, ou Diretório Ativo se integrado seletiva com a TFS.

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação**: Alavancar a Política Azure para alertar, auditar e impor configurações do sistema para a conta de Armazenamento. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas. 

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços do Azure

**Orientação**: Leverage Azure Security Center para realizar digitalizações de base para os recursos da sua conta de Armazenamento Azure. 

- [Como remediar recomendações no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gerir de forma segura os segredos do Azure

**Orientação**: O Armazenamento Azure encripta automaticamente os seus dados quando é persistido na nuvem. Pode utilizar chaves geridas pela Microsoft para a encriptação da conta de armazenamento, ou pode gerir a encriptação com as suas próprias chaves. Se estiver a utilizar as chaves fornecidas pelo cliente, pode aproveitar o Cofre de Chaves Azure para armazenar as chaves de forma segura. 

Adicionalmente, rode as Chaves da Conta de Armazenamento numa base frequente para limitar o impacto da perda ou divulgação das chaves da Conta de Armazenamento.

- [Encriptação azure storage para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Gerir as chaves de acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gerir de forma segura e automática as identidades

**Orientação**: Autorizar o acesso a blobs e filas dentro das Contas de Armazenamento Azure com Diretório Ativo Azure e Identidades Geridas. A autenticação azure Blob e de armazenamento de fila Azure Ative Directory (Azure AD) com identidades geridas para os recursos Azure. As identidades geridas para os recursos do Azure podem autorizar o acesso a dados de blob e fila utilizando credenciais Azure AD de aplicações em execução em máquinas virtuais Azure (VMs), aplicações de função, conjuntos de escala de máquinas virtuais e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação da AD Azure, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem. 

- [Como conceder acesso à bolha Azure e dados de fila usando uma identidade gerida](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilize software anti-malware gerido centralmente

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem carregados para recursos não computacionais do Azure

**Orientação**: Utilize a proteção contra ameaças para o Armazenamento Azure para detetar uploads de malware para o Armazenamento De Tee utilizando análise de reputação hash e acesso suspeito a partir de um nó de saída tor ativo (um proxy anoonizador). 

Também pode pré-digitalizar qualquer conteúdo para malware antes de enviar para recursos não computacionais do Azure, tais como App Service, Data Lake Storage, Blob Storage, etc, para satisfazer os requisitos das suas organizações.

- [Configure proteção avançada contra ameaças para armazenamento azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software e assinaturas anti-malware são atualizados

**Orientação**: Não aplicável; esta recomendação destina-se aos recursos informáticos. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para mais informações, consulte [Controlo de Segurança: Recuperação de Dados](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: Os dados na sua conta de armazenamento Microsoft Azure são sempre replicados automaticamente para garantir durabilidade e elevada disponibilidade. O Azure Storage copia os seus dados para que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas na rede ou falhas de energia e desastres naturais maciços. Pode optar por replicar os seus dados dentro do mesmo centro de dados, através de centros de dados zonais dentro da mesma região, ou em regiões geograficamente separadas. 

Também pode permitir que a automatização Azure tire fotografias regulares das bolhas.

- [Compreensão do despedimento de armazenamento azure e acordos de nível de serviço](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [Criar uma imagem de uma bolha](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Visão geral da Automação Azure](https://docs.microsoft.com/azure/automation/automation-intro)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e backup de quaisquer chaves geridas pelo cliente

**Orientação**: Para fazer backup de dados dos serviços suportados pela conta de armazenamento, existem múltiplos métodos disponíveis, incluindo a utilização de ferramentas de azcópia ou de terceiros. O armazenamento imutável para armazenamento De Blob Azure permite que os utilizadores armazenem objetos de dados críticos do negócio num estado WORM (Write Once, Read Many). Este estado torna os dados não apagamentos e não modificáveis para um intervalo especificado pelo utilizador.

- [Introdução ao AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

- [Definir e gerir políticas de imutabilidade para armazenamento blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

As chaves geridas pelo cliente /fornecidos podem ser apoiadas no Cofre de Chaves Azure utilizando o Azure CLI ou o PowerShell. 

- [Como apoiar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Realizar periodicamente a restauração de dados dos seus Certificados de Cofre chave, chaves, contas de armazenamento geridas e segredos, com os seguintes comandos PowerShell: 

Restaurar-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Como restaurar os certificados do cofre de chaves](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Como restaurar as chaves do cofre chave](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Como restaurar as contas de armazenamento geridas pelo cofre chave](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Como restaurar os segredos do cofre chave](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [AzCopy é um utilitário de linha de comando que pode usar para copiar bolhas, ficheiros e dados de tabela para ou a partir de uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

Nota: Se pretender copiar dados de e para o seu serviço de armazenamento De Mesa Azure, instale a versão AzCopy 7.3.


**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Para ativar as chaves geridas pelo cliente numa conta de armazenamento, deve utilizar um Cofre de Chave Azure para armazenar as suas chaves. Deve ativar as propriedades Soft Delete e Não purgar as propriedades no cofre da chave. A funcionalidade Soft Delete da Key Vault permite a recuperação de cofres apagados e objetos de cofre, tais como chaves, segredos e certificados. Se apoiar os dados da conta de armazenamento para as bolhas de armazenamento do Azure, ative a eliminação suave para guardar e recuperar os seus dados quando as bolhas ou as imagens blob ssão eliminada. Deve tratar as suas cópias de segurança como dados sensíveis e aplicar os controlos de acesso e proteção de dados relevantes como parte desta linha de base. Além disso, para uma melhor proteção, pode armazenar objetos de dados críticos do negócio num estado WORM (Write Once, Read Many).

- [Como usar o Soft Delete do Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Armazenar dados de blob críticos de negócios com armazenamento imutável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte [Controlo de Segurança: Resposta](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)a Incidentes .*

### <a name="101-create-incident-response-guide"></a>10.1: Criar guia de resposta a incidentes

**Orientação**: Eleve um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes desde a deteção até à revisão pós-incidente.

- [Orientação sobre a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Criar procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (para ex. produção, não prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis. É da sua responsabilidade priorizar a remediação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Use tags para organizar os seus recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança de teste

**Orientação**: Realizar exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Publicação do NIST - Guia para Programas de Teste, Formação e Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorização do Centro de Segurança Azure:** Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorpore alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exportar os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade De Exportação Contínua para ajudar a identificar riscos para os recursos do Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança Azure para transmitir os alertas para o Azure Sentinel.

- [Como configurar a exportação contínua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Como transmitir alertas para o Sentinela Azure](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a funcionalidade workflow Automation no Azure Security Center para ativar automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a Automatização do Fluxo de Trabalho e aplicações lógicas](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorização**do Azure Security Center : Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para mais informações, consulte [Controlo de Segurança: Testes de Penetração e Exercícios de Equipa Vermelha](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure

**Orientação**: Siga as Regras de Envolvimento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Utilize a estratégia e execução da Microsoft de Red Teaming e testes de penetração no site ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft.

- [Regras de teste de penetração de envolvimento](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização**do Azure Security Center : Não aplicável

**Responsabilidade**: Partilhado

## <a name="next-steps"></a>Passos seguintes

- Ver o [Benchmark de Segurança Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança do Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
