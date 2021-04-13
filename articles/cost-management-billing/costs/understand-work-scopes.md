---
title: Compreender e trabalhar com âmbitos do Azure Cost Management
description: Este artigo ajuda-o a compreender os âmbitos de gestão de faturação e de recursos disponíveis no Azure e como utilizar os âmbitos no Cost Management e nas APIs.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 693045d094ddbf5ad6df81eca8cd16d9cac62fe4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308613"
---
# <a name="understand-and-work-with-scopes"></a>Compreender e trabalhar com âmbitos

Este artigo ajuda-o a compreender os âmbitos de gestão de faturação e de recursos disponíveis no Azure e como utilizar os âmbitos no Cost Management e nas APIs.

## <a name="scopes"></a>Âmbitos

Um _âmbito_ é um nó na hierarquia de recursos do Azure onde os utilizadores do Azure Active Directory acedem e gerem os serviços. A maioria dos recursos do Azure são criados e implementados para grupos de recursos, que fazem parte das subscrições. A Microsoft também oferece duas hierarquias acima das subscrições do Azure que têm funções especializadas para gerir dados de faturação:
- Dados de faturação, como pagamentos e faturas
- Serviços cloud, como governação de custos e políticas

Os âmbitos são onde gere os dados de faturação, tem funções específicas de pagamentos, visualiza faturas e onde realiza a gestão global da conta. As funções de faturação e de conta são geridas separadamente das funções que são utilizadas na gestão de recursos, que utilizam as funções [RBAC do Azure](../../role-based-access-control/overview.md). Para fazermos a distinção clara do objetivo dos diferentes âmbitos, incluindo as diferenças de controlo de acesso, serão designados como _âmbitos de faturação_ e _âmbitos RBAC do Azure_, respetivamente.

