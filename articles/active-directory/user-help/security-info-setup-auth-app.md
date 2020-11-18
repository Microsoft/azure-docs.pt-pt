---
title: Configurar a app Microsoft Authenticator como o seu método de verificação - Azure AD
description: Como configurar a sua página de informações de Segurança (pré-visualização) para verificar a sua identidade utilizando a aplicação Microsoft Authenticator como método de verificação.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c96be79a349478db7d0452cae1ff07ee1c48aaa3
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700098"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Configurar a app Microsoft Authenticator como o seu método de verificação

Pode seguir estes passos para adicionar os seus métodos de verificação de dois fatores e de reset de palavra-passe. Depois de configurar isto pela primeira vez, pode voltar à página **de informações de Segurança** para adicionar, atualizar ou apagar as suas informações de segurança.

Se for solicitado que o mesmo o faça imediatamente após iniciar sôms na sua conta de trabalho ou na escola, consulte os passos detalhados na Configuração da sua informação de segurança a partir do artigo [de pedido de inscrição na página.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Se não vir a opção de aplicação autenticadora, é possível que a sua organização não permita que utilize esta opção para verificação. Neste caso, terá de escolher outro método ou contactar o balcão de ajuda da sua organização para obter mais assistência.

## <a name="security-vs-password-reset-verification"></a>Verificação de redefinição de passwords de segurança vs

Os métodos de informação de segurança são utilizados tanto para a verificação de segurança de dois fatores como para o reset da palavra-passe. No entanto, nem todos os métodos podem ser utilizados para ambos.

| Método | Utilizado para |
| ------ | -------- |
| App autenticador | Verificação de dois fatores e autenticação de redefinição de password. |
| Mensagens de texto | Verificação de dois fatores e autenticação de redefinição de password. |
| Chamadas telefónicas | Verificação de dois fatores e autenticação de redefinição de password. |
| Chave de segurança | Verificação de dois fatores e autenticação de redefinição de password. |
| Conta de e-mail | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |
| Perguntas de segurança | Apenas a autenticação de redetofinição de palavra-passe. Terá de escolher outro método para a verificação de dois fatores. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Configurar a aplicação Microsoft Authenticator a partir da página de informações de Segurança

Dependendo das definições da sua organização, poderá utilizar uma aplicação de autenticação como um dos seus métodos de informação de segurança. Não é obrigado a utilizar a aplicação Microsoft Authenticator, e pode escolher uma aplicação diferente durante o processo de configuração. No entanto, este artigo utiliza a aplicação Microsoft Authenticator.

>[!Important]
> Se tiver configurado a aplicação Microsoft Authenticator em 5 dispositivos diferentes ou 5 fichas de hardware , não poderá configurar uma sexta e poderá ver a seguinte mensagem de erro.
> 
> **Não é possível configurar o Microsoft Authenticator porque já tem cinco aplicações autenticadoras ou fichas de hardware. Contacte o seu administrador para eliminar uma das suas aplicações autenticadoras ou fichas de hardware.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Para configurar a aplicação Microsoft Authenticator

1. Inscreva-se na sua conta de trabalho ou escola e depois vá para a sua https://myaccount.microsoft.com/ página.

    ![A minha página de perfil, mostrando links de informações de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações** de segurança a partir do painel de navegação à esquerda ou do link no bloco **de informações de Segurança** e, em seguida, selecione Adicionar **método** a partir da página **de informações de Segurança.**

    ![Página de informações de segurança com opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página De método Adicionar uma página de **método,** selecione **a aplicação Authenticator** da lista de drop-down e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método, com app Autenticador selecionado](media/security-info/securityinfo-myprofile-addauthapp.png)

4. No **Início, obtenda a página da aplicação,** selecione **Baixar agora** para descarregar e instalar a aplicação Microsoft Authenticator no seu dispositivo móvel e, em seguida, selecione **Next**.

    Para obter mais informações sobre como descarregar e instalar a aplicação, consulte [Download e instale a aplicação Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Comece por obter a página da aplicação](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Se pretender utilizar uma aplicação autenticadora diferente da aplicação Microsoft Authenticator, selecione o I wanna use um link **de aplicação de autenticador diferente.**
   >
   > Se a sua organização permitir que escolha um método diferente para além da aplicação autenticadora, pode selecionar o **I want para configurar uma ligação de método diferente.**

5. Permaneça na página **'Configurar' a sua conta** enquanto configura a aplicação Microsoft Authenticator no seu dispositivo móvel.

    ![Configurar a página de aplicação do autenticador](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Abra a aplicação Microsoft Authenticator, selecione para permitir notificações (se solicitada), selecione **Adicionar a conta** a partir do ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione Trabalho ou conta **escolar**.

    >[!Note]
    >Se esta for a primeira vez que está a configurar a aplicação Microsoft Authenticator, poderá receber uma solicitação a perguntar se permite que a app aceda à sua câmara (iOS) ou se permite que a app tire fotografias e grave vídeos (Android). Tem de selecionar **Permitir** que a aplicação autenticadora possa aceder à sua câmara para tirar uma fotografia do código QR no passo seguinte. Se não permitir a câmara, ainda pode configurar a aplicação autenticadora, mas terá de adicionar manualmente a informação de código. Para obter informações sobre como adicionar o código manualmente, consulte [manualmente adicionar uma conta à aplicação.](user-help-auth-app-add-account-manual.md)

7. Volte à página **De Configurar a sua conta** no seu computador e, em seguida, selecione **Seguinte**.

    Aparece a página **de código QR.**

    ![Digitalize o código QR utilizando a aplicação Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Verifique o código fornecido com o leitor de código QR da aplicação Microsoft Authenticator, que apareceu no seu dispositivo móvel depois de ter criado a sua conta de trabalho ou escola no Passo 6.

    A aplicação autenticadora deve adicionar com sucesso o seu trabalho ou conta escolar sem necessitar de qualquer informação adicional da sua parte. No entanto, se o leitor de código QR não conseguir ler o código, pode selecionar **o link de código QR** e introduzir manualmente o código e o URL na aplicação Microsoft Authenticator. Para obter mais informações sobre a adição manual de um código, consulte [Manualmente adicionar uma conta à aplicação.](user-help-auth-app-add-account-manual.md)

9. Selecione **Seguinte** na página **de código QR** no seu computador.

    É enviada uma notificação para a aplicação Microsoft Authenticator no seu dispositivo móvel, para testar a sua conta.

    ![Teste a sua conta com a app autenticadora](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Aprove a notificação na aplicação Microsoft Authenticator e, em seguida, selecione **Next**.

     ![Notificação de sucesso, ligando a app e a sua conta](media/security-info/securityinfo-myprofile-successauthapp.png)

     As suas informações de segurança são atualizadas para utilizar a aplicação Microsoft Authenticator por padrão para verificar a sua identidade quando utilizar a verificação em duas etapas ou o reset da palavra-passe.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Elimine a sua aplicação autenticadora dos seus métodos de informação de segurança

Se já não pretender utilizar a sua aplicação autenticadora como método de informação de segurança, pode removê-la da página de informações de **Segurança.** Isto funciona para todas as aplicações autenticadoras, não apenas para a aplicação Microsoft Authenticator. Depois de eliminar a aplicação, terá de entrar na aplicação autenticadora do seu dispositivo móvel e apagar a conta.

>[!Important]
>Se eliminar a aplicação autenticadora por engano, não há como desfazê-la. Terá de voltar a adicionar a aplicação autenticadora, seguindo os passos na secção de [aplicações autenticadoras](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) deste artigo.

### <a name="to-delete-the-authenticator-app"></a>Para eliminar a aplicação autenticadora

1. Na página **de informações de Segurança,** selecione o link **Eliminar** ao lado da aplicação Autenticador.

    ![Link para eliminar a app autenticadora de informações de segurança](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Selecione **Sim** da caixa de confirmação para eliminar a aplicação autenticadora. Após a aplicação autenticadora ser eliminada, é removida da sua informação de segurança e desaparece da página de informações de **Segurança.** Se a aplicação autenticadora for o seu método padrão, o padrão mudará para outro método disponível.

3. Abra a aplicação autenticadora no seu dispositivo móvel, selecione **contas Editar** e, em seguida, elimine o seu trabalho ou conta escolar a partir da aplicação autenticadora.

    A sua conta é completamente removida da aplicação autenticadora para pedidos de verificação de dois fatores e de reposição de passwords.

## <a name="change-your-default-security-info-method"></a>Altere o seu método de informação de segurança padrão

Se pretender que a aplicação autenticadora seja o método predefinido utilizado quando iniciar singus na sua conta de trabalho ou escola utilizando a verificação de dois fatores ou para pedidos de reset de palavra-passe, pode descrevê-la a partir da página **de informações de** Segurança.

### <a name="to-change-your-default-security-info-method"></a>Para alterar o seu método de informação de segurança padrão

1. Na página **de informações de Segurança,** selecione o link **Alterar** ao lado das informações **do método de inscrição predefinido.**

    ![Alterar link para o método de inscrição predefinido](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Escolha **o Microsoft Authenticator - notificação** da lista de métodos disponíveis. Se não estiver a utilizar a aplicação Microsoft Authenticator, selecione a opção **Authenticator ou hardware token.**

    ![Escolha o método para o padrão de inscrição](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. **Selecione Confirmar**.

    O método predefinido utilizado para alterações de inscrição na aplicação Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Métodos adicionais de informação de segurança

Tem opções adicionais para saber como a sua organização o contacta para verificar a sua identidade, com base no que está a tentar fazer. As opções incluem:

- **Texto do dispositivo móvel.** Introduza o número do seu dispositivo móvel e obtenha um texto de um código que utilizará para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma mensagem de texto (SMS), consulte [Configurar informações de segurança para utilizar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Dispositivo móvel ou chamada telefónica de trabalho.** Introduza o número do seu dispositivo móvel e receba uma chamada telefónica para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com um número de telefone, consulte [Configurar informações de segurança para utilizar chamadas telefónicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registe a sua chave de segurança compatível com a Microsoft e utilize-a juntamente com um PIN para verificação em duas etapas ou redefinição de palavra-passe. Para obter instruções passo a passo sobre como verificar a sua identidade com uma chave de segurança, consulte [Configurar informações de segurança para utilizar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de e-mail.** Insira o seu endereço de e-mail de trabalho ou escola para obter um e-mail para reset de senha. Esta opção não está disponível para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar o seu e-mail, consulte [Configurar informações de segurança para utilizar o e-mail](security-info-setup-email.md).

- **Perguntas de segurança.** Responda a algumas questões de segurança criadas pelo seu administrador para a sua organização. Esta opção só está disponível para reset de palavra-passe e não para verificação em duas etapas. Para obter instruções passo a passo sobre como configurar as suas questões de segurança, consulte a informação de [segurança Configurar para utilizar](security-info-setup-questions.md) o artigo questões de segurança.

    >[!Note]
    >Se algumas destas opções estão em falta, é mais provável porque a sua organização não permite esses métodos. Se for esse o caso, terá de escolher um método disponível ou contactar o seu administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Iniciar sing-in utilizando a aplicação Microsoft Authenticator, seguindo os passos [no Signo na utilização de um artigo de verificação ou informação de segurança em duas etapas.](security-info-setup-signin.md)

- Reinicie a sua palavra-passe se a tiver perdido ou esquecido, a partir do [portal de reset password](https://passwordreset.microsoftonline.com/) ou siga os passos no artigo de Reposição do seu trabalho ou [palavra-passe escolar.](active-directory-passwords-update-your-own-password.md)

- Obtenha dicas de resolução de problemas e ajude a iniciar sessão no [Artigo da Conta Da Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
