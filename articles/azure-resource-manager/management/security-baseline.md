---
title: Linha de segurança Azure para gestor de recursos Azure
description: A linha de base de segurança do Azure Resource Manager fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b4f0c64abf150165e3239e78e8cf9b824b34eed6
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202373"
---
# <a name="azure-security-baseline-for-azure-resource-manager"></a>Linha de segurança Azure para gestor de recursos Azure

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../../security/benchmarks/overview-v1.md) ao Microsoft Azure Resource Manager. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Gestor de Recursos Azure. Foram excluídos **os controlos** não aplicáveis ao Gestor de Recursos Azure.

 Para ver como o Azure Resource Manager mapeia completamente para o Benchmark de Segurança Azure, consulte o ficheiro completo de [mapeamento de base de base do Azure Resource Manager](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingeste azure Policy registos de atividade via Azure Monitor. Dentro do Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo ou arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md) 

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../../azure-monitor/learn/quick-collect-azurevm.md) 

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: O Gestor de Recursos Azure utiliza registos de atividade, que são automaticamente habilitados, para incluir fonte de evento, data, utilizador, timetamp, endereços de origem, endereços de destino e outros elementos úteis.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../../azure-monitor/platform/platform-logs-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados. Utilize o Azure Monitor e um espaço de trabalho log Analytics para rever registos e realizar consultas nos dados de registo. 

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Começando com consultas log analytics](../../azure-monitor/log-query/log-analytics-tutorial.md) 

- [Como realizar consultas personalizadas no Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança Azure com o Log Analytics para monitorização e alerta sobre a atividade anómala encontrada nos registos de atividade. Em alternativa, pode ativar e a bordo dados para a Azure Sentinel. 

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Como gerir alertas no Centro de Segurança Azure](../../security-center/security-center-managing-and-responding-alerts.md) 

- [Como alertar os dados do registo do Log Analytics](../../azure-monitor/learn/tutorial-response.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O controlo de acesso baseado em funções (RBAC) permite-lhe gerir o acesso aos recursos Azure através de atribuições de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas pré-definidas para determinados recursos, e estas funções podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, pode utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Também pode ativar um acesso Just-In-Time utilizando a Azure AD Privileged Identity Management e a Azure Resource Manager. 

- [Saiba mais sobre Gestão de Identidade Privilegiada](../../active-directory/privileged-identity-management/index.yml)

- [Como usar a Política Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas por serviço. Use recomendações de identidade e acesso do Azure Security Center. 

- [Compreender SSO com Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a autenticação multi-factor do Diretório Ativo Azure e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como ativar o MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho segura gerida pelo Azure (também conhecida como Estação de Trabalho de Acesso Privilegiado, ou PAW) para tarefas administrativas que exijam privilégios elevados.

- [Compreenda estações de trabalho seguras e geridas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como ativar O Azure AD MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory e monitoriem para detetar quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerir os recursos do Azure apenas a partir de locais aprovados

**Orientação**: Utilize localizações nomeadas a Azure AD para permitir o acesso apenas a agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar a Azure AD nomeou localizações](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize localizações nomeadas a Azure AD para permitir o acesso apenas a agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar a Azure AD nomeou localizações](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: A Azure AD fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a identidade AZure E access reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado. 

- [Compreenda a Azure AD reportando](../../active-directory/reports-monitoring/index.yml)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a atividade de login, auditoria e fontes de registo de eventos de risco, que lhe permitem integrar-se com qualquer ferramenta SIEM/monitor.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do espaço de trabalho do Log Analytics.

- [Como integrar registos de atividades do Azure com o Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de Proteção de Identidade Ad Azure para configurar respostas automatizadas para detetar ações suspeitas relacionadas com as identidades dos utilizadores. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](./tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Utilize o Azure AD RBAC para controlar o acesso a dados e recursos, de outra forma utilizar métodos de controlo de acesso específicos ao serviço.

- [Como configurar o RBAC no Azure](../../role-based-access-control/role-assignments-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Para encriptação do lado do servidor em repouso, o Azure Resource Manager suporta as teclas geridas pela Microsoft.

- [Compreender a proteção de dados no Azure Resource Manager](azure-resource-manager-security-controls.md#data-protection)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas quando ocorrerem alterações para recursos críticos do Azure.

- [Como criar alertas para eventos de registo de atividades do Azure](../../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, e assim por diante) nas suas subscrições. Certifique-se de permissões (leia) adequadas no seu inquilino e enumere todas as subscrições do Azure, bem como os recursos nas suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph Explorer, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../../governance/resource-graph/first-query-portal.md)

- [Como ver as suas subscrições Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Compreender Azure RBAC](../../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Use o nome da política, descrição e categoria para organizar logicamente os ativos de acordo com uma taxonomia.

- [Para obter mais informações sobre a marcação de ativos, consulte o guia de decisão de nomeação de recursos e marcação](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Mais detalhes relacionados são fornecidos abaixo,

- [Como criar subscrições adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](./tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.

Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Mais detalhes relacionados são fornecidos abaixo,

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../../governance/policy/samples/built-in-policies.md#general)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional AD Azure para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts em recursos computacional

**Orientação**: Não aplicável; esta recomendação destina-se a recursos computacional.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos Azure. Também pode utilizar definições de Política Azure incorporadas.

O Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem os requisitos de segurança para a sua organização.

Também pode utilizar as recomendações do Azure Security Center como uma base de configuração segura para os seus recursos Azure.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

Além disso, como administrador, poderá ser necessário bloquear uma subscrição, um grupo de recursos ou um recurso para evitar que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos. Pode definir o nível do bloqueio para CanNotDelete ou ReadOnly.

- [Compreender os efeitos da Política Azure](../../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../templates/overview.md)

- [Como bloquear recursos para evitar mudanças inesperadas](lock-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como definições personalizadas de Política Azure, modelos de Gestor de Recursos Azure e scripts de configuração de estado desejados. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para recursos Azure utilizando a Política Azure. Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure. Também pode utilizar definições políticas incorporadas relacionadas com os seus recursos específicos.  Além disso, pode utilizar a Azure Automation para implementar alterações de configuração.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](../../governance/policy/concepts/definition-structure.md#aliases)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de Política Azure incorporadas, bem como políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Utilize práticas recomendadas ao construir o seu modelo ARM, estas recomendações ajudam-no a evitar problemas comuns ao utilizar um modelo ARM para implementar uma solução.

Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Implementar as melhores práticas de segurança do modelo](../templates/template-best-practices.md)

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [Azure Security Benchmark: Data Recovery](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Garantir a capacidade de executar periodicamente a implementação de modelos do Gestor de Recursos Azure regularmente para uma subscrição isolada, se necessário.

- [Implementar recursos com modelos ARM e portal Azure](../templates/deploy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como definições personalizadas de Política Azure, modelos de Gestor de Recursos Azure e scripts de configuração de estado desejados. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Desenvolva um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções do pessoal, bem como as fases de tratamento e gestão de incidentes, desde a deteção até à revisão pós-incidente. 

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Utilize o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e, em seguida, reveja o seu plano de resposta conforme necessário.

- [Publicação do NIST--Guia para Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação contínua para ajudar a identificar riscos para os recursos da Azure. A exportação contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização de fluxos de trabalho Azure Security Center para acionar automaticamente respostas a alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho no Centro de Segurança](../../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md)