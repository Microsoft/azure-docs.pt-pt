---
title: E-mail do Azure com o saldo antigo vencido | Documentos da Microsoft
description: Descreve como adaptar-pagamento se a sua subscrição do Azure tiver um passado saldo
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: d0b88c92-fb9d-4d12-931b-c26104ad63e9
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: f58969b167b6c0f0a66d46731ad76c1f6e9acc41
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491458"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Resolver em atraso saldo para a sua subscrição do Azure

Se o pagamento não é recebido ou se não foi possível processar o pagamento, poderá receber um e-mail ou ver um alerta no portal do Azure ou centro de contas.
Se estiver a [conta de administrador](billing-subscription-transfer.md#whoisaa), pode resolver as cobranças por liquidar [portal do Azure](https://portal.azure.com). Se estiver num modo de nota fiscal de pagamento, envie o pagamento para a localização listada na parte inferior da sua fatura.

> [!IMPORTANT]
> * Se tiver várias subscrições com o cartão de crédito e eles estão todos em atraso, terá de pagar o saldo pendente todo ao mesmo tempo.
> * O instrumento de pagamento que utiliza para liquidar as cobranças por liquidar se tornará o novo método de pagamento ativo para todas as subscrições que estivesse usando o método de pagamento com falha.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Resolver em atraso saldo no portal do Azure

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como administrador de conta.
1. Procure **custo Management + faturação**.
1. Na página Descrição geral, verá uma lista das suas subscrições. Se o estado da subscrição em atraso, clique nas **liquidar Saldo** ligação.
    ![Captura de ecrã que mostra liquidar link de saldo](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. O saldo pendente total reflete-se Cobranças por liquidar em todos os serviços da Microsoft com o método de pagamento com falha.
1. Selecione um método de pagamento para pagar o saldo. Este método de pagamento se tornará o método de pagamento ativo para todas as subscrições que atualmente a utilizar o método de pagamento com falha.
    ![Captura de ecrã que mostra o link do método de pagamento selecione](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Se o método de pagamento selecionado também tem Cobranças por liquidar para serviços da Microsoft, isso se refletirá no saldo total pendente. As cobranças por liquidar, terá de pagar demasiado.
1. Clique em **pagar**.

## <a name="not-getting-billing-email-notifications"></a>Não a obter notificações de e-mail de faturação?

Se for o administrador de conta [que endereço de e-mail é utilizado para notificações de verificação](billing-how-to-change-azure-account-profile.md). Recomendamos que utilize um endereço de e-mail que verificar regularmente. Se o e-mail está certo, verifique a pasta de spam.

## <a name="if-i-forget-to-pay-what-happens"></a>Se eu me esquecer a pagar, o que acontece?

O serviço é cancelado e seus recursos já não estão disponíveis. Seus dados do Azure são eliminados 90 dias após o serviço é encerrado. Para obter mais informações, consulte [Microsoft Trust Center - como podemos gerir os seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Se souber o pagamento foi processado, mas a sua subscrição ainda está desativada, contacte [suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
