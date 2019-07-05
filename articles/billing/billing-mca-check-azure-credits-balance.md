---
title: Acompanhar o saldo de crédito do Azure para um contrato de cliente da Microsoft
description: Saiba como verificar o saldo de crédito do Azure para um contrato de cliente da Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490972"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Acompanhar o saldo do crédito do Azure do contrato de cliente Microsoft

Pode verificar o saldo de crédito do Azure para o contrato de cliente da Microsoft no portal do Azure. Utilizar créditos para pagar custos que são abrangidos pelos créditos.

É cobrado quando utiliza os produtos que não são abrangidos pelos créditos ou a utilização exceder o seu saldo de crédito. Para obter mais informações, consulte [produtos que não são abrangidos por créditos do Azure. ( #products-that-aren't-covered-by-azure-credits).

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Verificar seu saldo de crédito

1. Inicie sessão no [portal do Azure]( https://portal.azure.com).

2. Procure **custo Management + faturação**.

    ![Captura de ecrã que mostra a pesquisa no portal de gestão de custos + faturação](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selecione **créditos do Azure** no lado esquerdo. Consoante o acesso, poderá ter de selecionar uma conta de cobrança ou um perfil de faturação e, em seguida, selecione **créditos do Azure**.

4. A página de créditos do Azure apresenta as seguintes informações:

   ![Captura de ecrã do saldo de crédito e transações para um perfil de faturação](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termo               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | Estimado montante de créditos tiver depois de considerar todos os faturados e pendentes de transações |
   | Saldo atual    | Montante de créditos desde a última fatura. Não inclui nenhum pendentes de transações |
   | Transações       | Todas as transações de faturas que afetados seu saldo de crédito do Azure |

   Quando o saldo da sua estimado cai a 0, é-lhe cobrada todos os sua utilização, incluindo para produtos que são abrangidos por créditos.

6. Selecione **lista de créditos** para ver a lista de créditos para o perfil de faturação. A lista de créditos fornece as seguintes informações:

   ![Captura de ecrã das listas de créditos para um perfil de faturação](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termo | Definição |
   |---|---|
   | Saldo estimado | Quantidade de crédito do Azure que tiver após subtraindo o crédito de faturação por faturar custos elegíveis do seu saldo atual|
   | Saldo atual | Quantidade de crédito do Azure que tem antes de considerar o crédito de faturação por faturar custos elegíveis. Esta é calculada ao adicionar créditos do Azure novo que recebeu para o saldo de crédito no momento da sua última fatura|
   | source | A origem de aquisição do crédito |
   | Data de início | A data de quando tiver adquirido o crédito |
   | Data de expiração | A data quando expira o crédito |
   | Saldo | O saldo desde a última fatura |
   | Quantidade original | A quantidade original de crédito |
   | Estado | O estado atual de crédito. Estado pode estar ativo, utilizado, expirou ou expira |

## <a name="how-credits-are-used"></a>Como esses créditos são utilizados

Uma conta de cobrança para um contrato de cliente da Microsoft, vai utilizar perfis de faturas para gerir as suas notas fiscais e métodos de pagamento. Uma nota fiscal mensal é gerada para cada perfil de faturação e utilizar os métodos de pagamento para pagar a nota fiscal.

Créditos do Azure são um dos métodos de pagamento. Obtém crédito da Microsoft, como o crédito promocional e crédito de nível de serviço. Estes créditos são atribuídos a um perfil de faturação. Quando uma nota fiscal é gerada para o perfil de faturação, os créditos são aplicados automaticamente para a quantidade total de faturação para calcular a quantidade que terá de pagar. Paga a quantidade restante com outro método de pagamento, como a verificação ou se conectar a transferência.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não são abrangidos por créditos do Azure

 Os seguintes produtos não são abrangidos por créditos do Azure. É-lhe cobrado para usar esses produtos, independentemente do seu saldo de crédito:

- Canónico
- Citrix XenApp Essentials
- Citrix XenDesktop
- Utilizador registado
- Openlogic
- Utilizador registado de direitos de acesso remoto o XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)
- Produtos do Azure Marketplace
- Planos de suporte do Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.

## <a name="next-steps"></a>Passos Seguintes

- [Compreender a conta de cobrança do contrato de cliente da Microsoft](billing-mca-overview.md)
- [Compreender os termos na sua fatura de contrato de cliente da Microsoft](billing-mca-understand-your-invoice.md)
