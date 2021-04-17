---
title: Linha de segurança Azure para armazenamento Azure
description: A linha de base de segurança do Azure Storage fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28d5bdc788e3b292545fd4c016fae36149f3a600
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589453"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Linha de segurança Azure para armazenamento Azure

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../../security/benchmarks/overview-v1.md) para o Azure Storage. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Storage. Foram excluídos **os controlos** não aplicáveis ao Azure Storage.

 
Para ver como o Azure Storage mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança Azure Storage completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Configure a firewall da sua conta de armazenamento, restringindo o acesso a clientes a partir de intervalos específicos de endereços IP públicos, selecione redes virtuais ou recursos específicos do Azure.  Também pode configurar private Endpoints para que o tráfego para o serviço de armazenamento da sua empresa viaje exclusivamente por redes privadas.

Nota: As contas de armazenamento clássicas não suportam firewalls e redes virtuais.

- [Como configurar a Firewall de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Como configurar pontos finais privados para armazenamento Azure](storage-private-endpoints.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: O Azure Storage fornece um modelo de segurança em camadas. Pode limitar o acesso à sua conta de armazenamento a pedidos originários de endereços IP especificados, intervalos IP ou de uma lista de sub-redes numa Rede Virtual Azure. Pode utilizar o Azure Security Center e seguir recomendações de proteção de rede para ajudar a proteger os recursos da sua rede em Azure. Além disso, permitir registos de fluxo de grupo de segurança de rede para redes virtuais ou sub-redes configurados para as contas de Armazenamento através da firewall da conta de armazenamento e enviar registos numa Conta de Armazenamento para auditoria de tráfego. 

 
Note que se tiver Pontos Finais Privados anexados à sua conta de armazenamento, não pode configurar as regras do grupo de segurança da rede para sub-redes. 
 

 
- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](storage-network-security.md) 
 

 
- [Como ativar registos de fluxo NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../../security-center/security-center-network-recommendations.md) 
 

 
- [Compreender pontos finais privados para armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Não aplicável; a recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative o Azure Defender para armazenamento para a sua conta de Armazenamento Azure. O Azure Defender para Armazenamento fornece uma camada adicional de informações de segurança que deteta tentativas invulgares e potencialmente nocivas de acesso ou de exploração das contas de armazenamento.  Os alertas integrados do Azure Security Center baseiam-se em atividades para as quais a comunicação de rede foi associada a um endereço IP que foi resolvido com sucesso, quer o endereço IP seja ou não um endereço IP de risco conhecido (por exemplo, um criptominador conhecido) ou um endereço IP que não seja reconhecido anteriormente como arriscado. Os alertas de segurança são acionados quando ocorrem anomalias de atividade. 

- [Configurar o Azure Defender para Armazenamento](azure-defender-storage-configure.md) 

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: A captura de pacotes do Observador de Rede permite-lhe criar sessões de captura para rastrear o tráfego entre a conta de Armazenamento e uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede, tanto reativas como proativamente. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar comunicações de servidores de clientes, e muito mais. Ser capaz de ativar remotamente capturas de pacotes, alivia o fardo de executar uma captura de pacote manualmente numa máquina virtual desejada, o que poupa tempo valioso. 

- [ Gerir capturas de pacotes com o Azure Network Watcher usando o portal](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: O Azure Defender for Storage fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Os alertas de segurança são acionados quando ocorrem anomalias de atividade. Estes alertas de segurança estão integrados no Centro de Segurança do Azure. Além disso, são enviados por e-mail aos administradores de subscrições com detalhes sobre a atividade suspeita e recomendações sobre como investigar e remediar ameaças. 

- [Configurar o Azure Defender para Armazenamento](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; a recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos em Redes Virtuais que necessitem de acesso à sua conta de Armazenamento, utilize tags de Serviço de Rede Virtual para a Rede Virtual configurada para definir controlos de acesso à rede em grupos de segurança de rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (como armazenamento) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. 

Quando o acesso à rede precisar de ser examinado para contas de armazenamento específicas, utilize políticas de ponto final de serviço de rede virtual.

- [Para mais informações sobre a utilização de Tags de Serviço](../../virtual-network/service-tags-overview.md)

- [Para mais informações sobre as políticas de ponto final do serviço de rede virtual para o Armazenamento Azure](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para os recursos de rede associados à sua Conta de Armazenamento Azure com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.Storage" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos da sua conta de armazenamento. 

Também pode utilizar definições de política incorporadas relacionadas com a conta de Armazenamento, tais como: As Contas de Armazenamento devem utilizar um ponto final de serviço de rede virtual 

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 

- [Amostras da Política Azure para armazenamento](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Amostras de política Azure para rede](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Como criar uma Planta Azure](../../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para grupos de segurança de rede e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. A marcação permite associar pares de valor de nome incorporados e personalizados a um determinado recurso de rede, ajudando-o a organizar recursos de rede e a relacionar os recursos do Azure de volta ao seu design de rede.

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados. 

Os grupos de segurança da rede suportam etiquetas, mas as regras de segurança individuais não. As regras de segurança têm um campo **descrição** que pode usar para armazenar algumas das informações que normalmente colocaria numa etiqueta.

- [Como criar e utilizar tags de recursos](../../azure-resource-manager/management/tag-resources.md)

- [Como filtrar o tráfego da rede com um grupo de segurança de rede](../../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize a Política Azure para registar alterações na configuração dos recursos de rede.  Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.  

 
- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Como criar alertas no Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de pontos finais, recursos de rede e outros sistemas de segurança. Dentro do Azure Monitor, utilize o Log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo, opcionalmente com funcionalidades de segurança, tais como armazenamento imutável e retenção forçada.

 
- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: A Azure Storage Analytics fornece registos para bolhas, filas e mesas. Pode utilizar o portal Azure para configurar quais os registos registados para a sua conta.   

 
- [Como configurar a monitorização para a sua conta de Armazenamento Azure](manage-storage-analytics-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Ao armazenar registos de eventos de segurança na conta de armazenamento Azure ou no espaço de trabalho Log Analytics, poderá definir a política de retenção de acordo com os requisitos da sua organização.  

 
- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Alterar o período de retenção de dados em Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Para rever os registos de Armazenamento Azure, existem as opções habituais, como consultas através da oferta Log Analytics, bem como uma opção única de visualização direta dos ficheiros de registo. No Azure Storage, os registos são armazenados em bolhas que devem ser acedidas diretamente `http://accountname.blob.core.windows.net/$logs` (a pasta de registo está escondida por predefinição, pelo que terá de navegar diretamente. Não será exibido nos comandos lista) 

 
Além disso, Ative o Azure Defender para armazenamento para a sua conta de armazenamento. O Azure Defender para Armazenamento fornece uma camada adicional de informações de segurança que deteta tentativas invulgares e potencialmente nocivas de acesso ou de exploração das contas de armazenamento. Os alertas de segurança são acionados quando ocorrem anomalias de atividade. Estes alertas de segurança estão integrados no Centro de Segurança do Azure. Além disso, são enviados por e-mail aos administradores de subscrições com detalhes sobre a atividade suspeita e recomendações sobre como investigar e remediar ameaças. 
 

 
- [Dados de registo e revisão](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Configurar o Azure Defender para Armazenamento](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: No Centro de Segurança Azure, ative a conta de armazenamento do Azure Defender. Ativar as Definições de Diagnóstico da conta de Armazenamento e enviar registos para um espaço de trabalho de "Log Analytics". A bordo do seu Log Analytics Workspace para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança.  

 
- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)  

 
- [Como gerir alertas no Centro de Segurança Azure](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Como alertar nos dados de registo de registo de registos de registos](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Azure Storage analytics logging](storage-analytics-logging.md) (Registo de análise do Armazenamento do Azure)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Utilize o Centro de Segurança Azure e permita ao Azure Defender para armazenamento para detetar uploads de malware para o Azure Storage utilizando análises de reputação de haxixe e acesso suspeito a partir de um nó de saída tor ativo (um proxy anonimizador). 

- [Configurar o Azure Defender para Armazenamento](azure-defender-storage-configure.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: A solução Azure DNS Analytics (Preview) no Azure Monitor reúne informações sobre infraestruturas de DNS sobre segurança, desempenho e operações.  Atualmente isto não suporta contas de Armazenamento Azure, no entanto pode utilizar a solução de registo de DNS de terceiros.  

 
- [Recolha informações sobre a sua infraestrutura DNS com a solução de pré-visualização do DNS Analytics](../../azure-monitor/insights/dns-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que devem ser explicitamente atribuídas e que são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: As contas de Armazenamento Azure nem o Azure Ative Directory (Azure AD) têm o conceito de senhas padrão ou em branco. O Azure Storage implementa um modelo de controlo de acesso que suporta o controlo de acesso baseado em funções Azure (Azure RBAC), bem como a Chave Partilhada e Assinaturas de Acesso Partilhado (SAS). Uma característica da autenticação da Chave Partilhada e da SAS é que nenhuma identidade está associada ao chamador e, portanto, não pode ser realizada uma autorização baseada na autorização principal de segurança.

- [Autorizar o acesso aos dados no Azure Storage](storage-auth.md)

- [Compreender os princípios de segurança e o controlo de acessos para a conta de armazenamento Azure](storage-introduction.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso à sua conta de Armazenamento. Utilize a identidade do Centro de Segurança Azure e a gestão de acessos para monitorizar o número de contas administrativas.

Também pode ativar um Acesso Rápido / Justo-Suficiente utilizando o Azure Ative Directory (Azure AD) Funções privilegiadas de gestão de identidade privilegiada para serviços da Microsoft e gestor de recursos Azure.

- [Compreender a identidade e o acesso do Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

- [Visão geral da gestão de identidade privilegiada](/azure/active-directory/privileged-identity-management/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Sempre que possível, utilize o Azure Ative Directory (Azure AD) SSO em vez de configurar credenciais individuais autónomas por serviço. Utilize recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.
 

 
- [Compreender SSO com Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Autorizar o acesso aos dados no Azure Storage](storage-auth.md)
 

 
- [Autorizar o acesso a bolhas e filas usando Azure AD](storage-auth-aad.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactorA Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity e access management para ajudar a proteger os recursos da sua conta de Armazenamento.

- [Como permitir a autenticação multifactor em Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com autenticação multifactor configurada para iniciar sessão e configurar recursos da conta de armazenamento.  

 
- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Como permitir a autenticação multifactor em Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Enviar alertas de deteção de risco do Centro de Segurança Azure para o Monitor Azure e configurar alertas/notificações personalizados utilizando grupos de ação. Ativar a conta Azure Defender for Storage para gerar alertas para atividades suspeitas. Além disso, utilize Azure Ative Directory (Azure AD) Deteções de Risco para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Configurar o Azure Defender para Armazenamento](azure-defender-storage-configure.md)
 

- [Compreender deteções de risco Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Como configurar grupos de ação para alerta e notificação personalizados](../../azure-monitor/alerts/action-groups.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados para acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar localizações nomeadas em Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. O Azure fornece o controlo de acesso baseado em funções (Azure RBAC) para controlo de grãos finos sobre o acesso de um cliente aos recursos numa conta de armazenamento.   Use credenciais AZure AD quando possível como uma melhor prática de segurança, em vez de usar a chave de conta, que pode ser mais facilmente comprometida. Quando o design da sua aplicação requer assinaturas de acesso partilhadas para acesso ao armazenamento blob, use credenciais Azure AD para criar uma delegação de utilizador assinaturas de acesso compartilhadas (SAS) quando possível para uma segurança superior.

- [Como criar e configurar instâncias do Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão](authorization-resource-provider.md)

- [Como configurar o acesso aos dados da Azure Blob e da Fila com o Azure RBAC no portal Azure](storage-auth-aad-rbac-portal.md)

- [Autorizar o acesso aos dados no Azure Storage](storage-auth.md)

- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Reveja os registos do Azure Ative Directory (Azure AD) para ajudar a descobrir contas velhas que podem incluir as que têm funções administrativas de conta de Armazenamento. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais que possam ser usadas para aceder a recursos de conta de Armazenamento e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente para garantir que apenas os Utilizadores certos tenham acesso continuado. 
 

 
Também pode utilizar a assinatura de acesso partilhado (SAS) para fornecer acesso delegado seguro aos recursos na sua conta de armazenamento sem comprometer a segurança dos seus dados. Pode controlar os recursos a que o cliente pode aceder, quais as permissões que têm sobre esses recursos, e quanto tempo o SAS é válido, entre outros parâmetros.
 

 
Além disso, reveja o acesso anónimo a contentores e bolhas. Por predefinição, um contentor e quaisquer blobs dentro do mesmo podem apenas ser acedidos por um utilizador ao qual foram dadas as permissões adequadas. Pode utilizar o Azure Monitor para alertar sobre o acesso anónimo às contas de Armazenamento utilizando a condição de autenticação anónima.
 

 
Uma forma eficaz de reduzir o risco de acesso insuspeito à conta de utilizador é limitar a duração de acesso que concede aos utilizadores. Os SAS URIs limitados no tempo são uma forma eficaz de expirar automaticamente o acesso do utilizador a uma conta de Armazenamento. Além disso, a rotação das chaves de conta de armazenamento numa base frequente é uma forma de garantir que o acesso inesperado através das chaves da Conta de Armazenamento é de duração limitada.
 

 
- [Compreenda a Azure Ad Reporting](/azure/active-directory/reports-monitoring/) 
 

 
- [Como visualizar e alterar o acesso ao nível da conta de armazenamento Azure](storage-auth-aad-rbac-portal.md)
 

 
- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)
 

 
- [Gerir o acesso de leitura anónimo a contentores e blobs](../blobs/anonymous-read-access-configure.md)
 

 
- [Monitorizar uma conta de armazenamento no portal do Azure](manage-storage-analytics-logs.md)
 

 
- [Gerir chaves de acesso à conta de armazenamento](storage-account-keys-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Utilize o Storage Analytics para registar informações detalhadas sobre pedidos bem sucedidos e falhados a um serviço de armazenamento. Todos os registos são armazenados em blocos num recipiente chamado $logs, que são automaticamente criados quando o Storage Analytics está ativado para uma conta de armazenamento.
 

Crie definições de diagnóstico para contas de utilizador do Azure Ative (Azure AD), enviando os registos de auditoria e registos de inscrição para um espaço de trabalho de Log Analytics. Pode configurar os alertas desejados dentro do log analytics workspace.

Para monitorizar falhas de autenticação contra contas de armazenamento Azure, pode criar alertas para o notificar quando determinados limiares tiverem sido atingidos para métricas de recursos de armazenamento. Além disso, utilize o Azure Monitor para alertar sobre o acesso anónimo às contas de Armazenamento utilizando a condição de autenticação anónima.

- [Azure Storage analytics logging](storage-analytics-logging.md) (Registo de análise do Armazenamento do Azure)
 

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Como configurar alertas de métricas para contas de armazenamento Azure](manage-storage-analytics-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize as funcionalidades de Proteção de Riscos e Identidade da Azure (Azure AD) para configurar respostas automatizadas para ações suspeitas detetadas relacionadas com os recursos da sua conta de armazenamento. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

- [Como ver os inícios de sessão de risco do Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Em cenários de suporte em que a Microsoft precisa de aceder aos dados dos clientes, o Customer Lockbox (Preview for Storage account) fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. A Microsoft não exigirá, nem solicitará o acesso aos segredos da sua organização armazenados na conta de Armazenamento.

- [Compreender o bloqueio do cliente](../../security/fundamentals/customer-lockbox-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da conta de armazenamento que armazenam ou processam informações sensíveis. 

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando subscrições separadas, grupos de gestão e contas de armazenamento para domínios de segurança individuais, como ambiente, e sensibilidade aos dados.  Pode restringir a sua Conta de Armazenamento para controlar o nível de acesso às suas contas de armazenamento que as suas aplicações e ambientes empresariais exigem, com base no tipo e subconjunto de redes utilizadas. Quando as regras de rede são configuradas, apenas as aplicações que solicitam dados sobre o conjunto especificado de redes podem aceder a uma conta de armazenamento. Pode controlar o acesso ao Azure Storage via Azure RBAC (Azure RBAC).

Também pode configurar private endpoints para melhorar a segurança à medida que o tráfego entre a sua rede virtual e o serviço atravessa a rede de espinha dorsal da Microsoft, eliminando a exposição da Internet pública.

- [Como criar subscrições adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](storage-network-security.md)

- [Pontos finais de serviço de Rede Virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Para armazenar ou processar informações sensíveis, marque os recursos como sensíveis através de Tags. Para reduzir o risco de perda de dados através da exfiltração, restringir o tráfego de rede de saída para contas de Armazenamento Azure usando a Azure Firewall.  

Além disso, utilize políticas de ponto final do serviço de rede virtual para filtrar o tráfego de rede virtual de saída para contas de Armazenamento Azure sobre o ponto final do serviço, e permitir a exfiltração de dados apenas para contas específicas de Armazenamento Azure.

- [Configurar as firewalls e as redes virtuais do Armazenamento do Microsoft Azure](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Políticas do ponto final de serviço de rede virtual do Armazenamento do Microsoft Azure](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Compreender a proteção dos dados dos clientes no Azure](../../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Pode impor a utilização do HTTPS, permitindo a transferência segura necessária para a conta de armazenamento. As ligações através de HTTP serão recusadas assim que esta opção estiver ativada.  Além disso, utilize o Azure Security Center e a Azure Policy para impor transferência segura para a sua conta de armazenamento.

- [Como exigir transferência segura no Azure Storage](storage-require-secure-transfer.md)

- [Políticas de segurança Azure monitorizadas pelo Centro de Segurança](../../security-center/policy-reference.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As funcionalidades de identificação de dados ainda não estão disponíveis para a conta de armazenamento Azure e recursos conexos. Implementar solução de terceiros, se necessário para efeitos de conformidade. 

- [Compreender a proteção dos dados dos clientes no Azure](../../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: O Azure Ative Directory (Azure AD) autoriza os direitos de acesso a recursos seguros através do controlo de acesso baseado em funções da Azure (Azure RBAC). O Azure Storage define um conjunto de funções RBAC incorporadas em Azure que englobam conjuntos comuns de permissões usadas para aceder a dados de bolhas ou filas.

- [Como atribuir funções do Azure RBAC para conta de Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Utilize o fornecedor de recursos de armazenamento Azure para aceder a recursos de gestão](authorization-resource-provider.md)

- [Como configurar o acesso aos dados da Azure Blob e da Fila com o Azure RBAC no portal Azure](storage-auth-aad-rbac-portal.md)

- [Como criar e configurar instâncias do Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizar o acesso aos dados no Azure Storage](storage-auth.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: A encriptação do Armazenamento Azure está ativada para todas as contas de armazenamento e não pode ser desativada. O Azure Storage encripta automaticamente os seus dados quando estes são persistidos na nuvem. Quando lê os dados a partir do Armazenamento do Azure, estes são desencriptados pelo Armazenamento do Azure antes de serem devolvidos. A encriptação de Armazenamento Azure permite-lhe proteger os seus dados em repouso sem ter de modificar código ou adicionar código a quaisquer aplicações. 

- [Compreender a encriptação do armazenamento Azure em repouso](storage-service-encryption.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nos recursos da conta de Armazenamento.  Também pode permitir que o registo de armazenamento Azure rastreie a forma como cada pedido feito contra o Azure Storage foi autorizado. Os registos indicam se um pedido foi feito de forma anónima, utilizando um token OAuth 2.0, utilizando a Chave Partilhada, ou utilizando uma assinatura de acesso partilhado (SAS).  Além disso, utilize o Azure Monitor para alertar sobre o acesso anónimo às contas de Armazenamento utilizando a condição de autenticação anónima. 

 
- [Como criar alertas para eventos de Registo de Atividades Azure](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Azure Storage analytics logging](storage-analytics-logging.md) (Registo de análise do Armazenamento do Azure) 

 
- [Como configurar alertas de métricas para contas de armazenamento Azure](manage-storage-analytics-logs.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Siga as recomendações do Azure Security Center para auditar e monitorizar continuamente a configuração das suas contas de armazenamento.  

- [Recomendações de segurança: um guia de referência](../../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Não aplicável; A Microsoft realiza uma gestão de vulnerabilidades nos sistemas subjacentes que suportam as contas de Armazenamento.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center. 

- [Compreenda a pontuação segura do Centro de Segurança Azure](/azure/security-center/security-center-secure-score)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descubra todos os recursos (incluindo contas de Armazenamento) dentro da sua subscrição(s). Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

- [Como criar consultas com Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas nos recursos da conta de armazenamento, dando metadados para organizar logicamente numa taxonomia. 

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e acompanhar contas de Armazenamento e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil. 

Além disso, utilize o Azure Defender para armazenamento para detetar recursos Azure não autorizados. 

- [Como criar subscrições adicionais do Azure](../../cost-management-billing/manage/create-subscription.md) 

- [Como criar Grupos de Gestão](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../../azure-resource-manager/management/tag-resources.md)

- [Configurar o Azure Defender para Armazenamento](azure-defender-storage-configure.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Terá de criar um inventário dos recursos Azure aprovados de acordo com as suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada: 

 
 - Tipos de recursos não permitidos 
 
 - Tipos de recursos permitidos 
 

 
Além disso, utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das subscrições. Isto pode ajudar em ambientes de alta segurança, como aqueles com contas de Armazenamento. 
 

 
- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Como criar consultas com Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: O cliente pode impedir a criação ou utilização de recursos com a Política Azure, conforme exigido pelas políticas da empresa do cliente. 

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Definições incorporadas da Azure Policy - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações de recursos num ambiente de alta segurança, como os que têm contas de Armazenamento. 

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes **Microsoft.Storage** para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias de conta de Armazenamento. Também pode utilizar definições de Política Azure incorporadas para a conta de armazenamento Azure, tais como:

- Auditoria acesso ilimitado à rede a contas de armazenamento
- Implementar o Azure Defender para armazenamento
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure
- A transferência segura para contas de armazenamento deve ser ativada

Utilize recomendações do Azure Security Center como base de configuração segura para as suas contas de Armazenamento.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor definições seguras em todos os recursos da sua conta de armazenamento. 

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 

- [Compreender efeitos da política do Azure](../../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize o Azure Repos para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure, scripts de configuração do estado desejado, etc. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps ou Azure AD se integrados com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Alavancar a Política Azure para alertar, auditar e impor configurações do sistema para a conta de Armazenamento. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas. 

- [ Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Alavancar o Centro de Segurança Azure para realizar exames de base para os recursos da sua conta de armazenamento Azure. 

- [Como remediar recomendações no Centro de Segurança Azure](../../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: O Azure Storage encripta automaticamente os seus dados quando estes são persistidos na nuvem. Pode utilizar as teclas geridas pela Microsoft para a encriptação da conta de armazenamento, ou pode gerir a encriptação com as suas próprias chaves. Se estiver a utilizar as chaves fornecidas pelo cliente, pode aproveitar o Cofre da Chave Azure para armazenar as chaves de forma segura. 

Adicionalmente, rode as chaves da conta de armazenamento numa base frequente para limitar o impacto da perda ou divulgação das chaves da Conta de Armazenamento.

- [Azure Storage encryption for data at rest](storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)

- [Gerir chaves de acesso à conta de armazenamento](storage-account-keys-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Autorizar o acesso a bolhas e filas dentro das contas de armazenamento Azure com o Azure Ative Directory (Azure AD) e identidades geridas. Azure Blob e Suporte de armazenamento de fila a autenticação Azure AD com identidades geridas para recursos Azure. 

Identidades geridas para recursos Azure podem autorizar o acesso a dados de blob e fila usando credenciais AD AZure a partir de aplicações em execução em Azure Virtual Machines r, apps de função, conjuntos de escala de máquinas virtuais e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação Azure AD, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem.

- [Como conceder acesso a dados de blob e fila Azure usando uma identidade gerida](storage-auth-aad-rbac-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [ Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: Utilize o Azure Defender para armazenamento para detetar uploads de malware para o Azure Storage utilizando análises de reputação de haxixe e acesso suspeito a partir de um nó de saída tor ativo (um proxy anonimizador). 
 

 
Também pode pré-digitalizar qualquer conteúdo para malware antes de enviar para recursos Azure não computar, tais como App Service, Data Lake Storage, Blob Storage, e outros para satisfazer os requisitos das suas organizações.
 

 
- [Configurar o Azure Defender para Armazenamento](azure-defender-storage-configure.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Os dados na sua conta de armazenamento Microsoft Azure são sempre replicados automaticamente para garantir a durabilidade e alta disponibilidade. O Azure Storage copia os seus dados de modo a que esteja protegido contra eventos planeados e não planeados, incluindo falhas de hardware transitórios, falhas de rede ou de energia e desastres naturais maciços. Pode optar por replicar os seus dados dentro do mesmo centro de dados, através de centros de dados zonais dentro da mesma região, ou em regiões geograficamente separadas. 

Também pode permitir que a automatização Azure tire fotos regulares das bolhas.

- [Compreender os acordos de redundância e Service-Level de armazenamento da Azure](storage-redundancy.md)

- [Criar um instantâneo de uma bolha](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Visão geral da Azure Automation](../../automation/automation-intro.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Para fazer o back up dados dos serviços suportados pela conta de Armazenamento, existem vários métodos disponíveis, incluindo a utilização de ferramentas azcopia ou de terceiros. O armazenamento imutável para o armazenamento Azure Blob permite que os utilizadores armazenem objetos de dados críticos do negócio num estado WORM (Write Once, Read Many). Este estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo utilizador.
 

As chaves geridas pelo cliente/fornecidas podem ser apoiadas dentro do Cofre da Chave Azure utilizando o Azure CLI ou o PowerShell. 

 
- [Introdução ao AzCopy](storage-use-azcopy-v10.md)  

- [Definir e gerir políticas de imutabilidade para armazenamento blob](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Execute periodicamente a restauração de dados dos seus certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com os seguintes comandos PowerShell:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Como restaurar certificados de cofre chave](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Como restaurar chaves do cofre](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Como restaurar contas de armazenamento gerido de cofre chave](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Como restaurar os segredos do cofre da chave](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy é um utilitário de linha de comando que pode usar para copiar bolhas, ficheiros e dados de tabela de ou para uma conta de armazenamento](storage-use-azcopy-v10.md)

Nota: Se pretender copiar dados de e para o seu serviço de armazenamento Azure Table, instale a versão AzCopy 7.3.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Para ativar as chaves geridas pelo cliente numa conta de armazenamento, tem de utilizar um Cofre de Chaves Azure para guardar as suas chaves. Tem de ativar as propriedades Soft Delete e Não Purgar no cofre da chave.  A funcionalidade Soft Delete do Key Vault permite a recuperação de cofres apagados e objetos de abóbada, tais como chaves, segredos e certificados.  Se apoiar os dados da conta de armazenamento nas bolhas de armazenamento Azure, permita eliminar suavemente para guardar e recuperar os seus dados quando as bolhas ou as imagens blob forem eliminadas.  Deve tratar as suas cópias de segurança como dados sensíveis e aplicar os controlos de acesso e proteção de dados relevantes como parte desta linha de base.  Além disso, para uma melhor proteção, pode armazenar objetos de dados críticos para o negócio num estado WORM (Write Once, Read Many).

- [Como usar o Soft Delete do Azure Key Vault](../../key-vault/general/key-vault-recovery.md)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../blobs/soft-delete-blob-overview.md)

- [Armazenar dados de blobs críticos para a empresa com o armazenamento imutável](../blobs/storage-blob-immutable-storage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Guia de manuseamento de incidentes de segurança informática do NIST](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

 
Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.
 

 
- [Alertas de segurança no Centro de Segurança do Azure](../../security-center/security-center-alerts-overview.md)
 

 
- [Utilizar etiquetas para organizar os recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.
    

- [ Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
