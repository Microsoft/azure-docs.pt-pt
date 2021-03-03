---
title: Linha de segurança Azure para o Farol de Azure
description: A linha de base de segurança do Farol Azure fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 69234d7c22f1725f6f21fe52a455e64d743f052e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709857"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Linha de segurança Azure para o Farol de Azure

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para o Farol de Azure. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Farol Azure. Foram excluídos **os controlos** não aplicáveis ao Farol de Azure.

Para ver como o Azure Lighthouse mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança do Farol Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: O Farol Azure utiliza o Azure Ative Directory (Azure AD) como o serviço de gestão de identidade e acesso padrão. Padronize a Azure AD para governar a gestão de identidade e acesso da sua organização em:
- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machine (Linux e Windows), Azure Key Vault, PaaS e SaaS.
- recursos da sua organização, como as aplicações no Azure, ou os recursos da rede empresarial.

Com o Azure Lighthouse, os utilizadores designados num inquilino gerente têm uma função Azure incorporada que lhes permite aceder a subscrições delegadas e/ou grupos de recursos no inquilino de um cliente. Todas as funções incorporadas são atualmente suportadas, exceto para Proprietário ou quaisquer funções incorporadas com permissão DataActions. A função de Administrador de Acesso ao Utilizador é suportada apenas para uso limitado na atribuição de funções a identidades geridas. As funções personalizadas e as funções clássicas de administrador de subscrição não são suportadas.

- [Inquilinos no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Utilizar fornecedores de identidade externos para a aplicação](../active-directory/external-identities/identity-providers.md) 

