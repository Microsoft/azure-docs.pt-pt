---
title: Registrar informações de autenticação para redefinir sua própria senha-Azure AD
description: Registre suas informações de método de verificação para redefinição de senha de autoatendimento do Azure AD, para que você possa redefinir sua própria senha sem a ajuda do administrador.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062647"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registre suas informações do método de verificação para redefinir sua própria senha

Se você esqueceu sua senha corporativa ou de estudante, nunca recebeu uma senha da sua organização ou foi bloqueada por sua conta, você pode usar suas informações de segurança e seu dispositivo móvel para redefinir sua senha corporativa ou de estudante.

O administrador deve ativar esse recurso para que você possa registrar suas informações e redefinir sua própria senha. Se não vires a **opção esqueci-me** da minha senha, significa que o teu administrador não ligou a funcionalidade para a tua organização. Se você acredita que isso esteja incorreto, entre em contato com o suporte técnico para obter ajuda.

>[!Important]
>Este artigo destina-se a usuários que estão tentando usar a inscrição para redefinição de senha de autoatendimento. Isto significa que poderá redefinir o seu próprio trabalho ou senha escolar (como, alain@contoso.com), sem precisar da ajuda do seu administrador. Se for um administrador à procura de informações sobre como ativar o reset de palavra-passe de self-service para os seus colaboradores ou outros utilizadores, consulte o [reset de palavra-passe de autosserviço da Deploy Azure AD e outros artigos](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Configurar o método de verificação de redefinição de senha

1. Abra o navegador web no seu dispositivo e vá para a página de informações de [segurança.](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)

2. Dependendo de como o administrador configurou sua organização, uma ou mais das opções a seguir estarão disponíveis para você configurar como seu método de verificação de segurança. Se várias opções estiverem disponíveis, é altamente recomendável que você use mais de uma como seu método de verificação de segurança, caso um dos seus métodos se torne indisponível.

    - **App de autenticação.** Escolha usar o aplicativo Microsoft Authenticator ou outro aplicativo autenticador como seu método de verificação de segurança. Para obter mais informações sobre a configuração da aplicação, consulte [a configuração da aplicação Autenticadora Microsoft como o seu método de verificação](security-info-setup-auth-app.md).

    - **Mensagens de texto.** Escolha enviar mensagens de texto para seu dispositivo móvel. Para obter mais informações sobre a configuração de mensagens de texto, consulte [Configurar as mensagens de texto como o seu método de verificação](security-info-setup-text-msg.md).

    - **Telefonemas.** Opte por obter uma chamada telefônica para o número de telefone registrado. Para obter mais informações sobre a configuração de chamadas telefónicas, consulte [Configurar um número de telefone como método de verificação](security-info-setup-phone-number.md).

    - **Chave de segurança.** Escolha usar uma chave de segurança compatível com a Microsoft. Para mais informações, consulte [Configurar uma chave](security-info-setup-security-key.md)de segurança como o seu método de verificação .

    - **Endereço de e-mail.** Escolha usar um endereço de email alternativo que possa ser usado sem a necessidade de sua senha esquecida ou ausente. Isso só funciona para redefinição de senha, não como um método de verificação de segurança. Para mais informações sobre a configuração de um endereço de e-mail, consulte Configurar um endereço de [e-mail como método de verificação](security-info-setup-email.md).

    - **Questões de segurança.** Escolha configurar e responder a perguntas de segurança predefinidas configuradas pelo administrador. Isso só funciona para redefinição de senha, não como um método de verificação de segurança. Para obter mais informações sobre questões de segurança, consulte [Configurar questões](security-info-setup-questions.md)de segurança como o seu método de verificação .

3. Depois de selecionar e configurar os seus métodos, escolha **terminar** para completar o processo.

    > [!Note]
    > As informações adicionadas para seu número de telefone ou endereço de email não são compartilhadas com o diretório global da sua organização. As únicas pessoas que podem ver essas informações são você e seu administrador. Somente você pode ver as respostas para suas perguntas de segurança.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e suas soluções

 Aqui estão alguns casos de erro comuns e suas soluções:

| Mensagem de erro |  Solução possível |
| --- | --- | --- |
| Contacte o administrador.<br>Detectamos que a senha da sua conta de usuário não é gerenciada pela Microsoft. Como resultado, não é possível redefinir sua senha automaticamente.<br>Entre em contato com a equipe de ti para obter mais assistência.| Se receber esta mensagem de erro depois de digitar o id do utilizador, significa que a sua organização gere internamente a sua palavra-passe e não quer que reponha a sua palavra-passe a partir do **link Can't access your account.** Para redefinir sua senha nessa situação, você deve entrar em contato com o suporte técnico da sua organização ou com o administrador para obter ajuda. |
| Sua conta não está habilitada para redefinição de senha.<br>Lamentamos, mas sua equipe de ti não configurou sua conta para uso com este serviço.<br>Se desejar, poderemos entrar em contato com um administrador em sua organização para redefinir sua senha para você. | Se você receber essa mensagem de erro depois de digitar sua ID de usuário, significa que a sua organização não ativou o recurso de redefinição de senha ou você não tem permissão para usá-la. Para redefinir a sua palavra-passe nesta situação, deve selecionar o **link de 'Contato's' de um administrador.** Depois que você clicar no link, um email será enviado para o administrador ou assistência técnica de sua organização, informando-o que você deseja redefinir sua senha. |
| Não foi possível verificar sua conta.<br>Se desejar, poderemos entrar em contato com um administrador em sua organização para redefinir sua senha para você. | Se você receber essa mensagem de erro depois de digitar sua ID de usuário, significa que sua organização ativou a redefinição de senha e que você pode usá-la, mas que você ainda não registrou para o serviço. Nessa situação, você deve contatar o administrador ou o suporte técnico da sua organização para redefinir sua senha. Para obter informações sobre como se registrar para a redefinição de senha depois de voltar ao seu dispositivo, consulte o processo acima neste artigo. |

## <a name="next-steps"></a>Passos seguintes

- [Altere a sua palavra-passe utilizando o reset da palavra-passe self-service](active-directory-passwords-update-your-own-password.md)

- [Página de informações de segurança](https://mysignins.microsoft.com/security-info)

- [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)

- [Quando não pode iniciar sessão na sua conta microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
