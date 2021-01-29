---
title: Resolução de problemas quando se inscreve numa nova conta no portal do Azure ou no centro de contas do Azure
description: Resolver um problema quando se tenta inscrever numa nova conta no centro de contas do portal do Microsoft Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: v-miegge
ms.openlocfilehash: 015e6058236190a04df152573621f0e57030d23b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054166"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Resolução de problemas quando se inscreve numa nova conta no portal do Azure ou no centro de contas do Azure

Poderá ter problemas quando se tentar inscrever numa nova conta no portal do Microsoft Azure ou no centro de contas do Azure. Este pequeno guia orienta-o ao longo do processo de inscrição e aborda alguns dos problemas comuns em cada um dos passos.

> [!NOTE]
> Se já tem uma conta e está à procura de orientação para resolver problemas de início de sessão, veja [Resolver problemas relativos a início de sessão da subscrição do Azure](./troubleshoot-sign-in-issue.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a inscrição, verifique o seguinte:

- Se as informações no Perfil da Conta do Azure (incluindo o endereço de e-mail, a morada e o número de telefone) estão corretas.
- Se as informações do cartão de crédito estão corretas.
- Se já não tem uma conta Microsoft com as mesmas informações.

## <a name="guided-walkthrough-of-azure-sign-up"></a>Instruções passo a passo da inscrição do Azure

A experiência de inscrição do Azure consiste em quatro secções:

- Acerca de si
- Verificação de identidade por telefone
- Verificação de identidade por cartão
- Contrato

Estas instruções passo a passo incluem exemplos de informações corretas para se inscrever numa conta do Azure. Cada secção também contém alguns dos problemas comuns e como os resolver.

## <a name="about-you"></a>Acerca de si

![Acerca de si](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>Problemas conhecidos e soluções

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>É apresentada a mensagem “Não podemos continuar com a inscrição devido a um problema na conta. Contacte o apoio ao cliente de faturação”

Para resolver este erro, siga estes passos:

1.  Inicie sessão no [Centro de Contas do Azure](https://account.azure.com/Profile) com a credencial de administrador de conta.
1.  Selecione **Editar detalhes**.
1.  Confirme que todos os campos do endereço estão preenchidos e são válidos.
1.  Quando se inscrever na subscrição do Azure, verifique que o endereço de faturação do registo do cartão de crédito corresponde aos dados bancários.

Se continuar a receber a mensagem, tente inscrever-se com um browser diferente.

E quanto à navegação InPrivate?

#### <a name="free-trial-is-not-available"></a>A avaliação gratuita não está disponível

Já usou uma subscrição do Azure anteriormente? O contrato dos Termos de Utilização do Azure limita a ativação da avaliação gratuita a apenas um utilizador novo no Azure. Se já tiver outro tipo de subscrição do Azure, não poderá ativar uma avaliação gratuita. Considere inscrever-se numa [subscrição Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p/).

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>É apresentada a mensagem “Não é elegível para uma subscrição do Azure”

Para resolver este problema, verifique novamente se os seguintes itens são verdadeiros:

- As informações que forneceu no seu perfil da conta do Azure (incluindo o endereço de e-mail, o endereço e o número de telefone) estão corretas.
- As informações do cartão de crédito estão corretas.
- Se já não tem uma conta Microsoft que utilize as mesmas informações.

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>É apresentada a mensagem “O tipo de conta atual não é suportado”

Este problema pode ocorrer se a conta estiver registada num [diretório do Azure AD não gerido](../../active-directory/enterprise-users/directory-self-service-signup.md) e não estiver no diretório do Azure AD da sua organização.
Para resolver este problema, inscreva-se na conta do Azure com outra conta ou assuma o controlo do diretório do AD não gerido. Para obter mais informações, veja [Take over an unmanaged directory as administrator in Azure Active Directory](../../active-directory/enterprise-users/domains-admin-takeover.md) (Assumir um diretório não gerido como administrador no Azure Active Directory).

## <a name="identity-verification-by-phone"></a>Verificação de identidade por telefone

![Verificação de identidade por telefone](./media/troubleshoot-azure-sign-up/2.png)
 
Quando receber a mensagem de texto ou a chamada telefónica, introduza o código que receber na caixa de texto.

### <a name="common-issues-and-solutions"></a>Problemas conhecidos e soluções

#### <a name="no-verification-text-message-or-phone-call"></a>Não recebo a mensagem de texto de verificação nem a chamada telefónica

Embora o processo de verificação de inscrição seja normalmente rápido, pode demorar até quatro minutos para que um código de verificação seja enviado.

Eis algumas dicas adicionais:

- Pode utilizar qualquer número de telefone para a verificação, desde que cumpra os requisitos. O número de telefone que introduzir para verificação não será armazenado como um número de contacto para a conta.
  - Não pode usar um número de telefone VoiP (Voice-over-IP) para o processo de verificação por telefone.
  - Verifique se o telefone pode receber chamadas ou mensagens SMS de um número de telefone proveniente dos Estados Unidos.
- Verifique novamente o número de telefone que introduzir, incluindo o indicativo que selecionar no menu pendente.
- Se o seu telefone não receber mensagens de texto (SMS), experimente a opção **Ligar-me**.

## <a name="identity-verification-by-card"></a>Verificação de identidade por cartão

![Verificação de identidade por cartão](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>Problemas conhecidos e soluções

#### <a name="credit-card-declined-or-not-accepted"></a>Cartão de crédito rejeitado ou não aceite

Os cartões de débito ou de crédito pré-pagos ou virtuais não são aceites como pagamento para Subscrições do Azure. Para ver o que mais pode fazer com que o cartão seja rejeitado, veja [Resolução de problemas num cartão rejeitado na inscrição no Azure](./troubleshoot-declined-card.md).

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>O formulário do cartão de crédito não suporta o meu endereço de faturação

O endereço de faturação tem de estar no país que selecionar na secção **Acerca de si**. Verifique se selecionou o país correto.

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A barra de progresso fica parada na secção Verificação de identidade por cartão

Para concluir a verificação de identidade por cartão, tem de permitir cookies de terceiros no seu browser.

Utilize os passos seguintes para atualizar as definições de cookies do browser.

1. Atualize as definições de cookies.
   - Se estiver a utilizar o **Chrome**:
     - Selecione **Definições** > **Mostrar definições avançadas** > **Privacidade** > **Definições de conteúdo**. Desmarque **Bloquear cookies de terceiros e dados do site**.

   - Se estiver a utilizar o **Microsoft Edge**:
     - Selecione **Definições** > **Ver definições avançadas** > **Cookies** > **Não bloquear cookies**.

1. Atualize a página de inscrição do Azure e verifique se o problema foi resolvido.
1. Se a atualização não tiver resolvido o problema, saia, reinicie o browser e tente novamente.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Vi uma cobrança na minha conta de avaliação gratuita

Poderá ver uma breve retenção da verificação temporária na sua conta de cartão de crédito depois de se inscrever. Esta retenção é removida em três a cinco dias. Se estiver preocupado com a gestão dos custos, leia mais sobre como [Analisar custos inesperados](../understand/analyze-unexpected-charges.md).

## <a name="agreement"></a>Contrato

Complete o Contrato.

## <a name="other-issues"></a>Outros problemas

### <a name="cant-activate-azure-benefit-plan-like-visual-studio-bizspark-bizsparkplus-or-mpn"></a>Não é possível ativar o plano de benefícios da Azure como Visual Studio, BizSpark, BizSparkPlus ou MPN

Verifique se está a utilizar as informações de início de sessão corretas. Em seguida, verifique o programa de benefícios para confirmar se é elegível.
- Visual Studio
  - Verifique o seu estado de elegibilidade na sua [página de conta Visual Studio](https://my.visualstudio.com/Benefits).
  - Se não conseguir verificar o seu estado, contacte [o Suporte à Subscrição do Estúdio Visual](https://visualstudio.microsoft.com/subscriptions/support/).
- Microsoft para Startups
  - Inicie sessão no [portal Microsoft para Startups portal](https://startups.microsoft.com/#start-two) para verificar o seu estado de elegibilidade relativo à Microsoft para Startups.
  - Se não conseguir verificar o seu estado, pode obter ajuda nos [fóruns da Microsoft para Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Inicie sessão no [portal do MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) para verificar o seu estado de elegibilidade. Se tiver as [Competências da Plataforma da Cloud](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx) adequadas, poderá ser elegível para vantagens adicionais.
  - Se não conseguir verificar o seu estado, contacte o [Suporte do MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Não é possível ativar uma nova subscrição do Azure no Open

Para criar uma subscrição do Azure no Open, precisa de uma Chave de Ativação de Serviço Online (OSA) válida que tenha pelo menos um token do Azure no Open associado à mesma. Se não tiver uma chave de OSA, contacte um dos Parceiros da Microsoft listados no [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

## <a name="additional-help-resources"></a>Recursos de ajuda adicionais

Outros artigos de resolução de problemas para Subscrições e Faturação do Azure

- [Cartão rejeitado](./troubleshoot-declined-card.md)
- [Problemas de início de sessão da subscrição](./troubleshoot-sign-in-issue.md)
- [Não foram encontradas subscrições](./no-subscriptions-found.md)
- [Vista de custos empresariais desativada](./enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contacte-nos para obter ajuda

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="find-out-more-about-azure-cost-management"></a>Saiba mais sobre o Azure Cost Management

- [Documentação do Azure Cost Management e Faturação](../index.yml)