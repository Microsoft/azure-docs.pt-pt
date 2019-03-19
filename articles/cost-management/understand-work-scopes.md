---
title: Entender e trabalhar com âmbitos do Azure Cost Management | Documentos da Microsoft
description: Este artigo ajuda-o a compreender a faturação e de recursos âmbitos de gestão disponíveis no Azure e como utilizar os âmbitos na gestão de custos e APIs.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 4e7956e8873b552fcd73c51a51f51d99f21af324
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002932"
---
# <a name="understand-and-work-with-scopes"></a>Compreender e trabalhar com âmbitos

Este artigo ajuda-o a compreender a faturação e de recursos âmbitos de gestão disponíveis no Azure e como utilizar os âmbitos na gestão de custos e APIs.

## <a name="scopes"></a>Âmbitos

R _âmbito_ é um nó da hierarquia de recursos do Azure em que os utilizadores do Azure AD aceder e gerir os serviços. Recursos mais do Azure são criados e implementados em grupos de recursos, o que fazem parte das subscrições. A Microsoft também oferece duas hierarquias acima subscrições do Azure que tenham especializada funções para gerir dados de faturação:
- Dados de faturação, como pagamentos e notas fiscais
- Serviços cloud, como o custo e a política de governação

Os âmbitos são onde gerir dados de faturação, ter funções específicas para pagamentos, ver notas fiscais e realizar a gestão da conta geral. Funções de faturação e contas são geridas separadamente em relação às utilizadas para a gestão de recursos, que utilizam [RBAC do Azure](../role-based-access-control/overview.md). Para distinguir claramente a intenção dos âmbitos separados, incluindo as diferenças de controlo de acesso, essas soluções são referidas como _faturação âmbitos_ e _âmbitos RBAC_, respectivamente.

## <a name="how-cost-management-uses-scopes"></a>Como o Cost Management utiliza âmbitos

Funciona a gestão de custo em todos os âmbitos acima recursos para permitir às organizações gerir os custos no nível em que eles têm acesso, independentemente de ser toda a conta de faturação ou de um grupo de recursos. Embora a faturas âmbitos diferem com base no seu contrato do Microsoft (tipo de subscrição), os âmbitos RBAC não o fizer.

## <a name="azure-rbac-scopes"></a>Âmbitos de RBAC do Azure

O Azure suporta três âmbitos para a gestão de recursos. Cada âmbito suporta a gestão de acesso e governação, incluindo mas não se limitando a, a gestão de custos.

