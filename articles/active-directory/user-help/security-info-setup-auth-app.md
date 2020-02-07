---
title: Configurar o aplicativo Microsoft Authenticator como seu método de verificação-Azure AD
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade usando o aplicativo Microsoft Authenticator como seu método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 16cb512d3f8f8afcc199cb52c13e09b12107576f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062426"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Configurar o aplicativo Microsoft Authenticator como seu método de verificação

Você pode seguir estas etapas para adicionar os métodos de verificação de dois fatores e de redefinição de senha. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

Se for solicitado que o instale imediatamente após iniciar sessão na sua conta de trabalho ou escola, consulte os passos detalhados no Conjunto de informações de segurança a partir do artigo de solicitação da página de início de [sessão.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Se você não vir a opção de aplicativo autenticador, é possível que sua organização não permita que você use essa opção para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais assistência.

## <a name="security-vs-password-reset-verification"></a>Segurança vs. verificação de redefinição de senha

Os métodos de informações de segurança são usados para a verificação de segurança de dois fatores e para a redefinição de senha. No entanto, nem todos os métodos podem ser usados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicativo autenticador | Verificação de dois fatores e autenticação de redefinição de senha. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chamadas telefônicas | Verificação de dois fatores e autenticação de redefinição de senha. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de senha. |
| Conta de email | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |
| Questões de segurança | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configurar o aplicativo Microsoft Authenticator na página informações de segurança

Dependendo das configurações da sua organização, você poderá usar um aplicativo de autenticação como um dos seus métodos de informações de segurança. Você não precisa usar o aplicativo Microsoft Authenticator e pode escolher um aplicativo diferente durante o processo de configuração. No entanto, este artigo usa o aplicativo Microsoft Authenticator.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Para configurar o aplicativo Microsoft Authenticator

1. Inscreva-se na sua conta de trabalho ou escola e depois vá à sua página de https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **aplicação Autenticador** a partir da lista de lançamentos e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com o aplicativo autenticador selecionado](media/security-info/securityinfo-myprofile-addauthapp.png)

4. No **Início, obtendo a página da aplicação,** selecione **Descarregar agora** para descarregar e instalar a aplicação Microsoft Authenticator no seu dispositivo móvel e, em seguida, selecione **Next**.

    Para mais informações sobre como descarregar e instalar a aplicação, consulte [O Download e instale a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Comece obtendo a página do aplicativo](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Se pretender utilizar uma aplicação autenticadora diferente da aplicação Microsoft Authenticator, selecione o I pretendo utilizar um link de **aplicação autenticador diferente.**
   >
   > Se a sua organização permitir escolher um método diferente para além da aplicação autenticadora, pode selecionar o **I pretendo configurar uma ligação de método diferente**.

5. Mantenha-se na página de **'Configurar'** enquanto configura a aplicação Microsoft Authenticator no seu dispositivo móvel.

    ![Configurar a página do aplicativo autenticador](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Abra a aplicação Microsoft Authenticator, selecione para permitir notificações (se solicitado), selecione **Adicionar conta** a partir do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

7. Volte à página da **sua conta** No computador e, em seguida, selecione **Next**.

    Aparece a página de **código QR.**

    ![Digitalizar o código QR usando o aplicativo autenticador](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Digitalize o código fornecido com o leitor de código QR do aplicativo Microsoft Authenticator, que apareceu em seu dispositivo móvel depois de criar sua conta corporativa ou de estudante na etapa 6.

    O aplicativo autenticador deve adicionar com êxito sua conta corporativa ou de estudante sem precisar de nenhuma informação adicional de você. No entanto, se o leitor de códigoQ não conseguir ler o código, pode selecionar o link de **código QR e** introduzir manualmente o código e o URL na aplicação Microsoft Authenticator. Para obter mais informações sobre a adição manual de um código, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

9. Selecione **Em seguida** na página de **código QR** no seu computador.

    Uma notificação é enviada para o aplicativo Microsoft Authenticator em seu dispositivo móvel, para testar sua conta.

    ![Testar sua conta com o aplicativo autenticador](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Aprove a notificação na aplicação Microsoft Authenticator e, em seguida, selecione **Next**.

     ![Notificação de êxito, conectando o aplicativo e sua conta](media/security-info/securityinfo-myprofile-successauthapp.png)

     Suas informações de segurança são atualizadas para usar o aplicativo Microsoft Authenticator por padrão para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Excluir seu aplicativo autenticador de seus métodos de informações de segurança

Se já não quiser utilizar a sua aplicação autenticadora como método de informação de segurança, pode removê-la da página de informações de **Segurança.** Isso funciona para todos os aplicativos autenticadores, não apenas para o aplicativo Microsoft Authenticator. Depois de excluir o aplicativo, você precisará entrar no aplicativo autenticador em seu dispositivo móvel e excluir a conta.

>[!Important]
>Se você excluir o aplicativo autenticador por engano, não há como desfazê-lo. Terá de adicionar novamente a aplicação autenticadora, seguindo os passos na secção de aplicações do [autenticador](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) deste artigo.

### <a name="to-delete-the-authenticator-app"></a>Para excluir o aplicativo autenticador

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da aplicação Autenticador.

    ![Link para excluir o aplicativo autenticador das informações de segurança](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selecione **Sim** da caixa de confirmação para eliminar a aplicação autenticadora. Após a eliminação da aplicação autenticadora, é removida das suas informações de segurança e desaparece da página de informações de **Segurança.** Se o aplicativo autenticador for seu método padrão, o padrão será alterado para outro método disponível.

3. Abra a aplicação autenticadora no seu dispositivo móvel, selecione **contas Edit**e, em seguida, apague o seu trabalho ou conta escolar da aplicação autenticadora.

    Sua conta foi completamente removida do aplicativo autenticador para solicitações de verificação de dois fatores e de redefinição de senha.

## <a name="change-your-default-security-info-method"></a>Alterar o método de informações de segurança padrão

Se pretender que a aplicação autenticadora seja o método predefinido utilizado quando iniciar sessão na sua conta de trabalho ou escolar utilizando verificação de dois fatores ou para pedidos de reset de palavra-passe, pode defini-la a partir da página de **informações** de Segurança.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. Na página de informações de **Segurança,** selecione o link **Alterar** ao lado da informação do método de **iniciar sessão Predefinido.**

    ![Alterar o link para o método de entrada padrão](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Escolha **o Autenticador Microsoft - notificação** da lista de métodos disponíveis. Se não estiver a utilizar a aplicação Microsoft Authenticator, selecione a **aplicação Authenticator ou** a opção de ficha de hardware.

    ![Escolha o método para entrar no padrão](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Selecione **Confirmar**.

    O método padrão usado para alterações de entrada no aplicativo Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um texto de um código que você usará para a verificação em duas etapas ou a redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte Configurar informações de segurança para utilizar mensagens de [texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações](security-info-setup-phone-number.md)de segurança para utilizar chamadas telefónicas .

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Endereço de e-mail.** Insira seu endereço de email corporativo ou de estudante para obter um email para redefinição de senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações](security-info-setup-email.md)de segurança para utilizar o e-mail .

- **Questões de segurança.** Responda a algumas perguntas de segurança criadas pelo administrador da sua organização. Essa opção só está disponível para a redefinição de senha e não para a verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança configurar para utilizar](security-info-setup-questions.md) o artigo de questões de segurança.

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Iniciar sessão utilizando a aplicação Microsoft Authenticator, seguindo os passos no [Sign o artigo de verificação ou informação de segurança](security-info-setup-signin.md) em duas etapas.

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
