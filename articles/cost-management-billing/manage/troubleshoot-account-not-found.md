---
title: Resolver problemas relativos à visualização da conta de faturação no portal do Azure
description: Este artigo ajuda a resolver problemas quando tenta ver a sua conta de faturação no portal do Azure.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 71561f6ecdde09f259e54260e7bab4dcc0e85c98
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132640"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Resolver problemas relativos à visualização da conta de faturação no portal do Azure

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. É possível que tenha acesso a múltiplas contas de faturação. Por exemplo, talvez se tenha inscrito no Azure para utilização pessoal. Também poderá ter acesso ao Azure através do Contrato Enterprise ou do Contrato de Cliente da Microsoft da sua organização. Para cada um desses cenários, teria uma conta de faturação separada. Este artigo ajuda a resolver problemas quando tenta ver a sua conta de faturação no portal do Azure.

Pode ver a conta de faturação na página [Azure Cost Management + Faturação](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade).

Para saber mais sobre as contas de faturação e identificar o tipo da sua conta de faturação, veja [Ver as contas de faturação no portal do Azure](view-all-accounts.md).

Se não conseguir ver a conta de faturação no portal do Azure, experimente as seguintes opções:

## <a name="sign-in-to-a-different-tenant"></a>Iniciar sessão noutro inquilino

A sua conta de faturação está associada a um único inquilino do Azure Active Directory. Se tiver sessão iniciada no inquilino errado, não verá a sua conta de faturação na página Cost Management + Faturação. Siga os passos abaixo para mudar para outro inquilino no portal do Azure e ver as suas contas de faturação nesse inquilino.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o seu perfil (endereço de e-mail) no canto superior direito da página.
1. Selecione **Trocar diretório**.  
    ![Captura de ecrã que mostra a seleção de Mudar de diretório no portal](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. Selecione um diretório na secção **Todos os diretórios**.  
    ![Captura de ecrã que mostra a seleção de um diretório no portal](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Iniciar sessão com outro endereço de e-mail

Alguns utilizadores têm vários endereços de e-mail com os quais iniciam sessão no [portal do Azure](https://portal.azure.com). Nem todos os endereços têm acesso a uma conta de faturação. Se iniciar sessão com um endereço de e-mail que tem permissões para gerir recursos, mas não para ver uma conta de faturação, não verá a conta de faturação na página [Cost Management + Faturação](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) no portal do Azure.

Para aceder à sua conta de faturação, inicie sessão no portal do Azure com um endereço de e-mail que tenha permissão para ver a conta de faturação.

## <a name="sign-in-with-a-different-identity"></a>Iniciar sessão com outra identidade

Alguns utilizadores têm duas identidades com o mesmo endereço de e-mail, uma conta escolar ou profissional e outra pessoal. Normalmente, apenas uma das identidades tem permissões para ver uma conta de faturação. É possível que tenha duas identidades com um único endereço de e-mail. Quando inicia sessão com uma identidade que não tenha permissão para ver uma conta de faturação, não verá a conta de faturação na página [Cost Management + Faturação](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade). Utilize os passos abaixo para mudar a identidade:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) numa janela InPrivate/Anónima.
1. Se o seu endereço de e-mail tiver duas identidades, verá uma opção para selecionar uma conta pessoal ou escolar ou profissional. Selecione uma das contas.
1. Se não vir a conta de faturação na página Cost Management + Faturação no portal do Azure, repita os passos 1 e 2 e selecione a outra identidade.

## <a name="contact-us-for-help"></a>Contacte-nos para obter ajuda

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

Leia os seguintes artigos relativos à faturação e subscrições para ajudar a resolver problemas.

- [Cartão rejeitado](./troubleshoot-declined-card.md)
- [Problemas ao iniciar sessão na subscrição](./troubleshoot-sign-in-issue.md)
- [Não foram encontradas subscrições](./no-subscriptions-found.md)
- [Vista de custos empresariais desativada](./enterprise-mgmt-grp-troubleshoot-cost-view.md)