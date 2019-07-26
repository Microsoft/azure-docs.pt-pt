---
title: Configurar informações de segurança (versão prévia) para usar suas perguntas de segurança-Azure Active Directory | Microsoft Docs
description: Como configurar suas informações de segurança para verificar sua identidade usando perguntas de segurança predefinidas.
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
ms.openlocfilehash: f1c375b64d93662ec50923078549c4f2153fba0a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382822"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>Configurar informações de segurança (versão prévia) para usar perguntas de segurança

Você pode seguir estas etapas para adicionar o método de redefinição de senha. Depois de configurar isso na primeira vez, você pode retornar à página informações de **segurança** para adicionar, atualizar ou excluir suas informações de segurança.

Depois de configurar o método de redefinição de senha, você também deve configurar o método de verificação de dois fatores, usando um [aplicativo autenticador](security-info-setup-auth-app.md), [mensagens de texto](security-info-setup-text-msg.md)ou uma [chamada telefônica](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Configurar suas perguntas de segurança na página de informações de segurança

Dependendo das configurações da sua organização, você poderá escolher e responder algumas perguntas de segurança como um dos seus métodos de informações de segurança. O administrador configura o número de perguntas de segurança que você precisa escolher e responder.

Se você usar perguntas de segurança, é recomendável usá-las em conjunto com outro método. As perguntas de segurança podem ser menos seguras do que outros métodos, pois algumas pessoas podem saber as respostas para suas perguntas.

> [!Note]
> As perguntas de segurança são armazenadas de forma privada e segura em um objeto de usuário no diretório e só podem ser respondidas por você durante o registro. Não há como seu administrador ler ou modificar suas perguntas ou respostas.
>
> Se você não vir a opção de perguntas de segurança, é possível que sua organização não permita que você use perguntas de segurança para verificação. Se esse for o caso, você precisará escolher outro método ou entrar em contato com o administrador para obter mais ajuda.
>
> As contas de administrador não têm permissão para usar perguntas de segurança como um método de redefinição de senha. Se você estiver conectado como uma conta de nível de administrador, essas opções não serão exibidas.

### <a name="to-set-up-your-security-questions"></a>Para configurar suas perguntas de segurança

1. Entre em sua conta corporativa ou de estudante e, em seguida, https://myprofile.microsoft.com/ vá para a página.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou no link no bloco **informações de segurança** e, em seguida, selecione **Adicionar método** na página **informações de segurança** .

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar um método** , selecione **perguntas de segurança** na lista suspensa e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com perguntas de segurança selecionadas](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na página **perguntas de segurança** , escolha e responda às suas perguntas de segurança e, em seguida, selecione **salvar**.

    ![Adicionar número de telefone e escolher chamadas telefônicas](media/security-info/securityinfo-myprofile-securityquestions.png)

    Suas informações de segurança são atualizadas e você pode usar suas perguntas de segurança para verificar sua identidade ao usar a redefinição de senha.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Excluir perguntas de segurança de seus métodos de informações de segurança

Se você não quiser mais usar suas perguntas de segurança como um método de informações de segurança, poderá removê-las da página de **informações de segurança** .

>[!Important]
>Se você excluir suas perguntas de segurança por engano, não há como desfazê-las. Você precisará adicionar o método novamente, seguindo as etapas na seção [configurar suas perguntas de segurança](#set-up-your-security-questions-from-the-security-info-page) deste artigo.

### <a name="to-delete-your-security-questions"></a>Para excluir suas perguntas de segurança

1. Na página **informações de segurança** , selecione o link **excluir** ao lado da opção **perguntas de segurança** .

    ![Link para excluir o método de telefone das informações de segurança](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Selecione **Sim** na caixa de confirmação para excluir suas **perguntas de segurança**. Depois que suas perguntas de segurança são excluídas, o método é removido de suas informações de segurança e desaparece da página **informações de segurança** .

## <a name="additional-security-info-methods"></a>Métodos adicionais de informações de segurança

Você tem opções adicionais de como sua organização entra em contato com você para verificar sua identidade, com base no que você está tentando fazer. As opções incluem:

- **Aplicativo autenticador.** Baixe e use um aplicativo autenticador para obter uma notificação de aprovação ou um código de aprovação gerado aleatoriamente para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como configurar e usar o aplicativo Microsoft Authenticator, consulte [configurar informações de segurança para usar um aplicativo autenticador](security-info-setup-auth-app.md).

- **Texto do dispositivo móvel.** Insira o número do seu dispositivo móvel e obtenha um texto de um código que você usará para a verificação em duas etapas ou a redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma mensagem de texto (SMS), consulte [configurar informações de segurança para usar mensagens de texto (SMS)](security-info-setup-text-msg.md).

- **Chamada telefônica de dispositivo ou trabalho móvel.** Insira o número do seu dispositivo móvel e obtenha uma chamada telefônica para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com um número de telefone, consulte [configurar informações de segurança para usar chamadas telefônicas](security-info-setup-phone-number.md).

- **Chave de segurança.** Registre sua chave de segurança compatível com a Microsoft e use-a junto com um PIN para verificação em duas etapas ou redefinição de senha. Para obter instruções detalhadas sobre como verificar sua identidade com uma chave de segurança, consulte [configurar informações de segurança para usar uma chave de segurança](security-info-setup-security-key.md).

- **Endereço de email.** Insira seu endereço de email corporativo ou de estudante para obter um email para redefinição de senha. Essa opção não está disponível para verificação em duas etapas. Para obter instruções detalhadas sobre como configurar seu email, consulte [configurar informações de segurança para usar email](security-info-setup-email.md).

    >[!Note]
    >Se algumas dessas opções estiverem ausentes, isso provavelmente ocorrerá porque sua organização não permite esses métodos. Se esse for o caso, você precisará escolher um método disponível ou entrar em contato com o administrador para obter mais ajuda.

## <a name="next-steps"></a>Passos Seguintes

- Redefina sua senha se você a tiver perdido ou esquecido, no [portal](https://passwordreset.microsoftonline.com/) de redefinição de senha ou siga as etapas no artigo [redefinir sua senha corporativa ou de estudante](user-help-reset-password.md) .

- Obtenha dicas de solução de problemas e ajuda para problemas de conexão no artigo [não é possível entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
