---
title: Resolver problemas relativos a início de sessão da subscrição do Azure
description: Ajuda a resolver os problemas nos quais não consegue iniciar sessão no portal do Azure ou no centro de contas do Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: e424a3d0e46f0dc154809eba501d2339bc94b1f5
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85806708"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Resolver problemas relativos a início de sessão da subscrição do Azure

Este guia ajuda a resolver os problemas nos quais não consegue iniciar sessão no portal do Azure ou no centro de contas do Azure.

> [!NOTE]
> Se tiver problemas ao inscrever-se numa conta do Azure nova, veja [Resolver problemas de inscrição numa subscrição do Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up).

## <a name="page-hangs-in-the-loading-status"></a>A página encrava no estado de carregamento

Se a página do browser encravar, tente cada um dos passos a seguir até que consiga aceder ao portal do Azure.

- Atualize a página.
- Utilize um browser diferente.
- Utilize o modo de navegação privada do browser.

   - **Edge:** abra as **Definições** (os três pontos junto à imagem do seu perfil), selecione **Nova janela InPrivate** e navegue e inicie sessão no [portal do Azure](https://portal.azure.com/) ou no [centro de contas do Azure](https://account.azure.com/Subscriptions). 
   - **Chrome:** escolha o modo de navegação **anónima**.
   - **Safari:** escolha **Ficheiro** e **Nova janela privada**.

- Limpe a cache e elimine os cookies da Internet:

   - **Edge:** abra as **Definições** e selecione **Privacidade e serviços**. Siga os passos em **Limpar dados de navegação**. Confirme que as caixas de verificação de **Histórico de navegação**, **Histórico de descarregamentos** e **Imagens e ficheiros em cache** estão selecionadas e escolha **Eliminar**.
   - **Chrome:** escolha **Definições** e selecione **Limpar dados de navegação** em **Privacidade e segurança**.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Inicia sessão automaticamente como um utilizador diferente

Este problema pode ocorrer se utilizar mais do que uma conta de utilizador num browser.

Para resolver o problema, experimente um dos métodos seguintes:

- Limpe a cache e elimine os cookies da Internet.

   - **Edge:** abra as **Definições** e selecione **Privacidade e serviços**. Siga os passos em **Limpar dados de navegação**. Confirme que as caixas de verificação de **Histórico de navegação**, **Histórico de descarregamentos**, **Cookies** e **Imagens e ficheiros em cache** estão selecionadas e escolha **Eliminar**.
   - **Chrome:** escolha **Definições** e selecione **Limpar dados de navegação** em **Privacidade e segurança**.
- Reponha as predefinições do browser.
- Utilize o modo de navegação privada do browser. 
   - **Edge:** abra as **Definições** (os três pontos junto à imagem do seu perfil), selecione **Nova janela InPrivate** e navegue e inicie sessão no [portal do Azure](https://portal.azure.com/) ou no [centro de contas do Azure](https://account.azure.com/Subscriptions). 
   - **Chrome:** escolha o modo de navegação **anónima**.
   - **Safari:** escolha **Ficheiro** e **Nova janela privada**.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Consigo iniciar sessão, mas vejo o erro "Nenhuma subscrição encontrada"

Este problema ocorre se tiver selecionado o diretório errado ou se a sua conta não tiver permissões suficientes.

**Cenário 1:** Recebe o erro ao iniciar sessão no [portal do Azure](https://portal.azure.com/)

Para resolver este problema:

- Selecione a sua conta no canto superior direito para confirmar que está selecionado o diretório do Azure certo.
- Se estiver selecionado o diretório do Azure certo, mas continuar a ser apresentada a mensagem de erro, [atribua a função de Proprietário à sua conta](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator).

**Cenário 2:** Recebe o erro ao iniciar sessão no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)

Verifique se a conta que utilizou é do Administrador de Conta. Para verificar quem é o Administrador de Conta, siga estes passos:

1.  Inicie sessão na [vista Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Selecione a subscrição que pretende verificar e selecione **Definições**.
1.  Selecione **Propriedades**. O administrador de conta da subscrição é apresentado na caixa **Administrador de Conta**.

## <a name="additional-help-resources"></a>Recursos de ajuda adicionais

Outros artigos de resolução de problemas para Subscrições e Faturação do Azure

- [Cartão rejeitado](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Problemas de início de sessão da subscrição](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-azure-sign-up)
- [Não foram encontradas subscrições](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Vista de custos empresariais desativada](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
- [Documentação de Faturação do Azure](https://docs.microsoft.com/azure/cost-management-billing/)

## <a name="contact-us-for-help"></a>Contacte-nos para obter ajuda

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
