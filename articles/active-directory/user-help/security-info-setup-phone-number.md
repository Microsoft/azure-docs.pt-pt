---
title: Configurar informações de segurança (versão prévia) para usar chamadas telefônicas-Azure Active Directory | Microsoft Docs
description: Como configurar suas informações de segurança para verificar sua identidade usando chamadas telefônicas.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbac4f364dedc28f7d5f3e100481a56bde70b4d3
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382919"
---
# <a name="set-up-security-info-preview-to-use-phone-calls"></a>Configurar informações de segurança (versão prévia) para usar chamadas telefônicas

Você pode seguir estas etapas para adicionar os métodos de verificação de dois fatores e de redefinição de senha. Depois de configurar isso na primeira vez, você pode retornar à página informações de **segurança** para adicionar, atualizar ou excluir suas informações de segurança.

Se você for solicitado a configurar isso imediatamente depois de entrar em sua conta corporativa ou de estudante, consulte as etapas detalhadas no artigo [configurar informações de segurança no prompt de página de entrada](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> As informações de segurança não dão suporte ao uso de extensões de telefone. Mesmo que você adicione o formato apropriado, + 1 4255551234X12345, as extensões serão removidas antes de a chamada ser colocada.
>
> Se você não vir uma opção de telefone, é possível que sua organização não permita que você use um número de telefone para verificação. Nesse caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Configurar chamadas telefônicas na página informações de segurança

Dependendo das configurações da sua organização, você poderá usar chamadas telefônicas como um dos seus métodos de informações de segurança.

>[!Note]
>Se você quiser receber uma mensagem de texto em vez de uma chamada telefônica, siga as etapas no artigo [configurar informações de segurança para usar mensagens de texto](security-info-setup-text-msg.md) .

### <a name="to-set-up-phone-calls"></a>Para configurar chamadas telefônicas

1. Entre em sua conta corporativa ou de estudante e, em seguida, https://myprofile.microsoft.com/ vá para a página.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou no link no bloco **informações de segurança** e, em seguida, selecione **Adicionar método** na página **informações de segurança** .

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar um método** , selecione **telefone** na lista suspensa e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com telefone selecionado](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na página **telefone** , digite o número de telefone do seu dispositivo móvel, escolha **ligar para mim**e, em seguida, selecione **Avançar**.

    ![Adicionar número de telefone e escolher chamadas telefônicas](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Responda à chamada telefônica de verificação, enviada para o número de telefone que você inseriu e siga as instruções.

    A página é alterada para mostrar o seu sucesso.

    ![Notificação de êxito, conectando o número de telefone, a escolha para receber chamadas telefônicas e sua conta](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Suas informações de segurança são atualizadas e você pode usar chamadas telefônicas para verificar sua identidade ao usar a verificação em duas etapas ou a redefinição de senha. Se você quiser fazer com que o telefone chame o método padrão, consulte a seção [alterar o método de informações de segurança padrão](#change-your-default-security-info-method) deste artigo.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Excluir chamadas telefônicas de seus métodos de informações de segurança

Se você não quiser mais usar chamadas telefônicas como um método de informações de segurança, poderá removê-la da página **informações de segurança** .

>[!Important]
>Se você excluir chamadas telefônicas por engano, não há como desfazê-las. Você precisará adicionar o método novamente, seguindo as etapas na seção [Configurar chamadas telefônicas](#set-up-phone-calls-from-the-security-info-page) deste artigo.

### <a name="to-delete-phone-calls"></a>Para excluir chamadas telefônicas

1. Na página **informações de segurança** , selecione o link **excluir** ao lado da opção **telefone** .

    ![Link para excluir o método de telefone das informações de segurança](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Selecione **Sim** na caixa de confirmação para excluir o número de **telefone** . Depois que o número de telefone for excluído, ele será removido das informações de segurança e desaparecerá da página **informações de segurança** . Se o **telefone** for seu método padrão, o padrão será alterado para outro método disponível.

## <a name="change-your-default-security-info-method"></a>Alterar o método de informações de segurança padrão

Se você quiser que as chamadas telefônicas sejam o método padrão usado quando entrar em sua conta corporativa ou de estudante usando a verificação de dois fatores ou para solicitações de redefinição de senha, você poderá defini-la na página **informações de segurança** .

### <a name="to-change-your-default-security-info-method"></a>Para alterar o método de informações de segurança padrão

1. Na página **informações de segurança** , selecione o link **alterar** ao lado das informações do **método de entrada padrão** .

    ![Alterar o link para o método de entrada padrão](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Selecione **chamada telefônica ( *_your_phone_number_* )** na lista suspensa de métodos disponíveis e, em seguida, selecione **confirmar**.

    ![Escolha o método para entrar no padrão](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    O método padrão usado para alterações de entrada em **chamada telefônica ( *_your_phone_number_* )** .

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Baixe e use um aplicativo autenticador para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um texto de um código que você usará para a verificação em duas etapas ou a redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma chave de segurança, consulte [configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de email.** Insira seu endereço de email corporativo ou de estudante para obter um email para redefinição de senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções detalhadas sobre como configurar seu email, consulte [configurar informações de segurança para usar email](security-info-setup-email.md).

- **Perguntas de segurança.** Responda a algumas perguntas de segurança criadas pelo administrador da sua organização. Essa opção só está disponível para a redefinição de senha e não para a verificação em duas etapas. Para obter instruções passo a passo sobre como configurar suas perguntas de segurança, consulte o artigo [configurar informações de segurança para usar perguntas de segurança](security-info-setup-questions.md) .

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos Seguintes

- Redefina sua senha se você a tiver perdido ou esquecido, no [portal](https://passwordreset.microsoftonline.com/) de redefinição de senha ou siga as etapas no artigo [redefinir sua senha corporativa ou de estudante](user-help-reset-password.md) .

- Obtenha dicas de solução de problemas e ajuda para problemas de conexão no artigo [não é possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
