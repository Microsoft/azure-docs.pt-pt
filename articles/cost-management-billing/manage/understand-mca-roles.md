---
title: Funções de faturação do Contrato de Cliente Microsoft – Azure
description: Saiba mais sobre as funções de faturação para contas de faturação no Azure para Contratos de Cliente da Microsoft.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.openlocfilehash: 00ac61567502984759c5db9837060c86aaee378d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593591"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Compreender as funções administrativas do Contrato de Cliente da Microsoft no Azure

Para gerir a sua conta de faturação para um Contrato de Cliente da Microsoft, utilize as funções descritas nas secções a seguir. Estas funções acrescem às funções incorporadas do Azure para controlar o acesso aos recursos. Para obter mais informações, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

Este artigo aplica-se a uma conta de faturação para um Contrato de Cliente da Microsoft. Verifique se tem acesso a um Contrato de Cliente da Microsoft.

## <a name="billing-role-definitions"></a>Definições de função de faturação

A tabela a seguir descreve as funções de faturação que utiliza para gerir a sua conta de faturação, os perfis de faturação e as secções de fatura.

|Função|Descrição|
|---|---|
|Proprietário da conta de faturação|Gere tudo na conta de faturação|
|Contribuidor da conta de faturação|Gere tudo exceto permissões na conta de faturação|
|Leitor da conta de faturação|Vista só de leitura de tudo na conta de faturação|
|Proprietário do perfil de faturação|Gere tudo no perfil de faturação|
|Contribuidor do perfil de faturação|Gere tudo exceto permissões no perfil de faturação|
|Leitor do perfil de faturação|Vista só de leitura de tudo no perfil de faturação|
|Gestor de faturas|Ver faturas para o perfil de faturação|
|Proprietário da secção de fatura|Gere tudo na secção de fatura|
|Contribuidor da secção de fatura|Gere tudo exceto permissões na secção de fatura|
|Leitor da secção de fatura|Vista só de leitura de tudo na secção de fatura|
|Criador de subscrições do Azure|Cria subscrições do Azure|

## <a name="billing-account-roles-and-tasks"></a>Funções e tarefas da conta de faturação

