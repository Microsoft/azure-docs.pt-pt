---
title: Configurar o aplicativo Microsoft Authenticator como seu método de verificação-Azure AD
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade usando o aplicativo Microsoft Authenticator como seu método de verificação.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.openlocfilehash: 9a3efc6f6b1b7137e8e29e37edd968765712fef5
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704974"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Configurar o aplicativo Microsoft Authenticator como seu método de verificação

Você pode seguir estas etapas para adicionar os métodos de verificação de dois fatores e de redefinição de senha. Depois de configurar isso na primeira vez, você pode retornar à página informações de **segurança** para adicionar, atualizar ou excluir suas informações de segurança.

Se você for solicitado a configurar isso imediatamente depois de entrar em sua conta corporativa ou de estudante, consulte as etapas detalhadas no artigo [configurar informações de segurança no prompt de página de entrada](security-info-setup-signin.md) .

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
| Perguntas de segurança | Somente autenticação de redefinição de senha. Você precisará escolher outro método para verificação de dois fatores. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configurar o aplicativo Microsoft Authenticator na página informações de segurança

Dependendo das configurações da sua organização, você poderá usar um aplicativo de autenticação como um dos seus métodos de informações de segurança. Você não precisa usar o aplicativo Microsoft Authenticator e pode escolher um aplicativo diferente durante o processo de configuração. No entanto, este artigo usa o aplicativo Microsoft Authenticator.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Para configurar o aplicativo Microsoft Authenticator

