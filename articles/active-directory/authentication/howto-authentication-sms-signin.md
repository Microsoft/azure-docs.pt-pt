---
title: SMS-based user sign in for Azure Ative Directory
description: Saiba como configurar e permitir que os utilizadores inscrevam-se no Azure Ative Directory utilizando SMS (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770556"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Configure e ative os utilizadores para autenticação baseada em SMS utilizando o Diretório Ativo do Azure (pré-visualização)

Para reduzir a complexidade e riscos de segurança para os utilizadores iniciarem a sua participação em aplicações e serviços, o Azure Ative Directory (Azure AD) oferece múltiplas opções de autenticação. A autenticação baseada em SMS, atualmente em pré-visualização, permite que os utilizadores inscrevam-se sem necessidade de fornecer, ou mesmo saber, o seu nome de utilizador e senha. Depois de a sua conta ser criada por um administrador de identidade, podem introduzir o seu número de telefone no pedido de entrada e fornecer um código de autenticação que lhes é enviado por mensagem de texto. Este método de autenticação simplifica o acesso a aplicações e serviços, especialmente para trabalhadores da linha da frente.

Este artigo mostra-lhe como ativar a autenticação baseada em SMS para utilizadores ou grupos selecionados em Azure AD.

|     |
| --- |
| A autenticação baseada em SMS para utilizadores é uma funcionalidade de pré-visualização pública do Diretório Ativo Azure. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure|
|     |

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Diretório associado à sua subscrição.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Você precisa de privilégios *de administrador global* no seu inquilino Azure AD para permitir a autenticação baseada em SMS.
* Cada utilizador habilitado na política do método de autenticação de mensagens de texto deve ser licenciado, mesmo que não o utilize. Cada utilizador ativado deve ter uma das seguintes licenças Azure AD ou Microsoft 365:
    * [Azure AD Premium P1 ou P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 ou F3][m365-firstline-workers-licensing]
    * [Mobilidade Empresarial + Segurança (EMS) E3 ou E5][ems-licensing] ou [Microsoft 365 (M365) E3 ou E5][m365-licensing]

## <a name="limitations"></a>Limitações

Durante a pré-visualização pública da autenticação baseada em SMS, aplicam-se as seguintes limitações:

* A autenticação baseada em SMS não é atualmente compatível com a autenticação Azure Multi-Factor.
* Com exceção das Equipas, a autenticação baseada em SMS não é atualmente compatível com as aplicações do Escritório Nativo.
* A autenticação baseada em SMS não é recomendada para contas B2B.
* Os utilizadores federados não autenticam no inquilino da casa. Só autenticam na nuvem.

## <a name="enable-the-sms-based-authentication-method"></a>Ativar o método de autenticação baseado em SMS

Existem três passos principais para ativar e utilizar a autenticação baseada em SMS na sua organização:

* Ativar a política do método de autenticação.
* Selecione utilizadores ou grupos que possam utilizar o método de autenticação baseado em SMS.
* Atribua um número de telefone para cada conta de utilizador.
    * Este número de telefone pode ser atribuído no portal Azure (que é mostrado neste artigo), e em *My Staff* ou *My Profile*.

Primeiro, vamos permitir a autenticação baseada em SMS para o seu inquilino Azure AD.

1. Inscreva-se no [portal Azure][azure-portal] como *administrador global.*
1. Procure e selecione **Azure Active Directory**.
1. A partir do menu de navegação no lado esquerdo da janela do Diretório Ativo Azure, selecione métodos de segurança > autenticação > política do método de **autenticação (pré-visualização)**.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. A partir da lista de métodos de autenticação disponíveis, selecione **mensagem de texto**.
1. Definir **ativar** *para sim*.

    ![Ativar a autenticação de texto na janela de política do método de autenticação](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Pode optar por ativar a autenticação baseada em SMS para *todos os utilizadores* ou selecionar *utilizadores* e grupos. Na secção seguinte, ativa a autenticação baseada em SMS para um utilizador de teste.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Atribuir o método de autenticação aos utilizadores e grupos

Com a autenticação baseada em SMS ativada no seu inquilino Azure AD, selecione agora alguns utilizadores ou grupos para poderem utilizar este método de autenticação.

1. Na janela da política de autenticação de mensagens de texto, desloque **o Target** para Selecionar *os utilizadores*.
1. Escolha **adicionar utilizadores ou grupos,** em seguida, selecione um utilizador ou grupo de teste, como utilizadores de *Contoso* ou *Contoso SMS*.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Quando tiver selecionado os seus utilizadores ou grupos, escolha **Select**e **guarde** a política atualizada do método de autenticação.

Cada utilizador habilitado na política do método de autenticação de mensagens de texto deve ser licenciado, mesmo que não o utilize. Certifique-se de que possui as licenças adequadas para os utilizadores que ativa na política do método de autenticação, especialmente quando ativa a funcionalidade para grandes grupos de utilizadores.

## <a name="set-a-phone-number-for-user-accounts"></a>Definir um número de telefone para as contas do utilizador

Os utilizadores estão agora habilitados para a autenticação baseada em SMS, mas o seu número de telefone deve estar associado ao perfil de utilizador em Azure AD antes de poder iniciar o seu início. O utilizador pode [configurar este número](../user-help/sms-sign-in-explainer.md) de telefone em si mesmo no Meu *Perfil,* ou pode atribuir o número de telefone através do portal Azure. Os números de telefone podem ser definidos por *administradores globais,* administradores de *autenticação*ou administradores de *autenticação privilegiados.*

Quando um número de telefone é definido para sinal sms, também está disponível para utilização com [autenticação de multi-factor sms Azure][tutorial-azure-mfa] e [reset de senha de autosserviço][tutorial-sspr].

1. Procure e selecione **Azure Active Directory**.
1. A partir do menu de navegação no lado esquerdo da janela do Diretório Ativo Azure, selecione **Utilizadores**.
1. Selecione o utilizador que ativou para autenticação baseada em SMS na secção anterior, como *o Utilizador Contoso,* e, em seguida, selecione métodos de **autenticação**.
1. Introduza o número de telefone do utilizador, incluindo o código do país, como *+1 xxxxxxxxx*. O portal Azure valida que o número de telefone está no formato correto.

    ![Detete um número de telefone para um utilizador no portal Azure utilizar com autenticação baseada em SMS](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    O número de telefone deve ser único no seu inquilino. Se tentar utilizar o mesmo número de telefone para vários utilizadores, é mostrada uma mensagem de erro.

1. Para aplicar o número de telefone na conta de um utilizador, selecione **Guardar**.

Quando for aprovisionado com sucesso, aparece uma marca de verificação para o *SMS Sign-in ativado*.

## <a name="test-sms-based-sign-in"></a>Teste de inscrição baseada em SMS

Para testar a conta de utilizador que está agora ativada para o sms de inscrição, complete os seguintes passos:

1. Abra uma nova janela do navegador InPrivate ou Incógnito para[https://www.office.com][office]
1. No canto superior direito, selecione **Iniciar sessão**.
1. No pedido de entrada, introduza o número de telefone associado ao utilizador na secção anterior e, em seguida, selecione **Seguinte**.

    ![Introduza um número de telefone no pedido de entrada para o utilizador do teste](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Uma mensagem de texto é enviada para o número de telefone fornecido. Para completar o processo de início de sessão, introduza o código de 6 dígitos fornecido na mensagem de texto no pedido de entrada.

    ![Introduza o código de confirmação enviado por mensagem de texto para o número de telefone do utilizador](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. O utilizador está agora inscrito sem a necessidade de fornecer um nome de utilizador ou senha.

## <a name="troubleshoot-sms-based-sign-in"></a>SmS baseado em sms

Os seguintes cenários e passos de resolução de problemas podem ser utilizados se tiver problemas em permitir e utilizar o sessão baseado em SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Número de telefone já definido para uma conta de utilizador

Se um utilizador já se registou para autenticação de multi-fatores E/ou reset de senha de autosserviço (SSPR), já tem um número de telefone associado à sua conta. Este número de telefone não está automaticamente disponível para utilização com sms de sms.

Um utilizador que tenha um número de telefone já definido para a sua conta é apresentado um botão para *ativar o sms de sms* na sua página **My Profile.** Selecione este botão e a conta está ativada para utilização com sms baseados em SMS e a autenticação anterior de multi-factores Azure ou registo SSPR.

Para obter mais informações sobre a experiência do utilizador final, consulte a [experiência do utilizador de sms para número de telefone (pré-visualização)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Erro ao tentar definir um número de telefone na conta de um utilizador

Se receber um erro quando tentar definir um número de telefone para uma conta de utilizador no portal Azure, reveja os seguintes passos de resolução de problemas:

1. Certifique-se de que está ativado para a pré-visualização de sms.
1. Confirme que a conta de utilizador está ativada na política do método de autenticação de *mensagens de texto.*
1. Certifique-se de que define o número de telefone com a formatação adequada, conforme validado no portal Azure (como *+1 4251234567*).
1. Certifique-se de que o número de telefone não é usado em outro lugar do seu inquilino.
1. Verifique se não há nenhum número de voz definido na conta. Se um número de voz estiver definido, apague e tente voltar a marcar o número de telefone.

## <a name="next-steps"></a>Passos seguintes

Para obter acesso adicional ao Azure AD sem senha, como a Aplicação autenticadora Microsoft ou as teclas de segurança FIDO2, consulte opções de [autenticação sem palavra-passe para a AD Azure][concepts-passwordless].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