Uma conta de faturação permite-lhe gerir a faturação da organização. Utiliza a conta de faturação para organizar custos, monitorizar custos e faturas e controlar o acesso de faturação para a sua organização. Para obter mais informações, veja [Compreender a conta de faturação](../understand/mca-overview.md#your-billing-account).

As tabelas a seguir mostram qual a função necessária para realizar tarefas no contexto da conta de faturação.

### <a name="manage-billing-account-permissions-and-properties"></a>Gerir permissões e propriedades da conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver permissões existentes para a conta de faturação|✔|✔|✔|
|Conceder permissões a outras pessoas para verem e gerirem a conta de faturação|✔|✘|✘|
|Ver propriedades da conta de faturação, como nome da empresa, endereço e mais|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Gerir perfis de faturação para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todos os perfis de faturação na conta|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Gerir faturas para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as faturas na conta|✔|✔|✔|
|Transferir faturas, ficheiros de utilização e custos do Azure, folhas de preços e documentos fiscais na conta|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Gerir secções de fatura para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as secções de fatura na conta|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Gerir transações para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as transações de faturação da conta|✔|✔|✔|
|Ver todos os produtos comprados para a conta|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Gerir subscrições da conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as subscrições do Azure na conta de faturação|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Funções e tarefas do perfil de faturação

Um perfil de faturação permite-lhe gerir as suas faturas e os métodos de pagamento. Uma fatura mensal é gerada para as subscrições do Azure e outros produtos comprados com o perfil de faturação. Utiliza os métodos de pagamento para pagar a fatura. Para obter mais informações, veja [Compreender os perfis de faturação](../understand/mca-overview.md#billing-profiles).

As tabelas a seguir mostram qual a função necessária para realizar tarefas no contexto do perfil de faturação.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gerir permissões, propriedades e políticas do perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver permissões existentes para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Conceder permissões a outras pessoas para verem e gerirem o perfil de faturação|✔|✘|✘|✘|✘|✘|✘|
|Ver propriedades do perfil de faturação, como número da OC, preferência de envio de fatura por e-mail e mais|✔|✔|✔|✔|✔|✔|✔|
|Atualizar propriedades do perfil de faturação |✔|✔|✘|✘|✘|✘|✘|
|Ver políticas aplicadas no perfil de faturação, como ativar compras de reservas do Azure, ativar compras no Azure Marketplace e mais|✔|✔|✔|✔|✔|✔|✔|
|Aplicar políticas no perfil de faturação |✔|✔|✘|✘|✘|✘|✘|
|Gerir encomendas de reservas |✔|✔|✘|✘|✘|✘|✘|
|Ver encomendas de reserva |✔|✔|✔|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gerir faturas para o perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as faturas do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Transferir faturas, ficheiros de utilização e custos do Azure, folhas de preços e documentos fiscais do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gerir as secções de fatura do perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as secções de fatura do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Criar nova secção de fatura do perfil de faturação|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Gerir transações para o perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as transações de faturação do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Gerir os métodos de pagamento do perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver os métodos de pagamento do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Controlar o saldo de créditos do Azure para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gerir subscrições do perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as subscrições do Azure do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Funções e tarefas da secção de fatura

Uma secção de fatura permite-lhe organizar os custos na fatura. Pode criar uma secção para organizar os custos por departamento, ambiente de desenvolvimento ou com base nas necessidades da sua organização. Conceda permissões a outras pessoas para criar subscrições do Azure para a secção. Todos os custos de utilização e compras para as subscrições são então apresentados na secção da fatura. Para obter mais informações, veja [Compreender a secção de fatura](../understand/mca-overview.md#invoice-sections).

As tabelas a seguir mostram qual a função necessária para realizar tarefas no contexto das secções de fatura.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gerir permissões e propriedades da secção de fatura

|Tarefas|Proprietário da secção de fatura|Contribuidor da secção de fatura|Leitor da secção de fatura|Criador de subscrições do Azure|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação |
|---|---|---|---|---|---|---|---|
|Ver todas as permissões na secção de fatura|✔|✔|✔|✔|✔|✔|✔|
|Conceder permissões a outras pessoas para verem e gerirem a secção de fatura|✔|✘|✘|✘|✘|✘|✘|
|Ver propriedades da secção de fatura|✔|✔|✔|✔|✔|✔|✔|
|Atualizar propriedades da secção de fatura|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Gerir produtos para a secção de fatura

|Tarefas|Proprietário da secção de fatura|Contribuidor da secção de fatura|Leitor da secção de fatura|Criador de subscrições do Azure|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todos os produtos comprados na secção de fatura|✔|✔|✔|✘|✔|✔|✔|
|Gerir a faturação de produtos na secção de fatura, como cancelar, desativar a renovação automática e mais|✔|✔|✘|✘|✘|✘|✘|
|Alterar a secção de fatura dos produtos|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gerir subscrições da secção de fatura

|Tarefas|Proprietário da secção de fatura|Contribuidor da secção de fatura|Leitor da secção de fatura|Criador de subscrições do Azure|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as subscrições do Azure da secção de fatura|✔|✔|✔|✘|✔|✔|✔|
|Alterar a secção de fatura das subscrições|✔|✔|✘|✘|✘|✘|✘|
|Solicitar a propriedade da faturação das subscrições de utilizadores noutras contas de faturação|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Tarefas e funções da faturação de subscrição

A tabela a seguir mostra qual a função necessária para realizar tarefas no contexto de uma subscrição.

|Tarefas|Proprietário da secção de fatura|Contribuidor da secção de fatura|Leitor da secção de fatura|Criador de subscrições do Azure|
|---|---|---|---|---|
|Cria subscrições do Azure|✔|✔|✘|✔|
|Atualizar o centro de custos da subscrição|✔|✔|✘|✘|
|Alterar a secção de fatura da subscrição|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gerir funções de faturação no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Selecione **Controlo de acesso (IAM)** num âmbito, tal como conta de faturação, perfil de faturação ou secção de fatura, no qual pretende conceder acesso.

4. A página Controlo de acesso (IAM) apresenta uma lista dos utilizadores e grupos que estão atribuídos a cada função para esse âmbito.

   ![Captura de ecrã a mostrar a lista de administradores para a conta de faturação](./media/understand-mca-roles/billing-list-admins.png)

5. Para conceder acesso a um utilizador, selecione **Adicionar** na parte superior da página. Na lista pendente Função, selecione uma função. Introduza o endereço de e-mail do utilizador ao qual pretende conceder acesso. Selecione **Guardar** para atribuir a função.

   ![Captura de ecrã a mostrar a adição de um administrador a uma conta de faturação](./media/understand-mca-roles/billing-add-admin.png)

6. Para remover o acesso de um utilizador, selecione o utilizador com a atribuição de função que pretende remover. Selecione Remover.

   ![Captura de ecrã a mostrar a remoção de um administrador de uma conta de faturação](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte
Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre a conta de faturação:

- [Introdução à conta de faturação do Contrato de Cliente da Microsoft](../understand/mca-overview.md)
- [Criar uma subscrição do Azure para a sua conta de faturação do Contrato de Cliente da Microsoft](create-subscription.md)
