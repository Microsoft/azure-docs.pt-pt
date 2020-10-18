---
title: Súm do utilizador baseado em SMS para O Diretório Ativo Azure
description: Saiba como configurar e permitir que os utilizadores entrem no Azure Ative Directory usando SMS (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc0dea825cb32275a2ada3a49d7d622180aa468
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166641"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Configure e permita aos utilizadores a autenticação baseada em SMS utilizando o Azure Ative Directory (pré-visualização)

Para reduzir a complexidade e os riscos de segurança para os utilizadores se inscreverem em aplicações e serviços, o Azure Ative Directory (Azure AD) oferece múltiplas opções de autenticação. A autenticação baseada em SMS, atualmente em pré-visualização, permite que os utilizadores entrem sem necessidade de fornecer, ou mesmo saber, o seu nome de utilizador e senha. Após a criação da sua conta por um administrador de identidade, podem introduzir o seu número de telefone no pedido de inscrição e fornecer um código de autenticação que lhes é enviado por mensagem de texto. Este método de autenticação simplifica o acesso a aplicações e serviços, especialmente para os trabalhadores da linha da frente.

Este artigo mostra-lhe como ativar a autenticação baseada em SMS para utilizadores ou grupos selecionados em Azure AD.

> [!NOTE]
> A autenticação por SMS para os utilizadores é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Precisa de privilégios *globais* de administrador no seu inquilino AZure AD para ativar a autenticação baseada em SMS.
* Cada utilizador que esteja ativado na política do método de autenticação de mensagens de texto deve ser licenciado, mesmo que não o utilize. Cada utilizador ativado deve ter uma das seguintes licenças Azure AD, EMS, Microsoft 365:
    * [Azure AD Premium P1 ou P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 ou F3][m365-firstline-workers-licensing]
    * [Mobilidade Empresarial + Segurança (EMS) E3 ou E5][ems-licensing] ou [Microsoft 365 (M365) E3 ou E5][m365-licensing]

## <a name="limitations"></a>Limitações

Durante a pré-visualização pública da autenticação baseada em SMS, aplicam-se as seguintes limitações:

* A autenticação baseada em SMS não é atualmente compatível com a autenticação multi-factor Azure.
* Com exceção das Equipas, a autenticação baseada em SMS não é atualmente compatível com aplicações nativas do Office.
* A autenticação baseada em SMS não é recomendada para contas B2B.
* Os utilizadores federados não autenticam no arrendatário. Só autenticam na nuvem.

## <a name="enable-the-sms-based-authentication-method"></a>Ativar o método de autenticação baseado em SMS

Existem três passos principais para ativar e utilizar a autenticação baseada em SMS na sua organização:

* Ativar a política do método de autenticação.
* Selecione utilizadores ou grupos que possam utilizar o método de autenticação baseado em SMS.
* Atribua um número de telefone para cada conta de utilizador.
    * Este número de telefone pode ser atribuído no portal Azure (que é mostrado neste artigo) e no *My Staff* ou No *Meu Perfil.*

Em primeiro lugar, vamos ativar a autenticação baseada em SMS para o seu inquilino AZure AD.

1. Inscreva-se no [portal Azure][azure-portal] como *administrador global.*
1. Procure e selecione **Azure Active Directory**.
1. A partir do menu de navegação no lado esquerdo da janela Azure Ative Directory, selecione **Métodos de Segurança > Autenticação > Política do método de autenticação (pré-visualização)**.

    [![Navegue e selecione a janela de política de método de autenticação (pré-visualização) no portal Azure.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. A partir da lista de métodos de autenticação disponíveis, selecione **mensagem de texto**.
1. Definir **Ativar** para *Sim*.

    ![Ativar a autenticação de texto na janela de política do método de autenticação](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Pode optar por ativar a autenticação baseada em SMS para *todos os utilizadores* ou *selecione utilizadores* e grupos. Na secção seguinte, ativa a autenticação por SMS para um utilizador de teste.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Atribuir o método de autenticação aos utilizadores e grupos

Com a autenticação baseada em SMS ativada no seu inquilino Azure AD, selecione agora alguns utilizadores ou grupos para poderem utilizar este método de autenticação.

1. Na janela da política de autenticação de mensagens de texto, desaponte **o Target** para *Selecionar os utilizadores*.
1. Escolha adicionar **utilizadores ou grupos,** em seguida, selecione um utilizador ou grupo de teste, como *o Utilizador Contoso* ou *os Utilizadores SMS contoso*.

    [![Escolha utilizadores ou grupos para ativar a autenticação baseada em SMS no portal Azure.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Quando tiver selecionado os seus utilizadores ou grupos, escolha **Selecione**e **guarde** a política de método de autenticação atualizada.

Cada utilizador que esteja ativado na política do método de autenticação de mensagens de texto deve ser licenciado, mesmo que não o utilize. Certifique-se de que possui as licenças adequadas para os utilizadores que ativa na política do método de autenticação, especialmente quando ativar a funcionalidade para grandes grupos de utilizadores.

## <a name="set-a-phone-number-for-user-accounts"></a>Definir um número de telefone para contas de utilizador

Os utilizadores estão agora habilitados para a autenticação por SMS, mas o seu número de telefone deve estar associado ao perfil do utilizador em Azure AD antes de poderem iniciar sessão. O utilizador pode [configurar este número de telefone](../user-help/sms-sign-in-explainer.md) no meu próprio *Perfil,* ou pode atribuir o número de telefone utilizando o portal Azure. Os números de telefone podem ser definidos por *administradores globais,* *administradores de autenticação*ou administradores *de autenticação privilegiada.*

Quando um número de telefone é definido para sinal de SMS, também está disponível para utilização com [autenticação multi-factor Azure][tutorial-azure-mfa] e [redefinição da palavra-passe de autosserviço][tutorial-sspr].

1. Procure e selecione **Azure Active Directory**.
1. A partir do menu de navegação no lado esquerdo da janela Azure Ative Directory, selecione **Utilizadores**.
1. Selecione o utilizador que ativou para a autenticação por SMS na secção anterior, como o *Utilizador Contoso,* selecione **métodos de autenticação**.
1. **Selecione + Adicione o método de autenticação,** em seguida, no menu de drop-down método *Escolha,* escolha número de **telefone**.

    Introduza o número de telefone do utilizador, incluindo o código do país, como *+1 xxxxxxxxx*. O portal Azure valida que o número de telefone está no formato correto.

    Em seguida, a partir do menu drop-down *do tipo Telefone,* selecione *Mobile*, *Alternate mobile*ou *Other,* conforme necessário.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Descreva um número de telefone para um utilizador no portal Azure para utilizar com autenticação baseada em SMS":::

    O número de telefone deve ser único no seu inquilino. Se tentar utilizar o mesmo número de telefone para vários utilizadores, é apresentada uma mensagem de erro.

1. Para aplicar o número de telefone na conta de um utilizador, selecione **Adicionar**.

Quando for forvisionado com sucesso, aparece uma marca de verificação para *o S-in SMS ativado*.

## <a name="test-sms-based-sign-in"></a>Teste de s-in baseado em SMS

Para testar a conta de utilizador que está agora ativada para o registo baseado em SMS, complete os seguintes passos:

1. Abra uma nova janela do navegador InPrivate ou Incógnito para [https://www.office.com][office]
1. No canto superior direito, **selecione Iniciar s-se-ão**.
1. Na introdução do pedido de inscrição, introduza o número de telefone associado ao utilizador na secção anterior e, em seguida, selecione **Seguinte**.

    ![Introduza um número de telefone na solicitação de inscrição para o utilizador de teste](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Uma mensagem de texto é enviada para o número de telefone fornecido. Para completar o processo de início de sação, introduza o código de 6 dígitos fornecido na mensagem de texto no pedido de inscrição.

    ![Introduza o código de confirmação enviado por mensagem de texto para o número de telefone do utilizador](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. O utilizador está agora inscrito sem a necessidade de fornecer um nome de utilizador ou senha.

## <a name="troubleshoot-sms-based-sign-in"></a>Sessão de Sessão por SMS com base em problemas

Os seguintes cenários e etapas de resolução de problemas podem ser utilizados se tiver problemas em ativar e utilizar o sinal de sessão baseado em SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Número de telefone já definido para uma conta de utilizador

Se um utilizador já se registou para autenticação multi-factor Azure e/ou redefinição de senha de autosserviço (SSPR), já tem um número de telefone associado à sua conta. Este número de telefone não está automaticamente disponível para utilização com o início de sing-in baseado em SMS.

Um utilizador que tenha um número de telefone já definido para a sua conta é apresentado um botão *para ativar para fazer o sessão por SMS na* sua página de **Perfil.** Selecione este botão e a conta está ativada para utilização com o início de súbs baseado em SMS e o registo anterior de Autenticação Multi-Factor Azure ou SSPR.

Para obter mais informações sobre a experiência do utilizador final, consulte a [experiência do utilizador de sing-in POR SMS para o número de telefone (pré-visualização)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Erro ao tentar definir um número de telefone na conta de um utilizador

Se receber um erro quando tentar definir um número de telefone para uma conta de utilizador no portal Azure, reveja as seguintes etapas de resolução de problemas:

1. Certifique-se de que está ativado para a pré-visualização de sindução baseada em SMS.
1. Confirme se a conta de utilizador está ativada na política do método de autenticação de *mensagens de texto.*
1. Certifique-se de que define o número de telefone com a formatação adequada, tal como validado no portal Azure (como *+1 4251234567).*
1. Certifique-se de que o número de telefone não é usado em outro lugar no seu inquilino.
1. Verifique se não há nenhum número de voz definido na conta. Se um número de voz estiver definido, apague e tente novamente o número de telefone.

## <a name="next-steps"></a>Passos seguintes

Para obter formas adicionais de iniciar sing no AD Azure sem uma senha, como a App autenticador microsoft ou as teclas de segurança FIDO2, consulte [opções de autenticação sem palavras-passe para Azure AD][concepts-passwordless].

Também pode utilizar a beta API do Microsoft Graph REST para [ativar][rest-enable] ou [desativar][rest-disable] o sôns in baseado em SMS.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
