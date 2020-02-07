---
title: Configurar suas informações de segurança (versão prévia) de um prompt de entrada-Azure AD
description: Como configurar suas informações de segurança (versão prévia) para sua conta corporativa ou de estudante, depois que você for solicitado na página de entrada da sua organização.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 08/05/2019
ms.author: curtand
ms.openlocfilehash: c216dbfef99422fc49fde774dc57d5cbcc9f879a
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063990"
---
# <a name="set-up-your-security-info-preview-from-a-sign-in-prompt"></a>Configurar suas informações de segurança (versão prévia) de um prompt de entrada

Você pode seguir estas etapas se for solicitado a configurar suas informações de segurança imediatamente depois de entrar em sua conta corporativa ou de estudante.

Você só verá esse prompt se não tiver configurado as informações de segurança exigidas pela sua organização. Se você tiver configurado anteriormente suas informações de segurança, mas quiser fazer alterações, você poderá seguir as etapas nos vários artigos de instruções baseados em método. Para mais informações, consulte Adicionar ou atualizar a sua visão geral da [informação](security-info-add-update-methods-overview.md)de segurança .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de redefinição de senha

Os métodos de informações de segurança são usados para a verificação de segurança de dois fatores e para a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicativo autenticador | Verificação de dois fatores e autenticação de redefinição de senha. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chamadas telefônicas | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de senha. |
| Conta de email | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |
| Questões de segurança | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |

## <a name="sign-in-to-your-work-or-school-account"></a>Entre em sua conta corporativa ou de estudante

Depois de entrar em sua conta corporativa ou de estudante, você verá um prompt solicitando que você forneça mais informações antes de permitir que você acesse sua conta.

