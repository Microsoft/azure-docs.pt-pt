---
title: Resolução de problemas quando se inscreve numa nova conta no portal do Azure ou no centro de contas do Azure
description: Resolver um problema quando se tenta inscrever numa nova conta no centro de contas do portal do Microsoft Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 2ccb2b7fca8a5615d56c25b9ea09a26caf295a2b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745534"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Resolução de problemas quando se inscreve numa nova conta no portal do Azure ou no centro de contas do Azure

Poderá ter problemas quando se tentar inscrever numa nova conta no portal do Microsoft Azure ou no centro de contas do Azure. Antes de resolver os problemas, verifique antes o seguinte:

- As informações que forneceu no Perfil da Conta do Azure (incluindo o endereço de e-mail, o endereço e o número de telefone) estão corretas.
- As informações do cartão de crédito estão corretas.
- Se já não tem uma conta Microsoft com as mesmas informações.

> [!NOTE]
> Se já tem uma conta e está à procura de orientação para resolver problemas de início de sessão, veja [Resolver problemas relativos a início de sessão da subscrição do Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue).

## <a name="resolutions"></a>Resoluções

Para resolver os erros, selecione o problema que está a ter ao tentar inscrever-se no Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Erro: *Não podemos continuar com a inscrição devido a um problema na sua conta. Contacte o apoio ao cliente de faturação.*

Para resolver o problema, siga estes passos:

