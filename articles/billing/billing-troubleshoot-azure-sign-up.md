---
title: Solucionar problemas de inscrição no Azure
description: Resolvendo um problema ao tentar se inscrever para uma nova conta no centro de contas portal do Microsoft Azure.
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
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657076"
---
# <a name="troubleshoot-azure-sign-up"></a>Solucionar problemas de inscrição no Azure

Você pode enfrentar um problema ao tentar se inscrever em uma nova conta no portal do Microsoft Azure ou no centro de contas do Azure. Antes de solucionar o problema, primeiro verifique o seguinte:

- As informações que você forneceu para seu perfil de conta do Azure (incluindo endereço de email de contato, endereço e número de telefone) estão corretas.
- As informações de cartão de crédito estão corretas.
- Você ainda não tem um conta Microsoft que tem as mesmas informações.

## <a name="resolutions"></a>Suas

Para resolver quaisquer erros, selecione o problema que você enfrenta ao tentar se inscrever no Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Erro: *Não podemos continuar com a inscrição devido a um problema com sua conta. Entre em contato com o suporte de cobrança.*

Para resolver o problema, siga estas etapas:

1. Faça logon no [centro de contas do Azure](https://account.azure.com/Profile) usando a credencial de administrador da conta.

2. Selecione **Editar detalhes**.

3. Certifique-se de que todos os campos de endereço sejam concluídos e válidos.

4. Quando você se inscreve para a assinatura do Azure, verifique se o endereço de cobrança do registro do cartão de crédito corresponde aos seus registros bancários.

Se você continuar a receber a mensagem de erro, tente se inscrever usando um navegador diferente.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A barra de progresso trava na seção *verificação de identidade por cartão* .

Para concluir a verificação de identidade por cartão, cookies de terceiros devem ser permitidos para seu navegador.

![Verificação de identidade por cartão](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
Use as etapas a seguir para atualizar as configurações de cookie do navegador.

1. Se você estiver usando o Chrome, selecione **configurações** > **Mostrar configurações** > avançadas**configurações de conteúdo**de**privacidade** > . Desmarque **bloquear cookies de terceiros e dados do site**.

2. Se você estiver usando o Microsoft Edge, selecione **configurações** > **exibir configurações avançadas** > **cookies** > **não bloquear cookies**.

3. Atualize a página de inscrição do Azure e verifique se o problema foi resolvido.

4. Se a atualização não resolver o problema, saia e reinicie o navegador e tente novamente.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>O formulário de cartão de crédito não dá suporte ao meu endereço de cobrança

Seu endereço de cobrança deve estar localizado no país que você selecionar na seção **sobre você** . Certifique-se de selecionar o país correto.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Nenhuma mensagem de texto ou chamada durante a verificação da conta de inscrição

Embora o processo seja normalmente rápido, pode levar até quatro minutos para que um código de verificação seja entregue. O número de telefone que você digitar para verificação não será armazenado como um número de contato para a conta.

Aqui estão algumas dicas adicionais:

- Não é possível usar um número de telefone VoiP (voz sobre IP) para o processo de verificação do telefone.
- Verifique o número de telefone que você inseriu, incluindo o código do país que você selecionou no menu suspenso.
- Se o seu telefone não receber mensagens de texto (SMS), tente a opção **ligar para mim** .
- Verifique se o telefone pode receber chamadas ou mensagens SMS de um número de telefone baseado em Estados Unidos.

Quando você receber a mensagem de texto ou a chamada telefônica, insira o código que você recebe na caixa de texto.

### <a name="credit-card-declined-or-not-accepted"></a>Cartão de crédito recusado ou não aceito

Cartões de crédito ou débitos pré-pagos ou virtuais não são aceitos como pagamento para assinaturas do Azure. Para ver o que mais pode fazer com que o cartão seja recusado, consulte o cartão de débito ou crédito é [recusado na inscrição do Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>A avaliação gratuita não está disponível

Você já usou uma assinatura do Azure no passado? O contrato dos Termos de Utilização do Azure limita a ativação da avaliação gratuita a apenas um utilizador novo no Azure. Se já tiver outro tipo de subscrição do Azure, não poderá ativar uma avaliação gratuita. Considere inscrever [-se para uma assinatura paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Vi uma cobrança em minha conta de avaliação gratuita

Você pode ver uma pequena verificação na sua conta de cartão de crédito depois de se inscrever. Isso é removido dentro de três a cinco dias. Se você estiver preocupado com o gerenciamento de custos, leia mais sobre como [evitar custos](billing-getting-started.md)inesperados.

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Não é possível ativar o plano de benefícios do Azure como MSDN, BizSpark, BizSparkPlus ou MPN

Verifique se você está usando as credenciais de entrada corretas. Em seguida, verifique o programa de benefícios para certificar-se de que você está qualificado.

- MSDN 
  - Verifique seu status de qualificação na [página](https://msdn.microsoft.com/subscriptions/manage/default.aspx)da sua conta do MSDN.
  - Se você não puder verificar seu status, entre em contato com o [atendimento ao cliente das assinaturas do MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft para Startups
  - Entre no portal da [Microsoft para inicializações](https://startups.microsoft.com/#start-two) para verificar seu status de qualificação para a Microsoft para inicializações.
  - Se você não puder verificar seu status, poderá obter ajuda sobre os [fóruns da Microsoft para inicializações](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Entre no portal do [MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) para verificar seu status de qualificação. Se você tiver as [competências de plataforma de nuvem](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)apropriadas, poderá estar qualificado para obter benefícios adicionais.
  - Se você não puder verificar seu status, contate o [suporte do MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Não é possível ativar uma nova assinatura do Azure no Open

Para criar uma assinatura do Azure no Open, você deve ter uma chave de OSA (ativação de serviço online) válida que tenha pelo menos um token do Azure no Open associado a ela. Se você não tiver uma chave de OSA, entre em contato com um dos parceiros da Microsoft listados no [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Erro: *Você não está qualificado para uma assinatura do Azure*

Para resolver esse problema, verifique se os seguintes itens são verdadeiros:

- As informações que você forneceu para seu perfil de conta do Azure (incluindo endereço de email de contato, endereço e número de telefone) estão corretas.
- As informações de cartão de crédito estão corretas.
- Você ainda não tem um conta Microsoft que usa as mesmas informações.

### <a name="error-your-current-account-type-is-not-supported"></a>Erro: *Não há suporte para seu tipo de conta atual*

Esse problema pode ocorrer se a conta estiver registrada em um [diretório não gerenciado do Azure ad](../active-directory/users-groups-roles/directory-self-service-signup.md)e não estiver no diretório do Azure ad da sua organização. 

Para resolver esse problema, Inscreva-se na conta do Azure usando outra conta ou assuma o diretório do AD não gerenciado. Para obter mais informações, consulte [assumir um diretório não gerenciado como administrador em Azure Active Directory](../active-directory/users-groups-roles/domains-admin-takeover.md).
 
## <a name="additional-help-resources"></a>Recursos adicionais de ajuda

Outros artigos de solução de problemas para cobrança e assinaturas do Azure

- [Cartão rejeitado](billing-troubleshoot-declined-card.md)
- [Problemas de entrada da assinatura](billing-troubleshoot-sign-in-issue.md)
- [Não foram encontradas subscrições](billing-no-subscriptions-found.md)
- [Vista de custos empresariais desativada](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Entre em contato conosco para obter ajuda

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

- [Documentação de cobrança do Azure](index.md)
