---
title: Solucionar problemas de pagamento do Azure
description: Resolvendo um problema ao atualizar a conta de informações de pagamento no portal do Microsoft Azure ou no centro de contas.
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
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657089"
---
# <a name="troubleshoot-azure-payment-issues"></a>Solucionar problemas de pagamento do Azure

Você pode enfrentar um problema ao tentar atualizar a conta de informações de pagamento no portal do Microsoft Azure ou no centro de contas do Azure. Antes de solucionar o problema, considere as seguintes diretrizes:

- Certifique-se de que as informações que você forneceu para seu perfil de conta do Azure (incluindo endereço de email de contato, endereço e número de telefone) estejam corretas.
- Certifique-se de que as informações do cartão de crédito estão corretas.
- Confirme que ainda não tem uma conta Microsoft com as mesmas informações.

## <a name="issues"></a>Problemas

Para resolver quaisquer erros, selecione o problema que você enfrenta ao tentar se inscrever no Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Não é possível remover um cartão de crédito de um método de pagamento de cobrança salvo

Por design, você não pode remover um cartão de crédito da assinatura ativa.

Se um cartão existente tiver que ser excluído, um novo cartão deverá ser adicionado à assinatura para que o instrumento de pagamento antigo possa ser excluído com êxito ou você deverá cancelar a assinatura. Isso exclui a assinatura permanentemente e remove o cartão.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Não é possível excluir um antigo método de pagamento depois de adicionar um novo método de pagamento

O novo instrumento de pagamento pode não ser associado à subscrição. Para ajudar a associar o instrumento de pagamento à assinatura, consulte [Adicionar, atualizar ou remover um cartão de crédito ou débito para o Azure](billing-how-to-change-credit-card.md).

Para solucionar problemas relacionados a um cartão recusado, consulte [como solucionar problemas de um cartão recusado na inscrição do Azure](billing-troubleshoot-declined-card.md).

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Não é possível excluir um método de pagamento devido a um erro de *método de pagamento não pode ser excluído*

Isso ocorre devido a um saldo pendente. Limpe os saldos pendentes antes de excluir o método de pagamento.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Não é possível ver as assinaturas em minha conta para atualizar o método de pagamento

Você pode estar usando uma ID de email diferente da que é usada para as assinaturas.

Para solucionar esse problema, consulte [erro de entrada sem assinaturas encontrado para Portal do Azure ou centro de contas do Azure](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Não é possível fazer o pagamento de uma assinatura

Se você receber a mensagem de erro: *O pagamento está atrasado. Há um problema com seu método* de pagamento ou *lamentamos, as informações não podem ser salvas. Feche o navegador e tente novamente.* , há um pagamento pendente no cartão porque o cartão foi negado por sua instituição financeira.

Verifique se o cartão de crédito tem um saldo suficiente para fazer um pagamento. Se não tiver, use outro cartão para fazer o pagamento ou entre em contato com sua instituição financeira para resolver o problema.

Entre em contato com seu banco para os seguintes problemas:

- As transações internacionais não estão habilitadas.
- O cartão tem um limite de crédito e o saldo deve ser liquidado.
- Um pagamento recorrente está habilitado no cartão.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Não é possível alterar o método de pagamento devido a problemas do navegador (o navegador não responde, não carrega, e assim por diante)

Faça logoff de todas as sessões ativas do Azure e siga as etapas no [artigo procurar InPrivate no Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) para iniciar uma sessão InPrivate no Microsoft Edge ou no Internet Explorer.

Na sessão privada, siga as etapas em [como alterar um cartão de crédito](billing-how-to-change-credit-card.md) para atualizar ou alterar as informações do cartão de crédito.

Você também pode tentar fazer o seguinte:

- Atualizar seu navegador
- Usar outro navegador
- Excluir cookies em cache

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Minha assinatura ainda é desabilitada após a atualização do método de pagamento.

Esse problema ocorre devido a um saldo pendente. Limpe os saldos pendentes antes de excluir o método de pagamento.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Não é possível alterar o método de pagamento devido a uma página de resposta de erro XML

Você receberá essa mensagem se estiver usando [o portal do Azure](https://portal.azure.com/) para adicionar um novo cartão de crédito.

Para adicionar detalhes do cartão, entre no portal da conta do Azure usando o endereço de email do administrador da conta.

## <a name="additional-help-resources"></a>Recursos adicionais de ajuda

Outros artigos de solução de problemas para cobrança e assinaturas do Azure

- [Cartão rejeitado](billing-troubleshoot-declined-card.md)
- [Problemas de entrada da assinatura](billing-troubleshoot-sign-in-issue.md)
- [Não foram encontradas subscrições](billing-no-subscriptions-found.md)
- [Vista de custos empresariais desativada](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Entre em contato conosco para obter ajuda

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- [Documentação de cobrança do Azure](index.md)
