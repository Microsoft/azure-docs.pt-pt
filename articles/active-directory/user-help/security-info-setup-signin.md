---
title: Configurar as suas informações de Segurança a partir de um pedido de entrada - Azure AD
description: Como configurar as suas informações de Segurança para o seu trabalho ou conta escolar, depois de ter sido solicitada a partir da página de inscrição da sua organização.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/07/2020
ms.author: curtand
ms.openlocfilehash: 7de8a5bcc2f7883b00ec25e2674b1dc6bd6871c9
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747278"
---
# <a name="set-up-your-security-info-from-a-sign-in-prompt"></a>Configurar as suas informações de Segurança a partir de um pedido de início de sessão

Pode seguir estes passos se for solicitado a configurar as suas informações de segurança imediatamente após a sua inscrição no seu trabalho ou na sua conta escolar.

Só verá este pedido se não tiver configurado as informações de segurança exigidas pela sua organização. Se já configurar previamente as suas informações de segurança, mas quiser fazer alterações, pode seguir os passos nos vários artigos baseados em métodos. Para mais informações, consulte Adicionar ou atualizar a sua visão geral da [informação](security-info-add-update-methods-overview.md)de segurança .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de reset de palavra-passe

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset de palavras-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicação de autenticação | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Mensagens de texto | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Telefonemas | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Chave de segurança | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Conta de e-mail | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |

## <a name="sign-in-to-your-work-or-school-account"></a>Inscreva-se na sua conta de trabalho ou escolar

Depois de iniciar sessão na sua conta de trabalho ou escola, verá uma solicitação que lhe pede para fornecer mais informações antes de lhe permitir aceder à sua conta.