Para saber mais sobre os âmbitos, veja o vídeo [Cost Management setting up hierarchies](https://www.youtube.com/watch?v=n3TLRaYJ1NY) (Configuração de hierarquias do Cost Management). Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Como o Cost Management utiliza os âmbitos

O Cost Management funciona em todos os âmbitos acima dos recursos para permitir às organizações gerir os custos ao nível para o qual têm acesso, quer se trate da conta de faturação na sua globalidade ou de um de um grupo de recursos único. Apesar de os âmbitos de faturação diferirem com base no contrato Microsoft (tipo de subscrição), os âmbitos RBAC do Azure não.

## <a name="azure-rbac-scopes"></a>Âmbitos RBAC do Azure

O Azure suporta três âmbitos na gestão de recursos. Cada âmbito suporta o acesso de gestão e a governação, incluindo, entre outros, a gestão de custos.

- [**Grupos de gestão**](../../governance/management-groups/overview.md) – contentores hierárquicos, de até oito níveis, para organizar as subscrições do Azure.

    Tipo de recurso: [Microsoft.Management/managementGroups](/rest/api/managementgroups/)

- **Subscrições** – contentores primários dos recursos do Azure.

    Tipo de recurso: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupos de recursos**](../../azure-resource-manager/management/overview.md#resource-groups) – agrupamentos lógicos de recursos relacionados para uma solução do Azure que partilha o mesmo ciclo de vida. Por exemplo, recursos que são implementados e eliminados em conjunto.

    Tipo de recurso: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Os grupos de gestão permitem-lhe organizar as subscrições por hierarquia. Por exemplo, pode criar uma hierarquia de organização lógica com os grupos de gestão. Em seguida, atribua às equipas subscrições para cargas de trabalho de produção e Dev/Test. Crie, depois, os grupos de recursos nas subscrições para gerir cada subsistema ou componente.

A criação de uma hierarquia organizacional permite o rollup dos custos e da conformidade com a política a nível organizacional. Em seguida, cada líder pode ver e analisar os custos atuais. Depois disto, podem criar orçamentos para limitar os maus padrões de despesas e otimizar os custos com as recomendações do Assistente ao nível mais baixo.

A concessão de acesso para ver os custos e, opcionalmente, gerir a configuração dos custos, como orçamentos e exportações, é feita nos âmbitos de governação com o RBAC do Azure. Utiliza o RBAC do Azure para conceder acesso aos utilizadores e grupos do Azure Active Directory para realizarem um conjunto de ações predefinido que é definido numa função, num âmbito específico, e abaixo. Por exemplo, uma função atribuída a um âmbito de grupo de gestão também concede as mesmas permissões a subscrições e grupos de recursos aninhados.

O Cost Management suporta as seguintes funções incorporadas para cada um dos âmbitos que se seguem:

- [**Proprietário**](../../role-based-access-control/built-in-roles.md#owner) – pode ver os custos e gerir tudo, incluindo a configuração de custos.
- [**Contribuidor**](../../role-based-access-control/built-in-roles.md#contributor) – pode ver os custos e gerir tudo, incluindo a configuração de custos, mas excluindo o controlo de acesso.
- [**Leitor**](../../role-based-access-control/built-in-roles.md#reader) – pode ver tudo, incluindo os dados e a configuração dos custos, mas não pode fazer alterações.
- [**Contribuidor do Cost Management**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – pode ver os custos, gerir a configuração de custos e ver as recomendações.
- [**Leitor do Contribuidor do Cost Management**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – pode ver os dados de custos, gerir a configuração de custos e ver as recomendações.

O Contribuidor do Cost Management é a função de privilégios mínimos recomendada. Esta função permite criar e gerir orçamentos e exportações, de modo a monitorizar e gerar relatórios de custos de forma mais eficaz. Os Contribuidores do Cost Management também poderão necessitar de funções adicionais para suporte de cenários de gestão de custos complexos. Pondere os seguintes cenários:

- **Relatório da utilização de recursos** – o Azure Cost Management mostra o custo no portal do Azure, o que inclui a utilização, no que diz respeito aos custos nos padrões de utilização completos. Este relatório também pode mostrar custos associados a API e transferências, mas também pode querer aprofundar métricas de utilização detalhadas no Azure Monitor para obter uma compreensão mais profunda. Pondere conceder a função de [Leitor de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader) a qualquer âmbito onde também precise de relatórios de métricas de utilização detalhadas.
- **Agir quando os orçamentos são excedidos** – os Contribuidores do Cost Management também precisam de acesso para criar e gerir grupos de ações para reagir automaticamente a utilizações excedidas. Pondere conceder o [Contribuidor de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-contributor) a um grupo de recursos que contenha um grupo de ações para utilizar quando os limiares do orçamento são excedidos. A automatização de ações específicas requer funções adicionais para os serviços específicos utilizados, como a Automatização e as Funções do Azure.
- **Agendar exportação de dados de custos** – os Contribuidores do Cost Management também necessitam de acesso para gerir as contas de armazenamento para agendarem uma exportação para copiar dados para uma conta de armazenamento. Pondere conceder o [Contribuidor de Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor) a um grupo de recursos que contenha a conta de armazenamento de onde são exportados os dados de custos.
- **Ver recomendações de poupança de custos** – os Leitores do Cost Management e Contribuidores do Cost Management têm acesso para *ver* recomendações de custos por predefinição. Contudo, o acesso para agir sobre as recomendações de custos requer acesso a recursos individuais. Pondere conceder uma [função específica a um serviço](../../role-based-access-control/built-in-roles.md#all), caso queira agir sobre uma recomendação com base nos custos.

Os grupos de gestão apenas são suportados se incluírem subscrições Contrato Enterprise (EA), pay as you go (PAYG) ou internas da Microsoft. Os grupos de gestão com outros tipos de subscrição, como subscrições de Contrato de Cliente Microsoft ou Azure Active Directory, não podem ver os custos. Se tiver uma mistura de subscrições, mova as subscrições não suportadas para um arm separado da hierarquia do grupo de gestão para ativar o Cost Management nas subscrições suportadas. A título de exemplo, crie dois grupos de gestão no grupo de gestão de raiz: **Azure AD** e **My Org**. Mova a subscrição do Azure AD para o grupo de gestão **Azure AD** e, em seguida, veja e faça a gestão dos custos com o grupo de gestão **My Org**.

## <a name="enterprise-agreement-scopes"></a>Âmbitos do Contrato Enterprise

As contas de faturação do Contrato Enterprise (EA), também chamadas inscrições, têm os seguintes âmbitos:

- [**Conta de faturação**](../manage/view-all-accounts.md) – representa uma inscrição do EA. As faturas são geradas neste âmbito. As compras que não se baseiam na utilização, como no caso do Marketplace e das reservas, estão disponíveis apenas neste âmbito. Não são representadas em departamentos nem contas de inscrição. A utilização de reservas, juntamente com todos os outros tipos de utilização, é aplicada a recursos individuais. A utilização é implementada nas subscrições incluídas na conta de faturação. Para ver os custos de reserva discriminados em cada recurso, mude para ver os **Custos amortizados** na análise de custos.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Departamento** – agrupamento opcional de contas de inscrição.

    Tipo de recurso: `Billing/billingAccounts/departments`

- **Conta de inscrição** – representa um único proprietário da conta. Não suporta a concessão de acesso a várias pessoas.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Apesar de os âmbitos de governação estarem ligados a um diretório único, os âmbitos de faturação do EA não estão. Uma conta de faturação do EA poderá ter subscrições numa variada quantidade de diretórios do Azure Active Directory.

Os âmbitos de faturação do EA suportam as seguintes funções:

- **Administrador Enterprise** – pode gerir definições e acesso de contas de faturação, pode ver todos os custos e pode gerir a configuração de custos. Por exemplo, orçamentos e exportações. Na função, o âmbito de faturação do EA é o mesmo que a [função do Azure Contribuidor do Cost Management](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Utilizador só de leitura do Enterprise** – pode ver as definições da conta de faturação, os dados de custos e a configuração de custos. Por exemplo, orçamentos e exportações. Na função, o âmbito de faturação do EA é o mesmo que a [função do Azure Leitor do Cost Management](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrador de departamento** – pode gerir as definições de departamento, como o centro de custos, e pode aceder, ver todos os custos e gerir a configuração de custos. Por exemplo, orçamentos e exportações.  A definição da conta de faturação **Custos de vista do AD** tem de ser ativada para os administradores de departamento e os utilizadores só de leitura verem os custos. Se a opção **Os Administradores de Departamento podem ver os custos** estiver desativada, os utilizadores de departamento não conseguirão ver os custos em nenhum dos níveis, mesmo que sejam um proprietário de uma conta ou subscrição.
- **Utilizador só de leitura de departamento** – pode ver as definições de departamento, os dados de custos e a configuração de custos. Por exemplo, orçamentos e exportações. Se a opção **Os Administradores de Departamento podem ver os custos** estiver desativada, os utilizadores de departamento não conseguirão ver os custos em nenhum dos níveis, mesmo que sejam um proprietário de uma conta ou subscrição.
- **Proprietário da conta** – pode gerir as definições de contas de inscrição (como o centro de custos), ver todos os custos e gerir a configuração de custos (como os orçamentos e as exportações) da conta de inscrição. Para os proprietários de conta e os utilizadores de RBAC do Azure verem os custos, a definição da conta de faturação **Custos de vista do PC** tem de ser ativada.

Os utilizadores de contas de faturação do EA não têm acesso direto a faturas. As faturas estão disponíveis num sistema de licenciamento de volume externo.

As subscrições do Azure são aninhadas nas contas de inscrição. Os utilizadores de faturação têm acesso aos dados dos custos das subscrições e dos grupos de recursos que estão nos seus âmbitos. Não têm acesso para ver ou gerir recursos no portal do Azure. Os utilizadores conseguem ver os custos ao navegar para **Cost Management + Faturação** na lista de serviços do portal do Azure. Em seguida, podem filtrar os custos para as subscrições e os grupos de recursos específicos sobre os quais pretendem gerar relatórios.

Os utilizadores de faturação não têm acesso aos grupos de gestão porque não se encaixam explicitamente numa conta de faturação específica. O acesso tem de ser concedido a grupos de gestão explicitamente. Os grupos de gestão fazem o roll-up de custos de todas as subscrições aninhadas. Contudo, apenas incluem compras baseadas na utilização. Não incluem compras tais como reservas e ofertas do Marketplace de terceiros. Para ver estes custos, utilize a conta de faturação do EA.

## <a name="individual-agreement-scopes"></a>Âmbitos de contrato individual

As subscrições do Azure criadas a partir de ofertas individuais como de Pay As You Go e outros tipos relacionados, como ofertas de Avaliação Gratuita e de Dev/Test, não têm um âmbito de conta de faturação explícito. Em vez disso, cada subscrição tem um proprietário de conta ou administrador de conta, como o proprietário da conta do EA.

- [**Conta de faturação**](../manage/view-all-accounts.md) – representa um único proprietário de conta de uma ou mais subscrições do Azure. Atualmente, não suporta a concessão de acesso a várias pessoas nem o acesso a vistas de custos agregados.

    Tipo de recurso: Não aplicável

Os administradores de conta de subscrição individual Azure podem ver e gerir dados de faturação, tais como faturas e pagamentos, a partir do [portal Azure](https://portal.azure.com)  >  **As assinaturas** > selecionar uma subscrição.

Ao contrário do EA, os administradores de contas de subscrição do Azure podem ver as faturas deles no portal do Azure. Lembre-se de que as funções de Leitor do Cost Management e Contribuidor do Cost Management não fornecem acesso a faturas. Para obter mais informações, veja [Como conceder acesso a faturas](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Âmbitos do Contrato de Cliente Microsoft

As contas de faturação do Contrato de Cliente Microsoft têm os seguintes âmbitos:

- **Conta de faturação** – representa um contrato de cliente para vários produtos e serviços Microsoft. As contas de faturação do Contrato de Cliente não são funcionalmente o mesmo que as inscrições do EA. As inscrições do EA estão mais proximamente alinhadas com os perfis de faturação.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Perfil de faturação** – define as subscrições que estão incluídas numa fatura. Os perfis de faturação são o equivalente funcional de uma inscrição do EA, uma vez que é esse o âmbito em que são geradas as faturas. Da mesma forma, as compras que não se baseiam na utilização, como no caso do Marketplace e das reservas, estão disponíveis apenas neste âmbito. Não estão incluídas nas secções de faturação.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Secção de faturas** – representa um grupo de subscrições numa fatura ou num perfil de faturação. As secções de faturas são como departamentos: várias pessoas podem ter acesso a uma secção de faturação.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Cliente** – representa um grupo de subscrições que estão associadas a um cliente específico que foi integrado num Contrato de Cliente Microsoft por um parceiro. Este âmbito é específico dos Fornecedores de Soluções Cloud (CSP).

Ao contrário dos âmbitos de faturação do EA, as contas de faturação do Contrato de Cliente _estão_ ligadas a um único diretório e não podem ter subscrições em vários diretórios do Azure Active Directory.

Os âmbitos de faturação do Contrato do Cliente não se aplicam aos parceiros. As funções e permissões de parceiros estão documentadas em [Atribuir funções e permissões de utilizadores](/partner-center/permissions-overview).

Os âmbitos de faturação do Contrato do Cliente suportam as seguintes funções:

- **Proprietário** – pode gerir as definições de faturação e acesso, ver todos os custos e gerir a configuração de custos. Por exemplo, orçamentos e exportações. Na função, este âmbito de faturação do Contrato de Cliente é o mesmo que a [função do Azure Contribuidor do Cost Management](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Contribuidor** – pode gerir as definições de faturação, exceto o acesso, ver todos os custos e gerir a configuração de custos. Por exemplo, orçamentos e exportações. Na função, este âmbito de faturação do Contrato de Cliente é o mesmo que a [função do Azure Contribuidor do Cost Management](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Leitor** – pode ver as definições da faturação, os dados de custos e a configuração de custos. Por exemplo, orçamentos e exportações. Na função, este âmbito de faturação do Contrato de Cliente é o mesmo que a [função do Azure Leitor do Cost Management](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Gestor de faturas** – pode ver e pagar faturas e pode ver os dados de custos e a configuração. Por exemplo, orçamentos e exportações. Na função, este âmbito de faturação do Contrato de Cliente é o mesmo que a [função do Azure Leitor do Cost Management](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Criador de subscrições do Azure** – pode criar subscrições do Azure, ver custos e gerir a configuração de custos. Por exemplo, orçamentos e exportações. Na função, este âmbito da faturação do Contrato do Cliente é o mesmo que a função de proprietário da conta de inscrição do EA.

As subscrições do Azure são aninhadas nas secções de faturação, da mesma forma que são aninhadas nas contas de inscrição do EA. Os utilizadores de faturação têm acesso aos dados dos custos das subscrições e dos grupos de recursos que estão nos seus âmbitos. Contudo, não têm acesso para ver ou gerir recursos no portal do Azure. Os utilizadores de faturação podem ver os custos ao navegar para **Cost Management + Faturação** na lista de serviços do portal do Azure. Em seguida, basta filtrar os custos para as subscrições e os grupos de recursos específicos sobre os quais pretendem gerar relatórios.

Os utilizadores de faturação não têm acesso aos grupos de gestão porque não se encaixam explicitamente na conta de faturação. Contudo, quando os grupos de gestão são ativados para a organização, é feito o rollup de todos os custos de subscrições para a conta de faturação e para o grupo de gestão de raiz, pois estão ambos restritos a um único diretório. Os grupos de gestão apenas incluem compras baseadas na utilização. As compras como reservas e ofertas do Marketplace de terceiros não são incluídas nos grupos de gestão. Por isso, a conta de faturação e o grupo de gestão de raiz podem reportar totais diferentes. Para ver estes custos, utilize a conta de faturação ou o respetivo perfil de faturação.

## <a name="aws-scopes"></a>Âmbitos do AWS

Depois de a integração do AWS ser concluída, veja [Configurar a integração do AWS](aws-integration-set-up-configure.md). Estão disponíveis os seguintes âmbitos:

- **Conta de faturação externa** – representa um contrato do cliente com um fornecedor de terceiros. É semelhante à conta de faturação do EA.

    Tipo de recurso: `Microsoft.CostManagement/externalBillingAccounts`

- **Subscrição externa** – representa uma conta operacional do cliente com um fornecedor de terceiros. É semelhante a uma subscrição do Azure.

    Tipo de recurso: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Âmbitos de Fornecedor de Soluções Cloud (CSP)

Os seguintes âmbitos são suportados para CPSs com clientes com um Contrato de Cliente Microsoft:

- **Conta de faturação** – representa um contrato de cliente para vários produtos e serviços Microsoft. As contas de faturação do Contrato de Cliente não são funcionalmente o mesmo que as inscrições do EA. As inscrições do EA estão mais proximamente alinhadas com os perfis de faturação.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Perfil de faturação** – define as subscrições que estão incluídas numa fatura. Os perfis de faturação são o equivalente funcional de uma inscrição do EA, uma vez que é esse o âmbito em que são geradas as faturas. Da mesma forma, as compras que não se baseiam na utilização, como no caso do Marketplace e das reservas, estão disponíveis apenas neste âmbito.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Cliente** – representa um grupo de subscrições que estão associadas a um cliente específico que foi integrado num Contrato de Cliente Microsoft por um parceiro.

Apenas os utilizadores com as funções *Administrador global* e *Agente de administrador* podem gerir e ver os custos para contas de faturação, perfis de faturação e clientes diretamente no inquilino do Azure do parceiro. Para obter mais informações sobre as funções do centro de parceiros, veja [Assign users roles and permissions](/partner-center/permissions-overview) (Atribuir funções e permissões de utilizadores).

O Azure Cost Management apenas suporta clientes de parceiros CSP se os clientes tiverem um Contrato de Cliente Microsoft. Para clientes com suporte de CSP que não tenham ainda um Contrato de Cliente Microsoft, veja [Centro de Parceiros](/azure/cloud-solution-provider/overview/partner-center-overview).

Os grupos de gestão nos âmbitos CSP não possuem suporte para o Cost Management. Se tiver uma subscrição CSP e definir o âmbito para um grupo de gestão na análise de custos, verá um erro semelhante a:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Alternar entre âmbitos no Cost Management

Todas as vistas do Cost Management no portal do Azure incluem um atalho de seleção de **Âmbito** no canto superior esquerdo da vista. Utilize-o para alterar rapidamente o âmbito. Selecione o atalho de **Âmbito** para abrir o seletor de âmbitos. Este mostra as contas de faturação, o grupo de gestão de raiz e quaisquer subscrições que não estejam aninhadas sob o grupo de gestão de raiz. Para selecionar um âmbito, selecione o fundo para o realçar e, em seguida, escolha **Selecionar** na parte inferior. Para desagregar para âmbitos aninhados, como grupos de recursos numa subscrição, selecione a ligação do nome do âmbito. Para selecionar o âmbito principal em qualquer nível aninhado, selecione **Selecionar este &lt;âmbito&gt;** na parte superior do seletor de âmbitos.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificar o ID de recurso de um âmbito

Quando está a trabalhar com as APIs do Cost Management, é fundamental conhecer o âmbito. Utilize as informações que se seguem para criar o URI de âmbito adequado para as APIs do Cost Management.

### <a name="billing-accounts"></a>Contas de faturação

1. Abra o portal do Azure e, em seguida, navegue para **Cost Management + Faturação** na lista de serviços.
2. Selecione **Propriedades** no menu de contas de faturação.
3. Copie o ID da conta de faturação.
4. O seu âmbito é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Perfis de faturação

1. Abra o portal do Azure e, em seguida, navegue para **Cost Management + Faturação** na lista de serviços.
2. Selecione **Perfis de faturação** no menu de contas de faturação.
3. Selecione o nome do perfil de faturação.
4. Selecione **Propriedades** no menu de perfis de faturação.
5. Copie o ID da conta de faturação e do perfil de faturação.
6. O seu âmbito é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Secções de faturação

1. Abra o portal do Azure e, em seguida, navegue para **Cost Management + Faturação** na lista de serviços.
2. Selecione **Secções de faturação** no menu de contas de faturação.
3. Selecione o nome da secção de faturas.
4. Selecione **Propriedades** no menu da secção de faturação.
5. Copie o ID da conta de faturação e da secção de faturação.
6. O seu âmbito é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Departamentos do EA

1. Abra o portal do Azure e, em seguida, navegue para **Cost Management + Faturação** na lista de serviços.
2. Selecione **Departamentos** no menu de contas de faturação.
3. Selecione o nome do departamento.
4. Selecione **Propriedades** no menu de departamentos.
5. Copie os IDs da conta de faturação e do departamento.
6. O seu âmbito é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Conta de inscrição do EA

1. Abra o portal do Azure e navegue para **Cost Management + Faturação** na lista de serviços.
2. Selecione **Contas de inscrição** no menu de contas de faturação.
3. Selecione o nome da conta de inscrição.
4. Selecione **Propriedades** no menu de contas de inscrição.
5. Copie os IDs da conta de faturação e da conta de inscrição.
6. O seu âmbito é: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupo de gestão

1. Abra o portal do Azure e navegue para **Grupos de gestão** na lista de serviços.
2. Navegue para o grupo de gestão.
3. Copie o ID do grupo de gestão da tabela.
4. O seu âmbito é: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subscrição

1. Abra o portal do Azure e navegue para **Subscrições** na lista de serviços.
2. Copie o ID da subscrição da tabela.
3. O seu âmbito é: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupos de recursos

1. Abra o portal do Azure e navegue para **Grupos de recursos** na lista de serviços.
2. Selecione o nome do grupo de recursos.
3. Selecione **Propriedades** no menu de grupos de recursos.
4. Copie o valor do campo de ID do recurso.
5. O seu âmbito é: `"/subscriptions/{id}/resourceGroups/{name}"`

O Cost Management é atualmente suportado no [Azure Global](https://management.azure.com) e no [Azure Government](https://management.usgovcloudapi.net). Para obter mais informações sobre o Azure Government, veja [Azure Global and Government API endpoints](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping) (Pontos finais da API do Azure Global e Government).

## <a name="next-steps"></a>Passos seguintes

- Se ainda não tiver concluído o primeiro início rápido do Cost Management, leia-o em [Começar a analisar os custos](quick-acm-cost-analysis.md).
