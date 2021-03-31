---
title: Confiúva a sua informação de Segurança a partir de um pedido de inscrição - Azure AD
description: Como configurar a sua informação de Segurança para o seu trabalho ou conta escolar, depois de ser solicitado a partir da página de login da sua organização.
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
ms.openlocfilehash: 016e0de26bf3a9513940be161cfecf6d1c2b9d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88798504"
---
# <a name="set-up-your-security-info-from-a-sign-in-prompt"></a>Configurar a sua informação de Segurança a partir de um pedido de inscrição

Pode seguir estes passos se for solicitado que crie a sua informação de segurança imediatamente após o seu login na sua conta de trabalho ou escola.

Só verá esta solicitação se não tiver configurado as informações de segurança exigidas pela sua organização. Se já definiu previamente as suas informações de segurança, mas pretende fazer alterações, pode seguir os passos nos vários artigos baseados em métodos. Para mais informações, consulte [Adicionar ou atualizar a sua visão geral das informações de segurança.](./security-info-setup-auth-app.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Verificação de segurança versus autenticação de reset de palavra-passe

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset da palavra-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| App autenticador | Verificação de dois fatores e autenticação de redefinição de password. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de password. |
| Chamadas telefónicas | Verificação de dois fatores e autenticação de redefinição de password. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de password. |
| Conta de e-mail | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |

## <a name="sign-in-to-your-work-or-school-account"></a>Inscreva-se na sua conta de trabalho ou escola

Depois de iniciar sedutar na sua conta de trabalho ou escola, verá uma solicitação que lhe pede para fornecer mais informações antes de lhe permitir aceder à sua conta.

![Pedir mais informações](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Configurar a sua informação de segurança utilizando o assistente

Siga estes passos para configurar a sua informação de segurança para o seu trabalho ou conta escolar a partir do aviso.

>[!Important]
>Este é apenas um exemplo do processo. Dependendo dos requisitos da sua organização, o seu administrador pode ter configurado diferentes métodos de verificação que terá de configurar durante este processo. Para este exemplo, estamos a exigir dois métodos, a aplicação Microsoft Authenticator e um número de telemóvel para chamadas de verificação ou mensagens de texto.

1. Depois de selecionar **Next** a partir da solicitação, aparece um **assistente de segurança da sua conta,** mostrando o primeiro método que o seu administrador e organização exigem que você crie. Para este exemplo, é a aplicação Microsoft Authenticator.

   > [!Note]
   > Se pretender utilizar uma aplicação autenticadora diferente da aplicação Microsoft Authenticator, selecione o I wanna use um link **de aplicação de autenticador diferente.**
   >
   > Se a sua organização permitir que escolha um método diferente para além da aplicação autenticadora, pode selecionar o **I want para configurar uma ligação de método diferente.**

    ![Mantenha o assistente seguro da sua conta, mostrando a página de descarregamento da aplicação Auth](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Selecione **Baixar agora** para descarregar e instalar a aplicação Microsoft Authenticator no seu dispositivo móvel e, em seguida, selecione **Next**. Para obter mais informações sobre como descarregar e instalar a aplicação, consulte [Download e instale a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Mantenha o seu assistente seguro de conta, mostrando o autenticador Configurar a sua página de conta](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Permaneça na página **'Configurar' a sua conta** enquanto configura a aplicação Microsoft Authenticator no seu dispositivo móvel.

4. Abra a aplicação Microsoft Authenticator, selecione para permitir notificações (se solicitada), selecione **Adicionar a conta** a partir do ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione Trabalho ou conta **escolar**.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma solicitação a perguntar se permite que a app aceda à sua câmara (iOS) ou se permite que a app tire fotografias e grave vídeos (Android). Tem de selecionar **Permitir** que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no passo seguinte. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação de código. Para obter informações sobre como adicionar o código manualmente, consulte [manualmente adicionar uma conta à aplicação.](user-help-auth-app-add-account-manual.md)

5. Volte à página **De Configurar a sua conta** no seu computador e, em seguida, selecione **Seguinte**.

    Aparece a página **de código QR.**

    ![Digitalize o código QR utilizando a aplicação Authenticator](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Verifique o código fornecido com o leitor de código QR da aplicação Microsoft Authenticator, que apareceu no seu dispositivo móvel depois de ter criado a sua conta de trabalho ou escola no Passo 5.

    A aplicação autenticadora deve adicionar com sucesso o seu trabalho ou conta escolar sem necessitar de qualquer informação adicional da sua parte. No entanto, se o leitor de código QR não conseguir ler o código, pode selecionar **a imagem QR** e introduzir manualmente o código e o URL na aplicação Microsoft Authenticator. Para obter mais informações sobre a adição manual de um código, consulte [Manualmente adicionar uma conta à aplicação.](user-help-auth-app-add-account-manual.md)

7. Selecione **Seguinte** na página **de código QR** no seu computador.

    É enviada uma notificação para a aplicação Microsoft Authenticator no seu dispositivo móvel, para testar a sua conta.

    ![Teste a sua conta com a app autenticadora](media/security-info/securityinfo-prompt-test-app.png)

8. Aprove a notificação na aplicação Microsoft Authenticator e, em seguida, selecione **Next**.

    ![Notificação de sucesso, ligando a app e a sua conta](media/security-info/securityinfo-prompt-auth-app-success.png)

    As suas informações de segurança são atualizadas para utilizar a aplicação Microsoft Authenticator por padrão para verificar a sua identidade quando utilizar a verificação em duas etapas ou o reset da palavra-passe.

9. Na página configurada do **Telefone,** escolha se deseja receber uma mensagem de texto ou uma chamada telefónica e, em seguida, selecione **Next**. Para efeitos deste exemplo, estamos a usar mensagens de texto, por isso deve utilizar um número de telefone para um dispositivo que possa aceitar mensagens de texto.

    ![Comece a configurar o seu número de telefone para mensagens de texto](media/security-info/securityinfo-prompt-text-msg.png)

    Uma mensagem de texto é enviada para o seu número de telefone. Se preferir receber uma ligação, o processo é o mesmo. No entanto, receberá uma chamada telefónica com instruções, em vez de uma mensagem de texto.

10. Introduza o código fornecido pela mensagem de texto enviada para o seu dispositivo móvel e, em seguida, selecione **Seguinte**.

    ![Teste a sua conta com a mensagem de texto](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Reveja a notificação de sucesso e, em seguida, **selecione Feito**.

    ![Notificação de sucesso](media/security-info/securityinfo-prompt-call-answered-success.png)

    A sua informação de segurança é atualizada para utilizar mensagens de texto como método de backup para verificar a sua identidade ao utilizar a verificação em duas etapas ou o reset da palavra-passe.

12. Reveja a página **Sucesso** para verificar se criou com sucesso tanto a aplicação microsoft Authenticator como um método de telefone (mensagem de texto ou chamada telefónica) para as suas informações de segurança e, em seguida, selecione **'Fazer'.**

    ![Página completada com sucesso do assistente](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Se a sua organização exigir que utilize senhas de aplicação, poderá ver uma secção adicional neste assistente, onde pode configurar. Se vir uma terceira secção, chamada **passwords app,** tem de preenchê-la antes de conseguir completar o assistente. Para obter etapas sobre como adicionar uma palavra-passe de aplicação, consulte a secção de senhas da [sua aplicação](#manage-your-app-passwords) deste artigo.

### <a name="manage-your-app-passwords"></a>Gerencie as suas palavras-passe de aplicação

Algumas aplicações, como o Outlook 2010, não suportam a verificação em duas etapas. Esta falta de suporte significa que se estiver a utilizar uma verificação em duas etapas na sua organização, a aplicação não funcionará. Para contornar este problema, pode criar uma palavra-passe autogerada para usar com cada aplicação não-browser, separada da sua senha normal.

>[!Note]
>Se não vires esta opção no assistente, significa que o teu administrador ainda não a tramou. Se isto não estiver configurado, mas sabe que precisa de usar senhas de aplicação, pode seguir os passos nas [palavras-passe da aplicação Configurar a partir da página de informações de Segurança](security-info-app-passwords.md).

Ao utilizar palavras-passe de aplicações, é importante lembrar:

- As palavras-passe da aplicação são autogeridas e inseridas apenas uma vez por aplicação.

- Há um limite de 40 palavras-passe por utilizador. Se tentar criar um após esse limite, será solicitado a apagar uma palavra-passe existente antes de ser autorizado a criar a nova.

- Use uma palavra-passe de aplicação por dispositivo, não por aplicação. Por exemplo, crie uma única palavra-passe para todas as aplicações no seu portátil e, em seguida, outra única palavra-passe para todas as aplicações no seu ambiente de trabalho.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Para adicionar palavras-passe de aplicação no assistente de entrada

1. Depois de terminar as secções anteriores do assistente, selecione **Next** e preencha a secção **de palavra-passe da App.**

2. Digite o nome da aplicação que necessita da palavra-passe, por `Outlook 2010` exemplo, e, em seguida, selecione **Next**.

    ![Adicione o nome da palavra-passe da aplicação no assistente](media/security-info/app-password-app-password.png)

3. Copie o código de senha do ecrã da **palavra-passe da App** e cole-o na área de **Password** da aplicação (neste exemplo, Outlook 2010).

    ![Página de senha de aplicação, com senha para cópia](media/security-info/app-password-copy-password.png)

4. Depois de copiar a palavra-passe e colar na aplicação, volte a este assistente para se certificar de que todas as informações do seu método de entrada estão corretas e, em seguida, selecione **Fazer**.

    ![Página de senha de aplicação, com aviso de conclusão](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Passos seguintes

- Para alterar, eliminar ou atualizar métodos de informação de segurança predefinidos, consulte:

    - [Configurar informações de segurança para uma aplicação autenticadora.](security-info-setup-auth-app.md)

    - [Configurar informações de segurança para mensagens de texto.](security-info-setup-text-msg.md)

    - [Configurar informações de segurança para utilizar chamadas telefónicas.](security-info-setup-phone-number.md)

    - [Configurar informações de segurança para usar o e-mail.](security-info-setup-email.md)

    - [Configurar informações de segurança para utilizar questões de segurança pré-definidas.](security-info-setup-questions.md)

- Para obter informações sobre como iniciar seduções utilizando o seu método especificado, consulte [Como iniciar sedução](user-help-sign-in.md).

- Reinicie a sua palavra-passe se a tiver perdido ou esquecido, a partir do [portal de reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de Reposição do seu trabalho ou [palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da Conta Da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)