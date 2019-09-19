---
title: Ver as contas de faturação no portal do Azure | Microsoft Docs
description: Saiba como ver as suas contas de faturação no portal do Azure.
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490223"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Ver contas de faturação no portal do Azure  

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. Pode ter acesso a múltiplas contas de faturação. Por exemplo, talvez se tenha inscrito no Azure com o objetivo de desenvolver projetos pessoais. Também poderá ter acesso através do Contrato Enterprise ou do Contrato de Cliente da Microsoft da sua organização. Para cada um desses cenários, teria uma conta de faturação separada.

O portal do Azure suporta atualmente os seguintes tipos de conta de faturação:

- **Programa Microsoft Online Services**: uma conta de faturação para um Programa Microsoft Online Services é criada ao inscrever-se no Azure através do site do Azure. Por exemplo, quando se inscreve numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: uma conta de faturação para um Contrato Enterprise é criada quando a sua organização assina um [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) para utilizar o Azure.

- **Contrato de Cliente da Microsoft**: uma conta de faturação para um Contrato de Cliente Microsoft é criada quando a sua organização trabalha com um representante da Microsoft para assinar um Contrato de Cliente da Microsoft. Alguns clientes em determinadas regiões, que se inscrevem no site do Azure numa [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou atualizam a respetiva [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), também podem ter uma conta de faturação para um Contrato de Cliente da Microsoft. Para obter mais informações, veja [Introdução à conta de faturação para o Contrato de Cliente da Microsoft](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Âmbitos de contas de faturação
Um âmbito é um nó numa conta de faturação que os utilizadores utilizam para ver e gerir a faturação. É onde os utilizadores gerem os dados de faturação, pagamentos, faturas e realizam a gestão global da conta. 

### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação     | Representa um único proprietário (Administrador da conta) de uma ou mais subscrições do Azure. Um Administrador de Conta está autorizado a executar várias tarefas de faturação, como criar subscrições, ver faturas ou alterar a faturação para subscrições.  |
|Subscrição     |  Representa um agrupamento de recursos do Azure. A fatura é gerada neste âmbito. Tem os seus próprios métodos de pagamento que são utilizados para pagar as faturas.|


### <a name="enterprise-agreement"></a>Contrato Enterprise

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação    | Representa uma inscrição do Contrato Enterprise. A fatura é gerada neste âmbito. É estruturada através de departamentos e contas de inscrição.  |
|Departamento     |  Agrupamento opcional de contas de inscrição.      |
|Conta de inscrição     |  Representa um único proprietário da conta. As subscrições do Azure são criadas neste âmbito.  |


### <a name="microsoft-customer-agreement"></a>Contrato de Cliente da Microsoft

|Âmbito  |Tarefas  |
|---------|---------|
|Conta de faturação     |   Representa um contrato de cliente para múltiplos produtos e serviços Microsoft. É estruturada através de perfis de faturação e secções de fatura.   |
|Perfil de faturação     |  Representa uma fatura e os respetivos métodos de pagamento. A fatura é gerada neste âmbito. Pode ter múltiplas secções de fatura.      |
|Secção de fatura     |   Representa um grupo de custos numa fatura. As subscrições e outras compras estão associadas a este âmbito.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Alterar o âmbito de faturação no portal do Azure


1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Gestão de Custos + Faturação**.

   ![Captura de ecrã a mostrar a pesquisa no portal do Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Selecione **Todos os âmbitos de faturação** no lado esquerdo.

   ![Captura de ecrã a mostrar todos os âmbitos de faturação](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Não verá **Todos os âmbitos de faturação** se só tiver acesso a um âmbito.

4. Selecione um âmbito para ver os detalhes.



## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- Saiba como começar a [analisar os seus custos](../cost-management/quick-acm-cost-analysis.md).