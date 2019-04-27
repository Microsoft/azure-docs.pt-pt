---
title: Repor a palavra-passe - Azure Active Directory | Documentos da Microsoft
description: Usar senhas de auto-atendimento repor para recuperar o acesso ao seu trabalho ou escolares de conta de utilizador
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.custom: end-user
ms.collection: M365-identity-device-management
ms.openlocfilehash: d16b54df5b8392a294036978e0b917ecdb3ce1ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60862266"
---
# <a name="reset-your-work-or-school-password"></a>Repor a palavra-passe profissional ou escolar

Se esqueceu-se a sua palavra-passe, nunca recebeu uma do suporte da empresa, estão bloqueadas partido da sua conta ou quiser alterá-la, podemos ajudar. Se já sabe a palavra-passe e só precisa de alterá-la, avance para o [alterar a minha palavra-passe](#change-my-password) secção.

   > [!NOTE]
   > Se estiver a tentar aceder novamente à sua conta pessoal, como a Xbox, hotmail.com ou outlook.com, experimente as sugestões na [quando não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Repor ou desbloquear a palavra-passe de uma conta escolar ou profissional

Poderá não conseguir aceder à sua conta do Azure Active Directory (Azure AD) devido a uma das seguintes razões:

* A palavra-passe não está a funcionar e pretende repô-lo.
* Sabe a palavra-passe, mas sua conta está bloqueada e quiser desbloqueá-lo.

Utilize os seguintes passos para aceder a reposição de palavra-passe self-service do Azure AD (SSPR) e aceder novamente à sua conta.

1. De qualquer profissional ou escolar **início de sessão** página, selecione a **não é possível aceder à sua conta?** associar e, em seguida, selecione **trabalhar conta escolar ou profissional** ou aceda diretamente ao [ Página de reposição de palavra-passe](https://passwordreset.microsoftonline.com/).

    ![Não consegue aceder à sua conta?][Login]

2. Introduza a sua empresa ou escola **ID de utilizador**, prove que não é um robot, introduza os carateres, ver na tela e, em seguida, selecione **próxima**.

   > [!NOTE]
   > Se sua equipe de TI não tiver ativado esta funcionalidade, é apresentada uma ligação "Contacte o administrador" para que sua equipe de TI pode ajudar a por e-mail ou um portal web seus próprios.
   >
   > Se precisar de desbloquear a sua conta, selecione, neste momento, a opção **saiba minha senha, mas ainda não é possível iniciar sessão.**
   >

3. Dependendo de como sua equipe de TI tiver configurado o SSPR deverá ver um ou mais dos seguintes métodos de autenticação. Ou sua equipe de TI deve ter preenchido algumas destas informações ao seguir os passos a [Registre-se para a reposição de palavra-passe self-service](active-directory-passwords-reset-register.md) artigo.

   * **Enviar e-mail para o meu e-mail alternativo**
   * **Enviar mensagem de texto para o meu telemóvel**
   * **Ligar para o meu telemóvel**
   * **Ligar para o meu telefone do escritório**
   * **Responder às minhas perguntas de segurança**

   Escolha uma opção, forneça as respostas corretas e, em seguida, selecione **seguinte**.

   ![Verifique os dados de autenticação][Verification]

4. Sua equipe de TI poderá precisar de mais confirmações, e pode ter de repetir o passo 3 com outra opção.
5. Sobre o **escolher uma nova palavra-passe** página, introduza uma nova palavra-passe, confirme a palavra-passe e, em seguida, selecione **concluir**. A palavra-passe profissional ou escolar pode ter determinados requisitos que precisam de cumprir. Sugerimos que escolha uma palavra-passe é 8 a 16 carateres de comprimento e inclui caracteres maiúsculos e minúsculos, um número e um caráter especial.
6. Quando vir a mensagem **a palavra-passe foi reposta**, pode iniciar sessão com a nova palavra-passe.

    ![A palavra-passe do utilizador foi reposta][Complete]

Deve agora conseguir aceder à sua conta. Se não é possível aceder à sua conta, deve contactar da sua organização a equipe de TI para obter mais ajuda.

Poderá receber um e-mail de confirmação proveniente de uma conta como "Microsoft em nome de \<sua organização >." Se receber um e-mail como este e não utilizou o self-service reposição palavra-passe para recuperar o acesso à sua conta, contacte da sua organização a equipe de TI.

## <a name="change-my-password"></a>Alterar a minha palavra-passe

Se já sabe que a palavra-passe e quiser alterá-la, utilize os seguintes passos.

### <a name="change-your-password-from-the-office-365-portal"></a>Alterar a palavra-passe a partir do portal do Office 365

Utilize este método se normalmente aceder às suas aplicações através do portal do Office:

1. Inicie sessão no seu [conta do Office 365](https://www.office.com) com a palavra-passe existente.
2. Selecione o seu perfil no lado do canto superior direito e, em seguida, selecione **ver conta**.
3. Selecione **segurança e privacidade** > **palavra-passe**.
4. Introduza a palavra-passe antiga, definir e confirmar a sua nova palavra-passe e, em seguida, selecione **submeter**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Alterar a palavra-passe a partir do Painel de Acesso do Azure

Utilize este método se aceder às normalmente seus aplicativos de painel de acesso do Azure (MyApps):

1. Inicie sessão para o [painel de acesso do Azure](https://myapps.microsoft.com/) com a palavra-passe existente.
2. Selecione o seu perfil no lado do canto superior direito e, em seguida, selecione **perfil**.
3. Selecione **alterar palavra-passe**.
4. Introduza a palavra-passe antiga, definir e confirmar a sua nova palavra-passe e, em seguida, selecione **submeter**.

## <a name="reset-password-at-sign-in"></a>Repor palavra-passe no início de sessão

Se o administrador tiver ativado a funcionalidade, agora, pode ver uma ligação para **Repor palavra-passe** no seu ecrã de início de sessão do Windows 10 Fall Creators Update.

![Ecrã de início de sessão][LoginScreen]

Selecione o **Repor palavra-passe** link para abrir a experiência SSPR no ecrã de início de sessão para que pode redefinir a palavra-passe sem ter de iniciar sessão aceder a experiência baseada na web normal.

1. Confirme o seu ID de utilizador e selecione **seguinte**.
2. Selecione e confirme um método de contacto para verificação. Sua equipe de TI poderá precisar de mais confirmações, e pode ter de repetir este passo com outra opção.

   ![Método de contacto][ContactMethod]

3. Sobre o **criar uma nova palavra-passe** página, introduza uma nova palavra-passe, confirme a palavra-passe e, em seguida, selecione **próxima**. Sugerimos que a palavra-passe é 8 a 16 carateres de comprimento e é composto por letras maiúsculas e minúsculas, números e carateres especiais.

   ![Repor palavra-passe][ResetPassword]

4. Quando vir a mensagem **a palavra-passe foi reposta**, selecione **concluir**.

Deve agora conseguir aceder à sua conta. Se não, contacte a sua organização a equipe de TI para obter mais ajuda.

## <a name="common-problems-and-their-solutions"></a>Problemas comuns e as suas soluções

 Aqui estão alguns casos de erro comuns e as respectivas soluções:

| Caso de erro| Que erro vê?| Solução |
| --- | --- | --- |
| Posso ver um erro ao tentar alterar a minha palavra-passe. | Infelizmente, a sua palavra-passe contém uma palavra, frase ou padrão que faz com que a palavra-passe guessable facilmente. Tente novamente com uma palavra-passe diferente. | Escolha uma palavra-passe que é mais difícil de adivinhar. |
| Posso obter uma página "Contacte o administrador" depois de introduzir o meu ID de utilizador | Contacte o administrador. <br> <br> Detetámos que a palavra-passe da conta de utilizador não é gerida pela Microsoft. Como resultado, estamos não é possível repor automaticamente a sua palavra-passe. <br> <br> Terá de contactar o seu departamento de TI para obter mais assistência. | Está a ver esta mensagem porque o seu departamento de TI gere a sua palavra-passe no seu ambiente no local. Não é possível repor a palavra-passe da ligação "Não é possível aceder à sua conta". <br> <br> Para repor a palavra-passe, contacte o seu departamento de TI diretamente para obter ajuda e informá-los de que pretende repor a palavra-passe, de modo que podem ativar esta funcionalidade para.|
| Recebo um erro "o sua conta não está ativada para a reposição de palavra-passe" depois de introduzir o meu ID de utilizador | Sua conta não está ativada para a reposição de palavra-passe. <br> <br> Lamentamos, mas sua equipe de TI não definiu a sua conta a utilizar este serviço. <br> <br> Se desejar, podemos pode contactar um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque o seu departamento de TI não tiver ativado a reposição para a sua organização a partir da hiperligação "Não é possível aceder à sua conta" palavra-passe, ou ainda não licenciado para utilizar a funcionalidade. <br> <br> Para repor a palavra-passe, selecione "Contacte uma ligação de administrador" para enviar um e-mail para a sua empresa da equipe de TI e informá-los de que pretende repor a palavra-passe, de modo que podem ativar esta funcionalidade para. |
| Recebo um erro "Não foi possível verificar a conta" depois de introduzir o meu ID de utilizador | Não foi possível verificar a sua conta. <br> <br> Se desejar, podemos pode contactar um administrador na sua organização para repor a palavra-passe por si. | Está a ver esta mensagem porque está ativada para a reposição de palavra-passe, mas não se registou para utilizar o serviço. Para se registar na reposição de palavra-passe, vá para https://aka.ms/ssprsetup depois de ter voltou a ter acesso à sua conta. <br> <br> Para repor a palavra-passe, selecione a ligação "Contacte o administrador" para enviar um e-mail para a sua empresa a equipe de TI. |

## <a name="next-steps"></a>Passos Seguintes

* [Como se registar para utilizar a reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Página de registo de reposição de palavra-passe](https://aka.ms/ssprsetup)
* [Portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com/)
* [Não consegue iniciar sessão com a sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Página de início de sessão Não consegue aceder à sua conta?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Verifique os dados de autenticação"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Alterar a palavra-passe"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "A palavra-passe do utilizador foi reposta"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Ligação de palavra-passe de reposição de ecrã de início de sessão do Windows 10 Fall Creators Update"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Verifique os dados de autenticação"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Alterar a palavra-passe"
