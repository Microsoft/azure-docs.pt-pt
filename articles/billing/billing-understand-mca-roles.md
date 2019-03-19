---
title: Compreender a faturas funções administrativas para contratos de cliente da Microsoft - Azure
description: Saiba mais sobre as funções de faturas para faturação contas no Azure para contratos de cliente da Microsoft.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 780870cc71e95507a52ba6a9338026f895a96ac1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834902"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Compreender as funções administrativas do contrato de cliente da Microsoft no Azure

Para gerir a sua conta de cobrança para um contrato de cliente da Microsoft, utilize as funções descritas nas seções a seguir. Estas funções são, além das funções incorporadas, que o Azure tem para controlar o acesso aos recursos. Para obter mais informações, consulte [funções incorporadas para recursos do Azure](../role-based-access-control/built-in-roles.md).

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. Verifique se tem acesso a um contrato de cliente da Microsoft.

## <a name="billing-role-definitions"></a>Definições de funções de faturação

A tabela seguinte descreve as funções de faturas que utiliza para gerir a sua conta de cobrança, perfis de faturação e seções de nota fiscal.

|Função|Descrição|
|---|---|
|Proprietário da conta de faturação|Gerir tudo para a conta de faturação|
|Contribuinte da conta de faturação|Gerir tudo, exceto as permissões na conta de faturação|
|Leitor da conta de faturação|Vista só de leitura de tudo na conta de faturação|
|Proprietário do perfil de faturação|Gerir tudo para o perfil de faturação|
|Contribuinte do perfil de faturação|Gerir tudo, exceto as permissões no perfil de faturação|
|Leitor do perfil de faturação|Vista só de leitura de tudo no perfil de faturação|
|Gestor de faturas|Ver e pagar notas fiscais para o perfil de faturação|
|Proprietário da secção de fatura|Gerir tudo na seção de nota fiscal|
|Contribuinte da secção de fatura|Gerir tudo, exceto as permissões na seção de nota fiscal|
|Leitor da secção de fatura|Vista só de leitura de tudo na seção de nota fiscal|
|Criador da subscrição do Azure|Criar subscrições do Azure|

## <a name="billing-account-roles-and-tasks"></a>Funções e tarefas da conta de faturação