- [O que é a pontuação de segurança de identidade no Azure Active Directory?](../active-directory/fundamentals/identity-secure-score.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir identidades de aplicação de forma segura e automática

**Orientação**: As identidades geridas pelo Azure podem autenticar para os serviços e recursos da Azure que suportam a autenticação AZure. A autenticação é ativada através de regras de concessão de acesso pré-definidas, evitando credenciais codificadas em código fonte ou ficheiros de configuração. Com o Azure Lighthouse, os utilizadores com a função de Administrador de Acesso ao Utilizador na subscrição de um cliente podem criar uma identidade gerida no inquilino desse cliente. Embora esta função não seja geralmente suportada com o Farol Azure, pode ser usada neste cenário específico, permitindo aos utilizadores com esta permissão atribuir uma ou mais funções incorporadas específicas a identidades geridas.

Para serviços que não suportem identidades geridas, utilize a Azure AD para criar um principal de serviço com permissões restritas ao nível dos recursos. O Azure Lighthouse permite que os diretores de serviços acedam aos recursos dos clientes de acordo com as funções que lhes são concedidas durante o processo de embarque. Recomenda-se configurar os principais do serviço com credenciais de certificado e voltar aos segredos dos clientes. Em ambos os casos, o Azure Key Vault pode ser usado em conjunto com identidades geridas aZure, de modo que o ambiente de tempo de execução (como uma função Azure) pode recuperar a credencial do cofre chave.

- [Identidades geridas por Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Principal de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Use o cofre da chave Azure para o registo principal de segurança](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Criar um utilizador que possa atribuir funções a uma identidade gerida num inquilino de cliente](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: Exija autenticação multi-factor (MFA) para todos os utilizadores do seu inquilino gerente, incluindo utilizadores que tenham acesso a recursos delegados do cliente. Sugerimos que peça aos seus clientes que implementem mFA nos seus inquilinos também.

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: A Azure AD fornece as seguintes fontes de dados: 

-   Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

-   Registos de auditoria - Fornece rastreabilidade através de registos para todas as alterações escoradas através de várias funcionalidades em Azure AD. Exemplos de alterações registadas nos registos de auditoria incluem adicionar ou remover utilizadores, apps, grupos, funções e políticas.

-   Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

-   Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Estas fontes de dados podem ser integradas com sistemas Azure Monitor, Azure Sentinel ou siem de terceiros.

Os prestadores de serviços que utilizam o Farol Azure podem encaminhar os registos AZure AD para Azure Sentinel e ver/definir alertas entre os inquilinos para monitorizar e alertar sobre anomalias de conta.

- [Relatórios de atividades de auditoria em Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Gerir espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

- [Ligue os dados do Azure AD ao Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso aos recursos do Azure com base em condições

**Orientação**: O Farol Azure não suporta uma capacidade de acesso condicional para recursos delegados do cliente. No inquilino gerente, utilize o acesso condicional Azure AD para um controlo de acesso mais granular com base em condições definidas pelo utilizador, tais como exigir logins de utilizadores de determinadas gamas IP até à utilização de Autenticação Multi-Factor (MFA). Uma gestão de sessão de autenticação granular também pode ser usada através da política de acesso condicional Azure AD para diferentes casos de uso. 

Você deve exigir MFA para todos os utilizadores do seu inquilino gerente, incluindo utilizadores que terão acesso a recursos de clientes delegados. Sugerimos que peça aos seus clientes que implementem mFA nos seus inquilinos também.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md)

- [Políticas comuns de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar a gestão de sessões de autenticação com o Acesso Condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteger e limitar utilizadores com muitos privilégios

**Orientação**: Limite o número de contas de utilizador altamente privilegiadas e proteja estas contas a um nível elevado. Uma conta Administrador Global não é necessária para ativar e utilizar o Farol Azure.

Para aceder aos dados do Registo de Atividade ao nível do inquilino, deve ser atribuída uma conta à função incorporada do Leitor de Monitorização Azure no âmbito raiz (/). Uma vez que a função de Monitoring Reader no âmbito raiz é um amplo nível de acesso, recomendamos que atribua esta função a uma conta principal de serviço, em vez de a um utilizador individual ou a um grupo. Esta atribuição deve ser executada por um utilizador que tenha a função de Administrador Global com acesso adicional elevado. Este acesso elevado deve ser adicionado imediatamente antes de então fazer a atribuição da função e, em seguida, removido quando a atribuição estiver concluída.

- [Permissões das funções de administrador no Azure AD](../active-directory/roles/permissions-reference.md)

- [Atribuição de acesso a dados de registo de atividade ao nível do inquilino](how-to/monitor-delegation-changes.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

**Orientação**: O Azure Lighthouse utiliza o controlo de acesso baseado em funções Azure (Azure RBAC) para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado às subscrições e grupos de gestão em que se encontram.

Certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para executar as suas tarefas específicas.

Certifique-se de que também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, tais como Controladores de Domínio de Diretório Ativo (DCs), ferramentas de segurança e ferramentas de gestão do sistema com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Melhores práticas para definir utilizadores e funções para o Farol de Azure](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: O Azure Lighthouse utiliza contas Azure Ative Directory (Azure AD) para gerir os seus recursos, rever as contas dos utilizadores e aceder regularmente para garantir que as contas e o seu acesso são válidos. Você pode usar comentários de acesso Azure AD para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Os clientes podem rever o nível de acesso concedido aos utilizadores no inquilino gerente através do Farol Azure no portal Azure. Podem remover este acesso a qualquer momento.

Além disso, a Azure Privileged Identity Management também pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente.

Nota: que alguns serviços da Azure apoiam utilizadores e funções locais que não foram geridos através do Azure AD. Terá de gerir estes utilizadores separadamente.

- [Criar uma revisão de acesso das funções de recursos Azure em Gestão de Identidade Privilegiada (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Remoção do acesso a uma delegação](how-to/remove-delegation.md)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Visualização da página de prestadores de serviços no portal Azure](how-to/view-manage-service-providers.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurar o acesso de emergência no AAD

**Orientação**: O Farol Azure está integrado com o Azure Ative Directory para gerir os seus recursos. Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são, normalmente, altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou de “interrupção de emergência”, em que as contas administrativas normais não podem ser utilizadas.

Deve garantir que as credenciais (por exemplo, palavra-passe, certificado ou smart card) das contas de acesso de emergência são mantidas em segurança e só são conhecidas por indivíduos que estão autorizados a utilizá-las apenas para uma emergência.

- [Gerir contas de acesso de emergência no AAD](../active-directory/roles/security-emergency-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: O Farol Azure está integrado com o Azure Ative Directory (Azure AD) para gerir os seus recursos. Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.

- [O que são avaliações de acesso AZure AD](../active-directory/governance/access-reviews-overview.md) 

- [O que é a gestão de direitos da AD Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilizar estações de trabalho privilegiadas

**Orientação**: As estações de trabalho seguras e isoladas são de importância crítica para a segurança de funções confidenciais, por exemplo, administradores, programadores e operadores de serviço de importância crítica. Dependendo dos seus requisitos, pode utilizar estações de trabalho de utilizador altamente seguras e/ou Azure Bastion para executar tarefas administrativas com o Farol Azure em ambientes de produção. Utilize o Azure Active Directory, a Proteção Avançada Contra Ameaças do Microsoft Defender (ATP) e/ou o Microsoft Intune para implementar uma estação de trabalho de utilizador gerida e segura para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor a configuração segura, incluindo a autenticação forte, linhas de base de software e hardware, e acesso lógico e de rede restrito. 

- [Compreender estações de trabalho de acesso privilegiada](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Implementar uma estação de trabalho de acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo) 

**Orientação**: O Farol Azure está integrado com o controlo de acesso baseado em funções (RBAC) para gerir os seus recursos. O RBAC do Azure permite-lhe gerir o acesso aos recursos do Azure através da atribuição de funções. Pode atribuir estas funções incorporadas a utilizadores, grupos, diretores de serviço e identidades geridas. Existem funções incorporadas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas com ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. Os privilégios que atribuir aos recursos através do RBAC do Azure devem estar sempre limitados àquilo que as funções requerem. Esta abordagem complementa a abordagem just-in-time (JIT) do Azure AD Privileged Identity Management (PIM) e deve ser revista periodicamente. Utilize funções incorporadas para alocar a permissão e apenas criar funções personalizadas quando necessário.

O Farol Azure permite o acesso aos recursos delegados do cliente utilizando funções incorporadas da Azure. Na maioria dos casos, você vai querer atribuir estas funções a um grupo ou diretor de serviço, em vez de a muitas contas individuais de utilizadores. Isto permite-lhe adicionar ou remover o acesso a utilizadores individuais sem ter de atualizar e reeditar o plano quando os seus requisitos de acesso mudarem.

Para delegar os recursos do cliente a um inquilino gerente, uma implantação deve ser feita por uma conta não hóspede no inquilino do cliente que tem o papel incorporado do Proprietário para a subscrição ser a bordo (ou que contém os grupos de recursos que estão a ser a bordo).

- [Compreender inquilinos, utilizadores e papéis no Farol de Azure](concepts/tenants-users-roles.md)

- [Como aplicar o princípio do menor privilégio ao Farol de Azure](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades destas equipas são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Nota: para obter visibilidade para cargas de trabalho e serviços, poderão ser necessárias permissões adicionais. 

- [Descrição Geral da Função de Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso aos metadados e inventário dos recursos

**Orientação**: As equipas de segurança dos clientes podem rever os registos de atividades para ver a atividade tomada pelos prestadores de serviços que utilizam o Farol Azure. 

Se um prestador de serviços quiser permitir que a sua equipa de segurança reveja os recursos delegados do cliente, as autorizações da equipa de segurança devem incluir o papel incorporado no Reader.

- [Como um cliente pode rever a atividade do prestador de serviços](how-to/view-service-provider-activity.md)

- [Como especificar funções ao embarcar um cliente para o Farol de Azure](how-to/onboard-customer.md#define-roles-and-permissions)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilizar apenas os serviços do Azure aprovados

**Orientação**: Utilize a Política Azure para auditar e restringir quais os serviços que os utilizadores podem prestação no seu ambiente. Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições. Também pode utilizar o Azure Monitor para criar regras para acionar alertas quando um serviço não aprovado for detetado.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garanta a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Remova o acesso aos recursos que tenham sido delegados através do Farol Azure uma vez que já não sejam necessários, para que os prestadores de serviços deixem de ter acesso. O acesso pode ser removido pelo cliente ou pelo prestador de serviços. 

- [Remover o acesso a uma delegação](how-to/remove-delegation.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: O Farol Azure está integrado com o Azure Ative Directory (Azure AD) para identidade e autenticação. Pode utilizar o Azure Conditional Access para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Permitir a deteção de ameaças para recursos Azure

**Orientação**: Através do Farol Azure, pode monitorizar os recursos Azure dos seus clientes para potenciais ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir falsos positivos para os analistas classificarem. Os alertas podem ser obtidos a partir de dados de registo, agentes ou outros dados.

Utilize a capacidade de deteção de ameaças incorporada do Azure Security Center, que se baseia na monitorização da telemetria de serviço Azure e na análise de registos de serviço. Os dados são recolhidos utilizando o agente Log Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos do sistema e copia os dados para o seu espaço de trabalho para análise. 

Além disso, use o Azure Sentinel para construir regras de análise, que caçam ameaças que correspondem a critérios específicos em todo o ambiente do seu cliente. As regras geram incidentes quando os critérios são combinados, para que possa investigar cada incidente. O Azure Sentinel também pode importar inteligência de ameaça de terceiros para aumentar a sua capacidade de deteção de ameaças. 

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md)

- [Guia de referência do Centro de Segurança Azure alerta](../security-center/alerts-reference.md)

- [Criar regras de análise personalizadas para detetar ameaças](../sentinel/tutorial-detect-threats-custom.md)

- [Gerir espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: Através do Farol Azure, pode utilizar o Azure Security Center para alertar sobre certas atividades suspeitas nos inquilinos do cliente que gere, como um número excessivo de tentativas de autenticação falhadas e contas depreciadas na subscrição.

O Azure Ative Directory (Azure AD) fornece os seguintes registos de utilizador que podem ser vistos em relatórios AD Azure ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas siem/monitorização para casos de monitorização e utilização analíticos mais sofisticados:
- Iniciar s nota – O relatório de inscrição fornece informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.
- Insusição arriscada - Um sinal de risco é um indicador para uma tentativa de inscrição que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador.
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Centro de Segurança do Azure também pode alertar para determinadas atividades suspeitas, como tentativas excessivas de autenticações mal-sucedidas ou contas descontinuadas na subscrição. Além da monitorização básica da higiene de segurança, o módulo Proteção Contra Ameaças do Centro de Segurança do Azure também pode recolher alertas de segurança mais profundos de recursos de computação do Azure individuais (máquinas virtuais, contentores, serviços de aplicações), recursos de dados (DB SQL e armazenamento) e camadas de serviços do Azure. Esta capacidade proporciona visibilidade sobre anomalias de conta dentro dos recursos individuais. 

- [Serviços e cenários melhorados com o Farol Azure](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Ativar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

**Orientação**: Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos do Farol Azure, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

Com o Azure Lighthouse, você pode usar registos Azure Monitor de forma escalável através dos inquilinos do cliente que você está gerindo. Crie espaços de trabalho log Analytics diretamente nos inquilinos do cliente para que os dados dos clientes permanecem nos seus inquilinos em vez de serem exportados para o seu. Isto também permite uma monitorização centralizada de quaisquer recursos ou serviços suportados pelo Log Analytics, proporcionando-lhe mais flexibilidade sobre os tipos de dados que monitoriza.

Os clientes que tenham delegado subscrições para o Azure Lighthouse podem ver os dados do registo da Atividade Azure para ver todas as ações tomadas. Isto dá aos clientes total visibilidade nas operações que os prestadores de serviços estão a realizar, juntamente com operações feitas pelos utilizadores dentro do inquilino Azure Ative Directory (Azure AD) do cliente.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Monitorar recursos delegados à escala](how-to/monitor-at-scale.md)

- [Ver a atividade do fornecedor de serviços](how-to/view-service-provider-activity.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

**Orientação**: Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de Atividade Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Com o Azure Lighthouse, você pode usar registos Azure Monitor de forma escalável através dos inquilinos do cliente que você está gerindo. Crie espaços de trabalho log Analytics diretamente nos inquilinos do cliente para que os dados dos clientes permanecem nos seus inquilinos em vez de serem exportados para o seu. Isto também permite uma monitorização centralizada de quaisquer recursos ou serviços suportados pelo Log Analytics, proporcionando-lhe mais flexibilidade sobre os tipos de dados que monitoriza.

Os clientes que tenham delegado subscrições para o Azure Lighthouse podem ver os dados do registo da Atividade Azure para ver todas as ações tomadas. Isto dá aos clientes total visibilidade nas operações que os prestadores de serviços estão a realizar, juntamente com operações feitas pelos utilizadores dentro do inquilino Azure Ative Directory (Azure AD) do cliente.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Monitorar recursos delegados à escala](how-to/monitor-at-scale.md)

- [Como os clientes podem ver a atividade do prestador de serviços](how-to/view-service-provider-activity.md)

- [Gerir espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurar a retenção de armazenamento dos registos

**Orientação**: O Farol Azure não produz atualmente quaisquer registos relacionados com a segurança. Os clientes que pretendam visualizar a atividade do prestador de serviços podem configurar a retenção de registos de acordo com os requisitos de conformidade, regulação e negócio. 

No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize o espaço de trabalho Azure Storage, Data Lake ou Log Analytics para armazenamento de longo prazo e arquivo.

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [O Azure Security Center alerta e recomendações para exportar](../security-center/continuous-export.md) configuração e o cliente não é capaz de definir qualquer retenção de registo.

- [Como um cliente pode rever dados de registo de atividade para prestadores de serviços](how-to/view-service-provider-activity.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, que atualizou esses processos para o Azure e que os pratica regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – configurar a notificação de incidentes

**Orientação**: Configure as informações de contacto dos incidentes de segurança no Centro de Segurança do Azure. A Microsoft utiliza estas informações de contacto para o contactar caso o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos de forma ilícita ou não autorizada. Também tem opções para personalizar os alertas e as notificações de incidentes em diferentes serviços do Azure de acordo com as suas necessidades de resposta aos incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes com base em alertas de alta qualidade

**Orientação**: garanta que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Desta forma, pode aprender lições com os incidentes passados e priorizar os alertas para os analistas, para que estes não percam tempo a lidar com falsos positivos. 

Podem ser desenvolvidos alertas de alta qualidade com base na experiência de incidentes passados, origens da comunidade validadas e ferramentas designadas para gerar e limpar alertas através da fusão e correlação de origens de sinais diversas. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite-lhe criar regras de alertas avançadas para gerar incidentes automaticamente para investigações. 

Exporte os alertas e as recomendações do Centro de Segurança do Azure com a funcionalidade de exportação para ajudar a identificar riscos para os recursos do Azure. Exporte os alertas e as recomendações manualmente ou de forma contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigar incidentes

**Orientação**: Certifique-se de que os analistas podem consultar e utilizar várias origens de dados para investigar potenciais incidentes e ter uma perspetiva geral do que aconteceu. Para monitorizar as atividades de um potencial atacante em toda a rede do ataque e evitar ângulos mortos, devem ser recolhidos diversos registos.  Também deve assegurar que são apreendidas informações e aprendizagens para outros analistas e para informação histórica futura.  

As origens de dados para investigação incluem as origens de registos centralizadas que já estão a ser recolhidas pelos serviços dentro do âmbito e pelos sistemas em execução, mas podem também incluir:

- Dados de rede – utilize os registos de fluxos dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar os registos de fluxos de rede e outras informações analíticas. 

- Instantâneos dos sistemas em execução: 

    - Utilize a capacidade de instantâneos das Máquinas Virtuais do Azure para criar um instantâneo do disco do sistema em execução. 

    - Utilize a capacidade de captura da memória nativa do sistema operativo para criar um instantâneo da memória do sistema operativo.

    - Utilize a funcionalidade de instantâneos dos serviços do Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas em execução.

O Azure Sentinel disponibiliza uma grande quantidade de análises de dados em praticamente qualquer origem de registos e um portal de gestão de casos para gerir o ciclo de vida completo dos incidentes. As informações de inteligência durante uma investigação podem ser associadas a um incidente para fins de monitorização e reporte. 

- [Criar um instantâneo de um disco de uma máquina Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de um disco de uma máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e recolha da captura da memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Dê contexto aos analistas quanto aos incidentes em que se devem concentrar primeiro tendo por base a gravidade dos alertas e a sensibilidade dos ativos. 

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança tem na descoberta ou na análise utilizada para emitir o alerta, bem como no nível de confiança em que havia uma intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o processamento dos incidentes

**Orientação**: Automatize tarefas manuais repetitivas para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais são mais morosas e atrasam todos os incidentes, reduzindo o número daqueles com que um analista pode lidar. Estas tarefas também aumentam a fadiga dos analistas, o que aumenta o risco de erro humanos e provoca atrasos, bem como degrada a capacidade de aqueles se concentrarem em tarefas complexas. Utilize as funcionalidades de automatização de fluxos de trabalho do Centro de Segurança do Azure e do Azure Sentinel para acionar automaticamente ações ou executar um manual de procedimentos para responder aos alertas de segurança recebidos. O manual de procedimentos efetua ações, como o envio de notificações, a desativação de contas e o isolamento de redes problemáticas. 

- [Configurar a automatização dos fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças no Centro de Segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automatizadas a ameaças no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão da Postura e da Vulnerabilidade

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Estabelecer configurações seguras para os serviços do Azure 

**Orientação**: O Azure Lighthouse suporta abaixo as políticas específicas do serviço que estão disponíveis no Azure Security Center para auditar e impor configurações dos seus recursos Azure. Isto pode ser configurado em iniciativas do Azure Security Center ou da Azure Policy.

- Permitir a gestão de IDs de inquilino a bordo através do Farol de Azure

- Delegação de auditoria de âmbitos a um inquilino gerente

Pode utilizar a Azure Blueprints para automatizar a implementação e configuração de ambientes de serviços e aplicações, incluindo modelos de gestores de recursos Azure, controlos e políticas do Azure RBAC, e políticas, numa única definição de planta.

- [Políticas do Farol de Azure](samples/policy-reference.md)

- [Tutorial - Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Suportar configurações seguras para os serviços do Azure

**Orientação**: O Azure Lighthouse suporta abaixo as políticas específicas do serviço que estão disponíveis no Azure Security Center para auditar e impor configurações dos seus recursos Azure. Isto pode ser configurado em iniciativas do Azure Security Center ou da Azure Policy.

- [Políticas do Farol de Azure](samples/policy-reference.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Estabelecer configurações seguras para recursos computacional

**Orientação**: Utilize o Azure Security Center e a Azure Policy para estabelecer configurações seguras em todos os recursos computacional, incluindo VMs, contentores e outros.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md) 

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: Conforme necessário, faça testes de penetração ou atividades de "equipa de ataque" nos seus recursos do Azure e assegure a remediação de todas as descobertas de segurança críticas.
Para ter a certeza de que os seus testes de penetração não infringem as políticas da Microsoft, siga as Regras de Interação para Testes de Penetração da Microsoft Cloud. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Testes de Penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para obter mais informações, veja [Referência de Segurança do Azure: Governação e Estratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir a gestão dos ativos e a estratégia de proteção de dados 

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para a monitorização e proteção contínua dos sistemas e dos dados. Priorize a descoberta, a avaliação, a proteção e a monitorização de dados e sistemas críticos para a empresa. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma para a classificação de dados, de acordo com os riscos da atividade

-   Visibilidade da organização de segurança para os riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure a utilizar 

-   Segurança dos ativos ao longo do ciclo de vida

-   Estratégia de controlo de acesso obrigatória, de acordo com a classificação dos dados organizacionais

-   Utilização de capacidades de proteção de dados de terceiros e nativas do Azure

-   Requisitos de encriptação de dados para casos de utilização de dados em trânsito e inativos

-   Normas criptográficas adequadas

Para obter mais informações, veja as seguintes referências:
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Melhores práticas de segurança e encriptação de dados do Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Referência de Segurança do Azure - Gestão de ativos](../security/benchmarks/security-controls-v2-incident-response.md)

- [Referência de Segurança do Azure - Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir a estratégia de segmentação da empresa 

**Orientação**: Estabeleça uma estratégia ao nível de toda a empresa para segmentar o acesso aos ativos através de uma combinação de controlos de identidade, rede, aplicações, subscrições, grupos de gestão, entre outros.

Equilibre cuidadosamente a necessidade de separação da segurança com a necessidade de permitir o funcionamento diário dos sistemas que têm de comunicar entre si e aceder a dados.

Certifique-se de que a estratégia de segmentação está implementada de forma consistente em todos os tipos de controlos, incluindo segurança de rede, modelos de identidade e acesso e modelos de permissão/acesso a aplicações e controlos de processos humanos.

- [Orientação para a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação para a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir a estratégia de gestão da postura de segurança

**Orientação**: Meça e mitigue continuamente os riscos para os seus ativos individuais e para o ambiente em que estão alojados. Dê prioridade aos ativos de valor alto e a superfícies de ataque muito expostas, como aplicações publicadas, pontos de entrada e saída de rede, pontos finais de utilizador e administrador, etc.

- [Referência de Segurança do Azure - Gestão da postura e das vulnerabilidades](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar as funções, as responsabilidades e as responsabilizações na organização

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para as funções e responsabilidades na sua organização de segurança. Estabeleça prioridades ao indicar de forma clara a responsabilização quanto às decisões de segurança, explicando o modelo de responsabilização partilhada a todos e explicando às equipas técnicas a tecnologia para proteger a cloud.

- [Melhor Prática de Segurança do Azure 1 – Pessoas: Explicar às Equipas o Percurso da Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor Prática de Segurança do Azure 2 – Pessoas: Explicar às Equipas a Tecnologia de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor Prática de Segurança do Azure 3 – Processo: Atribuir Responsabilização Quanto às Decisões de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir uma estratégia de segurança de rede

**Orientação**: Crie uma abordagem à segurança de rede do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Gestão centralizada da rede e responsabilidade quanto à segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação empresarial

-   Estratégia de remediação em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e entrada e saída da Internet /azure/security/benchmarks/security-controls-v2-logging-ins-ação-deteção de ameaças
-   Estratégia de interconectividade entre a cloud híbrida e o ambiente no local

-   Artefactos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para obter mais informações, veja as seguintes referências:
- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Referência de Segurança do Azure - Segurança de Rede](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Descrição geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura da rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir a estratégia de identidades e acessos privilegiados

**Orientação**: Crie uma abordagem às identidades e aos acessos privilegiados do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema de identidades e autenticação centralizado e respetiva interconectividade com outros sistemas de identidades internas e externas

-   Métodos de autenticação fortes em diferentes casos de utilização e condições

-   Proteção de utilizadores com muitos privilégios

-   Monitorização e processamento de atividades anómalas de utilizadores  

-   Processo de revisão e reconciliação de identidades e acessos dos utilizadores

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Gestão de identidades](../security/benchmarks/security-controls-v2-identity-management.md)

- [Referência de Segurança do Azure - Acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Descrição geral da segurança da gestão de identidades do Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta a ameaças e registos

**Orientação**: Crie uma estratégia de resposta a ameaças e registos para detetar e remediar rapidamente as ameaças e cumprir, em paralelo, os requisitos de conformidade. Dê prioridade a proporcionar aos analistas alertas de alta qualidade e experiências totalmente integradas, para que se possam focar nas ameaças em vez de se focarem na integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   As funções e as responsabilidades da organização quanto às operações de segurança (SecOps) 

-   Processo de resposta a incidentes bem definido e alinhado com as normas da agência norte-americana NIST ou outro framework da indústria 

-   Captura e retenção de registos para suportar a deteção de ameaças, a resposta a incidentes e as necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, mediante a utilização de SIEM, das capacidades nativas do Azure e de outras origens 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes interessadas públicas

-   Utilização de plataformas nativas do Azure e de terceiros para processamento de incidentes, como registo e deteção de ameaças, análises forenses e remediação e erradicação de ataques

-   Processos para lidar com incidentes e atividades pós-incidentes, como lições aprendidas e retenção de provas

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Registos e deteção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Referência de Segurança do Azure - Resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md)

- [Melhor Prática de Segurança do Azure 4 – Processo. Atualizar os Processos de Resposta a Incidentes para a Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, registos e guia de decisão de relatórios](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização empresarial do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)