---
title: Linha de base de segurança Azure para armazenamento Azure
description: Linha de base de segurança Azure para armazenamento Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5fda2038475b9fdd20c1bb9f641597145b136883
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790547"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Linha de base de segurança Azure para armazenamento Azure

A Linha de Base de Segurança Azure para armazenamento Azure contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark,](../../security/benchmarks/overview.md)que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure com a nossa orientação de boas práticas.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, consulte [Controlo de Segurança: Segurança da Rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteger recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual

**Orientação** : Configure a firewall da sua conta de armazenamento, restringindo o acesso aos clientes a partir de intervalos específicos de endereços IP públicos, selecione redes virtuais (VNets) no Azure ou recursos Azure específicos. Também pode configurar private Endpoints para que o tráfego para o serviço de armazenamento da sua empresa viaje exclusivamente por redes privadas.

Nota: As contas de armazenamento clássicas não suportam firewalls e redes virtuais.

- [Como configurar a Firewall de Armazenamento Azure](./storage-network-security.md#change-the-default-network-access-rule)

- [Como configurar pontos finais privados para armazenamento Azure](./storage-private-endpoints.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1.2: Monitor e log Vnet, Subnet e configuração e tráfego NIC

**Orientação** : O Azure Storage fornece um modelo de segurança em camadas. Pode limitar o acesso à sua conta de armazenamento a pedidos originários de endereços IP especificados, intervalos IP ou de uma lista de sub-redes numa Rede Virtual Azure (VNet). Pode utilizar o Azure Security Center e seguir recomendações de proteção de rede para ajudar a proteger os recursos da sua rede em Azure. Além disso, ative registos de fluxo NSG para redes virtuais/sub-rede configurados para as contas de Armazenamento através da firewall da conta de armazenamento e envie registos numa Conta de Armazenamento para auditoria de tráfego. 

Note que se tiver Pontos Finais Privados anexados à sua conta de armazenamento, não pode configurar as regras do Grupo de Segurança da Rede (NSG) para sub-redes. 

- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](./storage-network-security.md)

- [Como ativar registos de fluxo NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../../security-center/security-center-network-recommendations.md)

- [Compreender pontos finais privados para armazenamento Azure](./storage-private-endpoints.md#known-issues)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação** : Não aplicável; a recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação** : Ative a Proteção avançada de ameaças para a sua conta de Armazenamento Azure. A proteção avançada de ameaças para o Azure Storage fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Os alertas integrados do Azure Security Center baseiam-se em atividades para as quais a comunicação de rede foi associada a um endereço IP que foi resolvido com sucesso, quer o endereço IP seja ou não um endereço IP de risco conhecido (por exemplo, um criptominador conhecido) ou um endereço IP que não seja reconhecido anteriormente como arriscado. Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. 

- [Como permitir a Proteção Avançada de Ameaças](./azure-defender-storage-configure.md?tabs=azure-portal)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../../security-center/azure-defender.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Pacotes de rede de registos e registos de fluxo

**Orientação** : A captura de pacotes do Observador de Rede permite-lhe criar sessões de captura para rastrear o tráfego entre a conta de Armazenamento e uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede, tanto reativas como proativamente. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar comunicações de servidores de clientes, e muito mais. Ser capaz de ativar remotamente capturas de pacotes, alivia o fardo de executar uma captura de pacote manualmente numa máquina virtual desejada, o que poupa tempo valioso. 

- [Gerir capturas de pacotes com o Azure Network Watcher usando o portal](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede

**Orientação** : A proteção avançada de ameaças para o Azure Storage fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no Azure Security Center, e são também enviados por e-mail para administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças. 

- [Configure a proteção avançada de ameaças para o armazenamento de Azure](./azure-defender-storage-configure.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Gerir o tráfego para as suas aplicações web

**Orientação** : Não aplicável; a recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação** : Para recursos em Redes Virtuais que necessitem de acesso à sua conta de Armazenamento, utilize Tags de Serviço de Rede Virtual para a rede Virtual configurada para definir controlos de acesso à rede em Grupos de Segurança de Rede ou Firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, Armazenamento) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. 

Quando o acesso à rede precisar de ser examinado para contas de armazenamento específicas, utilize políticas de ponto final de serviço de rede virtual.

- [Para mais informações sobre a utilização de Tags de Serviço](../../virtual-network/service-tags-overview.md)

- [Para mais informações sobre as políticas de ponto final do serviço de rede virtual para o Armazenamento Azure](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Serviço

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação** : Defina e implemente configurações de segurança padrão para os recursos de rede associados à sua Conta de Armazenamento Azure com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.Storage" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos da sua conta de armazenamento. 

Também pode utilizar definições de política incorporadas relacionadas com a conta de Armazenamento, tais como: As Contas de Armazenamento devem utilizar um ponto final de serviço de rede virtual 

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para armazenamento](../../governance/policy/samples/built-in-policies.md#storage)

- [Amostras de política Azure para rede](../../governance/policy/samples/built-in-policies.md#network)

- [Como criar uma Planta Azure](../../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego de documentos

**Orientação** : Utilize etiquetas para grupos de segurança de rede (NSG) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede. Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados. Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas. 

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizar ferramentas automatizadas para monitorizar configurações de recursos de rede e detetar alterações

**Orientação** : Utilize a Política Azure para registar alterações na configuração dos recursos de rede. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede. 

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Como criar alertas no Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-resource"></a>2.1: Utilizar recurso de sincronização de tempo aprovado

**Orientação** : Não aplicável; A Microsoft mantém fontes de tempo para contas de Armazenamento Azure.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a Gestão central de registos de segurança

**Orientação** : Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de pontos finais, recursos de rede e outros sistemas de segurança. Dentro do Azure Monitor, utilize o Log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo, opcionalmente com funcionalidades de segurança, tais como armazenamento imutável e retenção forçada.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos azure

**Orientação** : A Azure Storage Analytics fornece registos para bolhas, filas e mesas. Pode utilizar o portal Azure para configurar quais os registos registados para a sua conta. 

- [Como configurar a monitorização para a sua conta de Armazenamento Azure](./storage-monitor-storage-account.md#configure-monitoring-for-a-storage-account)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registo de segurança

**Orientação** : Ao armazenar registos de eventos de segurança na conta de armazenamento Azure ou no espaço de trabalho Log Analytics, poderá definir a política de retenção de acordo com os requisitos da sua organização. 

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](./storage-monitor-storage-account.md#configure-logging)

- [Alterar o período de retenção de dados em Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação** : Para rever os registos de Armazenamento Azure, existem as opções habituais, como consultas através da oferta Log Analytics, bem como uma opção única de visualização direta dos ficheiros de registo. No Azure Storage, os registos são armazenados em bolhas que devem ser acedidas diretamente http://accountname.blob.core.windows.net/ a $logs (a pasta de registo está escondida por predefinição, pelo que terá de navegar diretamente. Não será exibido nos comandos lista) 

Além disso, Ative a Proteção Avançada de Ameaças para a sua conta de Armazenamento Azure. A proteção avançada de ameaças para o Azure Storage fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no Azure Security Center, e são também enviados por e-mail para administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças. 

- [Dados de registo e revisão](./storage-analytics-logging.md#how-logs-are-stored)

- [Como permitir a Proteção Avançada de Ameaças](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas

**Orientação** : No Centro de Segurança Azure, ative a conta de proteção de ameaças avançadas para armazenamento. Ativar as Definições de Diagnóstico da conta de Armazenamento e enviar registos para um espaço de trabalho de "Log Analytics". A bordo do seu Log Analytics Workspace para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança. 

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../../azure-monitor/learn/tutorial-response.md)

- [Azure Storage analytics logging](./storage-analytics-logging.md) (Registo de análise do Armazenamento do Azure)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a exploração anti-malware

**Orientação** : Utilize o Centro de Segurança Azure e permita a proteção contra ameaças para o Azure Storage para detetar uploads de malware para o Azure Storage utilizando análises de reputação de haxixe e acesso suspeito a partir de um nó de saída tor ativo (um proxy anonimizador). 

- [Configure a proteção avançada de ameaças para o armazenamento de Azure](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Permitir a colocação de registo de consultas de DNS

**Orientação** : A solução Azure DNS Analytics (Preview) no Azure Monitor reúne informações sobre infraestruturas de DNS sobre segurança, desempenho e operações. Atualmente isto não suporta contas Azure Storage no entanto pode utilizar a solução de registo de dns de terceiros. 

- [Recolha informações sobre a sua infraestrutura DNS com a solução de pré-visualização do DNS Analytics](../../azure-monitor/insights/dns-analytics.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Ativar o registo de auditoria da linha de comando

**Orientação** : Não aplicável; referência destina-se a recursos de computação.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte [Controlo de Segurança: Identidade e Controlo de Acesso.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Manter o Inventário das Contas Administrativas

**Orientação** : A Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos. 

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação** : As contas de Armazenamento Azure nem o Azure Ative Directory têm o conceito de senhas padrão ou em branco. O Azure Storage implementa um modelo de controlo de acesso que suporta o controlo de acesso baseado em funções Azure (Azure RBAC), bem como a Chave Partilhada e Assinaturas de Acesso Partilhado (SAS). Uma característica da autenticação da Chave Partilhada e da SAS é que nenhuma identidade está associada ao chamador e, portanto, não pode ser realizada uma autorização baseada na autorização principal de segurança. 

- [Autorizar o acesso aos dados no Azure Storage](./storage-auth.md)

- [Compreender os princípios de segurança e o controlo de acessos para a conta de armazenamento Azure](./storage-introduction.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação** : Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso à sua conta de Armazenamento. Utilize a identidade do Centro de Segurança Azure e a gestão de acessos para monitorizar o número de contas administrativas. 

Também pode ativar um Acesso Just-In-Time / Just-Enough-Enough utilizando funções privilegiadas de gestão de identidade privilegiada Azure AD para serviços microsoft e Azure ARM. 

- [Compreender a identidade e o acesso do Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

- [Visão geral da gestão de identidade privilegiada](../../active-directory/privileged-identity-management/index.yml)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilizar o Azure Ative Directory Single Sign-On (SSO)

**Orientação** : Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure. 

- [Compreender SSO com Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [Autorizar o acesso aos dados no Azure Storage](./storage-auth.md)

- [Autorizar o acesso a bolhas e filas utilizando o Azure Ative Directory](./storage-auth-aad.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multifactor para todos os acessos baseados no Diretório Ativo Azure.

**Orientação** : Ativar a autenticação multifactor do Diretório Ativo Azure e seguir as recomendações de gestão do Centro de Segurança Azure e para ajudar a proteger os recursos da sua conta de armazenamento. 

- [Como permitir o MFA em Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação** : Utilize PAWs (estações de trabalho privilegiadas de acesso) com MFA configurados para iniciar sessão e configurar recursos de conta de armazenamento. 

- [Saiba mais sobre estações de trabalho de acesso privilegiado](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Como permitir o MFA em Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Registo e alerta sobre atividades suspeitas a partir de contas administrativas

**Orientação** : Enviar alertas de deteção de risco do Centro de Segurança Azure para o Monitor Azure e configurar alertas/notificações personalizados utilizando grupos de ação. Ativar a proteção avançada de ameaças para a conta de armazenamento Azure para gerar alertas para atividades suspeitas. Além disso, utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. 

- [Como configurar a proteção avançada de ameaças para a conta de armazenamento Azure](./azure-defender-storage-configure.md)

- [Compreender deteções de risco Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar grupos de ação para alerta e notificação personalizados](../../azure-monitor/platform/action-groups.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Gerir o Recurso Azure a partir de apenas locais aprovados

**Orientação** : Utilize locais nomeados para acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar localizações nomeadas em Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação** : Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. O Azure fornece o controlo de acesso baseado em funções (Azure RBAC) para controlo de grãos finos sobre o acesso de um cliente aos recursos numa conta de armazenamento.  Use credenciais AZure AD quando possível como uma melhor prática de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design da sua aplicação requer assinaturas de acesso partilhadas para acesso ao armazenamento blob, use credenciais Azure AD para criar uma delegação de utilizador assinaturas de acesso compartilhadas (SAS) quando possível para uma segurança superior.

- [Como criar e configurar uma instância AD Azure](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão](./authorization-resource-provider.md)

- [Como configurar o acesso aos dados da Azure Blob e da Fila com o Azure RBAC no portal Azure](./storage-auth-aad-rbac-portal.md)

- [Autorizar o acesso aos dados no Azure Storage](./storage-auth.md)

- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](./storage-sas-overview.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rever e conciliar regularmente o acesso ao utilizador

**Orientação** : Reveja os registos do Azure Ative Directory para ajudar a descobrir contas velhas que podem incluir as que têm funções administrativas de conta de Armazenamento. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais que possam ser usadas para aceder a recursos de conta de Armazenamento e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado.  

Também pode utilizar a assinatura de acesso partilhado (SAS) para fornecer acesso delegado seguro aos recursos na sua conta de armazenamento sem comprometer a segurança dos seus dados. Pode controlar os recursos a que o cliente pode aceder, quais as permissões que têm sobre esses recursos, e quanto tempo o SAS é válido, entre outros parâmetros.

Além disso, reveja o acesso anónimo a contentores e bolhas. Por predefinição, um contentor e quaisquer blobs dentro do mesmo podem apenas ser acedidos por um utilizador ao qual foram dadas as permissões adequadas. Pode utilizar o Azure Monitor para alertar sobre o acesso anónimo às contas de Armazenamento utilizando a condição de autenticação anónima.

Uma forma eficaz de reduzir o risco de acesso insuspeito à conta de utilizador é limitar a duração de acesso que concede aos utilizadores. Os SAS URIs limitados no tempo são uma forma eficaz de expirar automaticamente o acesso do utilizador a uma conta de Armazenamento. Além disso, a rotação das chaves de conta de armazenamento numa base frequente é uma forma de garantir que o acesso inesperado através das chaves da Conta de Armazenamento é de duração limitada.

- [Compreenda a Azure Ad Reporting](../../active-directory/reports-monitoring/index.yml)

- [Como visualizar e alterar o acesso ao nível da conta de armazenamento Azure](./storage-auth-aad-rbac-portal.md)

- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](./storage-sas-overview.md)

- [Gerir o acesso de leitura anónimo a contentores e blobs](../blobs/anonymous-read-access-configure.md)

- [Monitorizar uma conta de armazenamento no portal do Azure](./storage-monitor-storage-account.md)

- [Gerir chaves de acesso à conta de armazenamento](./storage-account-keys-manage.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Monitorização tentativas de acesso a contas desativadas

**Orientação** : Utilize o Storage Analytics para registar informações detalhadas sobre pedidos bem sucedidos e falhados a um serviço de armazenamento. Todos os registos são armazenados em blocos num recipiente chamado $logs, que é automaticamente criado quando o Storage Analytics está ativado para uma conta de armazenamento.

Criar Definições de Diagnóstico para contas de utilizadores do Azure Ative Directory, enviar os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace. Para monitorizar falhas de autenticação contra contas de armazenamento Azure, pode criar alertas para o notificar quando determinados limiares tiverem sido atingidos para métricas de recursos de armazenamento. Além disso, utilize o Azure Monitor para alertar sobre o acesso anónimo às contas de Armazenamento utilizando a condição de autenticação anónima.

- [Azure Storage analytics logging](./storage-analytics-logging.md) (Registo de análise do Armazenamento do Azure)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como configurar alertas de métricas para contas de armazenamento Azure](./storage-monitor-storage-account.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação** : Utilize as funcionalidades de Proteção de Risco e Identidade do Azure Ative Para configurar respostas automatizadas para detetar ações suspeitas relacionadas com os recursos da sua conta de armazenamento. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização. 

- [Como ver a Azure AD a entrar em risco](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação** : Em cenários de suporte em que a Microsoft precisa de aceder aos dados dos clientes, o Customer Lockbox (Preview for Storage account) fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. A Microsoft não exigirá, nem solicitará o acesso aos segredos da sua organização armazenados na conta de Armazenamento.

- [Compreender o bloqueio do cliente](../../security/fundamentals/customer-lockbox-overview.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Proteção de Dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação** : Utilize etiquetas para ajudar a rastrear os recursos da conta de armazenamento que armazenam ou processam informações sensíveis. 

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação** : Implementar o isolamento utilizando subscrições separadas, grupos de gestão e contas de armazenamento para domínios de segurança individuais, tais como ambiente, sensibilidade aos dados.  Pode restringir a sua Conta de Armazenamento para controlar o nível de acesso às suas contas de armazenamento que as suas aplicações e ambientes empresariais exigem, com base no tipo e subconjunto de redes utilizadas. Quando as regras de rede são configuradas, apenas as aplicações que solicitam dados sobre o conjunto especificado de redes podem aceder a uma conta de armazenamento. Você pode controlar o acesso ao Azure Storage via Azure RBAC. Também pode configurar private endpoints para melhorar a segurança à medida que o tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública. 

- [Como criar subscrições adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](./storage-network-security.md)

- [Pontos finais de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorize e bloqueie a transferência não autorizada de informações sensíveis.

**Orientação** : Para armazenar ou processar informações sensíveis, marque os recursos como sensíveis através de Tags. Para reduzir o risco de perda de dados através da exfiltração, restringir o tráfego de rede de saída para contas de Armazenamento Azure usando a Azure Firewall. 

Além disso, utilize políticas de ponto final do serviço de rede virtual para filtrar o tráfego de rede virtual de saída para contas de Armazenamento Azure sobre o ponto final do serviço, e permitir a exfiltração de dados apenas para contas específicas de Armazenamento Azure.

- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Políticas do ponto final de serviço de rede virtual do Armazenamento do Microsoft Azure](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Compreender a proteção de dados do cliente em Azure](../../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação** : Pode impor a utilização do HTTPS, permitindo a transferência segura necessária para a conta de armazenamento. As ligações através de HTTP serão recusadas assim que esta opção estiver ativada. Além disso, utilize o Azure Security Center e a Azure Policy para impor transferência segura para a sua conta de armazenamento.

- [Como exigir transferência segura no Azure Storage](./storage-require-secure-transfer.md)

- [Políticas de segurança Azure monitorizadas pelo Centro de Segurança](../../security-center/policy-reference.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação** : As funcionalidades de identificação de dados ainda não estão disponíveis para a conta de armazenamento Azure e recursos conexos. Implementar solução de terceiros, se necessário para efeitos de conformidade. 

- [Compreender a proteção de dados do cliente em Azure](../../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação** : O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através do controlo de acesso baseado em funções da Azure (Azure RBAC). O Azure Storage define um conjunto de funções incorporadas do Azure que englobam conjuntos comuns de permissões usadas para aceder a dados de bolhas ou filas. 

- [Como atribuir funções da Azure para a conta de armazenamento Azure](./storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)

- [Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão](./authorization-resource-provider.md)

- [Como configurar o acesso aos dados da Azure Blob e da Fila com o Azure RBAC no portal Azure](./storage-auth-aad-rbac-portal.md)

- [Como criar e configurar um caso AAD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizar o acesso aos dados no Azure Storage](./storage-auth.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo de acesso

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Encriptar informações sensíveis em repouso

**Orientação** : A encriptação do Armazenamento Azure está ativada para todas as contas de armazenamento e não pode ser desativada. O Azure Storage encripta automaticamente os seus dados quando estes são persistidos na nuvem. Quando lê os dados a partir do Armazenamento do Azure, estes são desencriptados pelo Armazenamento do Azure antes de serem devolvidos. A encriptação de Armazenamento Azure permite-lhe proteger os seus dados em repouso sem ter de modificar código ou adicionar código a quaisquer aplicações. 

- [Compreender a encriptação do armazenamento Azure em repouso](./storage-service-encryption.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação** : Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nos recursos da conta de Armazenamento. Também pode permitir que o registo de armazenamento Azure rastreie a forma como cada pedido feito contra o Azure Storage foi autorizado. Os registos indicam se um pedido foi feito de forma anónima, utilizando um token OAuth 2.0, utilizando a Chave Partilhada, ou utilizando uma assinatura de acesso partilhado (SAS). Além disso, utilize o Azure Monitor para alertar sobre o acesso anónimo às contas de Armazenamento utilizando a condição de autenticação anónima.

- [Como criar alertas para eventos de Registo de Atividades Azure](../../azure-monitor/platform/alerts-activity-log.md)

- [Azure Storage analytics logging](./storage-analytics-logging.md) (Registo de análise do Armazenamento do Azure)

- [Como configurar alertas de métricas para contas de armazenamento Azure](./storage-monitor-storage-account.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para obter mais informações, consulte [Controlo de Segurança: Gestão de Vulnerabilidades.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas de digitalização de vulnerabilidade automatizada

**Orientação** : Siga as recomendações do Azure Security Center para auditar e monitorizar continuamente a configuração das suas contas de armazenamento. 

- [Recomendações de segurança: um guia de referência](../../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de patchs de sistema operativo

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implementar solução automatizada de gestão de patch de software de terceiros

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Compare as análises de vulnerabilidade de trás para a outra

**Orientação** : Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam as contas de Armazenamento.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas.

**Orientação** : Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center. 

- [Compreenda a pontuação segura do Centro de Segurança Azure](../../security-center/secure-score-security-controls.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte [Controlo de Segurança: Inventário e Gestão de Ativos.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-azure-asset-discovery"></a>6.1: Use a Azure Asset Discovery

**Orientação** : Utilize o Azure Resource Graph para consultar e descubra todos os recursos (incluindo contas de Armazenamento) dentro da sua subscrição(s). Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições. 

- [Como criar consultas com Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação** : Aplicar etiquetas nos recursos da conta de armazenamento, dando metadados para organizar logicamente numa taxonomia. 

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos de azure não autorizados

**Orientação** : Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e acompanhar contas de Armazenamento e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil. 

Além disso, utilize a Proteção Avançada de Ameaças para o Armazenamento Azure para detetar recursos Azure não autorizados. 

- [Como criar subscrições adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

- [Configure a proteção avançada de ameaças para o armazenamento de Azure](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Manter o inventário dos recursos aprovados da Azure e dos títulos de software.

**Orientação** : Terá de criar um inventário dos recursos Azure aprovados de acordo com as suas necessidades organizacionais. 


**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor de Recursos Azure Não Aprovados

**Orientação** : Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada: 

 - Tipos de recursos não permitidos 
 - Tipos de recursos permitidos 

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s) subscrição. Isto pode ajudar em ambientes de alta segurança, como aqueles com contas de Armazenamento. 

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor de aplicações de software não aprovadas dentro de Recursos Compute

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação** : O cliente pode impedir a criação ou utilização de recursos com a Política Azure, conforme exigido pelas políticas da empresa do cliente. 

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação** : Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada: 

- Tipos de recursos não permitidos 
- Tipos de recursos permitidos 

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="610-implement-approved-application-list"></a>6.10: Implementar lista de candidaturas aprovada

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure através de Scripts

**Orientação** : Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações de recursos num ambiente de alta segurança, como os que têm contas de Armazenamento. 

- [Como configurar o Acesso Condicional para bloquear o acesso à ARM](../../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts dentro de recursos computacional

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação** : Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte [Controlo de Segurança: Configuração Segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos azuis

**Orientação** : Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.Storage" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias de conta de Armazenamento. Também pode utilizar definições de Política Azure incorporadas para a conta de armazenamento Azure, tais como: 

Auditoria acesso ilimitado à rede a contas de armazenamento  
Implementar proteção avançada de ameaças em contas de armazenamento  
As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure  
A transferência segura para contas de armazenamento deve ser ativada  

Utilize recomendações do Azure Security Center como base de configuração segura para as suas contas de Armazenamento. 

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Estabelecer configurações seguras para o seu Sistema Operativo

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Manter configurações seguras para todos os recursos azuis

**Orientação** : Use a Política Azure [negar] e [implementar se não existir] para impor definições seguras em todos os recursos da sua conta de Armazenamento. 

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Manter configurações seguras para sistemas operativos

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração de loja segura de recursos Azure

**Orientação** : Utilize o Azure Repos para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure, scripts de configuração do estado desejado, etc. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

**Orientação** : Alavancar a Política Azure para alertar, auditar e impor configurações do sistema para a conta de Armazenamento. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas. 

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização de configuração automatizada para serviços de Azure

**Orientação** : Alavancar o Centro de Segurança Azure para realizar exames de base para os recursos da sua conta de armazenamento Azure. 

- [Como remediar recomendações no Centro de Segurança Azure](../../security-center/security-center-remediate-recommendations.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização de configuração automatizada para sistemas operativos

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="711-securely-manage-azure-secrets"></a>7.11: Gerir de forma segura os segredos do Azure

**Orientação** : O Azure Storage encripta automaticamente os seus dados quando estes são persistidos na nuvem. Pode utilizar as teclas geridas pela Microsoft para a encriptação da conta de armazenamento, ou pode gerir a encriptação com as suas próprias chaves. Se estiver a utilizar as chaves fornecidas pelo cliente, pode aproveitar o Cofre da Chave Azure para armazenar as chaves de forma segura. 

Adicionalmente, rode as chaves da conta de armazenamento numa base frequente para limitar o impacto da perda ou divulgação das chaves da Conta de Armazenamento.

- [Azure Storage encryption for data at rest](./storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)

- [Gerir chaves de acesso à conta de armazenamento](./storage-account-keys-manage.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Gerir de forma segura e automática as identidades

**Orientação** : Autorizar o acesso a bolhas e filas dentro das contas de armazenamento Azure com o Azure Ative Directory e identidades geridas. Azure Blob e Suporte de armazenamento de fila a autenticação do Azure Ative Directory (Azure AD) com identidades geridas para recursos Azure. Identidades geridas para recursos Azure podem autorizar o acesso a dados de blob e fila usando credenciais AD Azure a partir de aplicações em execução em máquinas virtuais Azure (VMs), aplicações de função, conjuntos de escala de máquinas virtuais e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação Azure AD, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem. 

- [Como conceder acesso a dados de blob e fila Azure usando uma identidade gerida](./storage-auth-aad-rbac-portal.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação** : Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte [Controlo de Segurança: Defesa de Malware.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação** : Utilize proteção contra ameaças para o Azure Storage para detetar uploads de malware para o Azure Storage utilizando análises de reputação de haxixe e acesso suspeito a partir de um nó de saída tor ativo (um proxy anonimizador). 

Também pode pré-digitalizar qualquer conteúdo para malware antes de enviar para recursos Azure não computar, tais como App Service, Data Lake Storage, Blob Storage, etc. para satisfazer os requisitos das suas organizações.

- [Configure a proteção avançada de ameaças para o armazenamento de Azure](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que software e assinaturas anti-malware são atualizados

**Orientação** : Não aplicável; esta recomendação destina-se a recursos computacional. A Microsoft lida com anti-malware para a plataforma subjacente.

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Não aplicável

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte [Controlo de Segurança: Recuperação de Dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação** : Os dados na sua conta de armazenamento Microsoft Azure são sempre replicados automaticamente para garantir a durabilidade e alta disponibilidade. O Azure Storage copia os seus dados de modo a que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. Pode optar por replicar os seus dados dentro do mesmo centro de dados, através de centros de dados zonais dentro da mesma região, ou em regiões geograficamente separadas. 

Também pode permitir que a automatização Azure tire fotos regulares das bolhas.

- [Compreender os acordos de redundância e Service-Level de armazenamento da Azure](./storage-redundancy.md)

- [Criar um instantâneo de uma bolha](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Visão geral da Azure Automation](../../automation/automation-intro.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer backup de qualquer tecla gerida pelo cliente

**Orientação** : Para fazer cópias de segurança dos dados dos serviços suportados pela conta de Armazenamento, existem vários métodos disponíveis, incluindo a utilização de ferramentas de azcopia ou de terceiros. O armazenamento imutável para o armazenamento Azure Blob permite que os utilizadores armazenem objetos de dados críticos do negócio num estado WORM (Write Once, Read Many). Este estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo utilizador.

- [Introdução ao AzCopy](./storage-use-azcopy-v10.md)

- [Definir e gerir políticas de imutabilidade para armazenamento blob](../blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

As chaves geridas /fornecidas pelo cliente podem ser apoiadas dentro do Cofre da Chave Azure utilizando o Azure CLI ou o PowerShell. 

- [Como backup chaves chave cofre em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação** : Execute periodicamente a restauração de dados dos seus certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com os seguintes comandos PowerShell: 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Como restaurar certificados de cofre chave](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

- [Como restaurar chaves do cofre](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

- [Como restaurar contas de armazenamento gerido de cofre chave](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Como restaurar os segredos do cofre da chave](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret)

- [AzCopy é um utilitário de linha de comando que pode usar para copiar bolhas, ficheiros e dados de tabela de ou para uma conta de armazenamento](./storage-use-azcopy-v10.md)

Nota: Se pretender copiar dados de e para o seu serviço de armazenamento Azure Table, instale a versão AzCopy 7.3.


**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção de backups e chaves geridas pelo cliente

**Orientação** : Para ativar as chaves geridas pelo cliente numa conta de armazenamento, tem de utilizar um Cofre de Chaves Azure para guardar as suas chaves. Tem de ativar as propriedades Soft Delete e Não Purgar no cofre da chave. A funcionalidade Soft Delete do Key Vault permite a recuperação de cofres apagados e objetos de abóbada, tais como chaves, segredos e certificados. Se apoiar os dados da conta de armazenamento nas bolhas de armazenamento Azure, permita eliminar suavemente para guardar e recuperar os seus dados quando as bolhas ou as imagens blob forem eliminadas. Deve tratar as suas cópias de segurança como dados sensíveis e aplicar os controlos de acesso e proteção de dados relevantes como parte desta linha de base. Além disso, para uma melhor proteção, pode armazenar objetos de dados críticos para o negócio num estado WORM (Write Once, Read Many).

- [Como usar o Soft Delete do Azure Key Vault](../../key-vault/general/key-vault-recovery.md)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Armazenar dados de blob críticos de negócio com armazenamento imutável](../blobs/storage-blob-immutable-storage.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, consulte [Controlo de Segurança: Resposta a incidentes.](../../security/benchmarks/security-control-incident-response.md)*

### <a name="101-create-incident-response-guide"></a>10.1: Criar guia de resposta a incidentes

**Orientação** : Crie um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Criar procedimento de pontuação e priorização de incidentes

**Orientação** : O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis. É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação** : Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato de incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação** : As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação** : Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação** : Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte [Controlo de Segurança: Testes de penetração e exercícios da equipa vermelha.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure

**Orientação** : Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Azure Security Benchmark](../../security/benchmarks/overview.md)
- Saiba mais sobre [as Linhas de Base de Segurança Azure](../../security/benchmarks/security-baselines-overview.md)