- [**Grupos de gestão** ](../governance/management-groups/index.md) -contentores hierárquicas, até oito níveis, organizar as subscrições do Azure.

    Tipo de recurso: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Subscrições** -primários contentores para recursos do Azure.

    Tipo de recurso: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupos de recursos** ](../azure-resource-manager/resource-group-overview.md#resource-groups) -agrupamentos lógicos de recursos relacionados para uma solução do Azure que partilham o mesmo ciclo de vida. Por exemplo recursos que são implementados e eliminados em conjunto.

    Tipo de recurso: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Grupos de gestão permitem-lhe organizar subscrições para uma hierarquia. Por exemplo, poderá criar uma hierarquia de lógica da organização utilizando grupos de gestão. Em seguida, forneça as equipes subscrições para a produção e cargas de trabalho de programador/teste. E, em seguida, criar grupos de recursos nas subscrições para gerir cada subsistema ou componente.

Criar que uma hierarquia organizacional permite custo e a conformidade com a política rollup organizacional. Cada líder, em seguida, pode ver e analisar os seus custos atuais. E, em seguida, podem criar orçamentos para restringir os padrões de gastos ruins e otimizar os custos com as recomendações do assistente no nível mais baixo.

Conceder acesso para ver os custos e, opcionalmente, gerir a configuração de custo, como os orçamentos e exportações, é executada em âmbitos de governação através do RBAC do Azure. Utilizar o Azure RBAC para conceder a utilizadores do Azure AD e grupos de acesso para executar um conjunto predefinido de ações que são definidas numa função num âmbito específico e abaixo. Por exemplo, uma função atribuída a um âmbito de grupo de gestão também concede as mesmas permissões para aninhados subscrições e grupos de recursos.

O Cost Management suporta as seguintes funções incorporadas para cada um dos âmbitos seguintes:

- [**Proprietário** ](../role-based-access-control/built-in-roles.md#owner) – pode ver os custos e gerir tudo, incluindo a configuração de custo.
- [**Contribuinte** ](../role-based-access-control/built-in-roles.md#contributor) – pode ver os custos e gerir tudo, incluindo a configuração de custo, mas excluindo o controlo de acesso.
- [**Leitor** ](../role-based-access-control/built-in-roles.md#reader) – pode ver tudo, incluindo dados de custo e a configuração, mas não é possível fazer alterações.
- [**Contribuinte de gestão de custos** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) – pode ver os custos e gerir a configuração de custo.
- [**Leitor de gestão de custos** ](../role-based-access-control/built-in-roles.md#cost-management-reader) – pode ver os dados de custo e a configuração.

Contribuinte de gestão de custos é a função de privilégio mínimo recomendada. Ele permite que as pessoas acesso criar e gerir os orçamentos e exporta mais efetivamente monitorizar e comunicar de custos. Os contribuintes de gestão de custos também podem exigir funções adicionais para suportar cenários de gestão de custos-a-ponto. Considere os seguintes cenários:

- **Agir quando são ultrapassados orçamentos** – contribuidores de gestão de custos também precisam de acesso para criar e/ou gerir grupos de ação para reagirem automaticamente a utilização excedida. Considere a concessão [Contribuidor de monitorização](../role-based-access-control/built-in-roles.md#monitoring-contributor) num grupo de recursos que contém o grupo de ação para utilizar quando os limiares de orçamento forem ultrapassados. Automatizar as ações específicas necessita de funções adicionais para os serviços específicos usados, como a automatização e as funções do Azure.
- **Agenda de exportação de dados de custos** – contribuidores de gestão de custos também precisam de acesso para gerir contas de armazenamento para agendar uma exportação para copiar dados para uma conta de armazenamento. Considere a concessão [contribuinte de conta de armazenamento](../role-based-access-control/built-in-roles.md#storage-account-contributor) num grupo de recursos que contém o armazenamento de conta em que os dados de custos é exportada.
- **Ver recomendações de poupança de custos** – os leitores de gestão de custos e os contribuintes não tem acesso às recomendações por predefinição. Acesso a recomendações requer acesso de leitura para recursos individuais. Considere a concessão [leitor](../role-based-access-control/built-in-roles.md#reader) ou uma [específico do serviço de função](../role-based-access-control/built-in-roles.md#built-in-role-descriptions).

## <a name="enterprise-agreement-scopes"></a>Âmbitos de contrato Enterprise

As contas de faturação contrato Enterprise (EA), também chamadas de inscrições, tem os seguintes âmbitos:

- [**Conta de faturação** ](../billing/billing-view-all-accounts.md) -representa uma inscrição EA. Notas fiscais são gerados neste âmbito. Compras que não são baseados em utilização, como o Marketplace e as reservas, só estão disponíveis neste âmbito. Eles não são representados em departamentos ou contas de inscrição.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Departamento** - opcional de agrupamento de contas de inscrição.

    Tipo de recurso: `Billing/billingAccounts/departments`

- **Conta de inscrição** -representa o proprietário de uma única conta. Não suporta a conceder acesso a várias pessoas.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Embora os âmbitos de governação estão vinculados a um único diretório, não são âmbitos de faturas do EA. Uma conta de cobrança de EA pode ter subscrições em qualquer número de diretórios do Azure AD.

Os âmbitos de faturas de EA suportam as seguintes funções:

- **Administrador da empresa** – pode gerir as definições da conta de cobrança e acesso, pode ver todos os custos e pode gerir a configuração de custo. Por exemplo, orçamentos e exporta. Na função, EA faturação âmbito é igual a [função de contribuinte de gestão de custos Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Utilizador só de leitura de Enterprise** – pode ver definições de conta de faturação, dados de custo e a configuração de custo. Por exemplo, orçamentos e exporta. Na função, EA faturação âmbito é igual a [função de leitor de gestão de custos Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrador do departamento** – pode gerir as definições de departamento, como o Centro de custos e pode acessar, ver todos os custos e gerir a configuração de custo. Por exemplo, orçamentos e exporta.  O **cobranças de vista DA** definição da conta de Faturação tem de estar ativada para administradores de departamento e os utilizadores só de leitura para ver os custos. Se **cobranças de vista DA** é desativada, os utilizadores do departamento não é possível ver os custos em qualquer nível, mesmo que sejam um proprietário de conta ou a subscrição.
- **Utilizador só de leitura do departamento** – pode ver as definições de departamento, a dados de custo e a configuração de custo. Por exemplo, orçamentos e exporta. Se **cobranças de vista DA** é desativada, os utilizadores do departamento não é possível ver os custos em qualquer nível, mesmo que sejam um proprietário de conta ou a subscrição.
- **Proprietário da conta** – pode gerir as definições de conta de inscrição (por exemplo, o Centro de custos), ver todos os custos e gerir a configuração de custo (por exemplo, orçamentos e exportações) para a conta de inscrição. O **cobranças de exibição AO** definição da conta de Faturação tem de estar ativada para os proprietários de conta e os utilizadores do RBAC para ver os custos.

Os utilizadores da conta faturas EA não tem acesso direto às notas fiscais. Notas fiscais estão disponíveis a partir de um sistema de licenciamento de volume externo.

As subscrições do Azure estão aninhadas em contas de inscrição. Utilizadores de faturação têm acesso a dados de custos para as subscrições e grupos de recursos que estão sob seus respetivos âmbitos. Eles não têm acesso para ver ou gerir os recursos no portal do Azure. Os utilizadores de faturação podem ver os custos ao navegar até **gestão de custos + faturação** no portal do Azure lista de serviços. Em seguida, eles podem filtrar os custos para as subscrições específicas e que precisam para gerar relatórios sobre os grupos de recursos.

Os utilizadores de faturação não tem acesso a grupos de gestão porque eles não se enquadram explicitamente sob uma conta de cobrança específica. Acesso deve ser concedido a grupos de gestão explicitamente. Os custos de rollup de aninhada todas as subscrições de grupos de gestão. No entanto, eles incluem apenas compras baseada na utilização. Eles não incluem as compras, como as reservas e ofertas do Marketplace de terceiros. Para ver esses custos, utilize a conta de cobrança do EA.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>Âmbitos de contratos individuais (pay as you go)

Subscrições pay as you go (PAYG), incluindo tipos relacionados, como gratuitos/avaliação e ofertas de dev/test, não tem um âmbito de conta de faturação explícito. Em vez disso, cada subscrição tem um proprietário da conta ou administrador de conta, como o proprietário da conta EA.

- [**Conta de faturação** ](../billing/billing-view-all-accounts.md) -representa um proprietário da conta única para um ou mais subscrições do Azure. Atualmente não suporta a conceder acesso a várias pessoas ou acesso a vistas de custo agregado.

    Tipo de recurso: Não aplicável

Administradores de contas de subscrição PAYG podem ver e gerir dados de faturação, por exemplo, as notas fiscais e pagamentos, a partir da [Centro de contas do Azure](https://account.azure.com/subscriptions). No entanto, não é possível ver os dados de custo ou gerir os recursos no portal do Azure. Para conceder acesso para o administrador de conta, utilize as funções de gestão de custos mencionadas anteriormente.

Ao contrário dos EA, os administradores de contas de subscrição PAYG podem ver suas notas fiscais no portal do Azure. Tenha em atenção que as funções de leitor de gestão de custos e contribuinte de gestão de custos não fornecem acesso às notas fiscais. Para obter mais informações, consulte [como pode conceder acesso às notas fiscais em PAYG](../billing/billing-manage-access.md#give-access-to-billing).

## <a name="customer-agreement-scopes"></a>Âmbitos de contrato de cliente

Contas de faturas do contrato de cliente da Microsoft têm os seguintes âmbitos:

- **Conta de faturação** -representa um contrato de cliente para vários produtos e serviços Microsoft. Contas de faturas de contrato de cliente não são funcionalmente o mesmo que as inscrições de EA. Inscrições EA são mais intimamente alinhadas aos perfis de faturas.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Perfil de faturação** -define as subscrições que estão incluídas numa nota fiscal. Os perfis de faturação são o equivalente funcional de uma inscrição EA, uma vez que é o âmbito de notas fiscais gerados no. Da mesma forma, compras que não são baseadas na utilização (por exemplo, o Marketplace e as reservas) só estão disponíveis neste âmbito. Eles não estão incluídos nas secções de nota fiscal.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Secção de nota fiscal** -representa um grupo de subscrições de uma nota fiscal ou o perfil de faturação. As secções de nota fiscal são como os departamentos, várias pessoas podem ter acesso a uma secção de nota fiscal.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/invoiceSections`

Ao contrário dos EA faturação âmbitos, contas de faturação do contrato de cliente _são_ vinculado a um único diretório e não pode ter subscrições em vários diretórios do Azure AD.

Âmbitos de faturas de contrato de cliente suportam as seguintes funções:

- **Proprietário** – pode gerir as definições de faturas e o acesso, ver todos os custos e gerir a configuração de custo. Por exemplo, orçamentos e exporta. Na função, este escopo de faturação do contrato de cliente é igual a [função de contribuinte de gestão de custos Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Contribuinte** – pode gerir as definições de faturas, exceto o acesso, ver todos os custos e gerir a configuração de custo. Por exemplo, orçamentos e exporta. Na função, este escopo de faturação do contrato de cliente é igual a [função de contribuinte de gestão de custos Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Leitor** – pode ver as definições de faturas, dados de custo e a configuração de custo. Por exemplo, orçamentos e exporta. Na função, este escopo de faturação do contrato de cliente é igual a [função de leitor de gestão de custos Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Gerenciador de faturas** – pode ver e pagar notas fiscais e pode ver dados e a configuração de custos. Por exemplo, orçamentos e exporta. Na função, este escopo de faturação do contrato de cliente é igual a [função de leitor de gestão de custos Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Criador de subscrição do Azure** – pode criar subscrições do Azure, os custos de ver e gerir a configuração de custo. Por exemplo, orçamentos e exporta. Na função, este âmbito de faturação do contrato de cliente é o mesmo que a função de proprietário de conta de inscrição de EA.

As subscrições do Azure estão aninhadas em seções de nota fiscal, como como estão em contas de inscrição de EA. Utilizadores de faturação têm acesso a dados de custos para as subscrições e grupos de recursos que estão sob seus respetivos âmbitos. No entanto, não têm acesso para ver ou gerir os recursos no portal do Azure. Os utilizadores de faturação podem ver os custos ao navegar até **gestão de custos + faturação** no portal do Azure lista de serviços. Em seguida, filtre os custos para as subscrições específicas e que precisam para gerar relatórios sobre os grupos de recursos.

Os utilizadores de faturação não tem acesso a grupos de gestão, porque eles explicitamente não se enquadram na conta de faturação. No entanto, quando os grupos de gestão estão ativados para a organização, todos os custos de subscrição são acumulados para a conta de cobrança e para o grupo de gestão de raiz porque ambos estão restritas a um único diretório. Grupos de gestão incluem apenas compras que são baseadas na utilização. As compras, como as reservas e ofertas do Marketplace de terceiros não estão incluídas em grupos de gestão. Então, o grupo de gestão de conta e raiz faturação pode comunicar os totais de diferentes. Para ver esses custos, utilize a conta de cobrança ou o respetivo perfil de faturação.

## <a name="cloud-solution-provider-csp-scopes"></a>Âmbitos de fornecedor de soluções (CSP) da cloud

Parceiros da cloud Solution Provider (CSP) não são atualmente suportados no Cost Management. Em vez disso, pode usar [Centro de parceiros](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Alternar entre âmbitos no Cost Management

Todas as vistas de gestão de custos no portal do Azure incluem uma **âmbito** envenenadas no canto superior esquerdo da vista. Usá-lo para alterar rapidamente o âmbito. Clique nas **âmbito** envenenadas para abrir o Seletor de âmbito. Mostra contas de faturas, o grupo de gestão de raiz e quaisquer subscrições que não são aninhadas sob o grupo de gestão de raiz. Para selecionar um âmbito, clique em segundo plano para destacá-la e, em seguida, clique em **selecione** na parte inferior. Para desagregação em âmbitos aninhados, como grupos de recursos numa subscrição, clique na ligação de nome de âmbito. Para selecionar o âmbito principal em qualquer nível aninhada, clique em **Selecione esta opção &lt;âmbito&gt;**  na parte superior do Seletor de âmbito.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificar o ID de recurso para um âmbito

Ao trabalhar com APIs de gestão de custos, saber que o âmbito é fundamental. Utilize as seguintes informações para criar o URI de âmbito adequado para as APIs de gestão de custos.

### <a name="billing-accounts"></a>Contas de faturação

1. Abra o portal do Azure e, em seguida, navegue para **gestão de custos + faturação** na lista de serviços.
2. Selecione **propriedades** no menu de conta de faturação.
3. Copie o ID da conta de faturação no.
4. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Perfis de faturação

1. Abra o portal do Azure e, em seguida, navegue para **gestão de custos + faturação** na lista de serviços.
2. Selecione **perfis de faturação** no menu de conta de faturação.
3. Clique no nome do perfil de faturação pretendido.
4. Selecione **propriedades** no menu de perfil de faturação.
5. Copie a conta de cobrança e IDs de perfil de faturação.
6. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Secções de fatura

1. Abra o portal do Azure e, em seguida, navegue para **gestão de custos + faturação** na lista de serviços.
2. Selecione **secções de nota fiscal** no menu de conta de faturação.
3. Clique no nome da secção de nota fiscal pretendido.
4. Selecione **propriedades** no menu de seção de nota fiscal.
5. Copie a conta de cobrança e secção IDs de nota fiscal.
6. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Departamentos de EA

1. Abra o portal do Azure e, em seguida, navegue para **gestão de custos + faturação** na lista de serviços.
2. Selecione **departamentos** no menu de conta de faturação.
3. Clique no nome do departamento pretendido.
4. Selecione **propriedades** no menu de departamento.
5. Copie os IDs de conta e o departamento de faturas.
6. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Conta de inscrição de EA

1. Abra o portal do Azure e navegue para **gestão de custos + faturação** na lista de serviços.
2. Selecione **contas de inscrição** no menu de conta de faturação.
3. Clique no nome da conta de inscrição pretendido.
4. Selecione **propriedades** no menu de conta de inscrição.
5. Copie a conta de cobrança e IDs de conta de inscrição.
6. O escopo é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupo de gestão

1. Abra o portal do Azure e navegue para **grupos de gestão** na lista de serviços.
2. Navegue para o grupo de gestão pretendido.
3. Copie o ID de grupo de gestão da tabela.
4. O escopo é: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subscrição

1. Abra o portal do Azure e navegue para **subscrições** na lista de serviços.
2. Copie o ID de subscrição da tabela.
3. O escopo é: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupos de recursos

1. Abra o portal do Azure e navegue para **grupos de recursos** na lista de serviços.
2. Clique no nome do grupo de recursos pretendido.
3. Selecione **propriedades** no menu de grupo de recursos.
4. Copie o valor de campo de ID de recurso.
5. O escopo é: `"/subscriptions/{id}/resourceGroups/{name}"`

Gestão de custos é atualmente suportada no [Global do Azure](https://management.azure.com) e [Azure Government](https://management.usgovcloudapi.net). Para obter mais informações sobre o Azure Government, consulte [pontos de extremidade Global do Azure e a API de Governo](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_._

## <a name="next-steps"></a>Passos Seguintes

- Se ainda não tiver concluído o primeira guia de introdução do Cost Management, leia-a em [começar a analisar os custos](quick-acm-cost-analysis.md).