Uma conta de cobrança permite-lhe gerir a faturação para a sua organização. Utilizar conta de cobrança para organizar os custos, custos de monitor e notas fiscais e controlar o acesso de faturação para a sua organização. Para obter mais informações, consulte [entender a conta de cobrança](billing-mca-overview.md#understand-billing-account).

As tabelas seguintes mostram que função tem de concluir as tarefas no contexto da conta de cobrança.

### <a name="manage-billing-account-permissions-and-properties"></a>Gerir permissões de conta de faturação e propriedades

|Tarefa|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver permissões existentes para a conta de faturação|✔|✔|✔|
|Atribuir permissões para ver e gerir a conta de faturação a outras pessoas|✔|✘|✘|
|Propriedades da conta de faturas vista como o nome da empresa, endereço e muito mais|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Gerir perfis de faturas para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todos os perfis de faturas na conta|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Gerir as notas fiscais para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as notas fiscais na conta|✔|✔|✔|
|Transferir faturas, ficheiros de utilização e os encargos do Azure, folhas de preços e de documentos na conta de imposto sobre vendas|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Gerir secções de nota fiscal para a conta de cobrança

|Tarefa|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as seções de nota fiscal na conta|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Gerir as transações de conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as transações de faturas para a conta|✔|✔|✔|
|Exibir todos os produtos comprados para a conta|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Gerir subscrições para a conta de faturação

|Tarefa|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação|
|---|---|---|---|
|Ver todas as subscrições do Azure na conta de faturação|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Funções e tarefas do perfil de faturação

Um perfil de faturação permite-lhe gerir as suas notas fiscais e métodos de pagamento. Uma nota fiscal mensal é gerada para as subscrições do Azure e outros produtos comprados com o perfil de faturação. Utilize os métodos de pagamentos para pagar a nota fiscal. Para obter mais informações, consulte [compreender os perfis de faturas](billing-mca-overview.md#understand-billing-profiles).

As tabelas seguintes mostram que função tem de concluir as tarefas no contexto do perfil de faturação.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Gerir a faturação de perfil de permissões, propriedades e as políticas

|Tarefa|Proprietário do perfil de faturação|Contribuinte do perfil de faturação|Leitor do perfil de faturação|Gerenciador de faturas|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver permissões existentes para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Atribuir permissões para ver e gerir o perfil de faturação a outras pessoas|✔|✘|✘|✘|✘|✘|✘|
|Ver as propriedades de perfil faturas como oC número, preferência de nota fiscal de e-mail e muito mais|✔|✔|✔|✔|✔|✔|✔|
|Atualizar propriedades de perfil de faturação |✔|✔|✘|✘|✘|✘|✘|
|Ver políticas aplicadas no perfil de faturação, como ativar a compras de reserva do Azure, ative as compras no Azure marketplace e muito mais|✔|✔|✔|✔|✔|✔|✔|
|Aplicar políticas de perfil de faturação |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Gerir as notas fiscais para o perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuinte do perfil de faturação|Leitor do perfil de faturação|Gerenciador de faturas|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as notas fiscais para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Transferir faturas, ficheiros de utilização e os encargos do Azure, folhas de preços e documentos para o perfil de faturação de imposto sobre vendas|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Gerir secções de nota fiscal para o perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuinte do perfil de faturação|Leitor do perfil de faturação|Gerenciador de faturas|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as seções de nota fiscal para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Criar nova seção de nota fiscal para o perfil de faturação|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Gerenciar transações para o perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuinte do perfil de faturação|Leitor do perfil de faturação|Gerenciador de faturas|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as transações de faturas para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Gerir métodos de pagamento para o perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuinte do perfil de faturação|Leitor do perfil de faturação|Gerenciador de faturas|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Métodos de pagamento de vista para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|
|Acompanhar o saldo de créditos do Azure para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Gerir subscrições para o perfil de faturação

|Tarefa|Proprietário do perfil de faturação|Contribuinte do perfil de faturação|Leitor do perfil de faturação|Gerenciador de faturas|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as subscrições do Azure para o perfil de faturação|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Funções e tarefas da secção de faturação

Uma seção de nota fiscal permite-lhe organizar os custos na sua fatura. Pode criar uma seção para organizar os seus custos por departamento, ambiente de desenvolvimento, ou com base nas necessidades da sua organização. Atribua a outras pessoas permissão para criar subscrições do Azure para a seção. Quaisquer custos de utilização e compras para as subscrições, em seguida, mostrar na seção da nota fiscal. Para obter mais informações, consulte [secção de nota fiscal de compreender](billing-mca-overview.md#understand-invoice-sections).

As tabelas seguintes mostram que função tem de concluir as tarefas no contexto das seções de nota fiscal.

### <a name="manage-invoice-section-permissions-and-properties"></a>Gerir permissões de seção de nota fiscal e propriedades

|Tarefas|Proprietário da secção de fatura|Contribuinte da secção de fatura|Leitor da secção de fatura|Criador da subscrição do Azure|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação | |
|---|---|---|---|---|---|---|---|---|
|Ver todas as permissões na seção de nota fiscal|✔|✔|✔|✔|✔|✔|✔| |
|Atribuir permissões para ver e gerir a secção de nota fiscal a outras pessoas|✔|✘|✘|✘|✘|✘|✘| |
|Ver propriedades de seção de nota fiscal|✔|✔|✔|✔|✔|✔|✔| |
|Atualizar propriedades de seção de nota fiscal|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Gerir produtos para a secção de nota fiscal

|Tarefas|Proprietário da secção de fatura|Contribuinte da secção de fatura|Leitor da secção de fatura|Criador da subscrição do Azure|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Exibir todos os produtos comprados na secção de nota fiscal|✔|✔|✔|✘|✔|✔|✔|
|Gerir a faturação de produtos para a secção de nota fiscal como cancelar, desativar a renovação automática e muito mais|✔|✔|✘|✘|✘|✘|✘|
|Secção de nota fiscal de alteração para os produtos|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Gerir subscrições para a secção de nota fiscal

|Tarefas|Proprietário da secção de fatura|Contribuinte da secção de fatura|Leitor da secção de fatura|Criador da subscrição do Azure|Proprietário da conta de faturação|Contribuinte da conta de faturação|Leitor da conta de faturação
|---|---|---|---|---|---|---|---|
|Ver todas as subscrições do Azure para a secção de nota fiscal|✔|✔|✔|✘|✔|✔|✔|
|Secção de nota fiscal de alteração para as subscrições|✔|✔|✘|✘|✘|✘|✘|
|Pedir de propriedade de faturação das subscrições dos utilizadores nas outras contas de faturas|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Tarefas e funções da faturação de subscrição

A tabela seguinte mostra que função tem de concluir as tarefas no contexto de uma subscrição.

|Tarefas|Proprietário da secção de fatura|Contribuinte da secção de fatura|Leitor da secção de fatura|Criador da subscrição do Azure|
|---|---|---|---|---|
|Criar subscrições do Azure|✔|✔|✘|✔|
|Centro de custos de atualização para a subscrição|✔|✔|✘|✘|
|Secção de nota fiscal de alteração para a subscrição|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Gerir funções de faturas no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. Selecione **controlo de acesso (IAM)** num âmbito como conta de cobrança, perfil de faturação ou secção de notas fiscais, onde pretende conceder acesso.

4. A página de controlo (IAM) de acesso apresenta uma lista de utilizadores e grupos que estão atribuídos a cada função para esse âmbito.

   ![Captura de ecrã que mostra a lista de administradores para a conta de cobrança](./media/billing-understand-mca-roles/billing-list-admins.png)

5. Para dar acesso a um utilizador, selecione **adicionar** da parte superior da página. Na lista pendente de função, selecione uma função. Introduza o endereço de e-mail do utilizador a quem pretende conceder acesso. Selecione **guardar** para atribuir a função.

   ![Captura de ecrã que mostra a adição de um administrador para uma conta de cobrança](./media/billing-understand-mca-roles/billing-add-admin.png)

6. Para remover o acesso de um utilizador, selecione o utilizador com a atribuição de função que pretende remover. Selecione remover.

   ![Captura de ecrã que mostra a remoção de um administrador de uma conta de cobrança](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte
Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes

Consulte os seguintes artigos para saber mais sobre a sua conta de cobrança:

- [Introdução ao sua conta de cobrança para o contrato de cliente da Microsoft](billing-mca-overview.md)
- [Criar uma subscrição do Azure para a sua conta de faturação para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)
