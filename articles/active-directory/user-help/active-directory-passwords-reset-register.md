---
title: Registe informações de autenticação para redefinir a sua própria palavra-passe - Azure AD
description: Registe as informações do seu método de verificação para o reset da palavra-passe de autosserviço da AD Azure, para que possa redefinir a sua própria palavra-passe sem a ajuda do administrador.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062647"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registe as informações do seu método de verificação para redefinir a sua própria palavra-passe

Se esqueceu do seu trabalho ou palavra-passe escolar, nunca obteve uma senha da sua organização, ou se foi bloqueado fora da sua conta, pode usar as suas informações de segurança e o seu dispositivo móvel para redefinir o seu trabalho ou senha escolar.

O seu administrador deve ativar esta funcionalidade para que possa registar as suas informações e redefinir a sua própria palavra-passe. Se não vires a **opção esqueci-me** da minha senha, significa que o teu administrador não ligou a funcionalidade para a tua organização. Se acredita que está incorreto, contacte o seu balcão de ajuda para obter assistência.

>[!Important]
>Este artigo destina-se a utilizadores que pretendam utilizar o 'sign-up' para reset de palavra-passe de self-service. Isto significa que poderá redefinir o seu próprio trabalho ou alain@contoso.comsenha escolar (como, por exemplo), sem precisar da ajuda do seu administrador. Se for um administrador à procura de informações sobre como ativar o reset de palavra-passe de self-service para os seus colaboradores ou outros utilizadores, consulte o [reset de palavra-passe de autosserviço da Deploy Azure AD e outros artigos](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Configurar o seu método de verificação de redefinição de palavra-passe

1. Abra o navegador web no seu dispositivo e vá para a página de informações de [segurança.](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)

2. Dependendo da forma como o seu administrador criou a sua organização, uma ou mais das seguintes opções estarão disponíveis para que possa configurar como o seu método de verificação de segurança. Se várias opções estiverem disponíveis, recomendamos vivamente que utilize mais do que uma como método de verificação de segurança, caso um dos seus métodos fique indisponível.

    - **App de autenticação.** Opte por utilizar a aplicação Microsoft Authenticator ou outra aplicação autenticadora como método de verificação de segurança. Para obter mais informações sobre a configuração da aplicação, consulte [a configuração da aplicação Autenticadora Microsoft como o seu método de verificação](security-info-setup-auth-app.md).

    - **Mensagens de texto.** Opte por enviar mensagens de texto para o seu dispositivo móvel. Para obter mais informações sobre a configuração de mensagens de texto, consulte [Configurar as mensagens de texto como o seu método de verificação](security-info-setup-text-msg.md).

    - **Telefonemas.** Opte por receber uma chamada para o seu número de telefone registado. Para obter mais informações sobre a configuração de chamadas telefónicas, consulte [Configurar um número de telefone como método de verificação](security-info-setup-phone-number.md).

    - **Chave de segurança.** Opte por utilizar uma chave de segurança compatível com a Microsoft. Para mais informações, consulte [Configurar uma chave](security-info-setup-security-key.md)de segurança como o seu método de verificação .

    - **Endereço de e-mail.** Opte por utilizar um endereço de e-mail alternativo que possa ser usado sem necessitar da sua senha esquecida ou em falta. Isto funciona apenas para repor a palavra-passe, não como um método de verificação de segurança. Para mais informações sobre a configuração de um endereço de e-mail, consulte Configurar um endereço de [e-mail como método de verificação](security-info-setup-email.md).

    - **Questões de segurança.** Opte por configurar e responder a questões de segurança pré-definidas criadas pelo seu administrador. Isto funciona apenas para repor a palavra-passe, não como um método de verificação de segurança. Para obter mais informações sobre questões de segurança, consulte [Configurar questões](security-info-setup-questions.md)de segurança como o seu método de verificação .

3. Depois de selecionar e configurar os seus métodos, escolha **terminar** para completar o processo.

    > [!Note]
    > As informações adicionadas para o seu número de telefone ou endereço de e-mail não são partilhadas com o diretório global da sua organização. As únicas pessoas que podem ver esta informação são você e o seu administrador. Só você pode ver as respostas para as suas perguntas de segurança.

## <a name="common-problems-and-their-solutions"></a>Common problems and their solutions (Problemas comuns e suas soluções)

 Aqui estão alguns casos de erro comuns e as suas soluções:

| Mensagem de erro |  Possível solução |
| --- | --- | --- |
| Contacte o administrador.<br>Detetámos que a sua palavra-passe da conta de utilizador não é gerida pela Microsoft. Como resultado, não podemos redefinir automaticamente a sua palavra-passe.<br>Contacte o seu pessoal de TI para obter qualquer assistência adicional.| Se receber esta mensagem de erro depois de digitar o id do utilizador, significa que a sua organização gere internamente a sua palavra-passe e não quer que reponha a sua palavra-passe a partir do **link Can't access your account.** Para redefinir a sua palavra-passe nesta situação, deve contactar o balcão de ajuda da sua organização ou o seu administrador para obter ajuda. |
| A sua conta não está ativada para reposição de palavra-passe.<br>Lamentamos, mas o seu pessoal de TI não criou a sua conta para uso com este serviço.<br>Se quiser, podemos contactar um administrador da sua organização para redefinir a sua senha para si. | Se receber esta mensagem de erro depois de digitar o id do utilizador, significa que ou a sua organização não ligou a função de reset da palavra-passe ou não está autorizada a usá-la. Para redefinir a sua palavra-passe nesta situação, deve selecionar o **link de 'Contato's' de um administrador.** Depois de clicar no link, um e-mail é enviado para o balcão de ajuda ou administrador da sua organização, informando-os de que pretende redefinir a sua palavra-passe. |
| Não pudemos verificar a sua conta.<br>Se quiser, podemos contactar um administrador da sua organização para redefinir a sua senha para si. | Se receber esta mensagem de erro depois de digitar o seu ID do utilizador, significa que a sua organização ligou o reset da palavra-passe e que pode utilizá-la, mas que não se registou para o serviço. Nesta situação, deve contactar o balcão de ajuda ou administrador da sua organização para redefinir a sua palavra-passe. Para obter informações sobre o registo para reset de palavra-passe depois de estar de volta ao seu dispositivo, consulte o processo acima neste artigo. |

## <a name="next-steps"></a>Passos seguintes

- [Altere a sua palavra-passe utilizando o reset da palavra-passe self-service](active-directory-passwords-update-your-own-password.md)

- [Página de informações de segurança](https://mysignins.microsoft.com/security-info)

- [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)

- [Quando não consegue iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
