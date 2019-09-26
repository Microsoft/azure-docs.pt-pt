---
title: Erro Não foram encontradas subscrições – início de sessão no portal do Azure | Microsoft Docs
description: Fornece a solução para um problema no qual ocorre o erro Não foram encontradas subscrições ao iniciar sessão no portal do Azure ou no centro de contas do Azure.
services: ''
documentationcenter: ''
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 19a30a99b9df23358aafd88c55bde08c73a3be72
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121549"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Erro de início de sessão Não foram encontradas subscrições no portal do Azure ou no centro de contas do Azure

Poderá ser apresentada uma mensagem de erro "Não foram encontradas subscrições" ao tentar iniciar sessão no [portal do Azure](https://portal.azure.com/) ou no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions). Este artigo fornece uma solução para este problema.

## <a name="symptom"></a>Sintoma

Ao tentar iniciar sessão no [portal do Azure](https://portal.azure.com/) ou no [centro de contas do Azure](https://account.windowsazure.com/Subscriptions), é apresentada a seguinte mensagem de erro: "Não foram encontradas subscrições".

## <a name="cause"></a>Causa

Este problema ocorre se tiver selecionado o diretório errado ou se a sua conta não tiver permissões suficientes. 

## <a name="solution"></a>Solução

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Cenário 1: É apresentada uma mensagem de erro no [portal do Azure](https://portal.azure.com)

Para resolver este problema:

* Certifique-se de que está selecionado o diretório do Azure correto ao clicar na sua conta no canto superior direito.

  ![Selecione o diretório no canto superior direito do portal do Azure](./media/billing-no-subscriptions-found/directory-switch.png)
* Se o diretório do Azure correto estiver selecionado, mas continuar a ser apresentada a mensagem de erro, [atribua a função Proprietário à sua conta](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Cenário 2: A mensagem de erro é apresentada no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta que utilizou é do Administrador de Conta. Para verificar quem é o Administrador de Conta, siga estes passos:

1. Inicie sessão na [vista Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição que pretende verificar e, em seguida, procure em **Definições**.
1. Selecione **Propriedades**. O administrador de conta da subscrição é apresentado na caixa **Administrador de Conta**.  

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