![Solicitar mais informações](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Configurar suas informações de segurança usando o assistente

Siga estas etapas para configurar suas informações de segurança para sua conta corporativa ou de estudante a partir do prompt.

>[!Important]
>Esse é apenas um exemplo do processo. Dependendo dos requisitos da sua organização, o administrador pode ter configurado métodos de verificação diferentes que você precisará configurar durante esse processo. Para este exemplo, estamos exigindo dois métodos, o Microsoft Authenticator aplicativo e um número de telefone celular para chamadas de verificação ou mensagens de texto.

1. Depois de selecionar **Seguinte** a partir do pedido, aparece um **assistente seguro de conta,** mostrando o primeiro método que o seu administrador e organização exigem que configura. Para este exemplo, é o aplicativo Microsoft Authenticator.

   > [!Note]
   > Se pretender utilizar uma aplicação autenticadora diferente da aplicação Microsoft Authenticator, selecione o I pretendo utilizar um link de **aplicação autenticador diferente.**
   >
   > Se a sua organização permitir escolher um método diferente para além da aplicação autenticadora, pode selecionar o **I pretendo configurar uma ligação de método diferente**.

    ![Mantenha o assistente de segurança de sua conta, mostrando a página de download do aplicativo de autenticação](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Selecione **agora o Download** para descarregar e instalar a aplicação Microsoft Authenticator no seu dispositivo móvel e, em seguida, selecione **Next**. Para mais informações sobre como descarregar e instalar a aplicação, consulte [O Download e instale a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Mantenha o assistente de segurança de sua conta, mostrando a página de configuração do autenticador configurar sua conta](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Mantenha-se na página de **'Configurar'** enquanto configura a aplicação Microsoft Authenticator no seu dispositivo móvel.

4. Abra a aplicação Microsoft Authenticator, selecione para permitir notificações (se solicitado), selecione **Adicionar conta** a partir do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

5. Volte à página da **sua conta** No computador e, em seguida, selecione **Next**.

    Aparece a página de **código QR.**

    ![Digitalizar o código QR usando o aplicativo autenticador](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Digitalize o código fornecido com o leitor de código QR do aplicativo Microsoft Authenticator, que apareceu em seu dispositivo móvel depois de criar sua conta corporativa ou de estudante na etapa 5.

    O aplicativo autenticador deve adicionar com êxito sua conta corporativa ou de estudante sem precisar de nenhuma informação adicional de você. No entanto, se o leitor de códigoQ não conseguir ler o código, pode selecionar a **imagem QR e** introduzir manualmente o código e o URL na aplicação Microsoft Authenticator. Para obter mais informações sobre a adição manual de um código, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

7. Selecione **Em seguida** na página de **código QR** no seu computador.

    Uma notificação é enviada para o aplicativo Microsoft Authenticator em seu dispositivo móvel, para testar sua conta.

    ![Testar sua conta com o aplicativo autenticador](media/security-info/securityinfo-prompt-test-app.png)

8. Aprove a notificação na aplicação Microsoft Authenticator e, em seguida, selecione **Next**.

    ![Notificação de êxito, conectando o aplicativo e sua conta](media/security-info/securityinfo-prompt-auth-app-success.png)

    Suas informações de segurança são atualizadas para usar o aplicativo Microsoft Authenticator por padrão para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

9. Na página de configuração do **Telefone,** escolha se pretende receber uma mensagem de texto ou uma chamada telefónica e, em seguida, selecione **Next**. Para os fins deste exemplo, estamos usando mensagens de texto, portanto, você deve usar um número de telefone para um dispositivo que possa aceitar mensagens de texto.

    ![Comece a configurar seu número de telefone para mensagens de texto](media/security-info/securityinfo-prompt-text-msg.png)

    Uma mensagem de texto é enviada para seu número de telefone. Se preferir obter uma chamada telefônica, o processo é o mesmo. No entanto, você receberá uma chamada telefônica com instruções, em vez de uma mensagem de texto.

10. Introduza o código fornecido pela mensagem de texto enviada para o seu dispositivo móvel e, em seguida, selecione **Next**.

    ![Teste sua conta com a mensagem de texto](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Reveja a notificação de sucesso e, em seguida, selecione **Done**.

    ![Notificação de êxito](media/security-info/securityinfo-prompt-call-answered-success.png)

    Suas informações de segurança são atualizadas para usar mensagens de texto como um método de backup para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

12. Reveja a página **de Sucesso** para verificar se configura com sucesso tanto a aplicação Microsoft Authenticator como um método de telefone (mensagem de texto ou chamada telefónica) para as suas informações de segurança e, em seguida, selecione **Done**.

    ![O assistente concluiu a página com êxito](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Se sua organização exigir que você use senhas de aplicativo, você poderá ver uma seção adicional neste assistente, onde você pode configurá-la. Se vir uma terceira secção, chamada **palavras-passe da App,** deve preenchê-la antes de poder completar o assistente. Para obter passos sobre como adicionar uma palavra-passe da aplicação, consulte a secção de passwords da [sua aplicação](#manage-your-app-passwords) deste artigo.

### <a name="manage-your-app-passwords"></a>Gerenciar suas senhas de aplicativo

Determinados aplicativos, como o Outlook 2010, não dão suporte à verificação em duas etapas. Essa falta de suporte significa que, se você estiver usando a verificação em duas etapas em sua organização, o aplicativo não funcionará. Para contornar esse problema, você pode criar uma senha gerada automaticamente para usar com cada aplicativo sem navegador, separado da sua senha normal.

>[!Note]
>Se você não vir essa opção no assistente, isso significa que o administrador não o configurou. Se isto não estiver configurado, mas sabe que precisa de usar palavras-passe de aplicação, pode seguir os passos nas [palavras-passe da aplicação Configurar a partir da página informação de Segurança (pré-visualização).](security-info-app-passwords.md)

Ao usar senhas de aplicativo, é importante lembrar:

- As senhas de aplicativo são geradas automaticamente e inseridas apenas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar uma após esse limite, será solicitado que você exclua uma senha existente antes de ter permissão para criar a nova.

- Use uma senha de aplicativo por dispositivo, não por aplicativo. Por exemplo, crie uma única senha para todos os aplicativos em seu laptop e, em seguida, outra senha única para todos os aplicativos na área de trabalho.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Para adicionar senhas de aplicativo no assistente de conexão

1. Depois de terminar as secções anteriores do assistente, selecione **Next** e complete a secção de **palavra-passe** da App.

2. Digite o nome da aplicação que precisa da palavra-passe, por exemplo, `Outlook 2010`, e depois selecione **Next**.

    ![Adicionar o nome da senha do aplicativo no assistente](media/security-info/app-password-app-password.png)

3. Copie o código de palavra-passe do ecrã de **palavra-passe** da App e cole-o na área password da aplicação (neste exemplo, Outlook 2010).

    ![Página senha do aplicativo, com senha para cópia](media/security-info/app-password-copy-password.png)

4. Depois de copiar a palavra-passe e colá-la na aplicação, volte a este assistente para se certificar de que todas as informações do seu método de entrada estão corretas e, em seguida, selecione **Done**.

    ![Página senha do aplicativo, com aviso de conclusão](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Passos seguintes

- Para alterar, excluir ou atualizar métodos de informações de segurança padrão, consulte:

    - [Configurar informações de segurança para uma aplicação autenticadora.](security-info-setup-auth-app.md)

    - [Configurar informações de segurança para mensagens de texto](security-info-setup-text-msg.md).

    - [Configurar informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

    - [Configurar informações de segurança para usar e-mail](security-info-setup-email.md).

    - [Configurar informações de segurança para utilizar questões de segurança pré-definidas](security-info-setup-questions.md).

- Para obter informações sobre como iniciar sessão utilizando o seu método especificado, consulte [Como iniciar sessão](user-help-sign-in.md).

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
