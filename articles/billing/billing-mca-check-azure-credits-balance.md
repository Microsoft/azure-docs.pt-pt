---
title: Controlar o saldo do Crédito do Azure de um Contrato de Cliente da Microsoft
description: Saiba como consultar o saldo do Crédito do Azure de um Contrato de Cliente da Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: b8faf3801a2217e6ef7040a7c2b67c7e6fad8e60
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709607"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Controlar o saldo do Crédito do Azure do Contrato de Cliente da Microsoft

Pode consultar o saldo do Crédito do Azure do Contrato de Cliente da Microsoft no portal do Azure. Utilize os créditos para pagar os custos abrangidos pelos créditos.

Ser.lhe-á cobrado quando utiliza produtos que não são abrangidos pelos créditos ou a sua utilização exceder o saldo do crédito. Para obter mais informações, veja Produtos que não são abrangidos pelos créditos do Azure.(#products-that-arent-covered-by-azure-credits).

Este artigo aplica-se à conta de faturação de um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Consultar o saldo do crédito

1. Inicie sessão no [portal do Azure]( https://portal.azure.com).

2. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa no portal para Cost Management + Faturação](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selecione **Créditos do Azure** no lado esquerdo. Dependendo do seu acesso, pode ser preciso selecionar uma conta de faturação ou um perfil de faturação e, em seguida, selecionar **Créditos do Azure**.

4. Os Créditos do Azure apresentam as seguintes informações:

   ![Captura de ecrã do saldo do crédito e das transações de um Perfil de faturação](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termo               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | O montante estimado de créditos que possui depois de considerar todas as transações faturadas e pendentes |
   | Saldo atual    | Montante de créditos desde a última fatura. Não inclui as transações pendentes |
   | Transações       | Todas as transações de faturação que afetaram o saldo do Crédito do Azure |

   Quando o saldo estimado cair para 0, ser-lhe-á cobrado toda a utilização, incluindo os produtos abrangidos pelos créditos.

6. Selecione **Lista de créditos** para ver a lista de créditos do perfil de faturação. A lista de créditos disponibiliza as seguintes informações:

   ![Captura de ecrã das listas de créditos de um Perfil de faturação](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termo | Definição |
   |---|---|
   | Saldo estimado | O montante do Crédito do Azure que possui depois de subtrair os custos elegíveis de crédito não faturado do saldo atual|
   | Saldo atual | O montante do Crédito do Azure que possui antes de considerar os custos elegíveis de crédito não faturado. Este é calculado através da adição de novos Créditos do Azure que recebeu no saldo do crédito durante a última fatura|
   | Origem | A origem da aquisição do crédito |
   | Data de início | A data em que adquiriu o crédito |
   | Data de validade | A data em que o crédito expira |
   | Saldo | O saldo desde a última fatura |
   | Montante original | O montante original de crédito |
   | Estado | O estado atual do crédito. O estado pode ser ativo, utilizado, expirado ou a expirar |

## <a name="how-credits-are-used"></a>Como os créditos são utilizados

Numa conta de faturação de um contrato de cliente da Microsoft, deverá utilizar os perfis de faturação para gerir as faturas e os métodos de pagamento. Mensalmente, é gerada uma fatura para cada perfil de faturação e deverá utilizar os métodos de pagamento para a pagar.

Os Créditos do Azure são um dos métodos de pagamento. Obtém crédito da Microsoft como crédito promocional e crédito de nível de serviço. Estes créditos são atribuídos a um perfil de faturação. Quando uma fatura é gerada para o perfil de faturação, os créditos são automaticamente aplicados ao valor total faturado para calcular o montante que precisa de pagar. Paga o montante restante através de outro método de pagamento, como cheque ou transferência bancária.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não estão abrangidos pelos Créditos do Azure

 Os produtos seguintes não são abrangidos pelos Créditos do Azure. Ser-lhe-á cobrada a utilização destes produtos, independentemente do saldo de crédito:

- Canónico
- Citrix XenApp Essentials
- Citrix XenDesktop
- Utilizador Registado
- OpenLogic
- Utilizador Registado do Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (Mensal)
- Visual Studio Enterprise (Anual)
- Visual Studio Professional (Mensal)
- Visual Studio Professional (Anual)
- Produtos do Azure Marketplace
- Planos de suporte do Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

- [Compreender a conta de faturação do Contrato de Cliente da Microsoft](billing-mca-overview.md)
- [Compreender os termos na fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-invoice.md)
