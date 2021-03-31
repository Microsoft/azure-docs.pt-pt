---
title: Linha de base de segurança Azure para Azure Private Link
description: A linha de base de segurança Azure Private Link fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8fcbfa745a78d335606c11dcbd8851a235407b9d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588533"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Linha de base de segurança Azure para Azure Private Link

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para Azure Private Link. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Azure Private Link. Foram excluídos **os controlos** não aplicáveis ao Azure Private Link.

 
Para ver como a Azure Private Link mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança Azure Private Link](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações dos recursos e detetar alterações nos recursos da sua rede relacionadas com o Link Privado. 

Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados por recursos de rede como pontos finais de Private Link, Redes Virtuais e grupos de segurança de rede. 

Dentro do Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros, com base em requisitos de negócio organizacional.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Registo e monitorização de Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview#logging-and-monitoring)

- [Registo de diagnóstico para um grupo de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar os registos de atividade do Azure Monitor, que registam operações tomadas em recursos de Ligação Privada, tais como, quem iniciou a operação, quando ocorreu a operação, o estado da operação e outras informações úteis de auditoria. 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Registo e Monitorização de Ligação Privada](https://docs.microsoft.com/azure/private-link/private-link-overview#logging-and-monitoring)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Para registos relacionados com o Link Privado, deslote o período de retenção do espaço de trabalho Log Analytics de acordo com os regulamentos de conformidade da sua organização dentro do Azure Monitor. Utilize contas de armazenamento Azure para qualquer armazenamento de registos a longo prazo/arquivo.

- [Alterar o período de retenção de dados em Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados. Utilize o log analytics workspace do Azure Monitor para rever registos e efetuar consultas nos dados de registo.

Outra opção é ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Compreender log analytics workspace](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança configurado com um espaço de trabalho Log Analytics para monitorização e alerta sobre atividades anómalas encontradas em registos de segurança e eventos.

Ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros com base nos seus requisitos de negócio organizacional.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Utilize funções de administrador incorporado do Azure Ative (Azure AD), que podem ser explicitamente atribuídas e consultadas. Execute o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize as funcionalidades de Gestão de Identidade e Acesso do Centro de Segurança para monitorizar o número de contas administrativas.

Além disso, ative o Just-In-Time / Just-Enough-Access utilizando o Azure Ative Directory (Azure AD) Funções privilegiadas de gestão de identidade privilegiada para serviços da Microsoft e gestor de recursos Azure.

- [Saiba mais sobre Gestão de Identidade Privilegiada](/azure/active-directory/privileged-identity-management/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Sempre que possível, utilize um único sinal de acesso com o Azure Ative Directory (Azure AD) em vez de configurar credenciais individuais autónomas por serviço.

- [Inscrição única para aplicações em Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Permitir a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiado (PAW) com autenticação multifactor configurada para iniciar sessão e configurar recursos de rede Azure.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Funcionalidade de Deteção de Riscos para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. 

Ingeste alertas de deteção de risco do Centro de Segurança no Azure Monitor e configurar alertas/notificações personalizadas usando Grupos de Ação.

- [Compreender deteções de risco do Centro de Segurança Azure (atividade suspeita)](../active-directory/identity-protection/overview-identity-protection.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como configurar grupos de ação para alerta e notificação personalizados](../azure-monitor/alerts/action-groups.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize locais nomeados para acesso condicional para permitir o acesso a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito e também sais, hashes e armazena de forma segura as credenciais dos utilizadores.  

- [Como criar e configurar instâncias do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring/)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Utilize as fontes de registo de registo de eventos Azure Ative (Azure AD) para integrar com qualquer ferramenta SIEM/Monitoring.

Dinamize este processo criando Definições de Diagnóstico para contas de utilizadores Azure E envie os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Configure os alertas desejados dentro do log analytics workspace.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize funcionalidades de Proteção de Risco e Identidade do Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades dos utilizadores para os seus recursos de rede. 

Ingerir dados no Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando assinaturas e grupos de gestão separados para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade aos dados.

Restringir o nível de acesso aos seus recursos Azure com as suas aplicações e ambientes empresariais com base nos requisitos de negócio.

Controlar o acesso aos recursos Azure através do controlo de acesso baseado em funções Azure (Azure AD).

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure em redes virtuais seja capaz de negociar TLS 1.2 ou superior. O Private Link não interfere com os protocolos subjacentes. Utilize as melhores práticas para outras ofertas utilizadas pelos clientes.

Siga as recomendações do Centro de Segurança para encriptação em repouso e encriptação em trânsito, quando aplicável.

- [Compreender a encriptação em trânsito com Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos 

**Orientação**: Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso a dados e recursos, de outra forma utilizar métodos de controlo de acesso específicos ao serviço.

- [Familiarize-se com uma breve descrição e o ID exclusivo de funções incorporadas de Azure aqui](../role-based-access-control/built-in-roles.md)

Invoque a PowerShell cmd "Get-AzRoleDefinition" ou "az role definition list" para recuperar uma lista de papéis no seu ambiente.

Reveja as suas opções para controlar a exposição do seu serviço através da definição "Visibilidade". em Private Link. Estas definições de visibilidade decidem se um consumidor pode ligar-se ao seu serviço. 

Torne o seu serviço privado para consumo a partir das suas outras Redes Virtuais (apenas permissões Azure RBAC). Restringir a exposição a um conjunto limitado de subscrições fidedignas, ou torná-la pública para que todas as subscrições da Azure possam solicitar ligações no serviço Private Link.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Propriedades para serviço private link](private-link-service-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize alertas de registo de atividade do Azure Monitor para criar alertas para quando ocorrerem alterações para recursos críticos do Azure, como serviços de Ligação Privada e pontos finais. 

- [Registo de diagnóstico para um grupo de segurança de rede](https://docs.microsoft.com/azure/private-link/private-link-overview#logging-and-monitoring)

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descubra todos os recursos de networking, como serviços de Private Link, e pontos finais dentro das suas subscrições.

Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas nos recursos Azure utilizando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear o Private Link e recursos conexos. 

Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos e software Azure aprovados para obter recursos computacionais com base nas suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Utilize também o Gráfico de Recursos Azure para consultar/descobrir recursos dentro das subscrições. Isto pode ajudar em ambientes de alta segurança, como aqueles com contas de Armazenamento.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Amostra de política Azure incorporada para ligação privada](../governance/policy/samples/built-in-policies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources"></a>6.7: Remover os recursos não aprovados do Azure

**Orientação**: O cliente pode impedir a criação ou utilização de recursos com a Política Azure, conforme exigido pelas políticas da empresa do cliente. Pode implementar o seu próprio processo de remoção de recursos não autorizados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Amostra de política Azure incorporada para ligação privada](../governance/policy/samples/built-in-policies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos de rede Azure juntamente com as definições de Política Azure incorporadas.

O Azure Resource Manager tem a capacidade de exportar o modelo na Notação de Objetos JavaScript (JSON). Este artefacto deve ser revisto para garantir que as configurações cumprem ou excedem os requisitos de segurança para a sua organização.

Implemente recomendações do Security Center como uma base de configuração segura para os seus recursos Azure.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Amostra de política Azure incorporada para ligação privada](../governance/policy/samples/built-in-policies.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Utilize modelos de Gestor de Recursos Azure e Política Azure para configurar de forma segura os recursos Azure associados ao Private Link e recursos conexos.  

Os modelos do Gestor de Recursos Azure são ficheiros baseados em JavaScript Object Notation (JSON) utilizados para implementar recursos Azure, quaisquer modelos personalizados terão de ser armazenados e mantidos de forma segura num repositório de código. 

Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Informação sobre a criação de modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

- [Amostras de modelo do Gestor de Recursos Azure para pontos finais privados](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Amostra de política Azure incorporada para ligação privada](../governance/policy/samples/built-in-policies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize devOps Azure para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure e scripts de Configuração do Estado Desejado.

Conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps para o seu acesso, ou Ative Directory se integrado com o Team Foundation Server.

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para recursos Azure utilizando a Política Azure. 

Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure. 

Utilize também as definições de política incorporadas relacionadas com quaisquer recursos específicos geridos ao abrigo das suas subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

- [Amostra de política Azure incorporada para ligação privada](../governance/policy/samples/built-in-policies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize a Política Azure para auditar as configurações de recursos Azure. Por exemplo, a Política Azure pode ser usada para detetar recursos, que não são configurados com um ponto final privado.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostra de política Azure incorporada para ligação privada](../governance/policy/samples/built-in-policies.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Utilize o Gestor de Recursos Azure para implementar serviços de Ligação Privada, pontos finais e recursos conexos. O Azure Resource Manager fornece a capacidade de exportar modelos, que podem ser usados como backups para restaurar os pontos finais de Private Link e recursos relacionados. 

Utilize a Azure Automation para chamar regularmente o modelo de Azure Resource Manager para exportar API.

- [Descrição geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Amostras de modelo do Gestor de Recursos Azure para pontos finais privados](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos - Modelo de exportação](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à Azure Automation](../automation/automation-intro.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Utilize o Gestor de Recursos Azure para implementar serviços de Ligação Privada, pontos finais e recursos conexos. O Azure Resource Manager fornece a capacidade de exportar modelos, que podem ser usados como backups para restaurar os pontos finais de Private Link e recursos relacionados.

Utilize a Azure Automation para chamar regularmente o modelo de Azure Resource Manager para exportar API.

Faça o reforço das chaves geridas pelo cliente dentro do Cofre da Chave Azure.

- [Descrição geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Amostras de modelo do Gestor de Recursos Azure para pontos finais privados](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos - Modelo de exportação](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à Azure Automation](../automation/automation-intro.md)

- [Como backup chaves chave cofre em Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Validar a capacidade de executar periodicamente a implementação de modelos do Gestor de Recursos Azure regularmente para uma subscrição isolada de acordo com os requisitos do seu negócio.

Além disso, valide as restaurações de chaves geridas pelo cliente.

- [Implementar recursos com modelos ARM e portal Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Como restaurar chaves chave do cofre em Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como modelos do Azure Resource Manager.

Conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps para acesso a estes recursos, ou no Ative Directory se integrado com o Team Foundation Server.

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. 

Certifique-se de planos escritos de resposta a incidentes que definam todas as funções de pessoal e fases de manipulação ou gestão de incidentes, desde a deteção até à revisão pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Marque claramente as subscrições (por exemplo, produção, não produção) utilizando tags e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente os que processam dados sensíveis.  

O cliente é responsável por priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. 

Rever incidentes, pós-ocorrência, para garantir que as questões foram resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Alertas e recomendações do Centro de Segurança da Exportação utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. 

Além disso, utilize o conector de dados do Security Center para transmitir os alertas ao Azure Sentinel, conforme exigido pelas suas operações comerciais.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de Automatização do Fluxo de Trabalho no Centro de Segurança para ativar automaticamente respostas com aplicações lógicas em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. 

Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
