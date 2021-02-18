---
title: Linha de base de segurança Azure para Azure Data Share
description: A linha de base de segurança Azure Data Share fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: data-share
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f28020ba431d3dfdc79c2d4a98b6e6138f563aeb
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096490"
---
# <a name="azure-security-baseline-for-azure-data-share"></a>Linha de base de segurança Azure para Azure Data Share

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para a Microsoft Azure Data Share. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Azure Data Share. Foram excluídos **os controlos** não aplicáveis à Azure Data Share.

 
Para ver como a Azure Data Share mapeia completamente para o Benchmark de Segurança Azure, consulte o ficheiro completo de [mapeamento de base de base de partilha de dados Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos relacionados com a Azure Data Share via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Em alternativa, pode ativar e embarcar estes dados para a Azure Sentinel ou para um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos de partilha de dados Azure, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

Ativar registos de diagnóstico para Azure Data Share, especificamente os registos de diagnóstico do MicrosoftDataShareSentShareSnapshotsLog &amp; MicrosoftDataShareReceivedShareSnapshotsLog. Estes registos permitir-lhe-ão capturar informações chave como sincronização de início de tempo, fim de tempo, estado e outros detalhes. Estes registos podem ser cruciais para mais tarde investigar incidentes de segurança e realizar exercícios forenses.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Como configurar definições de diagnóstico para o registo de atividades azure](../azure-monitor/essentials/activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Certifique-se de que quaisquer contas de armazenamento ou espaços de trabalho do Log Analytics utilizados para armazenar registos Azure Data Share tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

- [Armazenar registos de recursos numa conta de armazenamento Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Analise e monitorize registos relacionados com os recursos de partilha de dados Azure para um comportamento anómalo e reveja regularmente os resultados. Utilize o Azure Monitor e um espaço de trabalho log Analytics para rever registos e realizar consultas nos dados de registo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Começando com consultas log analytics](../azure-monitor/logs/log-analytics-tutorial.md) 

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança Azure com o log analytics espaço de trabalho para monitorizar e alertar sobre a atividade anómala encontrada em registos de segurança e eventos. Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md) 

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: A azure Data Share suporta a autenticação SSO com o Azure Ative Directory. Reduza o número de identidades e credenciais que os utilizadores devem gerir, permitindo sSO para o serviço com as identidades pré-existentes da sua organização.

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar O Azure AD MFA e seguir recomendações de identidade e acesso do Azure Security Center.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md) 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho segura gerida pelo Azure (também conhecida como Estação de Trabalho de Acesso Privilegiado, ou PAW) para tarefas administrativas que exijam privilégios elevados.

- [Compreenda estações de trabalho seguras e geridas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

- [Como ativar O Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Azure Data Share trabalha com o General Azure construído em funções ](../role-based-access-control/built-in-roles.md#general)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: A Azure AD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a identidade AZure E access reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml) 

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Azure Data Share trabalha com o General Azure construído em funções ](../role-based-access-control/built-in-roles.md#general)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a atividade de login, auditoria e fontes de registo de eventos de risco, que lhe permitem integrar-se com qualquer ferramenta SIEM/monitor.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do espaço de trabalho do Log Analytics.

- [Como integrar registos de atividades do Azure com o Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de Proteção de Identidade Ad Azure para configurar respostas automatizadas para detetar ações suspeitas relacionadas com as identidades dos utilizadores. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso a dados e recursos relacionados com os recursos Azure Data Share, de outra forma utilizar métodos de controlo de acesso específicos ao serviço.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md) 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas do Monitor Azure para quando ocorrerem alterações para recursos críticos do Azure.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md) 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte o [Azure Security Benchmark: Gestão de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: A Azure Data Share não permite a instalação de ferramentas de digitalização de vulnerabilidades para os seus recursos.

Em geral, siga as recomendações do Azure Security Center para a realização de avaliações de vulnerabilidade nas suas máquinas virtuais Azure, imagens de contentores e servidores SQL.

Utilize uma solução de terceiros para realizar avaliações de vulnerabilidade em dispositivos de rede e aplicações web. Ao realizar exames remotos, não utilize uma única conta administrativa perpétua. Considere implementar a metodologia de provisionamento do JIT para a conta de digitalização. As credenciais para a conta de digitalização devem ser protegidas, monitorizadas e utilizadas apenas para a verificação de vulnerabilidades.

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md) 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Aplica etiquetas aos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.
Utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das suas subscrições. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Remova os recursos azuis quando já não são necessários, isto pode ser feito através do portal Azure, PowerShell ou CLI.

- [Grupo de recursos Azure e supressão de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)

O Azure Data Share não expõe o SISTEMA nem permite instalar aplicações de software de terceiros nos seus recursos.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para restringir os serviços que pode prestação no seu ambiente.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general) 

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como definições personalizadas de Política Azure, modelos de Gestor de Recursos Azure e scripts de configuração de estado desejados. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "DataShare" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como usar pseudónimos](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente em Azure AD. As identidades geridas permitem-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como configurar identidades geridas para os recursos da Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)