1. Entre em sua conta corporativa ou de estudante e, em seguida, vá para a página https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou no link no bloco **informações de segurança** e, em seguida, selecione **Adicionar método** na página **informações de segurança** .

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar um método** , selecione **aplicativo autenticador** na lista suspensa e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com o aplicativo autenticador selecionado](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Na página **Iniciar obtendo o aplicativo** , selecione **baixar agora** para baixar e instalar o aplicativo Microsoft Authenticator em seu dispositivo móvel e, em seguida, selecione **Avançar**.

    Para obter mais informações sobre como baixar e instalar o aplicativo, consulte [baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Comece obtendo a página do aplicativo](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Se você quiser usar um aplicativo autenticador diferente do Microsoft Authenticator aplicativo, selecione o link **desejo usar um aplicativo autenticador diferente** .
   >
   > Se sua organização permite que você escolha um método diferente além do aplicativo autenticador, você pode selecionar o **link desejo configurar um método diferente**.

5. Permaneça na página **configurar sua conta** enquanto configura o aplicativo Microsoft Authenticator em seu dispositivo móvel.

    ![Configurar a página do aplicativo autenticador](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Abra o aplicativo Microsoft Authenticator, selecione para permitir notificações (se solicitado), selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito e selecione **conta corporativa ou de estudante**.

    >[!Note]
    >Se esta for a primeira vez que você estiver configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou permitir que o aplicativo tire fotos e grave vídeo (Android). Você deve selecionar **permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma imagem do código QR na próxima etapa. Se você não permitir a câmera, ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para obter informações sobre como adicionar o código manualmente, consulte [adicionar manualmente uma conta ao aplicativo](user-help-auth-app-add-account-manual.md).

7. Retorne à página **configurar sua conta** no computador e, em seguida, selecione **Avançar**.

    A página **digitalizar o código QR** é exibida.

    ![Digitalizar o código QR usando o aplicativo autenticador](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Digitalize o código fornecido com o leitor de código QR do aplicativo Microsoft Authenticator, que apareceu em seu dispositivo móvel depois de criar sua conta corporativa ou de estudante na etapa 6.

    O aplicativo autenticador deve adicionar com êxito sua conta corporativa ou de estudante sem precisar de nenhuma informação adicional de você. No entanto, se o leitor de código QR não puder ler o código, você poderá selecionar o **link não é possível digitalizar o código QR** e inserir manualmente o código e a URL no aplicativo Microsoft Authenticator. Para obter mais informações sobre como adicionar um código manualmente, consulte [adicionar manualmente uma conta ao aplicativo](user-help-auth-app-add-account-manual.md).

9. Selecione **Avançar** na página **digitalizar o código QR** em seu computador.

    Uma notificação é enviada para o aplicativo Microsoft Authenticator em seu dispositivo móvel, para testar sua conta.

    ![Testar sua conta com o aplicativo autenticador](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Aprove a notificação no aplicativo Microsoft Authenticator e, em seguida, selecione **Avançar**.

     ![Notificação de êxito, conectando o aplicativo e sua conta](media/security-info/securityinfo-myprofile-successauthapp.png)

     Suas informações de segurança são atualizadas para usar o aplicativo Microsoft Authenticator por padrão para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Excluir seu aplicativo autenticador de seus métodos de informações de segurança

Se você não quiser mais usar seu aplicativo autenticador como um método de informações de segurança, poderá removê-lo da página **informações de segurança** . Isso funciona para todos os aplicativos autenticadores, não apenas para o aplicativo Microsoft Authenticator. Depois de excluir o aplicativo, você precisará entrar no aplicativo autenticador em seu dispositivo móvel e excluir a conta.

>[!Important]
>Se você excluir o aplicativo autenticador por engano, não há como desfazê-lo. Você precisará adicionar o aplicativo autenticador novamente, seguindo as etapas na seção [Configurar o aplicativo autenticador](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) deste artigo.

### <a name="to-delete-the-authenticator-app"></a>Para excluir o aplicativo autenticador

1. Na página **informações de segurança** , selecione o link **excluir** ao lado do aplicativo autenticador.

    ![Link para excluir o aplicativo autenticador das informações de segurança](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selecione **Sim** na caixa de confirmação para excluir o aplicativo autenticador. Depois que o aplicativo autenticador é excluído, ele é removido das informações de segurança e desaparece da página **informações de segurança** . Se o aplicativo autenticador for seu método padrão, o padrão será alterado para outro método disponível.

3. Abra o aplicativo autenticador em seu dispositivo móvel, selecione **Editar contas**e, em seguida, exclua sua conta corporativa ou de estudante do aplicativo autenticador.

    Sua conta foi completamente removida do aplicativo autenticador para solicitações de verificação de dois fatores e de redefinição de senha.

## <a name="change-your-default-security-info-method"></a>Alterar o método de informações de segurança padrão

Se você quiser que o aplicativo autenticador seja o método padrão usado quando entrar em sua conta corporativa ou de estudante usando a verificação de dois fatores ou para solicitações de redefinição de senha, você poderá defini-lo na página **informações** de segurança.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. Na página **informações de segurança** , selecione o link **alterar** ao lado das informações do **método de entrada padrão** .

    ![Alterar o link para o método de entrada padrão](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Escolha **Microsoft Authenticator-Notification** na lista suspensa de métodos disponíveis. Se você não estiver usando o aplicativo Microsoft Authenticator, selecione a opção **aplicativo autenticador ou token de hardware** .

    ![Escolha o método para entrar no padrão](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Selecione **confirmar**.

    O método padrão usado para alterações de entrada no aplicativo Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um texto de um código que você usará para a verificação em duas etapas ou a redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada telefônica de dispositivo ou trabalho móvel.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma chave de segurança, consulte [configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de email.** Insira seu endereço de email corporativo ou de estudante para obter um email para redefinição de senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções detalhadas sobre como configurar seu email, consulte [configurar informações de segurança para usar email](security-info-setup-email.md).

- **Perguntas de segurança.** Responda a algumas perguntas de segurança criadas pelo administrador da sua organização. Essa opção só está disponível para a redefinição de senha e não para a verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md) .

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Entre usando o aplicativo Microsoft Authenticator, as etapas a seguir no artigo [entrar usando a verificação em duas etapas ou informações de segurança](security-info-setup-signin.md) .

- Redefina sua senha se você a tiver perdido ou esquecido, no [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md) .

- Obtenha dicas de solução de problemas e ajuda para problemas de conexão no artigo [não é possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
