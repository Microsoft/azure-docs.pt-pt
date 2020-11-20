---
title: Linha de segurança Azure para o Farol de Azure
description: A linha de base de segurança do Farol Azure fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 51c786d12c372276d1cb007cc5a929ab6a6302f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974984"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Linha de segurança Azure para o Farol de Azure

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para o Farol de Azure. O Azure Security Benchmark fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Farol Azure. Foram excluídos **os controlos** não aplicáveis ao Farol de Azure.

Para ver como o Azure Lighthouse mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança do Farol Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Gestão de Identidades

*Para mais informações, consulte o [Azure Security Benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Normalizar o Azure Ative Directory como o sistema central de identidade e autenticação

**Orientação**: O Farol Azure utiliza o Azure Ative Directory (Azure AD) como o serviço de gestão de identidade e acesso padrão. Padronize a Azure AD para governar a gestão de identidade e acesso da sua organização em:
- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machine (Linux e Windows), Azure Key Vault, PaaS e SaaS.
- Os recursos da sua organização, tais como aplicações no Azure ou os recursos da sua rede corporativa.

Com o Azure Lighthouse, os utilizadores designados num inquilino gerente têm uma função Azure incorporada que lhes permite aceder a subscrições delegadas e/ou grupos de recursos no inquilino de um cliente. Todas as funções incorporadas são atualmente suportadas, exceto para Proprietário ou quaisquer funções incorporadas com permissão DataActions. A função de Administrador de Acesso ao Utilizador é suportada apenas para uso limitado na atribuição de funções a identidades geridas. As funções personalizadas e as funções clássicas de administrador de subscrição não são suportadas.

- [Arrendamento em Diretório Ativo Azure](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar uma instância AD Azure](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Utilizar fornecedores de identidade externos para aplicação](/azure/active-directory/b2b/identity-providers) 

- [Qual é a pontuação de segurança de identidade no Azure Ative Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir as identidades da aplicação de forma segura e automática

**Orientação**: As identidades geridas pelo Azure podem autenticar para os serviços e recursos da Azure que suportam a autenticação AZure. A autenticação é ativada através de regras de concessão de acesso pré-definidas, evitando credenciais codificadas em código fonte ou ficheiros de configuração. Com o Azure Lighthouse, os utilizadores com a função de Administrador de Acesso ao Utilizador na subscrição de um cliente podem criar uma identidade gerida no inquilino desse cliente. Embora esta função não seja geralmente suportada com o Farol Azure, pode ser usada neste cenário específico, permitindo aos utilizadores com esta permissão atribuir uma ou mais funções incorporadas específicas a identidades geridas.

Para serviços que não suportem identidades geridas, utilize a Azure AD para criar um principal de serviço com permissões restritas ao nível dos recursos. O Azure Lighthouse permite que os diretores de serviços acedam aos recursos dos clientes de acordo com as funções que lhes são concedidas durante o processo de embarque. Recomenda-se configurar os principais do serviço com credenciais de certificado e voltar aos segredos dos clientes. Em ambos os casos, o Azure Key Vault pode ser usado em conjunto com identidades geridas aZure, de modo que o ambiente de tempo de execução (como uma função Azure) pode recuperar a credencial do cofre chave.

- [Identidades geridas por Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Principal de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Use o cofre da chave Azure para o registo principal de segurança](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Criar um utilizador que possa atribuir funções a uma identidade gerida num inquilino de cliente](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilize controlos de autenticação forte para todo o acesso baseado no Azure Ative Directory

**Orientação**: Exija autenticação multi-factor (MFA) para todos os utilizadores do seu inquilino gerente, incluindo utilizadores que tenham acesso a recursos delegados do cliente. Sugerimos que peça aos seus clientes que implementem mFA nos seus inquilinos também.

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitor e alerta sobre anomalias de conta

**Orientação**: A Azure AD fornece as seguintes fontes de dados: 

-   Ins- O relatório de inscrições fornece informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.

-   Registos de auditoria - Fornece rastreabilidade através de registos para todas as alterações escoradas através de várias funcionalidades em Azure AD. Exemplos de alterações registadas nos registos de auditoria incluem adicionar ou remover utilizadores, apps, grupos, funções e políticas.

-   Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

-   Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Estas fontes de dados podem ser integradas com sistemas Azure Monitor, Azure Sentinel ou siem de terceiros.

Os prestadores de serviços que utilizam o Farol Azure podem encaminhar os registos AZure AD para Azure Sentinel e ver/definir alertas entre os inquilinos para monitorizar e alertar sobre anomalias de conta.

- [Relatórios de atividades de auditoria em Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como ver a Azure AD a entrar em risco](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Gerir espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

- [Ligue os dados do Azure AD ao Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso a recurso Azure com base em condições

**Orientação**: O Farol Azure não suporta uma capacidade de acesso condicional para recursos delegados do cliente. No inquilino gerente, utilize o acesso condicional Azure AD para um controlo de acesso mais granular com base em condições definidas pelo utilizador, tais como exigir logins de utilizadores de determinadas gamas IP até à utilização de Autenticação Multi-Factor (MFA). Uma gestão de sessão de autenticação granular também pode ser usada através da política de acesso condicional Azure AD para diferentes casos de uso. 

Você deve exigir MFA para todos os utilizadores do seu inquilino gerente, incluindo utilizadores que terão acesso a recursos de clientes delegados. Sugerimos que peça aos seus clientes que implementem mFA nos seus inquilinos também.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md)

- [Políticas de Acesso Condicional comuns](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar a gestão de sessões de autenticação com o Acesso Condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para mais informações, consulte o [Azure Security Benchmark: Acesso Privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteger e limitar utilizadores altamente privilegiados

**Orientação**: Limite o número de contas de utilizador altamente privilegiadas e proteja estas contas a um nível elevado. Uma conta Administrador Global não é necessária para ativar e utilizar o Farol Azure.

Para aceder aos dados do Registo de Atividade ao nível do inquilino, deve ser atribuída uma conta à função incorporada do Leitor de Monitorização Azure no âmbito raiz (/). Uma vez que a função de Monitoring Reader no âmbito raiz é um amplo nível de acesso, recomendamos que atribua esta função a uma conta principal de serviço, em vez de a um utilizador individual ou a um grupo. Esta atribuição deve ser executada por um utilizador que tenha a função de Administrador Global com acesso adicional elevado. Este acesso elevado deve ser adicionado imediatamente antes de então fazer a atribuição da função e, em seguida, removido quando a atribuição estiver concluída.

- [Permissões de função de administrador em Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Atribuição de acesso a dados de registo de atividade ao nível do inquilino](how-to/monitor-delegation-changes.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos de negócios

**Orientação**: O Azure Lighthouse utiliza o controlo de acesso baseado em funções Azure (Azure RBAC) para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado às subscrições e grupos de gestão em que se encontram.

Certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para executar as suas tarefas específicas.

Certifique-se de que também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, tais como Controladores de Domínio de Diretório Ativo (DCs), ferramentas de segurança e ferramentas de gestão do sistema com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Melhores práticas para definir utilizadores e funções para o Farol de Azure](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e conciliar o acesso dos utilizadores regularmente

**Orientação**: O Azure Lighthouse utiliza contas Azure Ative Directory (Azure AD) para gerir os seus recursos, rever as contas dos utilizadores e aceder regularmente para garantir que as contas e o seu acesso são válidos. Você pode usar comentários de acesso Azure AD para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Os clientes podem rever o nível de acesso concedido aos utilizadores no inquilino gerente através do Farol Azure no portal Azure. Podem remover este acesso a qualquer momento.

Além disso, a Azure Privileged Identity Management também pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente.

Nota: que alguns serviços da Azure apoiam utilizadores e funções locais que não foram geridos através do Azure AD. Terá de gerir estes utilizadores separadamente.

- [Criar uma revisão de acesso das funções de recursos Azure em Gestão de Identidade Privilegiada (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Remoção do acesso a uma delegação](how-to/remove-delegation.md)

- [Como utilizar a identidade AD do Azure e as avaliações de acesso](../active-directory/governance/access-reviews-overview.md)

- [Visualização da página de prestadores de serviços no portal Azure](how-to/view-manage-service-providers.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Criar acesso de emergência em Azure AD

**Orientação**: O Farol Azure está integrado com o Azure Ative Directory para gerir os seus recursos. Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são geralmente altamente privilegiadas, e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a cenários de emergência ou "vidro quebrado", onde as contas administrativas normais não podem ser utilizadas.

Deve certificar-se de que as credenciais (como palavra-passe, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas por indivíduos autorizados a usá-las apenas em caso de emergência.

- [Gerir contas de acesso de emergência em Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: O Farol Azure está integrado com o Azure Ative Directory (Azure AD) para gerir os seus recursos. Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.

- [O que são avaliações de acesso AZure AD](../active-directory/governance/access-reviews-overview.md) 

- [O que é a gestão de direitos da AD Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilize postos de trabalho privilegiados de acesso

**Orientação**: Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Dependendo dos seus requisitos, pode utilizar estações de trabalho de utilizador altamente seguras e/ou Azure Bastion para executar tarefas administrativas com o Farol Azure em ambientes de produção. Utilize o Azure Ative Directory, Microsoft Defender Advanced Threat Protection (ATP) e/ou Microsoft Intune para implementar uma estação de trabalho segura e gerida para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor a configuração segura, incluindo a autenticação forte, linhas de base de software e hardware, e acesso lógico e de rede restrito. 

- [Compreender estações de trabalho de acesso privilegiada](../active-directory/devices/concept-azure-managed-workstation.md)

- [Implementar uma estação de trabalho de acesso privilegiada](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Siga a administração suficiente (princípio de mínimo privilégio) 

**Orientação**: O Farol Azure está integrado com o controlo de acesso baseado em funções (RBAC) para gerir os seus recursos. O Azure RBAC permite-lhe gerir o acesso a recursos Azure através de atribuições de funções. Pode atribuir estas funções incorporadas a utilizadores, grupos, diretores de serviço e identidades geridas. Existem funções incorporadas pré-definidas para determinados recursos, e estas funções podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure. Os privilégios que atribui aos recursos através do RBAC Azure devem estar sempre limitados ao que é exigido pelas funções. Isto complementa a abordagem just in time (JIT) da Azure AD Privileged Identity Management (PIM) e deve ser revisto periodicamente. Utilize funções incorporadas para alocar a permissão e apenas criar funções personalizadas quando necessário.

O Farol Azure permite o acesso aos recursos delegados do cliente utilizando funções incorporadas da Azure. Na maioria dos casos, você vai querer atribuir estas funções a um grupo ou diretor de serviço, em vez de a muitas contas individuais de utilizadores. Isto permite-lhe adicionar ou remover o acesso a utilizadores individuais sem ter de atualizar e reeditar o plano quando os seus requisitos de acesso mudarem.

Para delegar os recursos do cliente a um inquilino gerente, uma implantação deve ser feita por uma conta não hóspede no inquilino do cliente que tem o papel incorporado do Proprietário para a subscrição ser a bordo (ou que contém os grupos de recursos que estão a ser a bordo).

- [Compreender inquilinos, utilizadores e papéis no Farol de Azure](concepts/tenants-users-roles.md)

- [Como aplicar o princípio do menor privilégio ao Farol de Azure](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Como utilizar a identidade AD do Azure e as avaliações de acesso](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gestão de Recursos

*Para mais informações, consulte o [Azure Security Benchmark: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Garantir que a equipa de segurança tem visibilidade em riscos para os ativos

**Orientação**: Certifique-se de que as equipas de segurança recebem permissões de Leitor de Segurança no seu inquilino Estaure e subscrições para que possam monitorizar riscos de segurança utilizando o Centro de Segurança Azure. 

Dependendo da forma como as responsabilidades das equipas de segurança são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, os conhecimentos de segurança e os riscos devem ser sempre agregados centralmente dentro de uma organização. 

As permissões do Security Reader podem ser aplicadas amplamente a um inquilino inteiro (Root Management Group) ou a grupos de gestão ou subscrições específicas. 

Nota: Podem ser necessárias permissões adicionais para obter visibilidade em cargas de trabalho e serviços. 

- [Visão geral do papel do leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos Grupos de Gestão Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso ao inventário de ativos e metadados

**Orientação**: As equipas de segurança dos clientes podem rever os registos de atividades para ver a atividade tomada pelos prestadores de serviços que utilizam o Farol Azure. 

Se um prestador de serviços quiser permitir que a sua equipa de segurança reveja os recursos delegados do cliente, as autorizações da equipa de segurança devem incluir o papel incorporado no Reader.

- [Como um cliente pode rever a atividade do prestador de serviços](how-to/view-service-provider-activity.md)

- [Como especificar funções ao embarcar um cliente para o Farol de Azure](how-to/onboard-customer.md#define-roles-and-permissions)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilize apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para auditar e restringir quais os serviços que os utilizadores podem prestação no seu ambiente. Utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das suas subscrições. Também pode utilizar o Azure Monitor para criar regras para desencadear alertas quando for detetado um serviço não aprovado.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantir a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Remova o acesso aos recursos que tenham sido delegados através do Farol Azure uma vez que já não sejam necessários, para que os prestadores de serviços deixem de ter acesso. O acesso pode ser removido pelo cliente ou pelo prestador de serviços. 

- [Remover o acesso a uma delegação](how-to/remove-delegation.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: O Farol Azure está integrado com o Azure Ative Directory (Azure AD) para identidade e autenticação. Pode utilizar o Azure Conditional Access para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registo e Deteção de Ameaças

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Permitir a deteção de ameaças para recursos Azure

**Orientação**: Através do Farol Azure, pode monitorizar os recursos Azure dos seus clientes para potenciais ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir falsos positivos para os analistas classificarem. Os alertas podem ser obtidos a partir de dados de registo, agentes ou outros dados.

Utilize a capacidade de deteção de ameaças incorporada do Azure Security Center, que se baseia na monitorização da telemetria de serviço Azure e na análise de registos de serviço. Os dados são recolhidos utilizando o agente Log Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos do sistema e copia os dados para o seu espaço de trabalho para análise. 

Além disso, use o Azure Sentinel para construir regras de análise, que caçam ameaças que correspondem a critérios específicos em todo o ambiente do seu cliente. As regras geram incidentes quando os critérios são combinados, para que possa investigar cada incidente. O Azure Sentinel também pode importar inteligência de ameaça de terceiros para aumentar a sua capacidade de deteção de ameaças. 

- [Proteção contra ameaças no Centro de Segurança do Azure](/azure/security-center/threat-protection)

- [Guia de referência do Centro de Segurança Azure alerta](../security-center/alerts-reference.md)

- [Criar regras de análise personalizadas para detetar ameaças](../sentinel/tutorial-detect-threats-custom.md)

- [Gerir espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Permitir a deteção de ameaças para a gestão da identidade e do acesso do Azure

**Orientação**: Através do Farol Azure, pode utilizar o Azure Security Center para alertar sobre certas atividades suspeitas nos inquilinos do cliente que gere, como um número excessivo de tentativas de autenticação falhadas e contas depreciadas na subscrição.

O Azure Ative Directory (Azure AD) fornece os seguintes registos de utilizador que podem ser vistos em relatórios AD Azure ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas siem/monitorização para casos de monitorização e utilização analíticos mais sofisticados:
- Iniciar s nota – O relatório de inscrição fornece informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AD Azure, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.
- Insusição arriscada - Um sinal de risco é um indicador para uma tentativa de inscrição que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador.
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Azure Security Center também pode alertar para certas atividades suspeitas, tais como um número excessivo de tentativas de autenticação falhadas, contas prectadas na subscrição. Além da monitorização básica da higiene de segurança, o módulo de Proteção de Ameaças do Azure Security Center também pode recolher alertas de segurança mais aprofundados de recursos computetivos individuais da Azure (máquinas virtuais, contentores, serviço de aplicações), recursos de dados (SQL DB e armazenamento) e camadas de serviço Azure. Esta capacidade proporciona visibilidade sobre anomalias de conta dentro dos recursos individuais. 

- [Serviços e cenários melhorados com o Farol Azure](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Relatórios de atividades de auditoria no Diretório Ativo do Azure](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Ativar a proteção de identidade Azure](../active-directory/identity-protection/overview-identity-protection.md) 

- [Proteção contra ameaças no Centro de Segurança do Azure](/azure/security-center/threat-protection)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Permitir a exploração de madeira para recursos Azure

**Orientação**: Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos do Farol Azure, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

Com o Azure Lighthouse, você pode usar registos Azure Monitor de forma escalável através dos inquilinos do cliente que você está gerindo. Crie espaços de trabalho log Analytics diretamente nos inquilinos do cliente para que os dados dos clientes permanecem nos seus inquilinos em vez de serem exportados para o seu. Isto também permite uma monitorização centralizada de quaisquer recursos ou serviços suportados pelo Log Analytics, proporcionando-lhe mais flexibilidade sobre os tipos de dados que monitoriza.

Os clientes que tenham delegado subscrições para o Azure Lighthouse podem ver os dados do registo da Atividade Azure para ver todas as ações tomadas. Isto dá aos clientes total visibilidade nas operações que os prestadores de serviços estão a realizar, juntamente com operações feitas pelos utilizadores dentro do inquilino Azure Ative Directory (Azure AD) do cliente.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Monitorar recursos delegados à escala](how-to/monitor-at-scale.md)

- [Ver a atividade do fornecedor de serviços](how-to/view-service-provider-activity.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a gestão e análise de registos de segurança

**Orientação**: Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de Atividade Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Com o Azure Lighthouse, você pode usar registos Azure Monitor de forma escalável através dos inquilinos do cliente que você está gerindo. Crie espaços de trabalho log Analytics diretamente nos inquilinos do cliente para que os dados dos clientes permanecem nos seus inquilinos em vez de serem exportados para o seu. Isto também permite uma monitorização centralizada de quaisquer recursos ou serviços suportados pelo Log Analytics, proporcionando-lhe mais flexibilidade sobre os tipos de dados que monitoriza.

Os clientes que tenham delegado subscrições para o Azure Lighthouse podem ver os dados do registo da Atividade Azure para ver todas as ações tomadas. Isto dá aos clientes total visibilidade nas operações que os prestadores de serviços estão a realizar, juntamente com operações feitas pelos utilizadores dentro do inquilino Azure Ative Directory (Azure AD) do cliente.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Monitorar recursos delegados à escala](how-to/monitor-at-scale.md)

- [Como os clientes podem ver a atividade do prestador de serviços](how-to/view-service-provider-activity.md)

- [Gerir espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Retenção de armazenamento de madeira configurada

**Orientação**: O Farol Azure não produz atualmente quaisquer registos relacionados com a segurança. Os clientes que pretendam visualizar a atividade do prestador de serviços podem configurar a retenção de registos de acordo com os requisitos de conformidade, regulação e negócio. 

No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize o espaço de trabalho Azure Storage, Data Lake ou Log Analytics para armazenamento de longo prazo e arquivo.

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [O Azure Security Center alerta e recomendações para exportar](../security-center/continuous-export.md) configuração e o cliente não é capaz de definir qualquer retenção de registo.

- [Como um cliente pode rever dados de registo de atividade para prestadores de serviços](how-to/view-service-provider-activity.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, atualizou estes processos para o Azure, e está regularmente a exercê-los para garantir a prontidão.

- [Implementar segurança em todo o ambiente da empresa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência de resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – notificação de incidente de configuração

**Orientação**: Configurar informações de contacto de incidentes de segurança no Centro de Segurança Azure. Estas informações de contacto são utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Também tem opções para personalizar alerta de incidentes e notificação em diferentes serviços Azure com base nas suas necessidades de resposta a incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes baseados em alertas de alta qualidade

**Orientação**: Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isto permite-lhe aprender lições de incidentes passados e priorizar alertas para analistas, para que não percam tempo com falsos positivos. 

Alertas de alta qualidade podem ser construídos com base na experiência de incidentes passados, fontes comunitárias validadas e ferramentas projetadas para gerar e limpar alertas fundindo e correlacionando diversas fontes de sinal. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas ao Azure Sentinel. O Azure Sentinel permite criar regras avançadas de alerta para gerar incidentes automaticamente para uma investigação. 

Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação para ajudar a identificar riscos para os recursos da Azure. Alertas e recomendações de exportação, manualmente ou de forma contínua e contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigue um incidente

**Orientação**: Certifique-se de que os analistas podem consultar e usar diversas fontes de dados à medida que investigam potenciais incidentes, para construir uma visão completa do que aconteceu. Devem ser recolhidos registos diversos para rastrear as atividades de um potencial atacante através da cadeia de morte para evitar pontos cegos.  Deve também garantir que os insights e aprendizagens são capturados para outros analistas e para referência histórica futura.  

As fontes de dados para investigação incluem as fontes centralizadas de registo que já estão a ser recolhidas dos serviços de âmbito e dos sistemas de funcionamento, mas também podem incluir:

- Dados da rede – utilize os registos de fluxo dos grupos de segurança da rede, o Azure Network Watcher e o Azure Monitor para capturar registos de fluxo de rede e outras informações analíticas. 

- Instantâneos dos sistemas de funcionamento: 

    - Utilize a capacidade de instantâneo da máquina virtual Azure para criar uma imagem do disco do sistema de funcionamento. 

    - Utilize a capacidade de despejo de memória nativa do sistema operativo para criar uma imagem da memória do sistema de funcionamento.

    - Utilize a funcionalidade instantânea dos serviços Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas de execução.

O Azure Sentinel fornece uma análise extensiva de dados em praticamente qualquer fonte de registo e um portal de gestão de casos para gerir todo o ciclo de vida dos incidentes. Informações de inteligência durante uma investigação podem ser associadas a um incidente para rastrear e reportar propósitos. 

- [Snapshot um disco de máquina do Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snapshot um disco de máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Suporta informações de diagnóstico e recolha de despejo de memória](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigue incidentes com Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Fornecer contexto aos analistas sobre quais incidentes se concentrar em primeiro lugar com base na gravidade do alerta e na sensibilidade do ativo. 

O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque recursos usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o tratamento do incidente

**Orientação**: Automatizar tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir o fardo sobre os analistas. As tarefas manuais demoram mais tempo a ser executadas, abrandando cada incidente e reduzindo quantos incidentes um analista pode lidar. As tarefas manuais também aumentam a fadiga dos analistas, o que aumenta o risco de erro humano que causa atrasos, e degrada a capacidade dos analistas de se concentrarem eficazmente em tarefas complexas. Utilize funcionalidades de automatização de fluxos de trabalho no Azure Security Center e no Azure Sentinel para desencadear automaticamente ações ou executar um livro de jogadas para responder aos alertas de segurança que chegam. O livro de jogadas toma medidas, tais como o envio de notificações, a desativação de contas e a isolação de redes problemáticas. 

- [Configurar automatização de fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Criar respostas automáticas de ameaças no Centro de Segurança Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automáticas de ameaças em Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão de Postura e Vulnerabilidade

*Para mais informações, consulte o [Azure Security Benchmark: Posture and Vulnerability Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Estabelecer configurações seguras para os serviços Azure 

**Orientação**: O Azure Lighthouse suporta abaixo as políticas específicas do serviço que estão disponíveis no Azure Security Center para auditar e impor configurações dos seus recursos Azure. Isto pode ser configurado em iniciativas do Azure Security Center ou da Azure Policy.

- Permitir a gestão de IDs de inquilino a bordo através do Farol de Azure

- Delegação de auditoria de âmbitos a um inquilino gerente

Pode utilizar a Azure Blueprints para automatizar a implementação e configuração de ambientes de serviços e aplicações, incluindo modelos de gestores de recursos Azure, controlos e políticas do Azure RBAC, e políticas, numa única definição de planta.

- [Políticas do Farol de Azure](samples/policy-reference.md)

- [Tutorial - Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Sustente configurações seguras para serviços Azure

**Orientação**: O Azure Lighthouse suporta abaixo as políticas específicas do serviço que estão disponíveis no Azure Security Center para auditar e impor configurações dos seus recursos Azure. Isto pode ser configurado em iniciativas do Azure Security Center ou da Azure Policy.

- [Políticas do Farol de Azure](samples/policy-reference.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Estabelecer configurações seguras para recursos computacional

**Orientação**: Utilize o Azure Security Center e a Azure Policy para estabelecer configurações seguras em todos os recursos computacional, incluindo VMs, contentores e outros.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md) 

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulação regular de ataque

**Orientação**: Conforme necessário, realize testes de penetração ou atividades de equipa vermelha nos seus recursos Azure e garanta a reparação de todos os resultados críticos de segurança.
Siga as Regras de Teste de Penetração na Nuvem da Microsoft para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Testes de penetração em Azure](../security/fundamentals/pen-testing.md)

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para mais informações, consulte o [Benchmark de Segurança Azure: Governação e Estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir estratégia de gestão de ativos e proteção de dados 

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara de monitorização e proteção contínua de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o acompanhamento de dados e sistemas críticos do negócio. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma de classificação de dados de acordo com os riscos empresariais

-   Visibilidade da organização de segurança em riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços da Azure para utilização 

-   Segurança dos bens através do seu ciclo de vida

-   Estratégia de controlo de acesso exigida de acordo com a classificação de dados organizacionais

-   Utilização de capacidades de proteção de dados nativas e de terceiros da Azure

-   Requisitos de encriptação de dados para casos de utilização em trânsito e em repouso

-   Padrões criptográficos adequados

Para mais informações, consulte as seguintes referências:
- [Recomendação de arquitetura de segurança Azure - Armazenamento, dados e encriptação](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Fundamentos de Segurança Azure - Segurança de dados Azure, encriptação e armazenamento](../security/fundamentals/encryption-overview.md)

- [Cloud Adopt Framework - Segurança de dados Azure e melhores práticas de encriptação](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Gestão de ativos](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Benchmark de segurança Azure - Proteção de Dados](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir estratégia de segmentação empresarial 

**Orientação**: Estabeleça uma estratégia em toda a empresa para segmentar o acesso a ativos utilizando uma combinação de identidade, rede, aplicação, subscrição, grupo de gestão e outros controlos.

Equilibra cuidadosamente a necessidade de separação de segurança com a necessidade de permitir o funcionamento diário dos sistemas que precisam de comunicar entre si e aceder aos dados.

Certifique-se de que a estratégia de segmentação é implementada de forma consistente em todos os tipos de controlo, incluindo modelos de segurança de rede, identidade e acesso, e modelos de permissão/acesso de aplicações e controlos de processos humanos.

- [Orientação sobre estratégia de segmentação em Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação sobre estratégia de segmentação em Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação da rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir estratégia de gestão da postura de segurança

**Orientação:** Medir e mitigar continuamente os riscos para os seus ativos individuais e para o ambiente em que estão hospedados. Priorize ativos de alto valor e superfícies de ataque altamente expostas, tais como aplicações publicadas, entradas de rede e pontos de saída, pontos finais de utilizador e administrador, etc.

- [Azure Security Benchmark - Postura e gestão de vulnerabilidades](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar papéis de organização, responsabilidades e responsabilidades

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorizar a prestação de contas claras às decisões de segurança, educar todos no modelo de responsabilidade partilhada e educar as equipas técnicas em tecnologia para garantir a nuvem.

- [Azure Security Best Practice 1 - Pessoas: Educar equipas na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security Best Practice 2 - Pessoas: Educar equipas sobre tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - Processo: Atribuir responsabilidade para decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir estratégia de segurança de rede

**Orientação**: Estabeleça uma abordagem de segurança da rede Azure como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Responsabilidade centralizada de gestão de redes e segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação da empresa

-   Estratégia de remediação em diferentes cenários de ameaça e ataque

-   Internet borda e estratégia de entrada e saída

-   Estratégia híbrida de interconectividade em nuvem e no local

-   Artefactos de segurança da rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para mais informações, consulte as seguintes referências:
- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark de segurança Azure - Segurança de rede](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Visão geral da segurança da rede Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir estratégia de identidade e acesso privilegiado

**Orientação**: Estabeleça uma identidade azul e abordagens privilegiadas de acesso como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema centralizado de identidade e autenticação e a sua interconectividade com outros sistemas de identidade interna e externa

-   Métodos de autenticação forte em diferentes casos e condições de utilização

-   Proteção de utilizadores altamente privilegiados

-   Monitorização e manuseamento de atividades de utilizadores de anomalias  

-   Revisão e reconciliação da identidade do utilizador e acesso

Para mais informações, consulte as seguintes referências:

- [Azure Security Benchmark - Gestão de identidade](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de Segurança Azure - Acesso privilegiado](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança da gestão de identidade Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta ao abate e à ameaça

**Orientação**: Estabeleça uma estratégia de resposta à exploração madeireira e a ameaças para detetar e remediar rapidamente as ameaças, cumprindo os requisitos de conformidade. Priorize fornecer aos analistas alertas de alta qualidade e experiências perfeitas para que se concentrem em ameaças em vez de integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   O papel e as responsabilidades da organização das operações de segurança (SecOps) 

-   Um processo de resposta a incidentes bem definido, alinhado com o NIST ou outro quadro da indústria 

-   Registar captura e retenção para apoiar a deteção de ameaças, resposta a incidentes e necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, utilizando siem, capacidades nativas de Azure, e outras fontes 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes públicas de interesse

-   Utilização de plataformas nativas e de terceiros da Azure para o tratamento de incidentes, tais como deteção de registos e ameaças, perícia e remediação e erradicação de ataques

-   Processos de tratamento de incidentes e atividades pós-incidente, tais como lições aprendidas e retenção de provas

Para mais informações, consulte as seguintes referências:

- [Azure Security Benchmark - Registo e deteção de ameaças](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Resposta a incidentes](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Processo. Atualizar processos de resposta a incidentes para cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Quadro de adoção Azure, registo e guia de decisão de reporte](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização da empresa Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](/azure/security/benchmarks/security-baselines-overview)
