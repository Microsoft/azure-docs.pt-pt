---
title: Ver as suas contas de faturas no portal do Azure | Documentos da Microsoft
description: Saiba como ver as suas contas de faturas no portal do Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490223"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Ver as contas de faturas no portal do Azure  

Uma conta de cobrança é criada quando se inscreve utilizar o Azure. Utilize a sua conta de cobrança para gerir as faturas, pagamentos e controlar os custos. Pode ter acesso a várias contas de faturas. Por exemplo, poderá ter inscrito para o Azure para os seus projetos pessoais. Também poderia ter acesso através do Enterprise Agreement ou contrato de cliente da Microsoft da sua organização. Para cada um destes cenários, teria uma conta de cobrança separada.

Atualmente, o portal do Azure suporta o seguinte tipo de contas de faturas:

- **Programa Microsoft Online Services**: Uma conta de cobrança para um programa Microsoft Online Services é criada quando se inscreve no Azure através do site do Azure. Por exemplo, ao inscrever-se para obter um [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com as tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou uma [subscritor do Visual studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: É criada uma conta de faturação de um Enterprise Agreement, quando sua organização se inscreve um [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) a utilização do Azure.

- **Contrato de cliente da Microsoft**: Quando a sua organização trabalha com um representante da Microsoft para assinar um contrato de cliente da Microsoft, é criada uma conta de faturação para um contrato de cliente da Microsoft. Alguns clientes em regiões selecionadas, o que se inscrevam através do site do Azure para uma [conta com as tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou atualizar seus [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) pode ter uma conta de cobrança para um Microsoft Customer Contrato também. Para obter mais informações, consulte [introdução à sua conta de cobrança para o contrato de cliente da Microsoft](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Âmbitos para as contas de faturação
Um âmbito é um nó dentro de uma conta de cobrança que os utilizadores utilizam para ver e gerir a faturação. É onde os usuários gerir dados de faturação, pagamentos, faturas e realizar a gestão de contas geral. 

### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

|Scope  |Definição  |
|---------|---------|
|Conta de faturação     | Representa um único proprietário (administrador de conta) para uma ou mais subscrições do Azure. Um administrador de conta está autorizado a efetuar várias tarefas de faturas como criar subscrições, ver notas fiscais ou alterar a faturação para subscrições.  |
|Subscrição     |  Representa um agrupamento de recursos do Azure. Nota fiscal é gerada neste âmbito. Ele tem seus próprios métodos de pagamento que são utilizados para pagar a nota fiscal.|


### <a name="enterprise-agreement"></a>Contrato Enterprise

|Scope  |Definição  |
|---------|---------|
|Conta de faturação    | Representa uma inscrição de Enterprise Agreement. Nota fiscal é gerada neste âmbito. Está estruturado por meio de departamentos e contas de inscrição.  |
|Departamento     |  Agrupamento opcional de contas de inscrição.      |
|Conta de inscrição     |  Representa o proprietário de uma única conta. Subscrições do Azure são criadas neste âmbito.  |


### <a name="microsoft-customer-agreement"></a>Contrato de Cliente Microsoft

|Scope  |Tarefas  |
|---------|---------|
|Conta de faturação     |   Representa um contrato de cliente para vários produtos e serviços Microsoft. Está estruturado por meio de perfis de faturas e seções de nota fiscal.   |
|Perfil de faturação     |  Representa uma nota fiscal e seus métodos de pagamento. Nota fiscal é gerada neste âmbito. Ele pode ter múltiplas secções de nota fiscal.      |
|Secção de nota fiscal     |   Representa um grupo de custos numa nota fiscal. Subscrições e outras compras estão associadas a este âmbito.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Mudar o âmbito de faturação no portal do Azure


1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **custo Management + faturação**.

   ![Captura de ecrã que mostra a pesquisa de portal do Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Selecione **todos os âmbitos de faturas** no lado esquerdo.

   ![Captura de ecrã que mostra todos os âmbitos de faturas](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   \* * Não verá **todos os âmbitos de faturas** se só tiver acesso a um âmbito.

4. Selecione um âmbito para ver os detalhes.



## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes
- Saiba como começar [analisar os seus custos](../cost-management/quick-acm-cost-analysis.md).