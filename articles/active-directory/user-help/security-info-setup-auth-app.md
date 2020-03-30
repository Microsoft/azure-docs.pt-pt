---
title: Configurar a aplicação Microsoft Authenticator como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade utilizando a aplicação Do Autenticador microsoft como método de verificação.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062426"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Configurar a aplicação Microsoft Authenticator como o seu método de verificação

Pode seguir estes passos para adicionar os seus métodos de verificação de dois fatores e redefinição de palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página de **informações** de Segurança para adicionar, atualizar ou apagar as suas informações de segurança.

Se for solicitado que o instale imediatamente após iniciar sessão na sua conta de trabalho ou escola, consulte os passos detalhados no Conjunto de informações de segurança a partir do artigo de solicitação da página de início de [sessão.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Se não vir a opção de aplicação autenticadora, é possível que a sua organização não lhe permita utilizar esta opção para verificação. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais assistência.

## <a name="security-vs-password-reset-verification"></a>Verificação de segurança vs redefinição de palavra-passe

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset de palavras-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| Aplicação de autenticação | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Mensagens de texto | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Telefonemas | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Chave de segurança | Verificação de dois fatores e autenticação de reset de palavra-passe. |
| Conta de e-mail | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de reset da palavra-passe. Terá de escolher outro método para verificação de dois fatores. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configurar a aplicação Microsoft Authenticator a partir da página de informações de Segurança

Dependendo das definições da sua organização, poderá utilizar uma aplicação de autenticação como um dos seus métodos de informação de segurança. Não é obrigado a utilizar a aplicação Microsoft Authenticator e pode escolher uma aplicação diferente durante o processo de configuração. No entanto, este artigo utiliza a aplicação Microsoft Authenticator.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Para configurar a aplicação Microsoft Authenticator

1. Inscreva-se na sua conta de trabalho https://myprofile.microsoft.com/ ou escola e depois vá para a sua página.

    ![A minha página de Perfil, mostrando links de informação de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** a partir do painel de navegação esquerdo ou do link no bloco de **informações** de Segurança e, em seguida, selecione **adicionar método** a partir da página de informações de **Segurança.**

    ![Página de informações de segurança com opção de método de adição realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione **aplicação Autenticador** a partir da lista de lançamentos e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método, com app Autenticador selecionado](media/security-info/securityinfo-myprofile-addauthapp.png)

4. No **Início, obtendo a página da aplicação,** selecione **Descarregar agora** para descarregar e instalar a aplicação Microsoft Authenticator no seu dispositivo móvel e, em seguida, selecione **Next**.

    Para mais informações sobre como descarregar e instalar a aplicação, consulte [O Download e instale a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Comece por obter a página de aplicativos](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Se pretender utilizar uma aplicação autenticadora diferente da aplicação Microsoft Authenticator, selecione o I pretendo utilizar um link de **aplicação autenticador diferente.**
   >
   > Se a sua organização permitir escolher um método diferente para além da aplicação autenticadora, pode selecionar o **I pretendo configurar uma ligação de método diferente**.

5. Mantenha-se na página de **'Configurar'** enquanto configura a aplicação Microsoft Authenticator no seu dispositivo móvel.

    ![Configurar a página de aplicações do autenticador](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Abra a aplicação Microsoft Authenticator, selecione para permitir notificações (se solicitado), selecione **Adicionar conta** a partir do ícone Personalizar **e controlar** no canto superior direito e, em seguida, selecione Trabalho ou **conta escolar**.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma pergunta rápida sobre se permite que a aplicação aceda à sua câmara (iOS) ou para permitir que a aplicação tire fotografias e grave vídeos (Android). Deve selecionar **Permitir** para que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no próximo passo. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação do código. Para obter informações sobre como adicionar o código manualmente, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

7. Volte à página da **sua conta** No computador e, em seguida, selecione **Next**.

    Aparece a página de **código QR.**

    ![Digitalizar o código QR utilizando a aplicação Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Resumie o código fornecido com o leitor de códigoQ da aplicação Microsoft Authenticator, que apareceu no seu dispositivo móvel depois de ter criado o seu trabalho ou conta escolar no Passo 6.

    A aplicação autenticadora deve adicionar com sucesso o seu trabalho ou conta escolar sem necessitar de qualquer informação adicional sua. No entanto, se o leitor de códigoQ não conseguir ler o código, pode selecionar o link de **código QR e** introduzir manualmente o código e o URL na aplicação Microsoft Authenticator. Para obter mais informações sobre a adição manual de um código, consulte [Manualmente adicionar uma conta à aplicação](user-help-auth-app-add-account-manual.md).

9. Selecione **Em seguida** na página de **código QR** no seu computador.

    Uma notificação é enviada para a aplicação Microsoft Authenticator no seu dispositivo móvel, para testar a sua conta.

    ![Teste a sua conta com a aplicação autenticadora](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Aprove a notificação na aplicação Microsoft Authenticator e, em seguida, selecione **Next**.

     ![Notificação de sucesso, ligando a app e a sua conta](media/security-info/securityinfo-myprofile-successauthapp.png)

     A sua informação de segurança é atualizada para utilizar a aplicação Microsoft Authenticator por padrão para verificar a sua identidade ao utilizar a verificação em duas etapas ou o reset da palavra-passe.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Elimine a sua aplicação autenticadora dos seus métodos de informação de segurança

Se já não quiser utilizar a sua aplicação autenticadora como método de informação de segurança, pode removê-la da página de informações de **Segurança.** Isto funciona para todas as aplicações autenticadoras, e não apenas para a aplicação Microsoft Authenticator. Depois de eliminar a aplicação, terá de entrar na aplicação autenticadora do seu dispositivo móvel e apagar a conta.

>[!Important]
>Se apagar a aplicação do autenticador por engano, não há como desfazê-la. Terá de adicionar novamente a aplicação autenticadora, seguindo os passos na secção de aplicações do [autenticador](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) deste artigo.

### <a name="to-delete-the-authenticator-app"></a>Para eliminar a aplicação autenticadora

1. Na página de informações de **Segurança,** selecione o link **Eliminar** ao lado da aplicação Autenticador.

    ![Link para eliminar a aplicação autenticadora a partir de informações de segurança](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selecione **Sim** da caixa de confirmação para eliminar a aplicação autenticadora. Após a eliminação da aplicação autenticadora, é removida das suas informações de segurança e desaparece da página de informações de **Segurança.** Se a aplicação autenticadora for o seu método padrão, a predefinição mudará para outro método disponível.

3. Abra a aplicação autenticadora no seu dispositivo móvel, selecione **contas Edit**e, em seguida, apague o seu trabalho ou conta escolar da aplicação autenticadora.

    A sua conta é completamente removida da aplicação autenticadora para pedidos de verificação de dois fatores e redefinição de palavra-passe.

## <a name="change-your-default-security-info-method"></a>Altere o seu método de informação de segurança padrão

Se pretender que a aplicação autenticadora seja o método predefinido utilizado quando iniciar sessão na sua conta de trabalho ou escolar utilizando verificação de dois fatores ou para pedidos de reset de palavra-passe, pode defini-la a partir da página de **informações** de Segurança.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o seu método de informação de segurança padrão

1. Na página de informações de **Segurança,** selecione o link **Alterar** ao lado da informação do método de **iniciar sessão Predefinido.**

    ![Alterar o link para o método de sessão predefinido](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Escolha **o Autenticador Microsoft - notificação** da lista de métodos disponíveis. Se não estiver a utilizar a aplicação Microsoft Authenticator, selecione a **aplicação Authenticator ou** a opção de ficha de hardware.

    ![Escolha o método de inscrição por defeito](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Selecione **Confirmar**.

    O método predefinido utilizado para alterações de sessão na aplicação Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um código de texto que utilizará para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte Configurar informações de segurança para utilizar mensagens de [texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefónica para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações](security-info-setup-phone-number.md)de segurança para utilizar chamadas telefónicas .

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou reset de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações](security-info-setup-security-key.md)de segurança para utilizar uma chave de segurança .

- **Endereço de e-mail.** Insira o seu endereço de e-mail de trabalho ou escola para obter um e-mail para reset de palavra-passe. Esta opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações](security-info-setup-email.md)de segurança para utilizar o e-mail .

- **Questões de segurança.** Responda a algumas questões de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para reset de palavra-passe e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança configurar para utilizar](security-info-setup-questions.md) o artigo de questões de segurança.

    >[!Note]
    >Se algumas destas opções estão em falta, é muito provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Iniciar sessão utilizando a aplicação Microsoft Authenticator, seguindo os passos no [Sign o artigo de verificação ou informação de segurança](security-info-setup-signin.md) em duas etapas.

- Redefinir a sua palavra-passe se a perdeu ou esqueceu, a partir do portal de [reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de [reset do seu trabalho ou palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da conta da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