![Pronta pedindo mais informações](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Configurar as suas informações de segurança utilizando o assistente

Siga estes passos para configurar a sua informação de segurança para o seu trabalho ou conta escolar a partir do pedido.

>[!Important]
>Este é apenas um exemplo do processo. Dependendo dos requisitos da sua organização, o seu administrador pode ter criado diferentes métodos de verificação que terá de configurar durante este processo. Para este exemplo, estamos a exigir dois métodos, a aplicação Microsoft Authenticator e um número de telemóvel para chamadas de verificação ou mensagens de texto.

1. Depois de selecionar **Seguinte** a partir do pedido, aparece um **assistente seguro de conta,** mostrando o primeiro método que o seu administrador e organização exigem que configura. Para este exemplo, é a aplicação Microsoft Authenticator.

   > [!Note]
   > Se pretender utilizar uma aplicação autenticadora diferente da aplicação Microsoft Authenticator, selecione o I pretendo utilizar um link de **aplicação autenticador diferente.**
   >
   > Se a sua organização permitir escolher um método diferente para além da aplicação autenticadora, pode selecionar o **I pretendo configurar uma ligação de método diferente**.

    ![Mantenha o assistente seguro da sua conta, mostrando a página de descarregamento da app auth](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Selecione **agora o Download** para descarregar e instalar a aplicação Microsoft Authenticator no seu dispositivo móvel e, em seguida, selecione **Next**. Para mais informações sobre como descarregar e instalar a aplicação, consulte [O Download e instale a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Mantenha o assistente seguro da sua conta, mostrando o autenticador Configurar a sua página de conta](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Mantenha-se na página de **'Configurar'** enquanto configura a aplicação Microsoft Authenticator no seu dispositivo móvel.

4. Abra a aplicação Microsoft Authenticator, selecione para permitir notificações (se solicitado), selecione **Adicionar conta** a partir do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma pergunta rápida sobre se permite que a aplicação aceda à sua câmara (iOS) ou para permitir que a aplicação tire fotografias e grave vídeos (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação do código. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

5. Volte à página da **sua conta** No computador e, em seguida, selecione **Next**.

    Aparece a página de **código QR.**

    ![Digitalizar o código QR utilizando a aplicação Authenticator](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Resumie o código fornecido com o leitor de códigoQ da aplicação Microsoft Authenticator, que apareceu no seu dispositivo móvel depois de ter criado o seu trabalho ou conta escolar no Passo 5.

    A aplicação autenticadora deve adicionar com sucesso o seu trabalho ou conta escolar sem necessitar de qualquer informação adicional sua. No entanto, se o leitor de códigoQ não conseguir ler o código, pode selecionar a **imagem QR e** introduzir manualmente o código e o URL na aplicação Microsoft Authenticator. Para obter mais informações sobre a adição manual de um código, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

7. Selecione **Em seguida** na página de **código QR** no seu computador.

    Uma notificação é enviada para a aplicação Microsoft Authenticator no seu dispositivo móvel, para testar a sua conta.

    ![Teste a sua conta com a aplicação autenticadora](media/security-info/securityinfo-prompt-test-app.png)

8. Aprove a notificação na aplicação Microsoft Authenticator e, em seguida, selecione **Next**.

    ![Notificação de sucesso, ligando a app e a sua conta](media/security-info/securityinfo-prompt-auth-app-success.png)

    A sua informação de segurança é atualizada para utilizar a aplicação Microsoft Authenticator por padrão para verificar a sua identidade ao utilizar a verificação em duas etapas ou o reset da palavra-passe.

9. Na página de configuração do **Telefone,** escolha se pretende receber uma mensagem de texto ou uma chamada telefónica e, em seguida, selecione **Next**. Para efeitos deste exemplo, estamos a utilizar mensagens de texto, por isso deve utilizar um número de telefone para um dispositivo que possa aceitar mensagens de texto.

    ![Comece a configurar o seu número de telefone para mensagens de texto](media/security-info/securityinfo-prompt-text-msg.png)

    Uma mensagem de texto é enviada para o seu número de telefone. Se preferir receber um telefonema, o processo é o mesmo. No entanto, receberá um telefonema com instruções, em vez de uma mensagem de texto.

10. Introduza o código fornecido pela mensagem de texto enviada para o seu dispositivo móvel e, em seguida, selecione **Next**.

    ![Teste a sua conta com a mensagem de texto](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Reveja a notificação de sucesso e, em seguida, selecione **Done**.

    ![Notificação de sucesso](media/security-info/securityinfo-prompt-call-answered-success.png)

    A sua informação de segurança é atualizada para utilizar mensagens de texto como um método de cópia de segurança para verificar a sua identidade ao utilizar a verificação em duas etapas ou o reset da palavra-passe.

12. Reveja a página **de Sucesso** para verificar se configura com sucesso tanto a aplicação Microsoft Authenticator como um método de telefone (mensagem de texto ou chamada telefónica) para as suas informações de segurança e, em seguida, selecione **Done**.

    ![Página completada com sucesso do Assistente](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Se a sua organização necessitar de usar palavras-passe de aplicação, poderá ver uma secção adicional neste assistente, onde poderá configurar. Se vir uma terceira secção, chamada **palavras-passe da App,** deve preenchê-la antes de poder completar o assistente. Para obter passos sobre como adicionar uma palavra-passe da aplicação, consulte a secção de passwords da [sua aplicação](#manage-your-app-passwords) deste artigo.

### <a name="manage-your-app-passwords"></a>Gerir as suas palavras-passe da sua aplicação

Algumas aplicações, como o Outlook 2010, não suportam a verificação em duas etapas. Esta falta de suporte significa que se estiver a usar uma verificação em duas etapas na sua organização, a aplicação não funcionará. Para contornar este problema, pode criar uma senha autogerada para usar em cada aplicação não-navegador, separada da sua senha normal.

>[!Note]
>Se não vir esta opção no assistente, significa que o seu administrador não a montou. Se isto não estiver configurado, mas sabe que precisa de usar palavras-passe de aplicação, pode seguir os passos nas [palavras-passe da aplicação Configurar a partir da página de informações de Segurança](security-info-app-passwords.md).

Ao utilizar palavras-passe de aplicativos, é importante lembrar:

- As palavras-passe das aplicações são autogeradas e só inseridas uma vez por aplicação.

- Há um limite de 40 senhas por utilizador. Se tentar criar uma após esse limite, será-lhe solicitado que apague uma palavra-passe existente antes de ser autorizada a criar a nova.

- Utilize uma palavra-passe de aplicação por dispositivo, não por app. Por exemplo, crie uma única palavra-passe para todas as aplicações no seu portátil e, em seguida, outra palavra-passe única para todas as aplicações no seu ambiente de trabalho.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Para adicionar palavras-passe de aplicativo no assistente de entrada

1. Depois de terminar as secções anteriores do assistente, selecione **Next** e complete a secção de **palavra-passe** da App.

2. Digite o nome da aplicação que precisa da palavra-passe, por `Outlook 2010` exemplo, e depois selecione **Next**.

    ![Adicione o nome da palavra-passe da aplicação no assistente](media/security-info/app-password-app-password.png)

3. Copie o código de palavra-passe do **Password** ecrã de **palavra-passe** da App e cole-o na área password da aplicação (neste exemplo, Outlook 2010).

    ![Página de palavra-passe de aplicativo, com palavra-passe para cópia](media/security-info/app-password-copy-password.png)

4. Depois de copiar a palavra-passe e colá-la na aplicação, volte a este assistente para se certificar de que todas as informações do seu método de entrada estão corretas e, em seguida, selecione **Done**.

    ![Página de palavra-passe de aplicativo, com aviso de conclusão](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Passos seguintes

- Para alterar, eliminar ou atualizar métodos de informação de segurança predefinidos, consulte:

    - [Configurar informações de segurança para uma aplicação autenticadora.](security-info-setup-auth-app.md)

    - [Configurar informações de segurança para mensagens de texto](security-info-setup-text-msg.md).

    - [Configurar informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

    - [Configurar informações de segurança para usar e-mail](security-info-setup-email.md).

    - [Configurar informações de segurança para utilizar questões de segurança pré-definidas](security-info-setup-questions.md).

- Para obter informações sobre como iniciar sessão utilizando o seu método especificado, consulte [Como iniciar sessão](user-help-sign-in.md).

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
