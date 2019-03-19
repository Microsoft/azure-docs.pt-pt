---
title: Não foram encontradas subscrições erro - início de sessão portal do Azure | Documentos da Microsoft
description: Oferece a solução para um problema em que não foram encontradas subscrições erro ocorre quando iniciar sessão no portal do Azure ou centro de contas do Azure.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 094d79026a55e651a1f67a5047dff20c769c359a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881117"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Não foram encontradas subscrições início de sessão no erro para o portal do Azure ou centro de contas do Azure

Poderá receber uma mensagem de erro "Não foram encontradas subscrições" ao tentar iniciar sessão para o [portal do Azure](https://portal.azure.com/) ou o [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions). Este artigo fornece uma solução para este problema.

## <a name="symptom"></a>Sintoma

Quando tenta iniciar sessão para o [portal do Azure](https://portal.azure.com/) ou o [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions), receberá a seguinte mensagem de erro: "Não foram encontradas subscrições".

## <a name="cause"></a>Causa

Este problema ocorre se tiver selecionado no diretório errado ou se a sua conta não tem permissões suficientes. 

## <a name="solution"></a>Solução

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Cenário 1: Mensagem de erro é recebida no [portal do Azure](https://portal.azure.com)

Para corrigir este problema:

* Certifique-se de que o diretório do Azure correto está selecionado ao clicar em sua conta no canto superior direito.

  ![Selecione o diretório na parte superior direita do portal do Azure](./media/billing-no-subscriptions-found/directory-switch.png)
* Se for selecionado o diretório do Azure certo, mas continuar a receber a mensagem de erro [atribuir a função de proprietário à sua conta](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Cenário 2: Mensagem de erro é recebida no [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta que utilizou é o administrador de conta. Para verificar quem é o administrador de conta, siga estes passos:

1. Inicie sessão para o [subscrições ver no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, procure em **definições**.
1. Selecione **propriedades**. O administrador de conta da subscrição é apresentado na **administrador de conta** caixa.  

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
