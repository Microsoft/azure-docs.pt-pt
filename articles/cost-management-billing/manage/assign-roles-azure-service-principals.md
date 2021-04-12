---
title: Atribuir funções aos nomes principais do serviço do Azure Enterprise Agreement
description: Este artigo ajuda-o a atribuir funções aos nomes principais do serviço utilizando As APIs powerShell e REST.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/05/2021
ms.author: banders
ms.openlocfilehash: d348eeb5cc789665d7e7004523b9feba0ea6e413
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490561"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Atribuir funções aos nomes principais do serviço do Azure Enterprise Agreement

Pode gerir a sua inscrição no [portal Azure Enterprise (Enterprise)](https://ea.azure.com/)no Acordo de Empresa ( Azure Enterprise). Pode criar diferentes funções para gerir a sua organização, ver custos e criar subscrições. Este artigo ajuda-o a automatizar algumas dessas tarefas utilizando Azure PowerShell e REST APIs com nomes principais do serviço Azure (SPNs).

Antes de começar, certifique-se de que está familiarizado com os seguintes artigos:

- [Funções de acordo de empresa](understand-ea-roles.md)
- [Iniciar sessão com o Azure PowerShell](/powershell/azure/authenticate-azureps)
- [Como chamar ASPis de REST com Carteiro](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Crie e aututenda o seu principal de serviço

Para automatizar as ações da EA utilizando uma SPN, é necessário criar uma aplicação Azure Ative Directory (Azure AD). Pode autenticar de forma automatizada. Leia os seguintes artigos e seguindo os passos neles para criar e autenticar o seu principal serviço.

1. [Criar um principal de serviço](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Obtenha valores de ID de inquilino e aplicativo para iniciar sessão](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Aqui está um exemplo de screenshot mostrando o registo da aplicação.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Screenshot mostrando Registar uma aplicação." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Encontre o seu SPN e O ID do Inquilino

Você também precisa do Objeto ID do SPN e o ID do inquilino da app. Precisa da informação para operações de atribuição de permissão em secções posteriores.

Pode encontrar o ID do Inquilino da aplicação AZure AD na página geral da aplicação. Para encontrá-lo no portal Azure, navegue para o Azure Ative Directory e selecione **aplicações enterprise**. Procure a aplicação.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Screenshot mostrando uma aplicação de empresa exemplo." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Selecione a aplicação. Aqui está um exemplo que mostra o ID da aplicação e o ID do objeto.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Screenshot mostrando um ID de aplicação e iD de objeto para uma aplicação da empresa." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

Pode encontrar o ID do inquilino na página geral do Microsoft Azure AD.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Screenshot mostrando onde ver a identificação do seu inquilino." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

O seu iD principal do inquilino também é referido como ID principal, SPN e ID de objeto em vários locais. O valor do seu ID de inquilino Azure AD parece um GUID com o seguinte formato: `11111111-1111-1111-1111-111111111111` .

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Permissões que podem ser atribuídas ao SPN

Para os próximos passos, você dá permissão à app Azure AD para fazer ações usando um papel EA. Pode atribuir apenas as seguintes funções à SPN. O ID de definição de função, exatamente como mostrado, é usado mais tarde em etapas de atribuição.

| Função | Ações permitidas | ID de definição de papel |
| --- | --- | --- |
| Leitor de Inscrições | Pode ver o uso e os encargos em todas as contas e subscrições. Pode ver o saldo do pré-pagamento do Azure (anteriormente chamado de compromisso monetário) associado à inscrição. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| Comprador da EA | Compre ordens de reserva e veja transações de reservas. Pode ver o uso e os encargos em todas as contas e subscrições. Pode ver o saldo do pré-pagamento do Azure (anteriormente chamado de compromisso monetário) associado à inscrição. | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | Descarregue os detalhes de utilização para o departamento que administram. Pode ver o uso e as taxas associadas ao seu departamento. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| Contrator | Criar novas subscrições no âmbito de conta. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Um leitor de inscrições só pode ser atribuído a um SPN por um utilizador com papel de escritor de inscrição.
- Um leitor de departamento só pode ser atribuído a um SPN por um utilizador que tenha papel de escritor de inscrição ou papel de escritor de departamento.
- Uma função de criador de subscrição só pode ser atribuída a um SPN por um utilizador que seja o Titular da Conta da conta de inscrição. O papel não é mostrado no portal da EA. É criado apenas por meios programáticos e é apenas para uso programático.
- O papel de comprador da EA não é mostrado no portal EA. É criado apenas por meios programáticos e é apenas para uso programático.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Atribuir permissão de conta de inscrição para o SPN

Leia as [Atribuições de Funções - Coloque](/rest/api/billing/2019-10-01-preview/roleassignments/put) o artigo da API REST.

Ao ler o artigo, selecione **Experimente-o** para começar a utilizar o SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Screenshot mostrando a opção Try It no artigo 'Put'." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Inscreva-se com a sua conta no arrendatário que tenha acesso à inscrição onde pretende atribuir acesso.

Fornecer os seguintes parâmetros como parte do pedido da API.

**billingAccountName**

O parâmetro é a identificação da conta de faturação. Pode encontrá-lo no portal Azure na página de visão geral de Gestão de Custos + Faturação.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot mostrando identificação da conta de Billing." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

O parâmetro é um GUID único que precisa fornecer. Pode gerar um GUID utilizando o comando [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell.

Ou, pode utilizar o website [Online GUID / UUID Generator](https://guidgenerator.com/) para gerar um GUID único.

**versão api**

Utilize a versão **de pré-visualização 2019-10-01.**

O corpo de pedido tem o código JSON que precisa de usar.

Utilize o corpo de pedido de amostra nas [atribuições de funções - Colocar - Exemplos](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

Há três parâmetros que precisa de usar como parte do JSON.

| Parâmetro | Onde encontrá-lo |
| --- | --- |
| propriedades.principalId | Consulte [a sua identificação SPN e Inquilino.](#find-your-spn-and-tenant-id) |
| propriedades.principalTenantId | Consulte [a sua identificação SPN e Inquilino.](#find-your-spn-and-tenant-id) |
| propriedades.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

O nome da Conta de Faturação é o mesmo parâmetro que utilizou nos parâmetros da API. É a identificação de inscrição que se vê no portal EA e no portal Azure.

Note que `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` é um ID de definição de função de faturação para um Leitor de Inscrição.

Selecione **Executar** para iniciar o comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Screenshot mostrando uma atribuição de função de exemplo colocou Try It com informações de exemplo prontas a executar." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

Uma `200 OK` resposta mostra que o SPN foi adicionado com sucesso.

Agora pode utilizar a SPN (App AD AD Azure com o ID do objeto) para aceder às APIs da EA de forma automatizada. O SPN tem o papel de Leitore de Matrículas.

## <a name="assign-ea-purchaser-role-permission-to-the-spn"></a>Atribuir permissão de papel de comprador da EA à SPN 

Para o papel de comprador da EA, utilize os mesmos passos para o leitor de inscrição. Especifique o `roleDefinitionId` exemplo seguinte.

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

 

## <a name="assign-the-department-reader-role-to-the-spn"></a>Atribua o papel de leitor de departamentos à SPN

Antes de começar, leia as [atribuições de funções do Departamento de Inscrição - Coloque](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) o artigo da API REST.

Ao ler o artigo, selecione **Experimente-o**.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Screenshot mostrando a opção Try It na função atribuições de funções do Departamento de Inscrição Colocar artigo." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Inscreva-se com a sua conta no arrendatário que tenha acesso à inscrição onde pretende atribuir acesso.

Fornecer os seguintes parâmetros como parte do pedido da API.

**billingAccountName**

É a identificação da conta billing. Pode encontrá-lo no portal Azure na página de visão geral de Gestão de Custos + Faturação.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot mostrando identificação da conta de Billing." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

O parâmetro é um GUID único que precisa fornecer. Pode gerar um GUID utilizando o comando [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell.

Ou, pode utilizar o website [Online GUID / UUID Generator](https://guidgenerator.com/) para gerar um GUID único.

**departmentName**

É a identificação do Departamento. Pode ver identificações do departamento no portal Azure. Navegar para a Gestão de Custos + Departamentos de > **de Faturação.**

Para este exemplo, usamos o departamento ACE. O ID para o exemplo é `84819` .

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Screenshot mostrando um exemplo de identificação do departamento." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**versão api**

Utilize a versão **de pré-visualização 2019-10-01.**

O corpo de pedido tem o código JSON que precisa de usar.

Utilize a amostra nas [atribuições de funções do Departamento de Inscrição - Put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put). Há três parâmetros que precisa de usar como parte do JSON.

| Parâmetro | Onde encontrá-lo |
| --- | --- |
| propriedades.principalId | Consulte [a sua identificação SPN e Inquilino.](#find-your-spn-and-tenant-id) |
| propriedades.principalTenantId | Consulte [a sua identificação SPN e Inquilino.](#find-your-spn-and-tenant-id) |
| propriedades.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

O nome da Conta de Faturação é o mesmo parâmetro que utilizou nos parâmetros da API. É a identificação de inscrição que se vê no portal EA e no portal Azure.

A definição de papel de faturação `db609904-a47f-4794-9be8-9bd86fbffd8a` iD é para um Leitor de Departamento.

Selecione **Executar** para iniciar o comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Screenshot mostrando um exemplo Atribuições de Função do Departamento de Inscrição - Coloque REST Tente-o com informações de exemplo prontas a executar." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

Uma `200 OK` resposta mostra que o SPN foi adicionado com sucesso.

Agora pode utilizar a SPN (App AD AD Azure com o ID do objeto) para aceder às APIs da EA de forma automatizada. O SPN tem o papel de DepartmentReader.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Atribuir o papel de criador de subscrição ao SPN

Leia as [Atribuições de Função de Conta de Inscrição - Coloque](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) artigo.

Ao lê-lo, **selecione Tente** atribuir o papel de criador de subscrição ao SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Screenshot mostrando a opção 'Tentar', nas atribuições de funções de conta de inscrição, coloque artigo." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Inscreva-se com a sua conta no arrendatário que tenha acesso à inscrição onde pretende atribuir acesso.

Fornecer os seguintes parâmetros como parte do pedido da API. Leia o artigo nas [Atribuições de Função de Conta de Inscrição - Colocar - Parâmetros URI](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

**billingAccountName**

O parâmetro é a identificação da conta de faturação. Pode encontrá-lo no portal Azure na página de visão geral de Gestão de Custos + Faturação.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot mostrando identificação da conta de Billing." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

O parâmetro é um GUID único que precisa fornecer. Pode gerar um GUID utilizando o comando [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) PowerShell.

Ou, pode utilizar o website [Online GUID / UUID Generator](https://guidgenerator.com/) para gerar um GUID único.
**matrículaName**

O parâmetro é o ID da conta. Encontre o ID da conta para o nome da conta no portal Azure em Gestão de Custos + Faturação no âmbito de Inscrição e departamento.

Para este exemplo, usamos a conta de Teste GTM. A identificação `196987` é.

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Screenshot mostrando a identificação da conta." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**versão api**

Utilize a versão **de pré-visualização 2019-10-01.**

O corpo de pedido tem o código JSON que precisa de usar.

Utilize a amostra nas [atribuições de funções do Departamento de Inscrição - Colocar - Exemplos](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

Há três parâmetros que precisa de usar como parte do JSON.

| Parâmetro | Onde encontrá-lo |
| --- | --- |
| propriedades.principalId | Consulte [a sua identificação SPN e Inquilino.](#find-your-spn-and-tenant-id) |
| propriedades.principalTenantId | Consulte [a sua identificação SPN e Inquilino.](#find-your-spn-and-tenant-id) |
| propriedades.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/registrationAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

O nome da Conta de Faturação é o mesmo parâmetro que utilizou nos parâmetros da API. É a identificação de inscrição que se vê no portal EA e no portal Azure.

O ID de definição de papel de faturação `a0bcee42-bf30-4d1b-926a-48d21664ef71` é para o papel de criador de subscrição.

Selecione **Executar** para iniciar o comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Screenshot mostrando a opção Try It nas atribuições de funções de conta de inscrição - Coloque artigo" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

Uma `200 OK` resposta mostra que o SPN foi adicionado com sucesso.

Agora pode utilizar a SPN (App AD AD Azure com o ID do objeto) para aceder às APIs da EA de forma automatizada. O SPN tem o papel de SubscriptionCreator.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a administração do portal Azure EA](ea-portal-administration.md).