1. Inicie sessão no [Centro de Contas do Azure](https://account.azure.com/Profile) com a credencial de administrador de conta.

2. Selecione **Editar detalhes**.

3. Certifique-se de que todos os campos do endereço são preenchidos e válidos.

4. Quando se inscrever na subscrição do Azure, certifique-se de que o endereço de faturação do registo do cartão de crédito corresponde aos seus dados bancários.

Se continuar a receber a mensagem de erro, tente inscrever-se com um browser diferente.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A barra de progresso fica parada na secção *Verificação de identidade por cartão*.

Para concluir a verificação de identidade por cartão, tem de permitir cookies de terceiros no seu browser.

![Verificação de identidade por cartão](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

Utilize os passos seguintes para atualizar as definições de cookies do browser.

1. Se estiver a usar o Chrome, selecione **Definições** > **Mostrar definições avançadas** > **Privacidade** > **Definições de conteúdo**. Desmarque **Bloquear cookies de terceiros e dados do site**.

2. Se estiver a usar o Microsoft Edge, selecione **Definições** > **Ver definições avançadas** > **Cookies** > **Não bloquear cookies**.

3. Atualize a página de inscrição no Azure e verifique se o problema foi resolvido.

4. Se a atualização não resolveu o problema, saia e reinicie o browser e, em seguida, tente novamente.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>O formulário do cartão de crédito não suporta o meu endereço de faturação

O seu endereço de faturação tem de estar situado no país/região que selecionar na secção **Acerca de si**. Certifique-se de que seleciona o país/região correto.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Não recebe mensagens de texto nem chamadas durante a verificação da conta de inscrição

Embora o processo seja normalmente rápido, pode demorar até quatro minutos para que um código de verificação seja entregue. O número de telefone que introduzir para verificação não será armazenado como um número de contacto para a conta.

Eis algumas dicas adicionais:

- Não pode usar um número de telefone VoiP (Voice-over-IP) para o processo de verificação por telefone.
- Verifique novamente o número de telefone que introduzir, incluindo o código de país/região que selecionar no menu pendente.
- Se o seu telefone não receber mensagens de texto (SMS), experimente a opção **Ligar-me**.
- Verifique se o telefone pode receber chamadas ou mensagens SMS de um número de telefone proveniente dos Estados Unidos.

Quando receber a mensagem de texto ou a chamada telefónica, introduza o código que receber na caixa de texto.

### <a name="credit-card-declined-or-not-accepted"></a>Cartão de crédito rejeitado ou não aceite

Os cartões de débito ou de crédito pré-pagos ou virtuais não são aceites como pagamento para Subscrições do Azure. Para ver o que mais pode fazer com que o cartão seja rejeitado, veja O seu cartão de débito ou de crédito é [recusado na inscrição no Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>Não está disponível uma Avaliação Gratuita

Já usou uma subscrição do Azure anteriormente? O contrato dos Termos de Utilização do Azure limita a ativação da avaliação gratuita a apenas um utilizador novo no Azure. Se já tiver outro tipo de subscrição do Azure, não poderá ativar uma avaliação gratuita. Considere inscrever-se numa [subscrição Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Vi uma cobrança na minha conta de Avaliação Gratuita

Poderá ver uma breve retenção da verificação na sua conta de cartão de crédito depois de se inscrever. Este valor é removido em três a cinco dias. Se estiver preocupado com a gestão dos custos, leia mais sobre como [evitar custos inesperados](getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Não consegue ativar o plano de vantagens do Azure como MSDN, BizSpark, BizSparkPlus ou MPN

Verifique se está a usar as informações de início de sessão corretas. Em seguida, verifique o programa de vantagens para confirmar que é elegível.

- MSDN
  - Verifique o seu estado de elegibilidade na [página da sua conta MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Se não conseguir verificar o seu estado, contacte os [Centros de Suporte ao Cliente das Subscrições do MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft para Startups
  - Inicie sessão no [portal Microsoft para Startups portal](https://startups.microsoft.com/#start-two) para verificar o seu estado de elegibilidade relativo à Microsoft para Startups.
  - Se não conseguir verificar o seu estado, pode obter ajuda nos [fóruns da Microsoft para Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Inicie sessão no [portal do MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) para verificar o seu estado de elegibilidade. Se tiver as [Competências da Plataforma da Cloud](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx) adequadas, poderá ser elegível para vantagens adicionais.
  - Se não conseguir verificar o seu estado, contacte o [Suporte do MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Não é possível ativar uma nova subscrição do Azure no Open

Para criar uma subscrição do Azure no Open, precisa de uma Chave de Ativação de Serviço Online (OSA) válida que tenha pelo menos um token do Azure no Open associado à mesma. Se não tiver uma chave de OSA, contacte um dos Parceiros da Microsoft listados no [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Erro: *Não é elegível para uma subscrição do Azure*

Para resolver este problema, verifique novamente se os seguintes itens são verdadeiros:

- As informações que forneceu no seu perfil da conta do Azure (incluindo o endereço de e-mail, o endereço e o número de telefone) estão corretas.
- As informações do cartão de crédito estão corretas.
- Se já não tem uma conta Microsoft que utilize as mesmas informações.

### <a name="error-your-current-account-type-is-not-supported"></a>Erro: *Não há suporte para o seu tipo de conta atual*

Este problema pode ocorrer se a conta estiver registada num [diretório do Azure AD não gerido](../../active-directory/users-groups-roles/directory-self-service-signup.md) e não estiver no diretório do Azure AD da sua organização.

Para resolver este problema, inscreva-se na conta do Azure com outra conta ou assuma o controlo do diretório do AD não gerido. Para obter mais informações, veja [Take over an unmanaged directory as administrator in Azure Active Directory](../../active-directory/users-groups-roles/domains-admin-takeover.md) (Assumir um diretório não gerido como administrador no Azure Active Directory).

## <a name="additional-help-resources"></a>Recursos de ajuda adicionais

Outros artigos de resolução de problemas para Subscrições e Faturação do Azure

- [Cartão rejeitado](troubleshoot-declined-card.md)
- [Problemas de início de sessão da subscrição](troubleshoot-sign-in-issue.md)
- [Não foram encontradas subscrições](no-subscriptions-found.md)
- [Vista de custos empresariais desativada](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contacte-nos para obter ajuda

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- [Documentação de Faturação do Azure](../../billing/index.md)
