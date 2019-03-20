---
title: Compreender a fatura do Azure Enterprise | Documentos da Microsoft
description: Saiba como ler e compreender a utilização e faturação para clientes do Azure com um contrato Enterprise
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 16feb41707e5afa07054d20b620f5822eedad773
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858917"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Compreender a sua fatura para clientes do Azure com um contrato Enterprise

Os clientes do Azure com um Enterprise Agreement recebem uma nota fiscal quando exceder o crédito da organização ou usar os serviços que não são abrangidos pelo crédito. 

Crédito da sua organização inclui seu compromisso monetário. A alocação monetária é a quantidade de sua organização pago inicialmente para utilização de serviços do Azure. Pode adicionar fundos de compromisso monetário para o Enterprise Agreement contactando o seu Gestor de conta Microsoft ou revendedor.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>Quando o crédito excedido ou não se aplica

Obter uma ou mais notas fiscais quando ocorre o seguinte:

- **Utilização excedida do serviço**: Os custos de utilização da sua organização excederem seu saldo de crédito.
- **Encargos faturados em separado**: Os serviços utilizado pela sua organização não são abrangidos pelo crédito. São faturadas para os seguintes serviços, independentemente do seu saldo de crédito:
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
- **Encargos do Marketplace**: As compras no Marketplace e utilização do Azure não são abrangidos por crédito da sua organização e são faturadas separadamente. O administrador de empresa tem a capacidade de ativar e desativar as compras no Marketplace para a sua organização no Portal da empresa. 

Quando tem custos devido para utilização excedida do serviço e encargos faturados em separado, durante o período de faturação, obtém uma nota fiscal que inclui os dois tipos de custos. Custos de marketplaces sempre são faturados separadamente.

## <a name="review-charges"></a>Custos de revisão

Para rever e confirmar os custos na sua fatura, tem de ser um administrador empresarial. Para obter mais informações, consulte [funções administrativas do Azure Enterprise Agreement compreender no Azure](billing-understand-ea-roles.md). Se não sabe quem é o administrador de empresa para a sua organização [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Fatura de utilização excedida de serviço de revisão

Comparar a quantidade total de utilização do portal da empresa na **relatórios** > **resumo de utilização** com a sua fatura de utilização excedida do serviço. A fatura de utilização excedida do serviço inclui a utilização que exceda o crédito da sua organização, e/ou serviços que não são abrangidos pelo crédito. As quantidades na **resumo de utilização** não incluem impostos. 

1. Inicie sessão para o [Enterprise portal](https://ea.azure.com).
1. Selecione **relatórios**.
1. No canto superior direito da guia, alterne da exibição de **M** ao **C** e corresponder o período da nota fiscal.
 
   ![Captura de ecrã que mostra M + a opção de C no resumo de utilização.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. O **utilização Total** quantidade deve corresponder a **Quantidade Total estendido** na sua fatura de utilização excedida do serviço. A tabela seguinte lista os termos e as descrições mostradas da nota fiscal e, no **resumo de utilização** no portal da empresa:

   |Termo de nota fiscal|Termo de resumo de utilização|Descrição|
   |---|---|---|
   |Total estendido quantidade|Utilização Total|O custo total de pré-imposto de utilização para o período específico antes do crédito é aplicado.|
   |Utilização de alocação|Utilização de alocação|O crédito aplicado durante o período específico.|
   |Total das vendas|Utilização Excedida Total|O custo total de utilização que exceda o montante de crédito. Este é o valor não incluem impostos.|
   |Valor do imposto|Não aplicável|Imposto que se aplica a quantidade total das vendas para o período específico.|
   |Quantidade total|Não aplicável|A quantidade vencimento da nota fiscal depois do crédito é aplicado e imposto é adicionado.|
1. Para obter mais informações sobre as suas Cobranças, aceda a **transferir utilização** > **transferir o relatório avançado**. Este relatório não inclui os impostos ou custos para as reservas ou encargos do marketplace.

      ![Captura de ecrã que mostra o relatório avançado baixar a guia de utilização de baixar.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Nota fiscal do marketplace de revisão

Comparar o Azure Marketplace total no **relatórios** > **resumo de utilização** no portal da empresa com a sua fatura do marketplace. A fatura do Marketplace diz respeito apenas a compras e utilização do Azure Marketplace. As quantidades na **resumo de utilização** não incluem impostos. 

1. Inicie sessão para o [Enterprise portal](https://ea.azure.com).
1. Selecione **relatórios**.
1. No canto superior direito da guia, alterne da exibição de **M** ao **C** e corresponder o período da nota fiscal.

     ![Captura de ecrã que mostra M + a opção de C no resumo de utilização.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. O **do Azure Marketplace** total deve corresponder a **Total das vendas** na sua fatura do marketplace.
1. Para obter mais informações sobre as suas Cobranças baseada na utilização, aceda a **transferir utilização**. Sob **encargos do Marketplace**, selecione **transferir**. Este relatório não incluem os impostos ou mostrar a compras de uso individual.

     ![Captura de ecrã que mostra transferir opção em encargos do Marketplace.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
