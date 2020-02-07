---
title: Configurar um endereço de email como seu método de verificação-Azure AD
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade usando um endereço de email como seu método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 9488b96dda15b0ffb794f83826dc299a5f397b77
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064007"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Configurar um endereço de email como seu método de verificação

Você pode seguir estas etapas para adicionar o método de redefinição de senha. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

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
| Questões de segurança | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Configurar seu endereço de email na página de informações de segurança

Dependendo das configurações da sua organização, você poderá usar seu endereço de email como um dos seus métodos de informações de segurança.

>[!Note]
>É recomendável usar um endereço de email que não exija sua senha de rede para acessar o. Se você não vir a opção de email, é possível que sua organização não permita que você use um email para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

### <a name="to-set-up-your-email-address"></a>Para configurar seu endereço de email

1. Inscreva-se na sua conta de trabalho ou escola e depois vá à sua página de https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **Email** da lista de drop-down e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com email selecionado](media/security-info/securityinfo-myprofile-addemail.png)

4. Na página de **E-mail,** digite o seu endereço de e-mail (por exemplo, alain@gmail.com), e depois selecione **Next**.

    ![Adicionar número de telefone e escolher chamadas telefônicas](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Este endereço de email não pode ser seu email corporativo ou de estudante.

5. Digite o código enviado para o seu endereço de e-mail especificado e, em seguida, selecione **Next**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-emailcode.png)

    Suas informações de segurança são atualizadas e você pode usar seu endereço de email para verificar sua identidade ao usar a redefinição de senha.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Exclua seu endereço de email de seus métodos de informações de segurança

Se já não quiser utilizar o seu endereço de e-mail como um método de informação de segurança, pode removê-lo da página de informações de **Segurança.**

>[!Important]
>Se você excluir seu endereço de email por engano, não há como desfazê-lo. Terá de adicionar o método novamente, seguindo os passos na secção de [endereço de e-mail](#set-up-your-email-address-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-email-address"></a>Para excluir seu endereço de email

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da opção **Email.**

    ![Link para excluir o método de telefone das informações de segurança](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Selecione **Sim** a partir da caixa de confirmação para eliminar a conta **de e-mail.** Após a eliminação da conta de e-mail, é removida das suas informações de segurança e desaparece da página de informações de **Segurança.**

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicação autenticadora.** Baixe e use um aplicativo autenticador para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como configurar e utilizar a aplicação Microsoft Authenticator, consulte [Configurar informações de segurança para utilizar uma aplicação autenticadora](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um texto de um código que você usará para a verificação em duas etapas ou a redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte Configurar informações de segurança para utilizar mensagens de [texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações](security-info-setup-phone-number.md)de segurança para utilizar chamadas telefónicas .

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Questões de segurança.** Responda a algumas perguntas de segurança criadas pelo administrador da sua organização. Essa opção só está disponível para a redefinição de senha e não para a verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança configurar para utilizar](security-info-setup-questions.md) o artigo de questões de segurança.

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
