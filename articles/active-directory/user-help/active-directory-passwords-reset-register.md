---
title: Registrar informações de autenticação para redefinir sua própria senha-Azure AD
description: Registre suas informações de método de verificação para redefinição de senha de autoatendimento do Azure AD, para que você possa redefinir sua própria senha sem a ajuda do administrador.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: lizross
ms.openlocfilehash: d27b0b7aadf5cffc2a362843ab5373ee0dc5b170
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261845"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registre suas informações do método de verificação para redefinir sua própria senha

Se você esqueceu sua senha corporativa ou de estudante, nunca recebeu uma senha da sua organização ou foi bloqueada por sua conta, você pode usar suas informações de segurança e seu dispositivo móvel para redefinir sua senha corporativa ou de estudante.

O administrador deve ativar esse recurso para que você possa registrar suas informações e redefinir sua própria senha. Se você não vir a opção **esqueceu minha senha** , isso significa que o administrador não ativou o recurso para sua organização. Se você acredita que isso esteja incorreto, entre em contato com o suporte técnico para obter ajuda.

>[!Important]
>Este artigo destina-se a usuários que estão tentando usar a inscrição para redefinição de senha de autoatendimento. Isso significa que você poderá redefinir sua própria senha corporativa ou de estudante (como, alain@contoso.com), sem a necessidade de ajuda do administrador. Se você for um administrador que procura informações sobre como ativar a redefinição de senha de autoatendimento para seus funcionários ou outros usuários, consulte [implantar a redefinição de senha de autoatendimento do Azure AD e outros artigos](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Configurar o método de verificação de redefinição de senha

1. Abra o navegador da Web em seu dispositivo e vá para a [página informações de segurança](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Dependendo de como o administrador configurou sua organização, uma ou mais das opções a seguir estarão disponíveis para você configurar como seu método de verificação de segurança. Se várias opções estiverem disponíveis, é altamente recomendável que você use mais de uma como seu método de verificação de segurança, caso um dos seus métodos se torne indisponível.

    - **Aplicativo de autenticação.** Escolha usar o aplicativo Microsoft Authenticator ou outro aplicativo autenticador como seu método de verificação de segurança. Para obter mais informações sobre como configurar o aplicativo, consulte [Configurar o aplicativo Microsoft Authenticator como seu método de verificação](security-info-setup-auth-app.md).

    - **Mensagens de texto.** Escolha enviar mensagens de texto para seu dispositivo móvel. Para obter mais informações sobre como configurar mensagens de texto, consulte [Configurar mensagens de texto como seu método de verificação](security-info-setup-text-msg.md).

    - **Chamadas telefônicas.** Opte por obter uma chamada telefônica para o número de telefone registrado. Para obter mais informações sobre como configurar chamadas telefônicas, consulte [configurar um número de telefone como seu método de verificação](security-info-setup-phone-number.md).

    - **Chave de segurança.** Escolha usar uma chave de segurança compatível com a Microsoft. Para obter mais informações, consulte [Configurar uma chave de segurança como seu método de verificação](security-info-setup-security-key.md).

    - **Endereço de email.** Escolha usar um endereço de email alternativo que possa ser usado sem a necessidade de sua senha esquecida ou ausente. Isso só funciona para redefinição de senha, não como um método de verificação de segurança. Para obter mais informações sobre como configurar um endereço de email, consulte [configurar um endereço de email como seu método de verificação](security-info-setup-email.md).

    - **Perguntas de segurança.** Escolha configurar e responder a perguntas de segurança predefinidas configuradas pelo administrador. Isso só funciona para redefinição de senha, não como um método de verificação de segurança. Para obter mais informações sobre perguntas de segurança, consulte [Configurar perguntas de segurança como seu método de verificação](security-info-setup-questions.md).

3. Depois de selecionar e configurar seus métodos, escolha **concluir** para concluir o processo.

    > [!Note]
    > As informações adicionadas para seu número de telefone ou endereço de email não são compartilhadas com o diretório global da sua organização. As únicas pessoas que podem ver essas informações são você e seu administrador. Somente você pode ver as respostas para suas perguntas de segurança.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Mensagem de erro |  Solução possível |
| --- | --- | --- |
| Contacte o administrador.<br>Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Como resultado, não é possível redefinir sua senha automaticamente.<br>Entre em contato com a equipe de ti para obter mais assistência.| Se você receber essa mensagem de erro depois de digitar sua ID de usuário, isso significa que sua organização gerencia internamente sua senha e não quer que você Redefina sua senha no link **não consegue acessar sua conta** . Para redefinir sua senha nessa situação, você deve entrar em contato com o suporte técnico da sua organização ou com o administrador para obter ajuda. |
| Sua conta não está habilitada para redefinição de senha.<br>Lamentamos, mas sua equipe de ti não configurou sua conta para uso com este serviço.<br>Se desejar, poderemos entrar em contato com um administrador em sua organização para redefinir sua senha para você. | Se você receber essa mensagem de erro depois de digitar sua ID de usuário, significa que a sua organização não ativou o recurso de redefinição de senha ou você não tem permissão para usá-la. Para redefinir sua senha nessa situação, você deve selecionar o link **entre em contato com um administrador** . Depois que você clicar no link, um email será enviado para o administrador ou assistência técnica de sua organização, informando-o que você deseja redefinir sua senha. |
| Não foi possível verificar sua conta.<br>Se desejar, poderemos entrar em contato com um administrador em sua organização para redefinir sua senha para você. | Se você receber essa mensagem de erro depois de digitar sua ID de usuário, significa que sua organização ativou a redefinição de senha e que você pode usá-la, mas que você ainda não registrou para o serviço. Nessa situação, você deve contatar o administrador ou o suporte técnico da sua organização para redefinir sua senha. Para obter informações sobre como se registrar para a redefinição de senha depois de voltar ao seu dispositivo, consulte o processo acima neste artigo. |

## <a name="next-steps"></a>Passos seguintes

- [Alterar sua senha usando a redefinição de senha de autoatendimento](active-directory-passwords-update-your-own-password.md)

- [Página de informações de segurança](https://mysignins.microsoft.com/security-info)

- [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)

- [Quando não for possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
