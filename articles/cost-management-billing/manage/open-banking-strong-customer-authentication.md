---
title: Banco Aberto (PSD2) e Autenticação de Clientes Segura (SCA) para os clientes do Azure
description: Este artigo explica por que razão a autenticação multifator é necessária para realizar certas compras do Azure e como completar a autenticação.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 847e9b692ddbd7880c37d1068d61447dcccb9c80
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747153"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Banco Aberto (PSD2) e Autenticação de Clientes Segura (SCA) para os clientes do Azure

Desde 14 de setembro de 2019 que os bancos dos 31 países/regiões do [Espaço Económico Europeu](https://en.wikipedia.org/wiki/European_Economic_Area) têm de verificar a identidade da pessoa que fizer uma compra online antes de processar o pagamento. Esta verificação requer a autenticação multifator para ajudar a garantir a segurança e proteção das suas compras online. A data de entrada em vigor deste requisito de verificação será adiada em alguns países/regiões. Para obter mais informações, veja a [FAQ da Microsoft sobre o PSD2](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>O que representa o PSD2 para os clientes do Azure

Se pagar o Azure com um cartão de crédito emitido por um banco do [Espaço Económico Europeu](https://en.wikipedia.org/wiki/European_Economic_Area), poderá ser-lhe pedido que complete a autenticação multifator do método de pagamento da sua conta. Poderá ser-lhe solicitado que complete o desafio da autenticação multifator quando se inscrever na sua conta do Azure ou quando a atualizar, ainda que não esteja a realizar uma compra. Também lhe poderá ser pedida a autenticação multifator se alterar o método de pagamento da sua conta do Azure, remover o limite de gastos ou realizar um pagamento imediato a partir do portal do Azure, como nos casos em que quer liquidar dívidas em atraso ou comprar crédito do Azure.

Se o seu banco rejeitar as suas cobranças mensais do Azure, receberá um e-mail de montante vencido do Azure com instruções sobre como resolver a situação. Pode completar o desafio da autenticação multifator e liquidar as cobranças em atraso no portal do Azure.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Completar a autenticação multifator no portal do Azure

As secções seguintes descrevem como completar a autenticação multifator no portal do Azure. Utilize as informações que se apliquem ao seu caso.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Alterar o método de pagamento ativo da sua conta do Azure

Para alterar o método de pagamento ativo da sua conta do Azure, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta e navegue para **Gestão de Custos + Faturação**.
2. Na página **Descrição Geral**, selecione a subscrição correspondente na grelha **As minhas subscrições**.
3. Em "Faturação", selecione **Métodos de pagamento**. Pode adicionar um novo cartão de crédito ou definir um cartão existente como método de pagamento ativo da subscrição. Se o seu banco pedir a autenticação multifator, ser-lhe-á solicitado que complete um desafio de autenticação durante o processo.

Para obter mais detalhes, veja [Adicionar, atualizar ou remover um cartão de crédito do Azure](change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Liquidar cobranças em atraso dos serviços do Azure

Se o seu banco rejeitar as cobranças, o estado da sua conta do Azure é alterado para **Vencido** no portal do Azure. Para verificar o estado da sua conta, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como Administrador de Conta.
2. Procure em **Gestão de Custos + Faturação**.
3. Em **Cost Management + Faturação**, na página **Descrição geral**, analise a coluna de estado na grelha **As minhas subscrições**.
4. Se a sua subscrição estiver marcada como **Vencido**, clique em **Liquidar dívida**. É-lhe pedido que complete a autenticação multifator durante o processo.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Liquidar cobranças em atraso de compras no Marketplace e de reservas

As compras no Marketplace e de reservas são cobradas separadamente dos serviços do Azure. Se o seu banco rejeitar as cobranças do Marketplace ou de reservas, a sua fatura ficará vencida e verá a opção **Pagar agora** no portal do Azure. Para pagar faturas vencidas do Marketplace e de reservas, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como Administrador de Conta.
2. Procure em **Gestão de Custos + Faturação**.
3. Em "Faturação", selecione **Faturas**.
5. No filtro pendente de subscrição, selecione a subscrição associada à sua compra no Marketplace ou de reservas.
6. Na grelha de faturas, consulte a coluna de tipo. Se o tipo for **Azure Marketplace e Reservas**, verá a ligação **Pagar agora** se a fatura estiver em dívida ou vencida. Se não vir a ligação **Pagar agora**, significa que a sua fatura já foi paga. Ser-lhe-á pedido que complete a autenticação multifator durante a ação Pagar agora.

## <a name="next-steps"></a>Passos seguintes
- Veja [Resolver saldo em dívida na subscrição do Azure](resolve-past-due-balance.md) se tiver de pagar uma fatura do Azure.
