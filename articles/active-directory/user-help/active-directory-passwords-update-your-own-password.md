---
title: Redefinir a sua palavra-passe utilizando informações de segurança - Diretório Ativo Azure / Microsoft Docs
description: Como redefinir a sua própria palavra-passe se a esquecer, utilizando as suas informações de segurança e verificação em duas etapas.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: b27b046d53a45c91d0a55a468758386437a7a6a5
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739669"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Redefinir o seu trabalho ou senha escolar usando informações de segurança

Se esqueceu do seu trabalho ou palavra-passe escolar, nunca obteve uma senha da sua organização, ou se foi bloqueado fora da sua conta, pode usar as suas informações de segurança e o seu dispositivo móvel para redefinir o seu trabalho ou senha escolar. O seu administrador deve ativar esta funcionalidade para que possa configurar as suas informações e redefinir a sua própria palavra-passe.

Se conhece a sua palavra-passe, mas quer trocá-la, consulte as secções de passos da [sua palavra-passe](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password#how-to-change-your-password) deste artigo.

>[!Important]
>Este artigo destina-se a utilizadores que pretendam utilizar uma palavra-passe de trabalho ou conta escolar esquecida ou desconhecida. Se for um administrador à procura de informações sobre como ativar o reset de palavra-passe de self-service para os seus colaboradores ou outros utilizadores, consulte o [reset de palavra-passe de autosserviço da Deploy Azure AD e outros artigos](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Como redefinir ou desbloquear a sua palavra-passe para uma conta de trabalho ou escola

Se não conseguir aceder à sua conta de Diretório Ativo Azure (Azure AD), pode ser porque:

- A sua palavra-passe não está a funcionar e quer redefini-la, ou.

- Sabes a tua palavra-passe, mas a tua conta está bloqueada e precisas de a desbloquear.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Para redefinir a sua palavra-passe e voltar para a sua conta

1. No ecrã de **palavra-passe Enter,** selecione **Esqueci-me da minha palavra-passe**.

2. No Ecrã da **sua conta,** escreva o seu trabalho ou id do **utilizador** escolar (por exemplo, o seu endereço de e-mail), prove que não é um robô ao introduzir os caracteres que vê no ecrã e, em seguida, selecione **Next**.

   ![Volte para o ecrã da sua conta](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Se o seu administrador não tiver ligado a capacidade de redefinir a sua própria palavra-passe, verá um **link de contacto com o seu administrador** em vez do Volte a entrar no ecrã da sua **conta.** Este link permite-lhe contactar o seu administrador sobre a redefinição da sua palavra-passe, através de um email ou de um portal web.

3. Escolha um dos seguintes métodos para verificar a sua identidade e altere a sua palavra-passe. Dependendo da forma como o seu administrador criou a sua organização, poderá ter de passar por este processo uma segunda vez, adicionando informações para um segundo passo de verificação.

    ![Volte para a sua conta, passo de verificação #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Dependendo da forma como o seu administrador criou a sua organização, algumas destas opções de verificação podem não estar disponíveis. Deve ter configurado previamente o seu dispositivo móvel para verificação utilizando pelo menos um destes métodos.<br><br>Além disso, a sua nova senha poderá ter de satisfazer certos requisitos de força. As palavras-passe fortes têm tipicamente 8 a 16 caracteres, incluindo caracteres superiores e minúsculos, pelo menos um número, e pelo menos um personagem especial.

- **Redefinir a sua palavra-passe utilizando um endereço de e-mail.** Envia um e-mail para o endereço de e-mail que previamente configurado em duas etapas de verificação ou informações de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a criação de um endereço de e-mail no artigo de segurança Da Configuração para usar o artigo [de e-mail (pré-visualização).](security-info-setup-email.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de um endereço de e-mail no Conjunto da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md) 

    1. **Selecione E-mail meu e-mail alternativo**, e depois selecione **Email**.

    2. Digite o código de verificação do e-mail para a caixa e, em seguida, selecione **Next**.

    3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe usando uma mensagem de texto.** Envia uma mensagem de texto para o número de telefone que configura previamente em informações de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a criação de mensagens de texto no Artigo de segurança Configurar para utilizar o artigo de mensagens de [texto (pré-visualização).](security-info-setup-text-msg.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de mensagens de texto no Conjunto da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Envie SMS para o meu telemóvel,** escreva o seu número de telefone e, em seguida, selecione **Texto**.

    2. Digite o código de verificação a partir da mensagem de texto na caixa e, em seguida, selecione **Next**.

    3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe usando um número de telefone.** Envia uma mensagem de texto para o número de telefone que configura previamente em informações de segurança. Se o seu administrador tiver ativado a experiência de informação de segurança, pode encontrar mais informações sobre a configuração de um número de telefone na Configuração de informações de segurança para usar um artigo [de chamada telefónica (pré-visualização).](security-info-setup-phone-number.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a configuração de um número de telefone na Configuração da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Ligue para o meu telemóvel,** escreva o seu número de telefone e, em seguida, selecione **Call**.

    2. Atenda a chamada telefónica e siga as instruções para verificar a sua identidade e, em seguida, selecione **Next**.

    3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe usando questões de segurança.** Mostra-lhe a lista de perguntas de segurança que criou em informações de segurança. Se o seu administrador tiver ligado a experiência de informação de segurança, pode encontrar mais informações sobre a configuração das suas questões de segurança no Conjunto de informações de [segurança para utilizar questões de segurança pré-definidas (pré-visualização).](security-info-setup-questions.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de questões de segurança no Conjunto da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Responder às minhas questões de segurança,** responda às perguntas e, em seguida, selecione **Next**.

    2. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe utilizando uma notificação da sua aplicação autenticadora.** Envia uma notificação de aprovação para a aplicação autenticadora. Se o seu administrador tiver ativado a experiência de informação de segurança, poderá encontrar mais informações sobre a criação de uma aplicação autenticadora para enviar uma notificação no artigo de segurança da Configuração para utilizar um artigo de aplicação de [autenticação (pré-visualização).](security-info-setup-auth-app.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de uma aplicação autenticadora para enviar uma notificação no Conjunto da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

    1. Selecione **Aprove uma notificação na minha aplicação autenticadora**e, em seguida, selecione **Enviar notificação**.

    2. Aprove o sessão a partir da sua aplicação autenticadora.

    3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

- **Redefinir a sua palavra-passe utilizando um código da sua aplicação autenticadora.** Aceita um código aleatório fornecido pela sua aplicação de autenticação. Se o seu administrador tiver ativado a experiência de informação de segurança, poderá encontrar mais informações sobre a criação de uma aplicação autenticadora para fornecer um código na configuração de informações de segurança para usar um artigo de aplicação de [autenticação (pré-visualização).](security-info-setup-auth-app.md) Se ainda não estiver a usar informações de segurança, pode encontrar mais informações sobre a criação de uma aplicação autenticadora para fornecer um código na Configuração da minha conta para artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

  1. Selecione **Introduzir um código na minha aplicação de autenticador**e, em seguida, selecione Enviar **notificação**.

  2. Abra a sua aplicação autenticadora, digite o código de verificação da sua conta na caixa e, em seguida, selecione **Next**.

  3. Digite e confirme a sua nova senha e, em seguida, selecione **Terminar**.

  4. Depois de receber a mensagem a dizer que a sua palavra-passe foi redefinida, pode iniciar sessão na sua conta através da sua nova senha.

     Se ainda não conseguir aceder à sua conta, deverá contactar o administrador da sua organização para obter mais ajuda.

Depois de redefinir a sua palavra-passe, poderá obter um e-mail de confirmação que vem de uma conta como: "Microsoft em nome de \< *your_organization*>." Se receber um e-mail semelhante, mas não redefiniu recentemente a sua palavra-passe, deve contactar imediatamente o administrador da sua organização.

## <a name="how-to-change-your-password"></a>How to change your password (Como alterar a palavra-passe) para alterar as palavras-passe

Se quiser apenas alterar a sua palavra-passe, pode fazê-lo através do portal office 365, do Painel de Acesso Azure ou da página de entrada do Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Para alterar a sua palavra-passe utilizando o portal Office 365

Utilize este método se aceder normalmente às suas aplicações através do portal do Office:

1. Inscreva-se na sua [conta Office 365,](https://portal.office.com)utilizando a sua senha existente.

2. Selecione o seu perfil no lado superior direito e, em seguida, selecione **a conta 'Ver '.**

3. Selecione **Segurança & privacidade**  >  **Password**.

4. Digite a sua palavra-passe antiga, crie e confirme a sua nova palavra-passe e, em seguida, selecione **Enviar**.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Para alterar a sua palavra-passe do Painel de Acesso Azure

Utilize este método se aceder normalmente às suas aplicações a partir do Painel de Acesso Azure (MyApps):

1. Inscreva-se no Painel de [Acesso Azure,](https://myapps.microsoft.com/)utilizando a sua senha existente.

2. Selecione o seu perfil no lado superior direito e, em seguida, selecione **Perfil**.

3. Selecione **Alterar a palavra-passe**.

4. Digite a sua palavra-passe antiga, crie e confirme a sua nova palavra-passe e, em seguida, selecione **Enviar**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Para alterar a sua palavra-passe no Windows iniciar sessão

Se o seu administrador ligar a funcionalidade, pode ver um link para redefinir a **palavra-passe** no windows 7, Windows 8, Windows 8.1 ou windows 10.

1. Selecione o link de **palavra-passe Reset** para iniciar o processo de reset da palavra-passe sem ter de utilizar a experiência normal baseada na Web.

2. Confirme o ID do utilizador e selecione **Next**.

3. Selecione e confirme um método de contacto para verificação. Se necessário, escolha uma segunda opção de verificação diferente da anterior, preenchendo as informações necessárias.

4. Na **página Criar uma nova página de palavra-passe,** digite e confirme a sua nova palavra-passe e, em seguida, selecione **Next**.

    As palavras-passe fortes têm tipicamente 8 a 16 caracteres, incluindo caracteres superiores e minúsculos, pelo menos um número, e pelo menos um personagem especial.

5. Depois de receber a mensagem dizendo que a sua palavra-passe foi redefinida, pode selecionar **Terminar**.

    Se ainda não conseguir aceder à sua conta, deverá contactar o administrador da sua organização para obter mais ajuda.

## <a name="common-problems-and-their-solutions"></a>Common problems and their solutions (Problemas comuns e suas soluções)

Aqui estão alguns casos de erro comuns e as suas soluções:

|Problema|Descrição|Solução|
| --- | --- | --- |
|Quando tento mudar a minha senha, tenho um erro. |A sua palavra-passe tem uma palavra, frase ou padrão que torna a sua palavra-passe facilmente adivinhada.| Tente novamente usando uma senha mais forte.|
|Depois de inserir o meu ID de utilizador, vou a uma página que diz: "Por favor, contacte o seu administrador."|A Microsoft determinou que a sua palavra-passe da conta de utilizador é gerida pelo seu administrador num ambiente no local. Como resultado, não é possível redefinir a sua palavra-passe a partir do link "Não posso aceder à sua conta". |Contacte o seu administrador para obter mais ajuda.|
|Depois de introduzir o meu ID de utilizador, tenho um erro que diz: "A sua conta não está ativada para reposição de palavra-passe."|O seu administrador não criou a sua conta para que possa redefinir a sua própria senha.|O seu administrador não ligou o reset de palavra-passe para a sua organização a partir do link "Não posso aceder à sua conta", ou não o licenciou para usar a funcionalidade.<br><br> Para redefinir a sua palavra-passe, deve selecionar o "contacto com um link de administrador" para enviar um e-mail ao administrador da sua empresa e informá-los de que pretende redefinir a sua palavra-passe.|
|Depois de introduzir a minha identificação do utilizador, tenho um erro que diz: "Não pudemos verificar a sua conta."|O processo de inscrição não pôde verificar a informação da sua conta.|Há duas razões para ver esta mensagem.<br><br>1. O seu administrador ligou o reset da palavra-passe para a sua organização, mas não se registou para utilizar o serviço. Para se registar para reset de palavra-passe, consulte um dos seguintes artigos, com base no seu método de verificação: [Configurar informações de segurança para utilizar uma aplicação autenticadora (pré-visualização)](security-info-setup-auth-app.md)- [Configurar informações de segurança para utilizar uma chamada telefónica (pré-visualização)](security-info-setup-phone-number.md), Configurar informações de segurança para usar mensagens de [texto (pré-visualização)](security-info-setup-text-msg.md)ou configurar informações de [segurança](security-info-setup-email.md)para usar questões de [segurança (pré-visualização)](security-info-setup-questions.md).<br><br>2. O seu administrador não ligou o reset da palavra-passe para a sua organização. Nesta situação, deve selecionar o "contacto com um link de administrador" para enviar um e-mail ao seu administrador, pedindo para redefinir a sua palavra-passe.|

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre informações de segurança no artigo de visão geral da informação de [segurança (pré-visualização).](user-help-security-info-overview.md)

- Se está a tentar voltar a entrar numa conta pessoal como a Xbox, hotmail.com ou outlook.com, experimente as sugestões no Artigo da [sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
