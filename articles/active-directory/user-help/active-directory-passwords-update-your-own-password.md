---
title: Redefina sua senha usando informações de segurança-Azure Active Directory | Microsoft Docs
description: Como redefinir sua própria senha se você esquecê-la, usando suas informações de segurança e a verificação em duas etapas.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: lizross
ms.openlocfilehash: ad4831cfb5b09083b4ee76b6badf3936b2256138
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263494"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Redefinir sua senha corporativa ou de estudante usando informações de segurança

Se você esqueceu sua senha corporativa ou de estudante, nunca recebeu uma senha da sua organização ou foi bloqueada por sua conta, você pode usar suas informações de segurança e seu dispositivo móvel para redefinir sua senha corporativa ou de estudante. O administrador deve ativar esse recurso para que você possa configurar suas informações e redefinir sua própria senha.

Se você souber sua senha, mas quiser alterá-la, consulte as seções [alterar as etapas de senha](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password#how-to-change-your-password) deste artigo.

>[!Important]
>Este artigo destina-se a usuários tentando usar redefinir uma senha de conta corporativa ou de estudante esquecida ou desconhecida. Se você for um administrador que procura informações sobre como ativar a redefinição de senha de autoatendimento para seus funcionários ou outros usuários, consulte [implantar a redefinição de senha de autoatendimento do Azure AD e outros artigos](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Como redefinir ou desbloquear sua senha para uma conta corporativa ou de estudante

Se você não puder acessar sua conta do Azure Active Directory (AD do Azure), isso pode ser devido a:

- Sua senha não está funcionando e você deseja redefini-la, ou

- Você sabe sua senha, mas sua conta está bloqueada e você precisa desbloqueá-la.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Para redefinir sua senha e voltar à sua conta

1. Na tela **digitar senha** , selecione **esqueci minha senha**.

2. Na tela **voltar à sua conta** , digite sua **ID de usuário** corporativa ou de estudante (por exemplo, seu endereço de email), prove que você não é um robô inserindo os caracteres que você vê na tela e, em seguida, selecione **Avançar**.

   ![Voltar à tela da sua conta](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Se o administrador não tiver ativado a capacidade de redefinir sua senha, você verá um link **entrar em contato com seu administrador** em vez da tela **retornar à sua conta** . Esse link permite que você entre em contato com o administrador sobre como redefinir sua senha, por email ou por um portal da Web.

3. Escolha um dos métodos a seguir para verificar sua identidade e alterar sua senha. Dependendo de como o administrador configurou sua organização, talvez seja necessário passar por esse processo uma segunda vez, adicionando informações para uma segunda etapa de verificação.

    ![Volte para sua conta, etapa de verificação #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Dependendo de como o administrador configurou sua organização, algumas dessas opções de verificação podem não estar disponíveis. Você deve ter configurado anteriormente seu dispositivo móvel para verificação usando pelo menos um desses métodos.<br><br>Além disso, sua nova senha pode precisar atender a determinados requisitos de força. Senhas fortes normalmente têm de 8 a 16 caracteres, incluindo caracteres maiúsculos e minúsculos, pelo menos um número e pelo menos um caractere especial.

- **Redefina sua senha usando um endereço de email.** Envia um email para o endereço de email que você configurou anteriormente na verificação em duas etapas ou nas informações de segurança. Se o administrador tiver ativado a experiência de informações de segurança, você poderá encontrar mais informações sobre como configurar um endereço de email no artigo [configurar informações de segurança para usar o email (versão prévia)](security-info-setup-email.md) . Se você ainda não estiver usando informações de segurança, poderá encontrar mais informações sobre como configurar um endereço de email no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) . 

    1. Selecione **Enviar email para meu email alternativo**e, em seguida, selecione **email**.

    2. Digite o código de verificação do email na caixa e, em seguida, selecione **Avançar**.

    3. Digite e confirme sua nova senha e, em seguida, selecione **concluir**.

- **Redefina sua senha usando uma mensagem de texto.** Envia uma mensagem de texto para o número de telefone que você configurou anteriormente nas informações de segurança. Se o administrador tiver ativado a experiência de informações de segurança, você poderá encontrar mais informações sobre como configurar mensagens de texto no artigo [configurar informações de segurança para usar mensagens de texto (versão prévia)](security-info-setup-text-msg.md) . Se você ainda não estiver usando informações de segurança, poderá encontrar mais informações sobre como configurar mensagens de texto no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .

    1. Selecione **texto meu**celular, digite seu número de telefone e, em seguida, selecione **texto**.

    2. Digite o código de verificação da mensagem de texto na caixa e, em seguida, selecione **Avançar**.

    3. Digite e confirme sua nova senha e, em seguida, selecione **concluir**.

- **Redefina sua senha usando um número de telefone.** Envia uma mensagem de texto para o número de telefone que você configurou anteriormente nas informações de segurança. Se o administrador tiver ativado a experiência de informações de segurança, você poderá encontrar mais informações sobre como configurar um número de telefone no artigo [configurar informações de segurança para usar uma chamada telefônica (versão prévia)](security-info-setup-phone-number.md) . Se você ainda não estiver usando informações de segurança, poderá encontrar mais informações sobre como configurar um número de telefone no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .

    1. Selecione **ligar para meu**celular, digite seu número de telefone e, em seguida, selecione **chamar**.

    2. Responda à chamada telefônica e siga as instruções para verificar sua identidade e, em seguida, selecione **Avançar**.

    3. Digite e confirme sua nova senha e, em seguida, selecione **concluir**.

- **Redefina sua senha usando perguntas de segurança.** Mostra a lista de perguntas de segurança que você configurou nas informações de segurança. Se o administrador tiver ativado a experiência de informações de segurança, você poderá encontrar mais informações sobre como configurar suas perguntas de segurança no artigo [configurar informações de segurança para usar perguntas de segurança predefinidas (versão prévia)](security-info-setup-questions.md) . Se você ainda não estiver usando informações de segurança, poderá encontrar mais informações sobre como configurar perguntas de segurança no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .

    1. Selecione **responder a minhas perguntas de segurança**, responder às perguntas e, em seguida, selecione **Avançar**.

    2. Digite e confirme sua nova senha e, em seguida, selecione **concluir**.

- **Redefina sua senha usando uma notificação do seu aplicativo autenticador.** Envia uma notificação de aprovação para o aplicativo autenticador. Se o administrador tiver ativado a experiência de informações de segurança, você poderá encontrar mais informações sobre como configurar um aplicativo autenticador para enviar uma notificação no artigo [configurar informações de segurança para usar um aplicativo de autenticação (versão prévia)](security-info-setup-auth-app.md) . Se você ainda não estiver usando informações de segurança, poderá encontrar mais informações sobre como configurar um aplicativo autenticador para enviar uma notificação no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .

    1. Selecione **aprovar uma notificação no meu aplicativo autenticador**e, em seguida, selecione **enviar notificação**.

    2. Aprove a entrada de seu aplicativo autenticador.

    3. Digite e confirme sua nova senha e, em seguida, selecione **concluir**.

- **Redefina sua senha usando um código de seu aplicativo autenticador.** Aceita um código aleatório fornecido pelo seu aplicativo de autenticação. Se o administrador tiver ativado a experiência de informações de segurança, você poderá encontrar mais informações sobre como configurar um aplicativo autenticador para fornecer um código no artigo [configurar informações de segurança para usar um aplicativo de autenticação (versão prévia)](security-info-setup-auth-app.md) . Se você ainda não estiver usando informações de segurança, poderá encontrar mais informações sobre como configurar um aplicativo autenticador para fornecer um código no artigo [configurar minha conta para a verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .

  1. Selecione **Inserir um código do meu aplicativo autenticador**e, em seguida, selecione **enviar notificação**.

  2. Abra seu aplicativo autenticador, digite o código de verificação para sua conta na caixa e, em seguida, selecione **Avançar**.

  3. Digite e confirme sua nova senha e, em seguida, selecione **concluir**.

  4. Depois de receber a mensagem informando que sua senha foi redefinida, você pode entrar em sua conta usando sua nova senha.

     Se você ainda não consegue acessar sua conta, entre em contato com o administrador da sua organização para obter mais ajuda.

Depois de redefinir sua senha, você pode receber um email de confirmação proveniente de uma conta como "Microsoft em nome de \<*your_organization*>". Se você receber um email semelhante, mas não redefiniu sua senha recentemente, deverá entrar em contato com o administrador da sua organização imediatamente.

## <a name="how-to-change-your-password"></a>Como alterar sua senha

Se você quiser apenas alterar sua senha, poderá fazê-lo por meio do portal do Office 365, do painel de acesso do Azure ou da página de entrada do Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Para alterar sua senha usando o portal do Office 365

Use esse método se você normalmente acessa seus aplicativos por meio do portal do Office:

1. Entre na sua [conta do Office 365](https://portal.office.com)usando sua senha existente.

2. Selecione seu perfil no lado superior direito e selecione **Exibir conta**.

3. Selecione **segurança & privacidade** > **senha**.

4. Digite sua senha antiga, crie e confirme sua nova senha e, em seguida, selecione **Enviar**.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Para alterar sua senha do painel de acesso do Azure

Use esse método se você normalmente acessa seus aplicativos no painel de acesso do Azure (myapps):

1. Entre no painel de [acesso do Azure](https://myapps.microsoft.com/), usando sua senha existente.

2. Selecione seu perfil no lado superior direito e, em seguida, selecione **perfil**.

3. Selecione **alterar senha**.

4. Digite sua senha antiga, crie e confirme sua nova senha e, em seguida, selecione **Enviar**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Para alterar sua senha na entrada do Windows

Se o administrador tiver ativado a funcionalidade, você poderá ver um link para **redefinir a senha** na tela de entrada do Windows 7, do Windows 8, do Windows 8.1 ou do Windows 10.

1. Selecione o link **Redefinir senha** para iniciar o processo de redefinição de senha sem precisar usar a experiência normal baseada na Web.

2. Confirme sua ID de usuário e selecione **Avançar**.

3. Selecione e confirme um método de contato para verificação. Se necessário, escolha uma segunda opção de verificação que seja diferente da anterior, preenchendo as informações necessárias.

4. Na página **criar uma nova senha** , digite e confirme sua nova senha e, em seguida, selecione **Avançar**.

    Senhas fortes normalmente têm de 8 a 16 caracteres, incluindo caracteres maiúsculos e minúsculos, pelo menos um número e pelo menos um caractere especial.

5. Depois de receber a mensagem dizendo que sua senha foi redefinida, você pode selecionar **concluir**.

    Se você ainda não consegue acessar sua conta, entre em contato com o administrador da sua organização para obter mais ajuda.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e suas soluções

Aqui estão alguns casos de erro comuns e suas soluções:

|Problema|Descrição|Solução|
| --- | --- | --- |
|Quando tento alterar minha senha, recebo um erro. |Sua senha tem uma palavra, frase ou padrão que torna sua senha facilmente adivinhada.| Tente novamente usando uma senha mais forte.|
|Depois de inserir minha ID de usuário, vou para uma página que diz "entre em contato com seu administrador".|A Microsoft determinou que a senha da sua conta de usuário é gerenciada pelo administrador em um ambiente local. Como resultado, você não pode redefinir sua senha no link "não é possível acessar sua conta". |Contate o administrador para obter mais ajuda.|
|Depois de inserir minha ID de usuário, recebo um erro dizendo "sua conta não está habilitada para redefinição de senha".|O administrador não configurou sua conta para que você possa redefinir sua própria senha.|O administrador não ativou a redefinição de senha para sua organização no link "não é possível acessar sua conta" ou não licenciou o uso do recurso.<br><br> Para redefinir sua senha, você deve selecionar o link "entre em contato com um administrador" para enviar um email ao administrador da sua empresa e informar que deseja redefinir sua senha.|
|Depois de inserir minha ID de usuário, recebo um erro dizendo "não foi possível verificar sua conta".|O processo de entrada não pôde verificar as informações da sua conta.|Há dois motivos pelos quais você pode estar vendo essa mensagem.<br><br>1. o administrador ativou a redefinição de senha para sua organização, mas você não se registrou para usar o serviço. Para se registrar para redefinição de senha, consulte um dos artigos a seguir, com base em seu método de verificação: [configurar informações de segurança para usar um aplicativo autenticador (versão prévia)](security-info-setup-auth-app.md), [configurar informações de segurança para usar uma chamada telefônica (versão prévia)](security-info-setup-phone-number.md), [configurar informações de segurança para usar mensagens de texto (versão prévia)](security-info-setup-text-msg.md), [configurar informações de segurança para usar email (](security-info-setup-email.md)versão prévia) ou [configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md)<br><br>2. o administrador não ativou a redefinição de senha para sua organização. Nessa situação, você deve selecionar o link "entre em contato com um administrador" para enviar um email ao administrador, solicitando que o Redefina sua senha.|

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre informações de segurança no artigo [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md) .

- Se você estiver tentando voltar a uma conta pessoal como Xbox, hotmail.com ou outlook.com, tente as sugestões no [artigo quando não puder entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
