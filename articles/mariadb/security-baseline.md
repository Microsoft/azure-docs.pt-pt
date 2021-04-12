---
title: Linha de segurança Azure para Azure Database for MariaDB
description: A Base de Dados Azure para a linha de base de segurança MariaDB fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/29/2021
ms.service: mariadb
ms.topic: conceptual
ms.custom: subject-security-benchmark
ms.openlocfilehash: fd169738c138d56932dbab87047b89e373b9c6ec
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227675"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Linha de segurança Azure para Azure Database for MariaDB

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para a Base de Dados Azure para a MariaDB. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Base de Dados Azure para a MariaDB. **Foram excluídos os controlos** não aplicáveis à Base de Dados Azure para o MariaDB, ou para os quais a responsabilidade é da Microsoft.

Para ver como a Base de Dados Azure para MariaDB mapeia completamente para o Benchmark de Segurança Azure, consulte a [base de dados Azure completa para o ficheiro de mapeamento de base de segurança MariaDB](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Configurar link privado para base de dados Azure para MariaDB com pontos finais privados. A Ligação Privada permite-lhe estabelecer ligação a vários serviços PaaS no Azure através de um ponto final privado. O Azure Private Link essencialmente traz os serviços do Azure dentro da sua Rede Privada Virtual (VNet). O tráfego entre a sua rede virtual e a instância MariaDB percorre a rede de espinha dorsal da Microsoft.

Em alternativa, poderá utilizar os pontos finais do Serviço de Rede Virtual para proteger e limitar o acesso à rede à sua Base de Dados Azure para implementações MariaDB. As regras de rede virtual são uma funcionalidade de segurança de firewall que controla se a sua Base de Dados Azure para MariaDB aceita comunicações que são enviadas a partir de sub-redes específicas em redes virtuais.

Também pode proteger a sua Base de Dados Azure para MariaDB com regras de firewall. A firewall do servidor impede todo o acesso ao servidor de base de dados até especificar quais os computadores que têm permissão. Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

- [Como configurar link privado para base de dados Azure para MariaDB](howto-configure-privatelink-portal.md)

- [Como criar e gerir os pontos finais do serviço VNet e as regras VNet na Base de Dados Azure para servidor MariaDB](howto-manage-vnet-portal.md)

- [Como configurar a Base de Dados Azure para as regras de firewall MariaDB](concepts-firewall-rules.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Política Azure - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Quando a sua Base de Dados Azure para servidor MariaDB estiver protegida num ponto final privado, pode implantar máquinas virtuais na mesma rede virtual. Pode utilizar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como configurar link privado para base de dados Azure para MariaDB](howto-configure-privatelink-portal.md)

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize proteção de ameaças avançadas para base de dados de azul para MariaDB. A Advanced Threat Protection deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

Ativar a Norma de Proteção DDoS nas redes virtuais associadas à sua Base de Dados Azure para instâncias MariaDB para proteger contra ataques de DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

- [Como configurar a Proteção Avançada de Ameaças para a Base de Dados de Azure para a MariaDB](howto-database-threat-protection-portal.md)

- [Como configurar a proteção DDoS](/azure/virtual-network/manage-ddos-protection)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Quando a sua Base de Dados Azure para servidor MariaDB estiver protegida num ponto final privado, pode implantar máquinas virtuais na mesma rede virtual. Em seguida, pode configurar um grupo de segurança de rede (NSG) para reduzir o risco de exfiltração de dados. Ativar os registos de fluxo NSG e enviar registos para uma conta de armazenamento para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho do Log Analytics e utilizar o Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Utilize proteção de ameaças avançadas para base de dados de azul para MariaDB. A Advanced Threat Protection deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

- [Como configurar a Proteção Avançada de Ameaças para a Base de Dados de Azure para a MariaDB](howto-database-threat-protection-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso à sua Base de Dados Azure para instâncias MariaDB, utilize tags de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Especificando o nome da etiqueta de serviço (por exemplo, SQL. WestUs) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

A Azure Database for MariaDB utiliza a etiqueta de serviço "Microsoft.Sql".

- [Para mais informações sobre a utilização de tags de serviço](../virtual-network/service-tags-overview.md)

- [Compreender o uso da etiqueta de serviço para a Base de Dados Azure para MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados à sua Base de Dados Azure para instâncias MariaDB com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.DBforMariaDB" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede da sua Base de Dados Azure para casos MariaDB. Pode também utilizar definições de políticas incorporadas relacionadas com o networking ou a sua Base de Dados Azure para casos mariaDB, tais como:

- A Norma de Proteção DDoS deve ser ativada

- O ponto final privado deve ser ativado para servidores MariaDB

- O servidor MariaDB deve usar um ponto final de serviço de rede virtual

Documentação de referência:

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize tags para recursos relacionados com a segurança da rede e fluxo de tráfego para as suas instâncias MariaDB para fornecer metadados e organização lógica.

Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com Tags e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas Tags.

- [Como criar e usar Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com a sua Base de Dados Azure para instâncias MariaDB. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ative as definições de diagnóstico e registos de servidores e ingere registos para agregar dados de segurança gerados pela sua Base de Dados Azure para instâncias MariaDB. Dentro do Azure Monitor, utilize o Log Analytics Workspace(s) para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como configurar e aceder a Registos de Servidor para Base de Dados Azure para MariaDB](concepts-server-logs.md)

- [Como configurar e aceder aos registos de auditoria do Azure Database para a MariaDB](howto-configure-audit-logs-portal.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar as definições de diagnóstico na sua Base de Dados Azure para instâncias MariaDB para acesso a registos de auditoria, segurança e diagnóstico. Certifique-se de que ativa especificamente o registo de auditoria mariaDB. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis. Pode também ativar as Definições de Diagnóstico do Registo de Atividade do Azure e enviar os registos para o mesmo espaço de trabalho ou conta de armazenamento do Log Analytics.

- [Como configurar e aceder a Registos de Servidor para Base de Dados Azure para MariaDB](concepts-server-logs.md)

- [Como configurar e aceder aos registos de auditoria do Azure Database para a MariaDB](howto-configure-audit-logs-portal.md)

- [Como configurar definições de diagnóstico para o registo de atividades azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Âmbito do Monitor Azure, para o espaço de trabalho do Log Analytics que está a ser utilizado para manter a sua Base de Dados Azure para registos MariaDB, decreva o período de retenção de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Como definir parâmetros de retenção de registos para log analytics workspaces](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Armazenar registos de recursos numa conta de armazenamento Azure](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos dos seus casos MariaDB para comportamento anómalo. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Para mais informações sobre o Log Analytics Workspace](/azure/azure-monitor/log-query/get-started-portal)

- [Como realizar consultas personalizadas no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Permitir a proteção de ameaças avançadas para o MariaDB. A Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

Além disso, pode ativar registos de servidores e definições de diagnóstico para MariaDB e enviar registos para um espaço de trabalho de Log Analytics. A bordo do seu Log Analytics Workspace para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança.

- [Como permitir a Proteção Avançada de Ameaças para o MariaDB](howto-database-threat-protection-portal.md)

- [Como configurar e aceder a Registos de Servidor para MariDB](concepts-server-logs.md)

- [Como configurar e aceder aos registos de auditoria do MariaDB](howto-configure-audit-logs-portal.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha um inventário das contas de utilizador que tenham acesso administrativo ao plano de gestão (Portal Azure/Gestor de Recursos Azure) das suas instâncias MariaDB. Além disso, mantenha um inventário das contas administrativas que tenham acesso ao plano de dados dos seus casos MariaDB. (Ao criar o servidor MariaDB, fornece credenciais para um utilizador administrador. Este administrador pode ser usado para criar utilizadores MariaDB adicionais.)

- [Compreender a gestão de acessos para MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-security#access-management)

- [Compreenda as funções incorporadas do Azure para as subscrições da Azure](../role-based-access-control/built-in-roles.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: O Azure Ative Directory (Azure AD) não tem o conceito de palavras-passe padrão.

Após a criação do próprio recurso MariaDB, o Azure força a criação de um utilizador administrativo com uma senha forte. No entanto, uma vez criada a instância MariaDB, poderá utilizar a primeira conta de administração do servidor que criou para criar utilizadores adicionais e conceder acesso administrativo aos mesmos. Ao criar estas contas, certifique-se de que configura uma senha diferente e forte para cada conta.

- [Como criar contas adicionais para a MariaDB](howto-create-users.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso às suas instâncias MariaDB. Utilize a identidade do Centro de Segurança Azure e a gestão de acessos para monitorizar o número de contas administrativas.

- [Compreender a identidade e o acesso do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: O acesso de avião de dados à MariaDB é controlado por identidades armazenadas na base de dados e não suporta SSO. O acesso do avião de controlo para MariaDB está disponível através da REST API e suporta sSO. Para autenticar, desacorda o cabeçalho de Autorização para os seus pedidos num Token Web JSON que obtenha do Azure Ative Directory (Azure AD).

- [Compreender a base de dados Azure para MariaDB REST API](/rest/api/mariadb/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com autenticação multifactor configurada para iniciar sessão e configurar recursos Azure. 

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Permitir que a Proteção Avançada de Ameaças para MariaDB gere alertas para atividades suspeitas.

Além disso, poderá utilizar o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como configurar a Proteção Avançada de Ameaças para MariaDB](howto-database-threat-protection-portal.md)

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso a agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões para limitar o acesso a recursos Azure, como o MariaDB.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

A autenticação AZURE AD não pode ser utilizada para o acesso direto ao plano de dados MariaDB, no entanto, as credenciais AZURE AD podem ser utilizadas para a administração a nível do avião de gestão (por exemplo, o portal Azure) para controlar as contas de administração mariadb.

- [Como atualizar a palavra-passe de administrador para MariaDB](https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Reveja os registos do Azure Ative Directory (Azure AD) para ajudar a descobrir contas velhas que podem incluir as que têm funções administrativas MariaDB. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais que possam ser usadas para aceder à MariaDB e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os Utilizadores certos tenham acesso continuado.

- [Compreenda a Azure Ad Reporting](/azure/active-directory/reports-monitoring/)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Ativar as definições de diagnóstico para o Diretório Ativo MariaDB e Azure (Azure AD), enviando todos os registos para um espaço de trabalho de Log Analytics. Configure os alertas desejados (tais como tentativas de autenticação falhada) dentro do log analytics workspace.

- [Como configurar e aceder a Registos de Servidor para MariaDB](concepts-server-logs.md)

- [Como configurar e aceder aos registos de auditoria do MariaDB](howto-configure-audit-logs-portal.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Permitir a proteção de ameaças avançadas para o MariaDB. A Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

Utilize o Diretório Ativo do Azure (Azure AD) na Proteção de Identidade e nas funcionalidades de deteção de riscos para configurar respostas automatizadas para ações suspeitas detetadas. Pode permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

- [Como permitir a Proteção Avançada de Ameaças para o MariaDB](howto-database-threat-protection-portal.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como ver os inícios de sessão de risco do Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize tags para ajudar a rastrear a Base de Dados Azure para casos MariaDB ou recursos relacionados que armazenam ou processam informações sensíveis.

- [Como criar e usar Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. Utilize uma combinação de regras de firewall Private Link, Service Endpoints e/ou MariaDB para isolar e limitar o acesso à rede às suas instâncias MariaDB.

- [Como criar subscrições adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](/azure/governance/management-groups/create)

- [Como configurar link privado para base de dados Azure para MariaDB](concepts-data-access-security-private-link.md)

- [Como configurar pontos finais de serviço para a base de dados Azure para MariaDB](howto-manage-vnet-portal.md)

- [Como configurar regras de firewall para Azure Database for MariaDB](concepts-firewall-rules.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Ao utilizar VMs Azure para aceder a instâncias MariaDB, utilize configurações de ligações privadas, configurações de rede MariaDB, Grupos de Segurança de Rede e Tags de Serviço para mitigar a possibilidade de exfiltração de dados.

A Microsoft gere a infraestrutura subjacente à MariaDB e implementou rigorosos controlos para evitar a perda ou exposição dos dados dos clientes.

- [Como mitigar a exfiltração de dados para a Base de Dados Azure para a MariaDB](concepts-data-access-security-private-link.md)

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: A base de dados Azure para MariaDB suporta a ligação da sua Base de Dados Azure para servidor MariaDB a aplicações de clientes utilizando a Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL). A aplicação das ligações TLS entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. No portal Azure, certifique-se de que **a ligação Enforce SSL** está ativada para todas as suas instâncias MariaDB.

- [Como configurar encriptação em trânsito para MariaDB](howto-configure-ssl.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para a Base de Dados Azure para o MariaDB. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso à Base de Dados Azure para o plano de gestão MariaDB (Portal Azure/Gestor de Recursos Azure). Para o acesso a um plano de dados (dentro da própria base de dados), utilize consultas SQL para criar utilizadores e configurar permissões do utilizador.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Como configurar o acesso do utilizador com SQL para MariaDB](howto-create-users.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nas instâncias de produção da Base de Dados Azure para a MariaDB e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Atualmente não disponível; O Azure Security Center ainda não suporta a avaliação de vulnerabilidade para a Base de Dados Azure para o servidor MariaDB.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar e descubra todos os recursos (incluindo a Base de Dados Azure para o servidor MariaDB) dentro das suas subscrições. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas na Base de Dados Azure para servidor MariaDB e outros recursos relacionados que dão metadados para organizá-los logicamente numa taxonomia.

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e subscrições separadas, se for caso disso, para organizar e rastrear a Base de Dados Azure para servidor mariaDB e recursos relacionados. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gestão](/azure/governance/management-groups/create)

- [Como criar e usar tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional e a Azure como um todo.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Para obter mais informações, veja as seguintes referências:

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações de recursos dentro de um ambiente de alta segurança, tal como a Base de Dados Azure para o servidor MariaDB que contém informações confidenciais.

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para a sua Base de Dados Azure para instâncias MariaDB com Azure Policy. Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.DBforMariaDB" para criar políticas personalizadas para auditar ou impor a configuração de rede da sua Base de Dados Azure para servidores MariaDB. Pode também utilizar definições de políticas incorporadas relacionadas com a sua Base de Dados Azure para servidores MariaDB, tais como:

- Backup geo-redundante deve ser ativado para Azure Database for MariaDB

Para obter mais informações, veja as seguintes referências:

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para a sua Base de Dados Azure para servidores MariaDB e recursos relacionados, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentação de Azure Repos](/azure/devops/repos/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.DBforMariaDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.DBforMariaDB" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Utilize a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para a sua Base de Dados Azure para instâncias MariaDB e recursos relacionados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder à sua Base de Dados Azure para servidores MariaDB, utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a Base de Dados Azure para a gestão secreta do servidor MariaDB. Certifique-se de que o cofre da chave é excluído suave.

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre-Chave](../key-vault/general/quick-create-portal.md)

- [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: A base de dados Azure para servidor MariaDB não suporta atualmente a autenticação do Azure Ative Directory (Azure AD) para aceder a bases de dados. Ao criar a Base de Dados Azure para o servidor MariaDB, fornece credenciais para um utilizador administrador. Este administrador pode ser usado para criar utilizadores MariaDB adicionais.

Para máquinas virtuais Azure ou aplicações web em execução no Azure App Service sendo usado para aceder à sua Base de Dados Azure para servidor MariaDB, utilize identidades geridas em conjunto com o Azure Key Vault para armazenar e recuperar credenciais para a Base de Dados Azure para servidor MariaDB. Certifique-se de que o cofre da chave é excluído suave.

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente em Azure AD. Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como configurar identidades geridas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O anti-malware da Microsoft é ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Database para o servidor MariaDB), no entanto não funciona com conteúdo do cliente.

Pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB server, etc. A Microsoft não pode aceder aos seus dados nestes casos.

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: A base de dados Azure para MariaDB recebe cópias de segurança completas, diferenciais e de registo de transações.  A Azure Database for MariaDB cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante do utilizador. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo. A cópia de segurança e o restauro são uma parte essencial de qualquer estratégia de continuidade empresarial, uma vez que protegem os seus dados contra danos e a eliminação acidentais.  O período de retenção de backup predefinido é de sete dias. Pode configurar opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits.

- [Compreenda os backups para a MariaDB](concepts-backup.md)

- [Compreender a configuração inicial do MariaDB](tutorial-design-database-using-portal.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Política Azure - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: A Base de Dados Azure para MariaDB cria automaticamente cópias de segurança do servidor e armazena-as em armazenamento localmente redundante ou geo-redundante do utilizador. As cópias de segurança podem ser utilizadas para restaurar o servidor para um ponto no tempo.  A cópia de segurança e o restauro são uma parte essencial de qualquer estratégia de continuidade empresarial, uma vez que protegem os seus dados contra danos e a eliminação acidentais.

Se utilizar o Key Vault para encriptação de dados do lado do cliente para os dados armazenados no seu servidor MariaDB, certifique-se de cópias de segurança automáticas regulares das suas chaves.

- [Compreenda os backups para a MariaDB](concepts-backup.md)

- [Como apoiar chaves do cofre](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Política Azure - Microsoft.DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Na Base de Dados Azure para MariaDB, efetue uma restauração a partir das cópias de segurança do servidor original para testes periódicos de backups. Existem dois tipos de restauro disponíveis: restauro pontual e restauro georrefero. O restauro pontual está disponível com a opção de redundância de backup e cria um novo servidor na mesma região que o seu servidor original. O geo-restauro só está disponível se configurar o seu servidor para armazenamento geo-redundante e permitir-lhe restaurar o seu servidor numa região diferente.

O tempo estimado de recuperação depende de vários fatores, incluindo os tamanhos da base de dados, o tamanho do registo de transações, a largura de banda da rede e o número total de bases de dados que recuperam na mesma região ao mesmo tempo. O tempo de recuperação é geralmente inferior a 12 horas.

- [Compreenda o backup e o restauro na Base de Dados Azure para a MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-backup#restore)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: A base de dados Azure para MariaDB recebe cópias de segurança completas, diferenciais e de registo de transações. Estas cópias de segurança permitem restaurar um servidor em qualquer ponto no tempo dentro do período de retenção de backup configurado. O período de retenção de backup predefinido é de sete dias. Pode configurar opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits.

- [Compreenda o backup e o restauro na Base de Dados Azure para a MariaDB](concepts-backup.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na métrica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições usando tags e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente aqueles que processam dados sensíveis. É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.
    

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
