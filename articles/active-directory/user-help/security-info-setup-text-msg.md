---
title: Configurar mensagens de texto como seu método de verificação-Azure AD
description: Como configurar sua página de informações de segurança (versão prévia) para verificar sua identidade usando mensagens de texto como seu método de verificação.
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
ms.openlocfilehash: 1d21445d12ea8ae7040627c6f9bdae7c744953c3
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704821"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Configurar mensagens de texto como seu método de verificação

Você pode seguir estas etapas para adicionar os métodos de verificação de dois fatores e de redefinição de senha. Depois de configurar isso na primeira vez, você pode retornar à página informações de **segurança** para adicionar, atualizar ou excluir suas informações de segurança.

Se você for solicitado a configurar isso imediatamente depois de entrar em sua conta corporativa ou de estudante, consulte as etapas detalhadas no artigo [configurar informações de segurança no prompt de página de entrada](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Se você não vir uma opção de telefone, é possível que sua organização não permita que você use essa opção para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o suporte técnico da sua organização para obter mais assistência.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Configurar mensagens de texto na página informações de segurança

Dependendo das configurações da sua organização, você poderá usar mensagens de texto como um dos seus métodos de informações de segurança. A opção de mensagem de texto é uma parte da opção de telefone, portanto, você definirá tudo da mesma maneira que faria para seu número de telefone, mas em vez de fazer com que a Microsoft chame você, você optará por usar uma mensagem de texto.

>[!Note]
>Se você quiser receber uma chamada telefônica em vez de uma mensagem de texto, siga as etapas no artigo [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md) .

### <a name="to-set-up-text-messages"></a>Para configurar mensagens de texto

1. Entre em sua conta corporativa ou de estudante e, em seguida, vá para a página https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou no link no bloco **informações de segurança** e, em seguida, selecione **Adicionar método** na página **informações de segurança** .

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar um método** , selecione **telefone** na lista suspensa e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **telefone** , digite o número de telefone do seu dispositivo móvel, escolha **texto para mim em um código**e, em seguida, selecione **Avançar**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Digite o código enviado a você por meio de mensagem de texto para seu dispositivo móvel e, em seguida, selecione **Avançar**.

    ![Adicionar número de telefone e escolher mensagens de texto](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    A página é alterada para mostrar o seu sucesso.

    ![Notificação de êxito, conectando o número de telefone, a escolha para receber mensagens de texto e sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Suas informações de segurança são atualizadas e você pode usar mensagens de texto para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha. Se você quiser fazer mensagens de texto como seu método padrão, consulte a seção [alterar o método de informações de segurança padrão](#change-your-default-security-info-method) deste artigo.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Excluir mensagens de texto de seus métodos de informações de segurança

Se você não quiser mais usar mensagens de texto como um método de informações de segurança, poderá removê-la da página de **informações de segurança** .

>[!Important]
>Se você excluir mensagens de texto por engano, não será possível desfazê-las. Você precisará adicionar o método novamente, seguindo as etapas na seção [Configurar mensagens de texto](#set-up-text-messages-from-the-security-info-page) deste artigo.

### <a name="to-delete-text-messaging"></a>Para excluir mensagens de texto

1. Na página **informações de segurança** , selecione o link **excluir** ao lado da opção **telefone** .

    ![Link para excluir o telefone e o método de mensagens de texto das informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** na caixa de confirmação para excluir o número de **telefone** . Depois que o número de telefone for excluído, ele será removido das informações de segurança e desaparecerá da página **informações de segurança** . Se o **telefone** for seu método padrão, o padrão será alterado para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Alterar o método de informações de segurança padrão

Se você quiser que o sistema de mensagens de texto seja o método padrão usado ao entrar em sua conta corporativa ou de estudante usando a verificação de dois fatores ou para solicitações de redefinição de senha, você poderá defini-lo na página **informações de segurança** .

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. Na página **informações de segurança** , selecione o link **alterar** ao lado das informações do **método de entrada padrão** .

    ![Alterar o link para o método de entrada padrão](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **Telefone - texto *_(your_phone_number_* )** da lista de métodos disponíveis e, em seguida, selecione **Confirmar**.

    ![Escolha o método para entrar no padrão](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    O método predefinido utilizado para alterações de sessão no **Telefone - texto ( *_your_phone_number_* )** .

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Baixe e use um aplicativo autenticador para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Chamada telefônica de dispositivo ou trabalho móvel.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma chave de segurança, consulte [configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de email.** Insira seu endereço de email corporativo ou de estudante para obter um email para redefinição de senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções detalhadas sobre como configurar seu email, consulte [configurar informações de segurança para usar email](security-info-setup-email.md).

- **Perguntas de segurança.** Responda a algumas perguntas de segurança criadas pelo administrador da sua organização. Essa opção só está disponível para a redefinição de senha e não para a verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md) .

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos seguintes

- Redefina sua senha se você a tiver perdido ou esquecido, no [portal de redefinição de senha](https://passwordreset.microsoftonline.com/) ou siga as etapas no artigo [redefinir sua senha corporativa ou de estudante](active-directory-passwords-update-your-own-password.md) .

- Obtenha dicas de solução de problemas e ajuda para problemas de conexão no artigo [não é possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
