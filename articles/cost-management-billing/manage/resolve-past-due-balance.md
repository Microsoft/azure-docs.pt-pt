---
title: E-mail de saldo em dívida do Azure
description: Descreve como fazer o pagamento se a subscrição do Azure tiver um saldo em dívida
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 9548900fe627f774aca08c05a243d807bb5a699c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282692"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Resolver saldo em dívida na subscrição do Azure

Este artigo aplica-se aos clientes que se inscreveram no Azure online com um cartão de crédito e que têm uma conta de faturação do Programa de Serviços Online da Microsoft. Saiba como [verificar o tipo de conta de faturação](#check-the-type-of-your-account). Se tiver uma conta de faturação do Contrato de Cliente Microsoft, veja [Pagar a fatura do Microsoft Azure](../understand/pay-bill.md).

Se o pagamento não for recebido ou se não o conseguirmos processar, receberá um e-mail e verá um alerta no portal do Azure a informar de que o pagamento da sua subscrição está atrasado. Se o seu método de pagamento predefinido for cartão de crédito, o [Administrador da Conta](billing-subscription-transfer.md#whoisaa) pode liquidar os custos vencidos no portal do Azure. Se pagar por fatura (cheque/transferência bancária), envie o pagamento para a localização apresentada no final da fatura.

> [!IMPORTANT]
> * Se tiver várias subscrições que utilizam o mesmo cartão de crédito e todas estiverem vencidas, deverá pagar todo o saldo pendente de uma só vez.
> * O cartão de crédito que utiliza para liquidar os custos pendentes tornar-se-á no novo método de pagamento predefinido para todas as subscrições que estavam a utilizar o método do pagamento em falta.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Resolver o saldo em dívida no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como o Administrador de Conta.
1. Procure **Cost Management + Faturação**.
1. Selecione a subscrição vencida na página **Descrição geral**.
1. Na página **Descrição geral da subscrição**, clique na faixa de data de vencimento vencida para liquidar o saldo.
    > [!NOTE]
    > Se não for o Administrador da Conta, não poderá liquidar o saldo.
1. Na nova página **Liquidar o saldo**, clique em **Selecionar método de pagamento**.
    ![Captura de ecrã que mostra a ligação de seleção do método de pagamento](./media/resolve-past-due-balance/settle-balance-screen.png)

1. No novo painel à direita, selecione um cartão de crédito na lista pendente ou adicione um novo. Para tal, clique na ligação azul **Adicionar novo método de pagamento**. Este cartão de crédito tornar-se-á no método de pagamento ativo para todas as subscrições que estão a utilizar o método do pagamento em falta.
     > [!NOTE]
     > * O saldo pendente total reflete os custos pendentes em todos os serviços da Microsoft que utilizam o método do pagamento em falta.
     > * Se o método de pagamento selecionado também tiver custos pendentes relativamente a serviços da Microsoft, tal será refletido no saldo pendente total. Também deverá pagar esses custos pendentes.
1. Clique em **Pagar**.

## <a name="troubleshoot-declined-credit-card"></a>Resolução de problemas de cartão de crédito recusado

Se o seu cartão de crédito for recusado pela sua instituição financeira, contacte a instituição financeira para resolver o problema. Informe-se junto do seu banco para se certificar de que:
- As transações internacionais estão ativadas no cartão.
- O cartão tem limite de crédito ou fundos suficientes para liquidar o saldo.
- Os pagamentos periódicos estão ativados no cartão.

## <a name="not-getting-billing-email-notifications"></a>Não está a obter as notificações por e-mail da faturação?

Se for o Administrador de Conta, [verifique qual é o endereço de e-mail utilizado para as notificações](change-azure-account-profile.md). Recomendamos que utilize um endereço de e-mail que utilize regularmente. Se o e-mail estiver correto, verifique a pasta de spam.

## <a name="if-i-forget-to-pay-what-happens"></a>Se eu me esquecer de pagar, o que acontece?

O serviço é cancelado e os seus recursos deixam de estar disponíveis. Os dados do Azure são eliminados 90 dias após terminar o serviço. Para saber mais, veja [Centro de Fidedignidade da Microsoft – Como gerimos os dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Se tiver a certeza que o pagamento foi processado, mas a subscrição continuar desativada, contacte o [Suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="check-the-type-of-your-account"></a>Verificar o tipo de conta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
