---
title: Configurar informações de segurança (versão prévia) de seu prompt de entrada-Azure Active Directory | Microsoft Docs
description: Como configurar informações de segurança para sua conta corporativa ou de estudante, se você for solicitado na página de entrada da sua organização.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 08/05/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4ca21ecf7ad0707ad33220a8e8c2700638f8e25
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824804"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>Configurar suas informações de segurança (versão prévia) no prompt da página de entrada

Você pode seguir estas etapas se for solicitado a configurar suas informações de segurança imediatamente depois de entrar em sua conta corporativa ou de estudante.

Você só verá esse prompt se não tiver configurado as informações de segurança exigidas pela sua organização. Se você tiver configurado anteriormente suas informações de segurança, mas quiser fazer alterações, você poderá seguir as etapas nos vários artigos de instruções baseados em método. Para obter mais informações, consulte [Adicionar ou atualizar sua visão geral das informações de segurança](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>Entre em sua conta corporativa ou de estudante

Depois de entrar em sua conta corporativa ou de estudante, você verá um prompt solicitando que você forneça mais informações antes de permitir que você acesse sua conta.

![Solicitar mais informações](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Configurar suas informações de segurança usando o assistente

Siga estas etapas para configurar suas informações de segurança para sua conta corporativa ou de estudante a partir do prompt.

>[!Important]
>Esse é apenas um exemplo do processo. Dependendo dos requisitos da sua organização, o administrador pode ter configurado métodos de verificação diferentes que você precisará configurar durante esse processo. Para este exemplo, estamos exigindo dois métodos, o Microsoft Authenticator aplicativo e um número de telefone celular para chamadas de verificação ou mensagens de texto.

1. Depois de selecionar **Avançar** no prompt, um **Assistente manter sua conta** é exibido, mostrando o primeiro método que seu administrador e organização exigem que você configure. Para este exemplo, é o aplicativo Microsoft Authenticator.

   > [!Note]
   > Se você quiser usar um aplicativo autenticador diferente do Microsoft Authenticator aplicativo, selecione o link **desejo usar um aplicativo autenticador diferente** .
   >
   > Se sua organização permite que você escolha um método diferente além do aplicativo autenticador, você pode selecionar o **link desejo configurar um método diferente**.

    ![Mantenha o assistente de segurança de sua conta, mostrando a página de download do aplicativo de autenticação](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Selecione **baixar agora** para baixar e instalar o aplicativo Microsoft Authenticator em seu dispositivo móvel e, em seguida, selecione **Avançar**. Para obter mais informações sobre como baixar e instalar o aplicativo, consulte [baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Mantenha o assistente de segurança de sua conta, mostrando a página de configuração do autenticador configurar sua conta](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Permaneça na página **configurar sua conta** enquanto configura o aplicativo Microsoft Authenticator em seu dispositivo móvel.

4. Abra o aplicativo Microsoft Authenticator, selecione para permitir notificações (se solicitado), selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito e selecione **conta corporativa ou de estudante**.

5. Retorne à página **configurar sua conta** no computador e, em seguida, selecione **Avançar**.

    A página **digitalizar o código QR** é exibida.

    ![Digitalizar o código QR usando o aplicativo autenticador](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Digitalize o código fornecido com o leitor de código QR do aplicativo Microsoft Authenticator, que apareceu em seu dispositivo móvel depois de criar sua conta corporativa ou de estudante na etapa 5.

    O aplicativo autenticador deve adicionar com êxito sua conta corporativa ou de estudante sem precisar de nenhuma informação adicional de você. No entanto, se o leitor de código QR não puder ler o código, você poderá selecionar o **link não é possível digitalizar o código QR** e inserir manualmente o código e a URL no aplicativo Microsoft Authenticator. Para obter mais informações sobre como adicionar um código manualmente, consulte [adicionar manualmente uma conta ao aplicativo](user-help-auth-app-add-account-manual.md).

7. Selecione **Avançar** na página **digitalizar o código QR** em seu computador.

    Uma notificação é enviada para o aplicativo Microsoft Authenticator em seu dispositivo móvel, para testar sua conta.

    ![Testar sua conta com o aplicativo autenticador](media/security-info/securityinfo-prompt-test-app.png)

8. Aprove a notificação no aplicativo Microsoft Authenticator e, em seguida, selecione **Avançar**.

    ![Notificação de êxito, conectando o aplicativo e sua conta](media/security-info/securityinfo-prompt-auth-app-success.png)

    Suas informações de segurança são atualizadas para usar o aplicativo Microsoft Authenticator por padrão para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

9. Na página configuração do **telefone** , escolha se deseja receber uma mensagem de texto ou uma chamada telefônica e, em seguida, selecione **Avançar**. Para os fins deste exemplo, estamos usando mensagens de texto, portanto, você deve usar um número de telefone para um dispositivo que possa aceitar mensagens de texto.

    ![Comece a configurar seu número de telefone para mensagens de texto](media/security-info/securityinfo-prompt-text-msg.png)

    Uma mensagem de texto é enviada para seu número de telefone. Se preferir obter uma chamada telefônica, o processo é o mesmo. No entanto, você receberá uma chamada telefônica com instruções, em vez de uma mensagem de texto.

10. Insira o código fornecido pela mensagem de texto enviada ao seu dispositivo móvel e, em seguida, selecione **Avançar**.

    ![Teste sua conta com a mensagem de texto](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Examine a notificação de êxito e, em seguida, selecione **concluído**.

    ![Notificação de êxito](media/security-info/securityinfo-prompt-call-answered-success.png)

    Suas informações de segurança são atualizadas para usar mensagens de texto como um método de backup para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

12. Examine a página **êxito** para verificar se você configurou com êxito o método Microsoft Authenticator aplicativo e um telefone (mensagem de texto ou chamada telefônica) para suas informações de segurança e, em seguida, selecione **concluído**.

    ![O assistente concluiu a página com êxito](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Se sua organização exigir que você use senhas de aplicativo, você poderá ver uma seção adicional neste assistente, onde você pode configurá-la. Se você vir uma terceira seção, chamada **senhas de aplicativo**, será necessário preenchê-la antes de poder concluir o assistente. Para obter as etapas sobre como adicionar uma senha de aplicativo, consulte a seção [gerenciar suas senhas de aplicativo](#manage-your-app-passwords) deste artigo.

### <a name="manage-your-app-passwords"></a>Gerenciar suas senhas de aplicativo

Determinados aplicativos, como o Outlook 2010, não dão suporte à verificação em duas etapas. Essa falta de suporte significa que, se você estiver usando a verificação em duas etapas em sua organização, o aplicativo não funcionará. Para contornar esse problema, você pode criar uma senha gerada automaticamente para usar com cada aplicativo sem navegador, separado da sua senha normal.

>[!Note]
>Se você não vir essa opção no assistente, isso significa que o administrador não o configurou. Se isso não estiver configurado, mas você souber que precisa usar senhas de aplicativo, poderá seguir as etapas na [página Configurar as senhas de aplicativo da informação de segurança (versão prévia)](security-info-app-passwords.md).

Ao usar senhas de aplicativo, é importante lembrar:

- As senhas de aplicativo são geradas automaticamente e inseridas apenas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar uma após esse limite, será solicitado que você exclua uma senha existente antes de ter permissão para criar a nova.

- Use uma senha de aplicativo por dispositivo, não por aplicativo. Por exemplo, crie uma única senha para todos os aplicativos em seu laptop e, em seguida, outra senha única para todos os aplicativos na área de trabalho.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Para adicionar senhas de aplicativo no assistente de conexão

1. Depois de concluir as seções anteriores do assistente, selecione **Avançar** e conclua a seção **senha do aplicativo** .

2. Digite o nome do aplicativo que precisa da senha, por exemplo `Outlook 2010`, e selecione **Avançar**.

    ![Adicionar o nome da senha do aplicativo no assistente](media/security-info/app-password-app-password.png)

3. Copie o código de senha da tela de **senha do aplicativo** e cole-o na área de **senha** do aplicativo (neste exemplo, Outlook 2010).

    ![Página senha do aplicativo, com senha para cópia](media/security-info/app-password-copy-password.png)

4. Depois de copiar a senha e colá-la no aplicativo, retorne a esse assistente para verificar se todas as informações do método de entrada estão corretas e, em seguida, selecione **concluído**.

    ![Página senha do aplicativo, com aviso de conclusão](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Passos Seguintes

- Para alterar, excluir ou atualizar métodos de informações de segurança padrão, consulte:

    - [Configure informações de segurança para um aplicativo autenticador](security-info-setup-auth-app.md).

    - [Configurar informações de segurança para mensagens de texto](security-info-setup-text-msg.md).

    - [Configure as informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

    - [Configure as informações de segurança para usar o email](security-info-setup-email.md).

    - [Configure informações de segurança para usar perguntas de segurança](security-info-setup-questions.md)predefinidas.

- Para obter informações sobre como entrar usando o método especificado, consulte [como entrar](user-help-sign-in.md).

- Redefina sua senha se você a tiver perdido ou esquecido, no [portal](https://passwordreset.microsoftonline.com/) de redefinição de senha ou siga as etapas no artigo [redefinir sua senha corporativa ou de estudante](user-help-reset-password.md) .

- Obtenha dicas de solução de problemas e ajuda para problemas de conexão no artigo [não é possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
