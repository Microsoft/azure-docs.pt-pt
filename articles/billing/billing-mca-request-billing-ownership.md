---
title: Obter a propriedade das subscrições do Azure de faturação | Documentos da Microsoft
description: Saiba como propriedade da faturação das subscrições do Azure de pedidos de outros utilizadores.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 01d34ef722034ef5b56c842bab97a3415a02b0bb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341872"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Obter a propriedade das subscrições do Azure com outros usuários de faturação

Pode querer obter a propriedade das subscrições do Azure, se o proprietário de faturação existente é deixar a sua organização ou pretende comprar subscrições através da sua conta de faturação.

Pode enviar um pedido para obter a propriedade das subscrições do Azure dos proprietários existentes nas outras contas de faturas. A propriedade transfere as responsabilidades de faturas das subscrições à seção de nota fiscal.

Para pedir a propriedade de faturação, tem de ser um **proprietário da secção de nota fiscal** ou **contribuinte da secção de nota fiscal**. Para obter mais informações, consulte [tarefas de funções de seção de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Pedir de propriedade de faturação no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure para gestão de custos + faturação](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Vá para a secção de nota fiscal. Consoante o acesso, terá de selecionar uma conta de cobrança ou o perfil de faturação. A partir da conta de faturação ou um perfil, selecione **secções de nota fiscal** e, em seguida, uma seção de nota fiscal.
   <!-- Todo - Add a screenshot -->

4. Selecione **pedidos de transferência** no lado inferior esquerdo.

5. Na parte superior da página, selecione **adicionar**.

6. Introduza o endereço de e-mail do utilizador que está solicitando a propriedade de faturação do. O utilizador tem de ser um administrador de conta numa conta de cobrança do programa de serviço Online da Microsoft ou um proprietário da conta num contrato Enterprise. Para obter mais informações, consulte [ver as suas contas de faturas no portal do Azure](billing-view-all-accounts.md).

   ![Captura de ecrã que mostra a adição de um novo pedido de transferência](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Selecione **pedido de transferência de envio**.

8. O utilizador obtém uma mensagem de e-mail com instruções para rever o pedido de transferência.

   ![Captura de ecrã que mostra de pedido de transferência de revisão de e-mail](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Para aprovar o pedido de transferência, o usuário seleciona a ligação no e-mail e segue as instruções.

    ![Captura de ecrã que mostra de pedido de transferência de revisão de e-mail](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Verificar o estado do pedido de transferência no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Pesquisar nos **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure para gestão de custos + faturação](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Vá para a secção de nota fiscal. Consoante o acesso, terá de selecionar uma conta de cobrança ou o perfil de faturação. A partir da conta de faturação ou um perfil, selecione **secções de nota fiscal** e, em seguida, uma seção de nota fiscal.
   <!-- Todo - Add a screenshot -->

4. Selecione **pedidos de transferência** no lado inferior esquerdo.

5. A página de pedidos de transferência apresenta as seguintes informações:

    ![Captura de ecrã que mostra a lista de pedidos de transferência](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Coluna|Definição|
   |---------|---------|
   |Data do pedido|A data quando o pedido de transferência foi enviado|
   |Destinatário|O endereço de e-mail do utilizador que enviou o pedido para transferir a propriedade de faturação|
   |Data de validade|A data quando o pedido expirar|
   |Estado|O estado do pedido de transferência|

    O pedido de transferência pode ter um dos seguintes Estados:

   |Estado|Definição|
   |---------|---------|
   |Em curso|O utilizador ainda não aceitou o pedido de transferência|
   |Em processamento|O usuário aprovou o pedido de transferência. A faturação para subscrições que o utilizador selecionou está a ser transferida à seção de nota fiscal|
   |Concluído| A faturação para subscrições que o utilizador selecionou é transferida para a seção de nota fiscal|
   |Foi concluída com erros|Não foi possível transferir o pedido foi concluído, mas a faturação para algumas das subscrições que o utilizador selecionado|
   |Fora do prazo|O utilizador não aceitou o pedido no tempo e expirado|
   |Cancelado|Qualquer pessoa com acesso para o pedido de transferência foi cancelada o pedido|
   |Recusada|O utilizador recusou o pedido de transferência|

6. Selecione um pedido de transferência para ver os detalhes. A página de detalhes de transferência apresenta as seguintes informações: <!-- Todo - Add a screenshot -->

   |Coluna  |Definição|
   |---------|---------|
   |ID do pedido de transferência|O ID exclusivo para o pedido de transferência. Se submeter um pedido de suporte, partilhar o ID com o suporte do Azure para acelerar o seu pedido de suporte|
   |Transferência solicitada em|A data quando o pedido de transferência foi enviado|
   |Transferência solicitada por|O endereço de e-mail do utilizador que enviou o pedido de transferência|
   |Pedido de transferência expira em| A data em que o pedido de transferência expira|
   |Endereço de e-mail do destinatário|O endereço de e-mail do utilizador que enviou o pedido para transferir a propriedade de faturação|
   |Ligação de transferência enviada ao destinatário|O url que foi enviada para o utilizador para rever o pedido de transferência|

## <a name="additional-information"></a>Informações adicionais

A secção seguinte fornece informações adicionais sobre a transferência de subscrições.

### <a name="no-service-downtime"></a>Sem períodos de indisponibilidade de serviço

Continuar a executar serviços do Azure na subscrição sem qualquer interrupção. Só que fazemos a transição da relação de faturação para as subscrições do Azure que o utilizador seleciona para transferir.

### <a name="disabled-subscriptions"></a>Subscrições desativadas

Não não possível transferir subscrições desativadas. Subscrições têm de estar no Estado ativo para transferir a respetiva propriedade de faturação.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos das subscrições, como VMs, discos e transferência de Web sites.

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Transferem de produtos do Azure Marketplace, juntamente com suas respectivas assinaturas.

### <a name="azure-reservations-transfer"></a>Transferência de reservas do Azure

Reservas do Azure não são movidas automaticamente com as assinaturas. [Contacte o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover as reservas.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Acesso aos recursos do Azure que foi definido com o Azure RBAC (controlo de acesso baseado em funções) não é afetado durante a transição.

### <a name="azure-support-plan"></a>Plano de suporte do Azure

Suporte do Azure não transferir com as subscrições. Se o utilizador transferir todas as subscrições do Azure, peça-lhes para cancelar o plano de suporte.

### <a name="charges-for-transferred-subscription"></a>Cobranças da subscrição transferida

O proprietário original de faturação das subscrições é responsável por quaisquer custos reportados até ao ponto que a transferência foi concluída. A seção de nota fiscal é responsável pelos custos reportados desde o momento de transferência e posteriores. Pode haver alguns custos que ocorria antes da transferência, mas foi reportados posteriormente. Estes custos apresentados na seção de nota fiscal.

### <a name="supported-offers"></a>Ofertas de suporte

Subscrição de quaisquer tipos ou ofertas, exceto pelo CSP oferece pode ser transferida.

### <a name="cancel-a-transfer-request"></a>Cancelar um pedido de transferência

Pode cancelar o pedido de transferência até que o pedido é aprovado ou recusado. Para cancelar o pedido de transferência [vá para a página de detalhes de transferência](#check-status-of-your-transfer-request-in-the-Azure-portal) e selecione Cancelar na parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Software como uma transferência de serviço (SaaS)

Não se transferem produtos SaaS com as subscrições. Pedir ao utilizador para [suporte do Azure de contacto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade de faturação dos produtos de SaaS. Juntamente com a propriedade de faturação, o utilizador também pode transferir a propriedade de recurso. Propriedade de recurso permite-lhe efetuar operações de gestão, como a eliminar e ver os detalhes do produto. Utilizador tem de ser um proprietário do recurso no produto SaaS para transferir a propriedade de recurso.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes

- A propriedade de faturação das subscrições do Azure é transferida para a seção de nota fiscal. Mantenha um registo de encargos para estas subscrições na [portal do Azure](https://portal.azure.com).
- Atribua a outras pessoas permissões para ver e gerir a faturação para estas subscrições. Para obter mais informações, consulte [secção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).