---
title: Resolução de problemas de pagamento no Azure
description: Resolver um problema quando está a atualizar uma conta de informações de pagamento no portal do Microsoft Azure ou no Centro de Contas.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "69657089"
---
# <a name="troubleshoot-azure-payment-issues"></a>Resolução de problemas de pagamento no Azure

Poderá deparar-se com problemas quando tenta atualizar a conta de informações de pagamento no portal do Microsoft Azure ou no Centro de Contas do Azure. Antes de resolver os problemas, considere primeiro as diretrizes abaixo:

- Confirme que as informações que forneceu no seu perfil da conta do Azure (incluindo o endereço de e-mail, o endereço e o número de telefone) estão corretas.
- Certifique-se de que as informações do cartão de crédito estão corretas.
- Confirme que ainda não tem uma conta Microsoft com as mesmas informações.

## <a name="issues"></a>Problemas

Para resolver quaisquer erros, selecione o problema que está a ter ao tentar inscrever-se no Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Não consegue remover um cartão de crédito de um método de pagamento de faturação guardado

Por predefinição, não pode remover um cartão de crédito da Subscrição ativa.

Se um cartão existente tiver de ser eliminado, um novo cartão terá de ser adicionado à subscrição para que o instrumento de pagamento antigo possa ser eliminado com êxito ou, em alternativa, terá de cancelar a subscrição. Esta ação elimina a subscrição permanentemente e remove o cartão.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Não consegue eliminar um método de pagamento antigo depois de adicionar um novo método de pagamento

O novo instrumento de pagamento pode não ser associado à subscrição. Para ajudar a associar o instrumento de pagamento à subscrição, veja [Adicionar, atualizar ou remover um cartão de crédito ou débito para o Azure](billing-how-to-change-credit-card.md).

Para resolver problemas relacionados com um cartão rejeitado, veja [How to troubleshoot a declined card at Azure sign-up](billing-troubleshoot-declined-card.md) (Como resolver problemas de um cartão rejeitado na inscrição no Azure).

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Não consegue eliminar um método de pagamento devido ao erro *Não é possível eliminar o método de pagamento*

Isso ocorre devido a um saldo pendente. Resolva quaisquer saldos pendentes antes de eliminar o método de pagamento.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Não consegue ver as subscrições em A minha conta para atualizar o método de pagamento

Poderá estar a usar um ID de e-mail diferente daquele que serve para as subscrições.

Para resolver este problema, veja [Erro de início de sessão Não foram encontradas subscrições no portal do Azure ou no Centro de Contas do Azure](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Não consegue fazer o pagamento de uma subscrição

Se receber a mensagem de erro: *O pagamento está em atraso. Existe um problema com o método de pagamento* ou *Não é possível guardar as informações. Feche o browser e tente novamente.* , tal deve-se à existência de um pagamento pendente no cartão, porque o cartão foi negado pela sua instituição financeira.

Verifique se o cartão de crédito tem um saldo suficiente para fazer um pagamento. Se não tiver, use outro cartão para fazer o pagamento ou entre em contacto com a sua instituição financeira para resolver o problema.

Entre em contacto com o seu banco para os seguintes problemas:

- As transações internacionais não estão ativadas.
- O cartão tem um limite de crédito e o saldo deve ser liquidado.
- Um pagamento recorrente está ativado no cartão.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Não consegue alterar o método de pagamento devido a problemas no browser (o browser não responde, não carrega, etc.)

Termine todas as sessões ativas do Azure e siga os passo no artigo [Navegação InPrivate no Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) para iniciar uma sessão InPrivate no Microsoft Edge ou no Internet Explorer.

Na sessão privada, siga os passo em [How to change a credit card](billing-how-to-change-credit-card.md) (Como alterar um cartão de crédito) para atualizar ou alterar as informações do cartão de crédito.

Também pode tentar fazer o seguinte:

- Atualizar o browser
- Usar outro browser
- Eliminar cookies em cache

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>A minha subscrição continua desativada depois de atualizar o método de pagamento.

Este problema ocorre devido a um saldo pendente. Resolva quaisquer saldos pendentes antes de eliminar o método de pagamento.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Não consegue alterar o método de pagamento por causa de uma página de resposta de erro XML

Recebe esta mensagem se estiver a usar [o portal do Azure](https://portal.azure.com/) para adicionar um novo cartão de crédito.

Para adicionar os detalhes do cartão, inicie sessão no portal de Contas do Azure com o endereço de e-mail do Administrador de Conta para adicionar os detalhes do cartão.

## <a name="additional-help-resources"></a>Recursos de ajuda adicional

Outros artigos de resolução de problemas para Subscrições e Faturação do Azure

- [Cartão rejeitado](billing-troubleshoot-declined-card.md)
- [Problemas de início de sessão da subscrição](billing-troubleshoot-sign-in-issue.md)
- [Não foram encontradas subscrições](billing-no-subscriptions-found.md)
- [Vista de custos empresariais desativada](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contacte-nos para obter ajuda

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- [Documentação da Faturação do Azure](index.md)
