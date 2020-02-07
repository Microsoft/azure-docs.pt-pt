---
title: Redefina sua senha usando informações de segurança-Azure Active Directory | Microsoft Docs
description: Como redefinir sua própria senha se você esquecê-la, usando suas informações de segurança e a verificação em duas etapas.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 3ed79be318319009aabb1b1ef0c42c4021bbbabe
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062664"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Redefinir sua senha corporativa ou de estudante usando informações de segurança

Se você esqueceu sua senha corporativa ou de estudante, nunca recebeu uma senha da sua organização ou foi bloqueada por sua conta, você pode usar suas informações de segurança e seu dispositivo móvel para redefinir sua senha corporativa ou de estudante. O administrador deve ativar esse recurso para que você possa configurar suas informações e redefinir sua própria senha.

Se conhece a sua palavra-passe, mas quer trocá-la, consulte as secções de passos da [sua palavra-passe](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password#how-to-change-your-password) deste artigo.

>[!Important]
>Este artigo destina-se a usuários tentando usar redefinir uma senha de conta corporativa ou de estudante esquecida ou desconhecida. Se for um administrador à procura de informações sobre como ativar o reset de palavra-passe de self-service para os seus colaboradores ou outros utilizadores, consulte o [reset de palavra-passe de autosserviço da Deploy Azure AD e outros artigos](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Como redefinir ou desbloquear sua senha para uma conta corporativa ou de estudante

Se você não puder acessar sua conta do Azure Active Directory (AD do Azure), isso pode ser devido a:

- Sua senha não está funcionando e você deseja redefini-la, ou

- Você sabe sua senha, mas sua conta está bloqueada e você precisa desbloqueá-la.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Para redefinir sua senha e voltar à sua conta

1. No ecrã de **palavra-passe Enter,** selecione **Esqueci-me da minha palavra-passe**.

2. No Ecrã da **sua conta,** escreva o seu trabalho ou id do **utilizador** escolar (por exemplo, o seu endereço de e-mail), prove que não é um robô ao introduzir os caracteres que vê no ecrã e, em seguida, selecione **Next**.

   ![Voltar à tela da sua conta](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Se o seu administrador não tiver ligado a capacidade de redefinir a sua própria palavra-passe, verá um **link de contacto com o seu administrador** em vez do Volte a entrar no ecrã da sua **conta.** Esse link permite que você entre em contato com o administrador sobre como redefinir sua senha, por email ou por um portal da Web.

3. Escolha um dos métodos a seguir para verificar sua identidade e alterar sua senha. Dependendo de como o administrador configurou sua organização, talvez seja necessário passar por esse processo uma segunda vez, adicionando informações para uma segunda etapa de verificação.

    ![Volte para sua conta, etapa de verificação #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Dependendo de como o administrador configurou sua organização, algumas dessas opções de verificação podem não estar disponíveis. Você deve ter configurado anteriormente seu dispositivo móvel para verificação usando pelo menos um desses métodos.<br><br>Além disso, sua nova senha pode precisar atender a determinados requisitos de força. Senhas fortes normalmente têm de 8 a 16 caracteres, incluindo caracteres maiúsculos e minúsculos, pelo menos um número e pelo menos um caractere especial.

- **Redefinir a sua palavra-passe utilizando um endereço de e-mail.** Envia um email para o endereço de email que você configurou anteriormente na verificação em duas etapas ou nas informações de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a criação de um endereço de e-mail no artigo de segurança Da Configuração para usar o artigo [de e-mail (pré-visualização).](security-info-setup-email.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de um endereço de e-mail no Conjunto da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md) 

    1. **Selecione E-mail meu e-mail alternativo**, e depois selecione **Email**.

    2. Digite o código de verificação do e-mail para a caixa e, em seguida, selecione **Next**.

    3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe usando uma mensagem de texto.** Envia uma mensagem de texto para o número de telefone que você configurou anteriormente nas informações de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a criação de mensagens de texto no Artigo de segurança Configurar para utilizar o artigo de mensagens de [texto (pré-visualização).](security-info-setup-text-msg.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de mensagens de texto no Conjunto da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Envie SMS para o meu telemóvel,** escreva o seu número de telefone e, em seguida, selecione **Texto**.

    2. Digite o código de verificação a partir da mensagem de texto na caixa e, em seguida, selecione **Next**.

    3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe usando um número de telefone.** Envia uma mensagem de texto para o número de telefone que você configurou anteriormente nas informações de segurança. Se o seu administrador tiver ativado a experiência de informação de segurança, pode encontrar mais informações sobre a configuração de um número de telefone na Configuração de informações de segurança para usar um artigo [de chamada telefónica (pré-visualização).](security-info-setup-phone-number.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a configuração de um número de telefone na Configuração da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Ligue para o meu telemóvel,** escreva o seu número de telefone e, em seguida, selecione **Call**.

    2. Atenda a chamada telefónica e siga as instruções para verificar a sua identidade e, em seguida, selecione **Next**.

    3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe usando questões de segurança.** Mostra a lista de perguntas de segurança que você configurou nas informações de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a configuração das suas questões de segurança no Conjunto de informações de [segurança para utilizar questões de segurança pré-definidas (pré-visualização).](security-info-setup-questions.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de questões de segurança no Conjunto da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Responder às minhas questões de segurança,** responda às perguntas e, em seguida, selecione **Next**.

    2. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe utilizando uma notificação da sua aplicação autenticadora.** Envia uma notificação de aprovação para o aplicativo autenticador. Se o seu administrador tiver ativado a experiência de informação de segurança, poderá encontrar mais informações sobre a criação de uma aplicação autenticadora para enviar uma notificação no artigo de segurança da Configuração para utilizar um artigo de aplicação de [autenticação (pré-visualização).](security-info-setup-auth-app.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de uma aplicação autenticadora para enviar uma notificação no Conjunto da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Aprove uma notificação na minha aplicação autenticadora**e, em seguida, selecione **Enviar notificação**.

    2. Aprove a entrada de seu aplicativo autenticador.

    3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe utilizando um código da sua aplicação autenticadora.** Aceita um código aleatório fornecido pelo seu aplicativo de autenticação. Se o seu administrador tiver ativado a experiência de informação de segurança, poderá encontrar mais informações sobre a criação de uma aplicação autenticadora para fornecer um código na configuração de informações de segurança para usar um artigo de aplicação de [autenticação (pré-visualização).](security-info-setup-auth-app.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de uma aplicação autenticadora para fornecer um código na Configuração da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

  1. Selecione **Introduzir um código na minha aplicação de autenticador**e, em seguida, selecione Enviar **notificação**.

  2. Abra a sua aplicação autenticadora, digite o código de verificação da sua conta na caixa e, em seguida, selecione **Next**.

  3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

  4. Depois de receber a mensagem informando que sua senha foi redefinida, você pode entrar em sua conta usando sua nova senha.

     Se você ainda não consegue acessar sua conta, entre em contato com o administrador da sua organização para obter mais ajuda.

Depois de redefinir a sua palavra-passe, poderá obter um e-mail de confirmação que vem de uma conta como: "Microsoft em nome de \<*your_organization*>". Se você receber um email semelhante, mas não redefiniu sua senha recentemente, deverá entrar em contato com o administrador da sua organização imediatamente.

## <a name="how-to-change-your-password"></a>Como alterar sua senha

Se você quiser apenas alterar sua senha, poderá fazê-lo por meio do portal do Office 365, do painel de acesso do Azure ou da página de entrada do Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Para alterar sua senha usando o portal do Office 365

Use esse método se você normalmente acessa seus aplicativos por meio do portal do Office:

1. Inscreva-se na sua [conta Office 365,](https://portal.office.com)utilizando a sua senha existente.

2. Selecione o seu perfil no lado superior direito e, em seguida, selecione **a conta 'Ver '.**

3. Selecione **Segurança e privacidade** > **Palavra-passe**.

4. Digite a sua palavra-passe antiga, crie e confirme a sua nova palavra-passe e, em seguida, selecione **Enviar**.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Para alterar sua senha do painel de acesso do Azure

Use esse método se você normalmente acessa seus aplicativos no painel de acesso do Azure (myapps):

1. Inscreva-se no Painel de [Acesso Azure,](https://myapps.microsoft.com/)utilizando a sua senha existente.

2. Selecione o seu perfil no lado superior direito e, em seguida, selecione **Perfil**.

3. Selecione **Alterar a palavra-passe**.

4. Digite a sua palavra-passe antiga, crie e confirme a sua nova palavra-passe e, em seguida, selecione **Enviar**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Para alterar sua senha na entrada do Windows

Se o seu administrador ligar a funcionalidade, pode ver um link para redefinir a **palavra-passe** no windows 7, Windows 8, Windows 8.1 ou windows 10.

1. Selecione o link de **palavra-passe Reset** para iniciar o processo de reset da palavra-passe sem ter de utilizar a experiência normal baseada na Web.

2. Confirme o ID do utilizador e selecione **Next**.

3. Selecione e confirme um método de contato para verificação. Se necessário, escolha uma segunda opção de verificação que seja diferente da anterior, preenchendo as informações necessárias.

4. Na **página Criar uma nova página de palavra-passe,** digite e confirme a sua nova palavra-passe e, em seguida, selecione **Next**.

    Senhas fortes normalmente têm de 8 a 16 caracteres, incluindo caracteres maiúsculos e minúsculos, pelo menos um número e pelo menos um caractere especial.

5. Depois de receber a mensagem dizendo que a sua palavra-passe foi redefinida, pode selecionar **Terminar**.

    Se você ainda não consegue acessar sua conta, entre em contato com o administrador da sua organização para obter mais ajuda.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e suas soluções

Aqui estão alguns casos de erro comuns e suas soluções:

|Problema|Descrição|Solução|
| --- | --- | --- |
|Quando tento alterar minha senha, recebo um erro. |Sua senha tem uma palavra, frase ou padrão que torna sua senha facilmente adivinhada.| Tente novamente usando uma senha mais forte.|
|Depois de inserir minha ID de usuário, vou para uma página que diz "entre em contato com seu administrador".|A Microsoft determinou que a senha da sua conta de usuário é gerenciada pelo administrador em um ambiente local. Como resultado, você não pode redefinir sua senha no link "não é possível acessar sua conta". |Contate o administrador para obter mais ajuda.|
|Depois de inserir minha ID de usuário, recebo um erro dizendo "sua conta não está habilitada para redefinição de senha".|O administrador não configurou sua conta para que você possa redefinir sua própria senha.|O administrador não ativou a redefinição de senha para sua organização no link "não é possível acessar sua conta" ou não licenciou o uso do recurso.<br><br> Para redefinir sua senha, você deve selecionar o link "entre em contato com um administrador" para enviar um email ao administrador da sua empresa e informar que deseja redefinir sua senha.|
|Depois de inserir minha ID de usuário, recebo um erro dizendo "não foi possível verificar sua conta".|O processo de entrada não pôde verificar as informações da sua conta.|Há dois motivos pelos quais você pode estar vendo essa mensagem.<br><br>1. o administrador ativou a redefinição de senha para sua organização, mas você não se registrou para usar o serviço. Para se registar para reset de palavra-passe, consulte um dos seguintes artigos, com base no seu método de verificação: [Configurar informações de segurança para utilizar uma aplicação autenticadora (pré-visualização)](security-info-setup-auth-app.md)- [Configurar informações de segurança para utilizar uma chamada telefónica (pré-visualização)](security-info-setup-phone-number.md), Configurar informações de segurança para usar mensagens de [texto (pré-visualização)](security-info-setup-text-msg.md)ou configurar informações de [segurança](security-info-setup-email.md)para usar questões de [segurança (pré-visualização)](security-info-setup-questions.md).<br><br>2. o administrador não ativou a redefinição de senha para sua organização. Nessa situação, você deve selecionar o link "entre em contato com um administrador" para enviar um email ao administrador, solicitando que o Redefina sua senha.|

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre informações de segurança no artigo de visão geral da informação de [segurança (pré-visualização).](user-help-security-info-overview.md)

- Se está a tentar voltar a entrar numa conta pessoal como a Xbox, hotmail.com ou outlook.com, experimente as sugestões no Artigo da [sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
