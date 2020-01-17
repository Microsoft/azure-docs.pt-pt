---
title: Configurar um endereço de email como seu método de verificação-Azure AD
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade usando um endereço de email como seu método de verificação.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82bbdd7f209b2ce22225628416a07524c62f1484
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156170"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Configurar um endereço de email como seu método de verificação

Você pode seguir estas etapas para adicionar o método de redefinição de senha. Depois de configurar isso na primeira vez, você pode retornar à página informações de **segurança** para adicionar, atualizar ou excluir suas informações de segurança.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se você não vir uma opção de email, é possível que sua organização não permita que você use essa opção para seu método de redefinição de senha. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais ajuda.

## <a name="security-vs-password-reset-verification"></a>Segurança vs. verificação de redefinição de senha

Os métodos de informações de segurança são usados para a verificação de segurança de dois fatores e para a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicativo autenticador | Verificação de dois fatores e autenticação de redefinição de senha. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chamadas telefônicas | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de senha. |
| Conta de email | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |
| Perguntas de segurança | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Configurar seu endereço de email na página de informações de segurança

Dependendo das configurações da sua organização, você poderá usar seu endereço de email como um dos seus métodos de informações de segurança.

>[!Note]
>É recomendável usar um endereço de email que não exija sua senha de rede para acessar o. Se você não vir a opção de email, é possível que sua organização não permita que você use um email para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

### <a name="to-set-up-your-email-address"></a>Para configurar seu endereço de email

1. Entre em sua conta corporativa ou de estudante e, em seguida, vá para a página https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou no link no bloco **informações de segurança** e, em seguida, selecione **Adicionar método** na página **informações de segurança** .

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar um método** , selecione **email** na lista suspensa e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com email selecionado](media/security-info/securityinfo-myprofile-addemail.png)

4. Na página **email** , digite seu endereço de email (por exemplo, alain@gmail.com) e, em seguida, selecione **Avançar**.

    ![Adicionar número de telefone e escolher chamadas telefônicas](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Este endereço de email não pode ser seu email corporativo ou de estudante.

5. Digite o código enviado para o endereço de email especificado e, em seguida, selecione **Avançar**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-emailcode.png)

    Suas informações de segurança são atualizadas e você pode usar seu endereço de email para verificar sua identidade ao usar a redefinição de senha.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Exclua seu endereço de email de seus métodos de informações de segurança

Se você não quiser mais usar seu endereço de email como um método de informações de segurança, poderá removê-lo da página **informações de segurança** .

>[!Important]
>Se você excluir seu endereço de email por engano, não há como desfazê-lo. Você precisará adicionar o método novamente, seguindo as etapas na seção [configurar seu endereço de email](#set-up-your-email-address-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-email-address"></a>Para excluir seu endereço de email

1. Na página **informações de segurança** , selecione o link **excluir** ao lado da opção **email** .

    ![Link para excluir o método de telefone das informações de segurança](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Selecione **Sim** na caixa de confirmação para excluir a conta de **email** . Depois que a conta de email for excluída, ela será removida das informações de segurança e desaparecerá da página **informações de segurança** .

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Baixe e use um aplicativo autenticador para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um texto de um código que você usará para a verificação em duas etapas ou a redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada telefônica de dispositivo ou trabalho móvel.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma chave de segurança, consulte [configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Perguntas de segurança.** Responda a algumas perguntas de segurança criadas pelo administrador da sua organização. Essa opção só está disponível para a redefinição de senha e não para a verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md) .

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefina sua senha se você a tiver perdido ou esquecido, no [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md) .

- Obtenha dicas de solução de problemas e ajuda para problemas de conexão no artigo [não é possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
