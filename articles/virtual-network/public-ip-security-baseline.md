---
title: Linha de segurança Azure para Azure Public IP
description: A linha de base de segurança Azure Public IP fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cba362004e764ffbbc734fb06c8898f380ffdf88
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806923"
---
# <a name="azure-security-baseline-for-azure-public-ip"></a>Linha de segurança Azure para Azure Public IP

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para Azure Public IP. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Public IP. Foram excluídos **os controlos** não aplicáveis ao Azure Public IP.  Nota Azure Public IPs não armazenam dados do cliente.

Para ver como o Azure Public IP mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança IP do Azure.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Podem ser atribuídas etiquetas Azure Public IPs. Utilize etiquetas de recursos para grupos de segurança de rede e outros recursos relacionados com a segurança da rede.  Utilize qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" que garantam que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.  

O Azure PowerShell ou o Azure CLI podem ser usados para procurar ou executar ações em recursos baseados nas suas etiquetas. 

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md) 

- [Como criar uma Rede Virtual Azure](quick-create-portal.md) 

- [Como filtrar o tráfego da rede com regras do grupo de segurança de rede](tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações e detetar alterações nas suas instâncias IP públicas. Para além do plano de controlo (por exemplo, portal Azure), o próprio IP público não gera registos relacionados com o tráfego de rede.

O IP público fornece ferramentas para monitorizar, diagnosticar, visualizar métricas e ativar ou desativar registos de recursos numa rede virtual Azure.

Em vez disso, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações e detetar alterações para as suas instâncias ip públicas. Para além do plano de controlo (por exemplo, portal Azure), o próprio IP público não gera registos de auditoria. O IP público fornece ferramentas para monitorizar, diagnosticar, visualizar métricas e ativar ou desativar registos de recursos numa rede virtual Azure.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Utilize o Azure Monitor para definir o período de retenção de registos para os espaços de trabalho do Log Analytics associados a instâncias IP públicas de acordo com as obrigações de conformidade da sua organização.

- [Como definir parâmetros de retenção de registos](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: O IP público fornece ferramentas para monitorizar, diagnosticar, visualizar métricas e ativar ou desativar registos de recursos numa rede virtual Azure. 

Utilize o Registo de Atividades Azure para monitorizar as configurações e detetar alterações para as suas instâncias IP públicas. 

O próprio IP público não gera registos relacionados com o tráfego de rede que não seja no plano de controlo (por exemplo, portal Azure).

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Configure os seus alertas com base em registos de atividades relacionados com o IP público. Utilize o Azure Monitor para configurar um alerta para enviar uma notificação de e-mail, ligar para um webhook ou invocar uma App Azure Logic.

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Use o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso a recursos Azure, como instâncias IP públicas com atribuições de funções. Atribua estas funções a utilizadores, grupos, diretores de serviços e identidades geridas. 

Os papéis incorporados pré-definidos do Azure são inventariados para determinados recursos através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. 

Acesso just-in-time através da utilização do Azure Ative Directory (Azure AD) Privileged Identity Management (PIM) e do Azure Resource Manager. 

- [Saiba mais sobre Gestão de Identidade Privilegiada](../active-directory/privileged-identity-management/index.yml)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a autenticação multi-factor do Diretório Ativo Azure e seguir as recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiado (PAW) com autenticação multi-factor AD (MFA) ativada para iniciar sessão e configurar os seus recursos relacionados com o Azure Sentinel.

- [Estações de Trabalho de Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planear a implementação da Multi-Factor Authentication do Azure AD com base na cloud](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

Rever e agir Azure AD deteções de risco para alertas e relatórios sobre comportamento de utilizador de risco.

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerir os recursos do Azure apenas a partir de locais aprovados

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso ao portal Azure apenas a partir de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Descubra contas velhas com registos no Azure Ative Directory (Azure AD). 

Use a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que os utilizadores tenham aprovado e acedido continuamente.

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Implementar a integração com qualquer ferramenta SIEM/monitoring com base no seu acesso à atividade de entrada, auditoria e registo de eventos de risco Azure Ative.
Dinamize este processo criando definições de diagnóstico para contas de utilizadores Azure AD e enviando os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Configure os alertas desejados dentro do espaço de trabalho Log Analytics. 

- [Como integrar registos de atividades do Azure com o Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de Proteção de Identidade Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Ingerir dados no Azure Sentinel para uma investigação mais aprofundada conforme desejado e com base nos requisitos do negócio.
- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos e assim por diante) dentro das suas subscrições. Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições do Azure, bem como os recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado que crie e utilize recursos do Azure Resource Manager para a frente.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.

Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Definir e implementar configurações de segurança padrão para Azure Public IP com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus casos de IP públicos Azure. Também pode utilizar definições políticas incorporadas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma política personalizada com pseudónimos políticos](../governance/policy/tutorials/create-custom-policy-definition.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure, utilize Azure DevOps ou Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para Azure Public IP com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus casos de IP públicos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar uma política personalizada com pseudónimos políticos](../governance/policy/tutorials/create-custom-policy-definition.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar definições personalizadas de Política Azure para alertar, auditar e impor configurações do sistema. Use a Política Azure [auditoria], [negar], e [implementar se não existir] para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar a automatização do fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Os clientes também podem utilizar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Seu Centro de Segurança utilizando a função Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)