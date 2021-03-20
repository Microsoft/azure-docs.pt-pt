---
title: Redefinir a sua palavra-passe utilizando informações de segurança - Azure Ative Directory | Microsoft Docs
description: Como redefinir a sua própria palavra-passe se a esquecer, utilizando a sua informação de segurança e verificação em duas etapas.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/10/2020
ms.author: curtand
ms.openlocfilehash: c47d573dab3e72a956f6f0b9d09a1dafadf0225c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91827776"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Reinicie o seu trabalho ou senha escolar usando informações de segurança

Se esqueceu do seu trabalho ou palavra-passe escolar, nunca obteve uma palavra-passe da sua organização, ou foi bloqueado fora da sua conta, pode usar a sua informação de segurança e o seu dispositivo móvel para redefinir o seu trabalho ou senha escolar. O seu administrador deve ligar esta funcionalidade para que possa configurar as suas informações e [redefinir a sua própria senha.](./active-directory-passwords-reset-register.md)

Se conhece a sua palavra-passe e pretende alterá-la, consulte as secções de [passos](#how-to-change-your-password) de senha deste artigo.

>[!Important]
>Este artigo destina-se a utilizadores que tentem utilizar uma palavra-passe de trabalho ou conta escolar esquecida ou desconhecida. Se for um administrador à procura de informações sobre como ligar a palavra-passe de autosserviço para os seus colaboradores ou outros utilizadores, consulte o [reset da palavra-passe de autosserviço do Azure Ad deploy e outros artigos](../authentication/howto-sspr-deployment.md).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Como redefinir ou desbloquear a sua palavra-passe para uma conta de trabalho ou escola

Se não consegue aceder à sua conta Azure Ative Directory (Azure AD), pode ser porque:

- A sua palavra-passe não está a funcionar e quer reiniciá-la, ou

- Conhece a sua senha, mas a sua conta está bloqueada e precisa de a desbloquear.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Para redefinir a sua palavra-passe e voltar à sua conta

1. No ecrã **de senha de entrada,** selecione **Esqueci-me da minha palavra-passe**.

2. No ecrã da **conta, escreva** o seu **ID** de utilizador ou de escola (por exemplo, o seu endereço de e-mail), prove que não é um robô introduzindo as caracteres que vê no ecrã e, em seguida, selecione **Next**.

   ![Volte para o ecrã da sua conta](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Se o seu administrador não tiver ligado a capacidade de redefinir a sua própria palavra-passe, verá um link **de administrador** em vez do Get de volta no ecrã da **sua conta.** Este link permite contactar o seu administrador sobre a reposição da sua palavra-passe, através de e-mail ou de um portal web.

3. Escolha um dos seguintes métodos para verificar a sua identidade e altere a sua palavra-passe. Dependendo da forma como o seu administrador criou a sua organização, poderá ter de passar por este processo uma segunda vez, adicionando informações para uma segunda etapa de verificação.

    ![Volte para a sua conta, passo de verificação #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Dependendo da forma como o seu administrador criou a sua organização, algumas destas opções de verificação podem não estar disponíveis. Deve ter configurado previamente o seu dispositivo móvel para verificação utilizando pelo menos um destes métodos.<br><br>Além disso, a sua nova palavra-passe poderá ter de satisfazer determinados requisitos de força. As palavras-passe fortes normalmente têm 8 a 16 caracteres, incluindo caracteres maiúsculas e minúsculas, pelo menos um número, e pelo menos um caráter especial.

- **Reinicie a sua palavra-passe através de um endereço de e-mail.** Envia um e-mail para o endereço de e-mail que previamente configurado em informações de verificação ou segurança em duas etapas. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a configuração de um endereço de e-mail na informação de segurança Configurar para usar o artigo [de e-mail (pré-visualização).](security-info-setup-email.md) Se ainda não estiver a utilizar informações de segurança, pode encontrar mais informações sobre a criação de um endereço de e-mail na [minha conta Configurar para](multi-factor-authentication-end-user-first-time.md) artigo de verificação em duas etapas. 

    1. Selecione **Enviar por email o meu e-mail alternativo** e, em seguida, selecione **Email**.

    2. Digite o código de verificação do e-mail para a caixa e, em seguida, selecione **Seguinte**.

    3. Digite e confirme a sua nova palavra-passe e, em seguida, **selecione Terminar**.

- **Reinicie a sua palavra-passe usando uma mensagem de texto.** Envia uma mensagem de texto para o número de telefone que estabeleceu anteriormente na informação de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a configuração de mensagens de texto na informação de segurança Configurar para utilizar o artigo [de mensagens de texto (pré-visualização).](security-info-setup-text-msg.md) Se ainda não estiver a utilizar informações de segurança, pode encontrar mais informações sobre a configuração de mensagens de texto na [minha conta configurar o artigo de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Enviar sms para o meu telemóvel,** digite o seu número de telefone e, em seguida, selecione **Texto**.

    2. Digite o código de verificação da mensagem de texto na caixa e, em seguida, selecione **Seguinte**.

    3. Digite e confirme a sua nova palavra-passe e, em seguida, **selecione Terminar**.

- **Reinicie a sua palavra-passe usando um número de telefone.** Coloca uma chamada de voz automatizada para o número de telefone que previamente configurado em informações de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a configuração de um número de telefone na [informação de segurança Configurar para utilizar um artigo de chamada telefónica (pré-visualização).](security-info-setup-phone-number.md) Se ainda não estiver a utilizar informações de segurança, pode encontrar mais informações sobre a configuração de um número de telefone na [minha conta configurar para](multi-factor-authentication-end-user-first-time.md) artigo de verificação em duas etapas.

    1. Selecione **Ligue para o meu telemóvel,** escreva o seu número de telefone e, em seguida, selecione **Call**.

    2. Atende a chamada telefónica e segue as instruções para verificar a sua identidade e, em seguida, selecione **Next**.

    3. Digite e confirme a sua nova palavra-passe e, em seguida, **selecione Terminar**.

- **Reinicie a sua palavra-passe utilizando questões de segurança.** Mostra-lhe a lista de questões de segurança que estabeleceu nas informações de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a configuração das suas questões de segurança na [informação de segurança Configurar para utilizar questões de segurança pré-definidas (pré-visualização).](security-info-setup-questions.md) Se ainda não estiver a utilizar informações de segurança, poderá encontrar mais informações sobre a configuração de questões de segurança na minha conta configurar para artigo de verificação em [duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Responder às minhas perguntas de segurança,** responder às perguntas e, em seguida, selecionar **Seguinte**.

    2. Digite e confirme a sua nova palavra-passe e, em seguida, **selecione Terminar**.

- **Reinicie a sua palavra-passe através de uma notificação da sua aplicação autenticadora.** Envia uma notificação de aprovação para a aplicação autenticadora. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a criação de uma aplicação autenticadora para enviar uma notificação na informação de segurança Configurar para utilizar um artigo de [aplicação de autenticação (pré-visualização).](security-info-setup-auth-app.md) Se ainda não estiver a utilizar informações de segurança, pode encontrar mais informações sobre a criação de uma aplicação autenticadora para enviar uma notificação na minha conta para o artigo de verificação em [duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Aprovar uma notificação na minha aplicação de autenticador** e, em seguida, selecione **Enviar Notificação**.

    2. Aprove a inscrição na sua aplicação de autenticador.

    3. Digite e confirme a sua nova palavra-passe e, em seguida, **selecione Terminar**.

- **Reinicie a sua palavra-passe utilizando um código da sua aplicação autenticadora.** Aceita um código aleatório fornecido pela sua aplicação de autenticação. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a criação de uma aplicação autenticadora para fornecer um código na [informação de segurança Configurar para utilizar um artigo de aplicação de autenticação (pré-visualização).](security-info-setup-auth-app.md) Se ainda não estiver a utilizar informações de segurança, pode encontrar mais informações sobre a criação de uma aplicação autenticadora para fornecer um código na minha conta para o artigo de verificação em [duas etapas.](multi-factor-authentication-end-user-first-time.md)

  1. Selecione **Introduza um código a partir da minha aplicação de autenticador** e, em seguida, selecione **Enviar Notificação**.

  2. Abra a sua aplicação autenticadora, digite o código de verificação da sua conta na caixa e, em seguida, selecione **Seguinte**.

  3. Digite e confirme a sua nova palavra-passe e, em seguida, **selecione Terminar**.

  4. Depois de receber a mensagem a dizer que a sua palavra-passe foi reposta, pode iniciar sômposição na sua conta utilizando a sua nova palavra-passe.

     Se ainda não conseguir aceder à sua conta, deverá contactar o administrador da sua organização para obter mais ajuda.

Depois de redefinir a sua palavra-passe, poderá receber um e-mail de confirmação que vem de uma conta como, "Microsoft em nome de \<*your_organization*> ." Se receber um e-mail semelhante, mas não reiniciou recentemente a sua palavra-passe, deve contactar imediatamente o administrador da sua organização.

## <a name="how-to-change-your-password"></a>How to change your password (Como alterar a palavra-passe) para alterar as palavras-passe

Se apenas quiser alterar a sua palavra-passe, pode fazê-lo através do portal Office 365, do portal My Apps ou da página de sinse-in do Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Para alterar a sua palavra-passe utilizando o portal Office 365

Utilize este método se normalmente aceder às suas apps através do portal Do Office:

1. Inscreva-se na sua [conta Office 365,](https://portal.office.com)utilizando a sua senha existente.

2. Selecione o seu perfil no lado superior direito e, em seguida, **selecione Ver conta**.

3. Selecione **Segurança &**  >  **palavra-passe de** privacidade .

4. Digite a sua senha antiga, crie e confirme a sua nova palavra-passe e, em seguida, **selecione Enviar por isso**.

### <a name="to-change-your-password-from-the-my-apps-portal"></a>Para alterar a sua palavra-passe a partir do portal My Apps

Utilize este método se normalmente aceder às suas aplicações a partir do portal My Apps:

1. Inscreva-se no [portal My Apps,](https://myapps.microsoft.com/)utilizando a sua senha existente.

2. Selecione o seu perfil no lado superior direito e, em seguida, selecione **Profile**.

3. Selecione **Alterar palavra-passe**.

4. Digite a sua senha antiga, crie e confirme a sua nova palavra-passe e, em seguida, **selecione Enviar por isso**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Para alterar a sua palavra-passe no início de sômito do Windows

Se o seu administrador ligou a funcionalidade, pode ver um link para redefinir a **palavra-passe** no Windows 7, Windows 8, Windows 8.1 ou no ecrã de entrada de sposição do Windows 10.

1. Selecione o link **de palavra-passe Reset** para iniciar o processo de reset da palavra-passe sem ter de utilizar a experiência normal baseada na Web.

2. Confirme o seu ID do utilizador e selecione **Next**.

3. Selecione e confirme um método de contacto para verificação. Se necessário, escolha uma segunda opção de verificação diferente da anterior, preenchendo a informação necessária.

4. Na página Criar uma nova página **de palavra-passe,** digite e confirme a sua nova palavra-passe e, em seguida, selecione **Next**.

    As palavras-passe fortes normalmente têm 8 a 16 caracteres, incluindo caracteres maiúsculas e minúsculas, pelo menos um número, e pelo menos um caráter especial.

5. Depois de receber a mensagem a dizer que a sua palavra-passe foi reposta, pode selecionar **Acabamento**.

    Se ainda não conseguir aceder à sua conta, deverá contactar o administrador da sua organização para obter mais ajuda.

## <a name="common-problems-and-their-solutions"></a>Common problems and their solutions (Problemas comuns e suas soluções)

Eis alguns casos de erro comuns e as suas soluções:

|Problema|Descrição|Solução|
| --- | --- | --- |
|Quando tento mudar a minha senha, sinto um erro. |A sua palavra-passe tem uma palavra, frase ou padrão que torna a sua palavra-passe facilmente adivinhável.| Tente novamente usando uma senha mais forte.|
|Depois de introduzir o meu ID do utilizador, vou a uma página que diz: "Por favor contacte o seu administrador."|A Microsoft determinou que a palavra-passe da sua conta de utilizador é gerida pelo seu administrador num ambiente no local. Como resultado, não é possível redefinir a sua palavra-passe a partir do link "Não pode aceder à sua conta". |Contacte o seu administrador para mais ajuda.|
|Depois de introduzir o meu ID do Utilizador, recebo um erro que diz: "A sua conta não está ativada para reset de palavra-passe."|O seu administrador ainda não criou a sua conta para que possa redefinir a sua própria senha.|O seu administrador não ligou a palavra-passe para a sua organização a partir do link "Não pode aceder à sua conta", ou não o licenciou para utilizar a funcionalidade.<br><br> Para redefinir a sua palavra-passe, tem de selecionar o "link de contacto com um administrador" para enviar um e-mail ao administrador da sua empresa e informá-los de que pretende redefinir a sua palavra-passe.|
|Depois de introduzir o meu ID do utilizador, recebo um erro que diz: "Não conseguimos verificar a sua conta."|O processo de login não foi capaz de verificar a informação da sua conta.|Há duas razões para estar a ver esta mensagem.<br><br>1. O seu administrador ligou a palavra-passe para a sua organização, mas não se registou para utilizar o serviço. Para se registar para reiniciar a palavra-passe, consulte um dos seguintes artigos, com base no seu método de verificação: [Configurar informações de segurança para utilizar uma aplicação autenticadora (pré-visualização)](security-info-setup-auth-app.md), [Configurar informações de segurança para utilizar uma chamada telefónica (pré-visualização)](security-info-setup-phone-number.md), [Configurar informações](security-info-setup-text-msg.md)de segurança para utilizar mensagens [de texto (pré-visualização)](security-info-setup-email.md) [](security-info-setup-questions.md)<br><br>2. O seu administrador não ligou a palavra-passe para a sua organização. Nesta situação, deve selecionar o "link de contacto com um administrador" para enviar um e-mail ao seu administrador, pedindo para redefinir a sua palavra-passe.|

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre informações de segurança no artigo de visão geral da [informação de segurança (pré-visualização).](./security-info-setup-signin.md)

- Se estiver a tentar voltar a uma conta pessoal como a Xbox, hotmail.com ou outlook.com, experimente as sugestões no [When you can't sign in your Microsoft account article](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
