---
title: Funções de faturação do Contrato de Cliente Microsoft – Azure
description: Saiba mais sobre as funções de faturação para contas de faturação no Azure para Contratos de Cliente da Microsoft.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: e334a423fd11aa3a357d52099a792dcc905aedeb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011668"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Compreender as funções administrativas do Contrato de Cliente da Microsoft no Azure

Para gerir a sua conta de faturação para um Contrato de Cliente da Microsoft, utilize as funções descritas nas secções a seguir. Estas funções acrescem às funções incorporadas do Azure para controlar o acesso aos recursos. Para obter mais informações, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md).

Este artigo aplica-se a uma conta de faturação para um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

Assista ao acesso a gerir o vídeo [da sua conta de faturação MCA](https://www.youtube.com/watch?v=9sqglBlKkho) para saber como pode controlar o acesso à sua conta de faturação do Microsoft Customer Agreement (MCA).

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

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
|Gestor de faturas|Visualiza e paga faturas para o perfil de faturação|
|Proprietário da secção de fatura|Gere tudo na secção de fatura|
|Contribuidor da secção de fatura|Gere tudo exceto permissões na secção de fatura|
|Leitor da secção de fatura|Vista só de leitura de tudo na secção de fatura|
|Criador de subscrições do Azure|Cria subscrições do Azure|

## <a name="billing-account-roles-and-tasks"></a>Funções e tarefas da conta de faturação

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. As funções na conta de faturação têm o mais alto nível de permissões e os utilizadores nestas funções obtêm visibilidade na informação de custos e faturação de toda a sua conta. Atribua estas funções apenas aos utilizadores que necessitem de visualizar faturas e rastreiem os custos de toda a sua conta, como membro das equipas de finanças e de contabilidade. Para obter mais informações, veja [Compreender a conta de faturação](../understand/mca-overview.md#your-billing-account).

As tabelas a seguir mostram qual a função necessária para realizar tarefas no contexto da conta de faturação.

### <a name="manage-billing-account-permissions-and-properties"></a>Gerir permissões e propriedades da conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver atribuições de funções para conta de faturação|✔|✔|✔|
|Conceder permissões a outras pessoas para verem e gerirem a conta de faturação|✔|✘|✘|
|Ver propriedades de conta de faturação como endereço, acordos e muito mais|✔|✔|✔|
|Atualizar propriedades de conta de faturação como vendido-para, nome de exibição, e muito mais|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>Gerir perfis de faturação para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todos os perfis de faturação da conta|✔|✔|✔|
|Criar novos perfis de faturação|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>Gerir faturas para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as faturas da conta|✔|✔|✔|
|Pagar faturas com cartão de crédito|✔|✔|✘|
|Baixar faturas, ficheiros de uso do Azure, folhas de preços e documentos fiscais|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Gerir produtos para conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todos os produtos comprados para a conta|✔|✔|✔|
|Gerir faturação para produtos como cancelar, desligar a renovação automática, e muito mais|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>Gerir subscrições da conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as subscrições do Azure criadas para a conta de faturação|✔|✔|✔|
|Criar novas subscrições do Azure|✔|✔|✘|
|Cancelar subscrições da Azure|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>Funções e tarefas do perfil de faturação

Cada conta de faturação tem pelo menos um perfil de faturação. O seu primeiro perfil de faturação é configurado quando se inscreve para usar o Azure. Uma fatura mensal é gerada para o perfil de faturação e contém todos os seus encargos associados do mês anterior. Pode configurar mais perfis de faturação com base nas suas necessidades. Os utilizadores com papéis num perfil de faturação podem ver o custo, definir o orçamento e gerir e pagar as suas faturas. Atribua estas funções aos utilizadores responsáveis pela gestão do orçamento e pelo pagamento de faturas pelo perfil de faturação como membros das equipas de administração de empresas da sua organização. Para obter mais informações, veja [Compreender os perfis de faturação](../understand/mca-overview.md#billing-profiles).

As tabelas a seguir mostram qual a função necessária para realizar tarefas no contexto do perfil de faturação.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gerir permissões, propriedades e políticas do perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver atribuições de funções para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Conceder permissões a outras pessoas para verem e gerirem o perfil de faturação|✔|✘|✘|✘|✔|✘|✘|
|Ver propriedades de perfil de faturação como número de PO, bill-to, e muito mais|✔|✔|✔|✔|✔|✔|✔|
|Atualizar propriedades do perfil de faturação |✔|✔|✘|✘|✔|✔|✘|
|Ver políticas aplicadas no perfil de faturação como compras de reservas Azure, compras do Azure Marketplace, e muito mais|✔|✔|✔|✔|✔|✔|✔|
|Aplicar políticas no perfil de faturação |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gerir faturas para o perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as faturas do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Pagar faturas com cartão de crédito|✔|✔|✘|✔|✔|✘|✘|
|Transferir faturas, ficheiros de utilização e custos do Azure, folhas de preços e documentos fiscais do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gerir as secções de fatura do perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as secções de fatura do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Criar nova secção de fatura do perfil de faturação|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>Gerir produtos para perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todos os produtos comprados para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Gerir faturação para produtos como cancelar, desligar a renovação automática, e muito mais|✔|✔|✘|✘|✔|✔|✘|
|Alterar perfil de faturação para os produtos|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>Gerir os métodos de pagamento do perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver os métodos de pagamento do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Gerir métodos de pagamento como substituir cartão de crédito, desvincular o cartão de crédito e muito mais|✔|✔|✘|✘|✔|✔|✘|
|Controlar o saldo de créditos do Azure para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gerir subscrições do perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação|Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as subscrições do Azure do perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Criar novas subscrições do Azure|✔|✔|✘|✘|✔|✔|✘|
|Cancelar subscrições da Azure|✘|✘|✘|✘|✘|✘|✘|
|Alterar o perfil de faturação para as subscrições do Azure|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>Funções e tarefas da secção de fatura

Cada perfil de faturação contém uma secção de fatura por predefinição. Pode criar mais secções de fatura para agrupar o custo na fatura do perfil de faturação.  Os utilizadores com funções numa secção de fatura podem controlar quem cria subscrições Azure e fazer outras compras. Atribua essas funções a utilizadores que configuram o ambiente do Azure para as equipas da nossa organização, como líderes de engenharia e arquitetos técnicos. Para obter mais informações, veja [Compreender a secção de fatura](../understand/mca-overview.md#invoice-sections).

As tabelas a seguir mostram qual a função necessária para realizar tarefas no contexto das secções de fatura.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gerir permissões e propriedades da secção de fatura

|Tarefas|Proprietário da secção de fatura|Contribuidor da secção de fatura|Leitor da secção de fatura|Criador de subscrições do Azure|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação |Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Ver atribuições de funções para a secção de faturas|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Conceder permissões a outras pessoas para verem e gerirem a secção de fatura|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|Ver propriedades da secção de fatura|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Atualizar propriedades da secção de fatura|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>Gerir produtos para a secção de fatura

|Tarefas|Proprietário da secção de fatura|Contribuidor da secção de fatura|Leitor da secção de fatura|Criador de subscrições do Azure|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação |Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Ver todos os produtos comprados para a secção de fatura|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Gerir faturação para produtos como cancelar, desligar a renovação automática, e muito mais|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Alterar a secção de fatura dos produtos|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gerir subscrições da secção de fatura

|Tarefas|Proprietário da secção de fatura|Contribuidor da secção de fatura|Leitor da secção de fatura|Criador de subscrições do Azure|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação |Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Ver todas as subscrições do Azure da secção de fatura|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Cria subscrições do Azure|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Cancelar subscrições da Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Alterar a secção de fatura para a subscrição do Azure|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Solicitar a propriedade da faturação das subscrições de utilizadores noutras contas de faturação|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Tarefas e funções da faturação de subscrição

A tabela a seguir mostra qual a função necessária para realizar tarefas no contexto de uma subscrição.

|Tarefas|Proprietário da secção de fatura|Contribuidor da secção de fatura|Leitor da secção de fatura|Criador de subscrições do Azure|Proprietário do perfil de faturação|Contribuidor do perfil de faturação|Leitor do perfil de faturação |Gestor de faturas|Proprietário da conta de faturação|Contribuidor da conta de faturação|Leitor da conta de faturação 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Criar subscrições|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Atualizar o centro de custos da subscrição|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Alterar a secção de fatura da subscrição|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Alterar o perfil de faturação para a subscrição|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Cancelar subscrições da Azure|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

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
