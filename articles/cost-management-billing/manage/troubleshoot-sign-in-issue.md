---
title: Resolver problemas relativos a início de sessão da subscrição do Azure
description: Ajuda a resolver os problemas nos quais não pode iniciar sessão no portal do Azure ou no centro de contas do Azure.
author: v-miegge
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 40a645fe4b74043d84dc770f15d5e3c3ec907f02
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199488"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Resolver problemas relativos a início de sessão da subscrição do Azure

Este guia ajuda a resolver os problemas nos quais não pode iniciar sessão no portal do Azure ou no centro de contas do Azure.

## <a name="issues"></a>Problemas

### <a name="page-hangs-in-the-loading-status"></a>A página encrava no estado de carregamento

Se a página do browser encravar, tente cada um dos passos a seguir até que consiga aceder ao portal do Azure.

- Atualize a página.
- Utilize um browser diferente.
- Utilize o modo de navegação privada do browser. No Internet Explorer: Clique em **Ferramentas** > **Segurança** > **Navegação InPrivate** e, em seguida, navegue e inicie sessão no [portal do Azure](https://portal.azure.com/) ou no [centro de contas do Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Inicia sessão automaticamente como um utilizador diferente

Este problema pode ocorrer se utilizar mais do que uma conta de utilizador num browser.

Para resolver o problema, experimente um dos métodos seguintes:

- Limpe a cache e elimine os cookies da Internet. No Internet Explorer, clique em **Ferramentas** > **Opções da Internet** > **Eliminar**. Confirme que as caixas de verificação dos ficheiros temporários, dos cookies, das palavras-passe e do histórico de navegação estão selecionadas e clique em Eliminar.
- Reponha as definições do Internet Explorer para reverter quaisquer definições pessoais que tenha realizada. Clique em **Ferramentas** > **Opções da Internet** > **Avançadas**, selecione a caixa **Eliminar definições pessoais** e prima **Repor**.
- Utilize o modo de navegação privada do browser. No Internet Explorer:  Clique em **Ferramentas** > **Segurança** > **Navegação InPrivate** e, em seguida, navegue e inicie sessão no [portal do Azure](https://portal.azure.com/) ou no [centro de contas do Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Consigo iniciar sessão, mas não vejo *Nenhuma subscrição encontrada*

Este problema ocorre se tiver selecionado o diretório errado ou se a sua conta não tiver permissões suficientes.

**Cenário 1:** É apresentada uma mensagem de erro no [portal do Azure](https://portal.azure.com/)

Para resolver este problema:

- Certifique-se de que está selecionado o diretório do Azure correto ao clicar na sua conta no canto superior direito.
- Se o diretório do Azure correto estiver selecionado, mas continuar a ser apresentada a mensagem de erro, [atribua a função de Proprietário à sua conta](add-change-subscription-administrator.md).

**Cenário 2:** A mensagem de erro é apresentada no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta que utilizou é do Administrador de Conta. Para verificar quem é o Administrador de Conta, siga estes passos:

1. Inicie sessão na [vista Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecione a subscrição que pretende verificar e, em seguida, procure em **Definições**.

3. Selecione **Propriedades**. O administrador de conta da subscrição é apresentado na caixa **Administrador de Conta**.

## <a name="additional-help-resources"></a>Recursos de ajuda adicionais

Outros artigos de resolução de problemas para Subscrições e Faturação do Azure

- [Cartão rejeitado](troubleshoot-declined-card.md)
- [Problemas de início de sessão da subscrição](troubleshoot-azure-sign-up.md)
- [Não foram encontradas subscrições](no-subscriptions-found.md)
- [Vista de custos empresariais desativada](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contacte-nos para obter ajuda

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- [Documentação de Faturação do Azure](../../billing/index.md)
