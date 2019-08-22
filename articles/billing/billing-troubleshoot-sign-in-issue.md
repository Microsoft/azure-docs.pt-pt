---
title: Solucionar problemas de entrada de assinatura do Azure
description: Ajuda a resolver os problemas nos quais você não pode entrar no portal do Azure ou no centro de contas do Azure.
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657050"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Solucionar problemas de entrada de assinatura do Azure

Este guia ajuda a resolver os problemas nos quais você não pode entrar no portal do Azure ou no centro de contas do Azure. 

## <a name="issues"></a>Problemas

### <a name="page-hangs-in-the-loading-status"></a>A página trava no status de carregamento

Se a página do seu navegador da Internet parar, tente cada uma das etapas a seguir até que você possa chegar ao portal do Azure.

- Atualize a página.
- Use um navegador de Internet diferente.
- Use o modo de navegação particular para seu navegador. Para o Internet Explorer: Clique em **ferramentas** > **segurança** > InPrivate**navegação**e, em seguida, navegue e entre no [portal do Azure](https://portal.azure.com/) ou no [centro de contas do Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Você está automaticamente conectado como um usuário diferente

Esse problema pode ocorrer se você usar mais de uma conta de usuário em um navegador da Internet.

Para resolver o problema, tente um dos seguintes métodos:

- Limpe o cache e exclua os cookies da Internet. No Internet Explorer, clique em **ferramentas** > **Opções** > da Internet**excluir**. Verifique se as caixas de seleção de arquivos temporários, cookies, senhas e histórico de navegação estão selecionadas e clique em excluir.
- Redefina as configurações do Internet Explorer para reverter as configurações pessoais que você fez. Clique em **ferramentas** > **Opções** da Internet Avançado > selecione a caixa excluir configurações pessoais > Redefinir. > 
- Use o modo de navegação particular para seu navegador. Para o Internet Explorer:  Clique em **ferramentas** > **segurança** > InPrivate**navegação**e, em seguida, navegue e entre no [portal do Azure](https://portal.azure.com/) ou no [centro de contas do Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Eu posso entrar, mas *não vejo nenhuma assinatura encontrada*

Este problema ocorre se tiver selecionado no diretório errado ou se a sua conta não tem permissões suficientes.

**Cenário 1:** A mensagem de erro é recebida no [portal do Azure](https://portal.azure.com/)

Para corrigir este problema:

- Certifique-se de que o diretório do Azure correto está selecionado ao clicar em sua conta no canto superior direito.
- Se o diretório do Azure correto estiver selecionado, mas você ainda receber a mensagem de erro, peça à sua conta [adicionada como proprietário](billing-add-change-azure-subscription-administrator.md).

**Cenário 2:** A mensagem de erro é recebida no [centro de contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta que utilizou é o administrador de conta. Para verificar quem é o administrador de conta, siga estes passos:

1. Inicie sessão para o [subscrições ver no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecione a subscrição que pretende verificar e, em seguida, procure em **definições**.

3. Selecione **propriedades**. O administrador de conta da subscrição é apresentado na **administrador de conta** caixa.

## <a name="additional-help-resources"></a>Recursos adicionais de ajuda

Outros artigos de solução de problemas para cobrança e assinaturas do Azure

- [Cartão rejeitado](billing-troubleshoot-declined-card.md)
- [Problemas de inscrição de assinatura](billing-troubleshoot-azure-sign-up.md)
- [Não foram encontradas subscrições](billing-no-subscriptions-found.md)
- [Vista de custos empresariais desativada](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Entre em contato conosco para obter ajuda

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

- [Documentação de cobrança do Azure](index.md)
