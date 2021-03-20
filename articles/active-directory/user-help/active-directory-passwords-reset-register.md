---
title: Registar informações de autenticação para redefinir a sua própria senha - Azure AD
description: Registe as informações do seu método de verificação para reiniciar a palavra-passe de autosserviço Azure AD, para que possa redefinir a sua própria palavra-passe sem ajuda do administrador.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799031"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registe a informação do seu método de verificação para redefinir a sua própria senha

Se esqueceu do seu trabalho ou palavra-passe escolar, nunca obteve uma palavra-passe da sua organização, ou foi bloqueado fora da sua conta, pode usar a sua informação de segurança e o seu dispositivo móvel para redefinir o seu trabalho ou senha escolar.

O seu administrador deve ligar esta funcionalidade para poder registar as suas informações e redefinir a sua própria palavra-passe. Se não vir a opção **Esqueci da minha palavra-passe,** significa que o seu administrador não ligou a funcionalidade para a sua organização. Se você acredita que isto está errado, contacte o seu balcão de ajuda para obter assistência.

>[!Important]
>Este artigo destina-se a utilizadores que tentem utilizar o pedido de reposição da palavra-passe de autosserviço. Isto significa que poderá redefinir o seu próprio trabalho ou senha escolar alain@contoso.com (como), sem precisar da ajuda do seu administrador. Se for um administrador à procura de informações sobre como ligar a palavra-passe de autosserviço para os seus colaboradores ou outros utilizadores, consulte o [reset da palavra-passe de autosserviço do Azure Ad deploy e outros artigos](../authentication/howto-sspr-deployment.md).

## <a name="set-up-your-password-reset-verification-method"></a>Configurar o seu método de verificação de reset de palavra-passe

1. Abra o navegador web no seu dispositivo e vá para a [página de informações de segurança](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Dependendo da forma como o seu administrador criou a sua organização, uma ou mais das seguintes opções estarão disponíveis para configurar como o seu método de verificação de segurança. Se várias opções estiverem disponíveis, recomendamos vivamente que utilize mais do que uma como método de verificação de segurança, caso um dos seus métodos fique indisponível.

    - **App de autenticação.** Opte por utilizar a aplicação Microsoft Authenticator ou outra aplicação autenticadora como método de verificação de segurança. Para obter mais informações sobre a configuração da aplicação, consulte [configurar a aplicação Microsoft Authenticator como o seu método de verificação.](security-info-setup-auth-app.md)

    - **Mensagens de texto.** Opte por enviar mensagens de texto para o seu dispositivo móvel. Para obter mais informações sobre a configuração de mensagens de texto, consulte [Configurar mensagens de texto como o seu método de verificação](security-info-setup-text-msg.md).

    - **Telefonemas.** Opte por receber uma chamada para o seu número de telefone registado. Para obter mais informações sobre a configuração de chamadas telefónicas, consulte [configurar um número de telefone como método de verificação.](security-info-setup-phone-number.md)

    - **Chave de segurança.** Opte por utilizar uma chave de segurança compatível com a Microsoft. Para obter mais informações, consulte [Configurar uma chave de segurança como método de verificação.](security-info-setup-security-key.md)

    - **Endereço de e-mail.** Opte por utilizar um endereço de e-mail alternativo que possa ser utilizado sem necessitar da sua senha esquecida ou em falta. Isto funciona apenas para reset de palavra-passe, não como um método de verificação de segurança. Para obter mais informações sobre a configuração de um endereço de e-mail, consulte [configurar um endereço de e-mail como o seu método de verificação.](security-info-setup-email.md)

    - **Perguntas de segurança.** Opte por configurar e responder a questões de segurança pré-definidas criadas pelo seu administrador. Isto funciona apenas para reset de palavra-passe, não como um método de verificação de segurança. Para obter mais informações sobre questões de segurança, consulte [Configurar questões de segurança como o seu método de verificação.](security-info-setup-questions.md)

3. Depois de selecionar e configurar os seus métodos, escolha **Finish** para concluir o processo.

    > [!Note]
    > As informações adicionadas para o seu número de telefone ou endereço de e-mail não são partilhadas com o diretório global da sua organização. As únicas pessoas que podem ver esta informação são você e o seu administrador. Só você pode ver as respostas para as suas perguntas de segurança.

## <a name="common-problems-and-their-solutions"></a>Common problems and their solutions (Problemas comuns e suas soluções)

 Eis alguns casos de erro comuns e as suas soluções:

| Mensagem de erro |  Solução possível |
| --- | --- | --- |
| Contacte o seu administrador.<br>Detetámos que a palavra-passe da sua conta de utilizador não é gerida pela Microsoft. Como resultado, não conseguimos redefinir automaticamente a sua palavra-passe.<br>Contacte o seu pessoal de TI para mais assistência.| Se receber esta mensagem de erro depois de escrever o seu ID do Utilizador, significa que a sua organização gere internamente a sua palavra-passe e não quer que reponha a sua palavra-passe a partir do link **'Não conseguir aceder'** ao link da conta. Para redefinir a sua palavra-passe nesta situação, deve contactar o balcão de ajuda da sua organização ou o seu administrador para obter ajuda. |
| A sua conta não está ativada para reiniciar a palavra-passe.<br>Lamentamos, mas a sua equipa de TI não criou a sua conta para uso neste serviço.<br>Se quiser, podemos contactar um administrador da sua organização para redefinir a sua senha. | Se receber esta mensagem de erro depois de escrever o seu ID do Utilizador, significa que ou a sua organização não ligou a funcionalidade de reset da palavra-passe ou não está autorizada a usá-la. Para redefinir a sua palavra-passe nesta situação, tem de selecionar o link **de contacto de um administrador.** Depois de clicar no link, é enviado um e-mail para o balcão de ajuda ou administrador da sua organização, informando-os de que pretende redefinir a sua palavra-passe. |
| Não pudemos verificar a sua conta.<br>Se quiser, podemos contactar um administrador da sua organização para redefinir a sua senha. | Se receber esta mensagem de erro depois de escrever o seu ID do Utilizador, significa que a sua organização ligou a palavra-passe e pode usá-la, mas que não se registou para o serviço. Nesta situação, deve contactar o balcão de ajuda ou administrador da sua organização para redefinir a sua palavra-passe. Para obter informações sobre o reposição da palavra-passe após o seu dispositivo, consulte o processo acima neste artigo. |

## <a name="next-steps"></a>Passos seguintes

- [Altere a sua palavra-passe utilizando o reset da palavra-passe de autosserviço](active-directory-passwords-update-your-own-password.md)

- [Página de informações de segurança](https://mysignins.microsoft.com/security-info)

- [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)

- [Quando não consegue iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)