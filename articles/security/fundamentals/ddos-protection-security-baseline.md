---
title: Linha de base de segurança Azure para Azure DDoS Protection Standard
description: A linha de base de segurança Azure DDoS Protection Standard fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1c1a5a96742d380ce42c0aea8c77a199083df47f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492255"
---
# <a name="azure-security-baseline-for-azure-ddos-protection-standard"></a>Linha de base de segurança Azure para Azure DDoS Protection Standard

Esta linha de base de segurança aplica orientações do Benchmark de [Segurança Azure](../benchmarks/overview.md) à norma Azure DDoS Protection. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à DDoS Protection. Foram excluídos **os controlos** não aplicáveis à Proteção DDoS. Para ver como a DDoS Protection mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança DDoS Protection](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte [o controlo de segurança: Registar e monitorizar](../benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho log Analytics, centro de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram realizadas nos seus planos de Proteção DDoS Azure ao nível do plano de controlo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para as suas instâncias de Proteção Azure DDoS.

- [Como ativar definições de diagnóstico para registo de atividades azure](../../azure-monitor/platform/activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Pode selecionar qualquer uma das métricas de proteção DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque, utilizando a configuração de alerta do Monitor Azure. Quando as condições são satisfeitas, o endereço especificado recebe um e-mail de alerta.

Ativar as definições de diagnóstico do Registo de Atividades Azure e enviar os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram realizadas na sua Cache Azure para instâncias Redis ao nível do plano de controlo. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas ao nível do plano de controlo para as suas instâncias de Proteção Azure DDoS.

- [Como configurar alertas para métricas de proteção do DDoS](../../ddos-protection/telemetry-monitoring-alerting.md#configure-alerts-for-ddos-protection-metrics)

- [Como ativar definições de diagnóstico para registo de atividades azure](../../azure-monitor/platform/activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados aos seus planos de Proteção Azure DDoS de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de registos](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados do Registo de Atividade que podem ter sido recolhidos para cofres dos Serviços de Recuperação.

- [Informações sobre como aceder à telemetria, registos e análise de ataque para o serviço DDoS Protection Standard](../../ddos-protection/telemetry-monitoring-alerting.md#configure-alerts-for-ddos-protection-metrics)

- [Como ativar as definições de diagnóstico para registo de atividades Azure](../../azure-monitor/platform/activity-log.md)

- [Como recolher e analisar registos de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](../../azure-monitor/platform/activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Alertas de configuração e análise de ataque. A Azure DDoS Protection identifica e atenua os ataques DDoS sem qualquer intervenção do utilizador.

A bordo de um espaço de trabalho Log Analytics para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança. Além disso, pode criar alertas de registo personalizados no seu espaço de trabalho Log Analytics utilizando o Azure Monitor.

- [Como configurar alertas para métricas de DDoS](https://azure.microsoft.com/blog/holiday-season-is-ddos-season/)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Criar, visualizar e gerir alertas de registo usando o Azure Monitor](../../azure-monitor/platform/alerts-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para obter mais informações, consulte [controlo de segurança: Identidade e controlo de acesso.](../benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Para trabalhar com planos de proteção DDoS, a sua conta deve ser atribuída à função de contribuinte de rede ou a uma função personalizada que seja atribuída às ações apropriadas.

Além disso, o Azure Ative Directory (AD) tem funções incorporadas que devem ser explicitamente atribuídas e são consultadas. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Compreender permissões na proteção Azure DDoS](../../ddos-protection/manage-permissions.md)

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: A Azure AD não tem o conceito de senhas padrão. Outros recursos Azure que requerem uma palavra-passe forçam a criação de uma palavra-passe com requisitos de complexidade e um comprimento mínimo de senha, que difere consoante o serviço. É responsável por aplicações de terceiros e serviços de marketplace que possam utilizar senhas padrão.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, pode utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição

- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](../../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Utilize um registo de aplicações Azure (principal serviço) para recuperar um token que pode ser usado para interagir com os seus planos de Proteção DDoS através de chamadas API.

- [Como chamar Azure REST APIs](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registar a sua aplicação ao cliente (principal serviço) com a Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informações sobre Azure DDos Protection API](/rest/api/virtual-network/)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multi-factor do Diretório Ativo Azure e seguir as recomendações do Azure Security Center Identity and Access Management.

- [Como ativar o MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas 

**Orientação**: Utilize uma estação de trabalho segura gerida pelo Azure com a autenticação multi-factor Ad (MFA) ativada para iniciar sessão e configurar os seus pedidos de Bloqueio de Cliente Azure.

- [Implementar uma estação de trabalho segura e gerida pelo Azure](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Planejando uma implementação de autenticação multi-factor Azure AD baseada na nuvem](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Além disso, utilize a deteção de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco.

- [Como implementar gestão de identidade privilegiada](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender a deteção de risco azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize localizações nomeadas a Azure AD para permitir o acesso ao portal Azure a partir de apenas agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar a Azure AD nomeou localizações](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização, sempre que aplicável. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar instâncias do Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para o ajudar a descobrir contas velhas. Além disso, utilize avaliações de acesso Azure AD para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

- [Compreenda a Azure AD reportando](../../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários de acesso a Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização, sempre que aplicável. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

Tem acesso a atividade de login AD, fontes de registo de auditoria e eventos de risco, que lhe permitem integrar-se com a Azure Sentinel ou com um SIEM de terceiros.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas de registo pretendidos no Log Analytics.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Para desvio de comportamento de sinalização de conta no plano de controlo (por exemplo, portal Azure), utilize recursos de proteção de identidade AZure AD e funcionalidades de deteção de riscos para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver a Azure AD a entrar em risco](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [o controlo de segurança: proteção de dados](../benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos

**Orientação**: Para trabalhar com os planos de proteção DDoS da Azure, a sua conta deve ser atribuída à função de contribuinte de rede ou a uma função personalizada que seja atribuída a ações específicas.

- [Gerir o controlo de acesso baseado em funções Azure (Azure RBAC) em Azure DDoS Protection](../../ddos-protection/manage-ddos-protection.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações nos planos de proteção do Azure DDoS, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](../../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para obter mais informações, consulte [controlo de segurança: Inventário e gestão de ativos.](../benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) dentro das suas subscrições.  Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado que crie e utilize recursos do Azure Resource Manager para a frente.

- [Como criar consultas com gráfico de recursos Azure](../../governance/resource-graph/first-query-portal.md)

- [Como ver as suas subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizá-los logicamente de acordo com uma taxonomia.

- [Como criar e usar tags](../../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

- [Como criar subscrições adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

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

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [o controlo de segurança: Configuração segura](../benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Definir e implementar configurações de segurança padrão para a Proteção de DDos Azure com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração dos cofres dos seus Serviços de Recuperação.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos da Política Azure](../../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte [o controlo de segurança: defesa contra malware.](../benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Anti-malware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure DDoS Protection), no entanto não funciona com conteúdo do cliente.

É da sua responsabilidade pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados. A Microsoft não pode aceder aos dados dos clientes e, portanto, não pode realizar análises anti-malware do conteúdo do cliente em seu nome.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [controlo de segurança: Resposta a incidentes](../benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia do Microsoft Security Response Center de um incidente com sSIRP](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Alavancar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) usando tags e criar um sistema de nomeação para identificar e categorizar claramente os recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

Teste as suas suposições sobre como os seus serviços responderão a um ataque gerando tráfego contra as suas aplicações para simular um ataque DDoS. Não espere que aconteça um ataque. A Microsoft estabeleceu uma parceria com a Ixia, uma empresa de Keysight, para fornecer um gerador de tráfego de autosserviço (BreakingPoint Cloud) que permite aos clientes da Azure DDoS Protection simular tráfego de teste DDoS contra os seus pontos finais públicos Azure.

- [Validação de proteção DDoS da Microsoft Azure](https://www.ixiacom.com/products/breakingpoint-cloud)

- [Publicação do NIST - Guia de Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos da Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

Selecione qualquer uma das métricas de proteção DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque, utilizando a configuração de alerta do Monitor Azure. Quando as condições são satisfeitas, o endereço especificado recebe um e-mail de alerta

- [Alertas de configuração para métricas de proteção DDoS](../../ddos-protection/telemetry-monitoring-alerting.md#configure-alerts-for-ddos-protection-metrics)

- [Como configurar a exportação contínua](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte [o controlo de segurança: Testes de penetração e exercícios de equipa vermelha](../benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../benchmarks/security-baselines-overview.md)