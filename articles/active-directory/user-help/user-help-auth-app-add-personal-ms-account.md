---
title: Adicione uma conta pessoal da Microsoft à aplicação Microsoft Authenticator - Azure AD
description: Adicione contas pessoais da Microsoft, como para Outlook.com ou Xbox LIVE à aplicação Microsoft Authenticator para verificar a sua identidade enquanto utiliza a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741618"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Adicione contas pessoais da Microsoft à aplicação Microsoft Authenticator

Adicione as suas contas pessoais da Microsoft, como para as contas Outlook.com e Xbox LIVE, à aplicação Microsoft Authenticator para o processo padrão de verificação de dois fatores e sem palavras-de-utilizador.

- **Método padrão de verificação de dois fatores.** Digite o seu nome de utilizador e palavra-passe no dispositivo em que está a iniciar sessão e, em seguida, escolha se a aplicação Microsoft Authenticator envia uma notificação ou se prefere copiar o código de verificação associado a partir do ecrã de **Contas** da aplicação Microsoft Authenticator.

- **Método de entrada sem palavras-passe.** Digite o seu nome de utilizador no dispositivo em que está a iniciar sessão utilizando a sua conta pessoal da Microsoft e, em seguida, use o seu dispositivo móvel para verificar se está a usar a sua impressão digital, rosto ou PIN. Para este método, não precisa de introduzir a sua palavra-passe.

>[!Important]
>Antes de poder adicionar a sua conta, tem de descarregar e instalar a aplicação Microsoft Authenticator. Se ainda não o fez, siga os passos no Download e instale o artigo [da aplicação.](user-help-auth-app-download-install.md)

Pode adicionar a sua conta pessoal da Microsoft, ligando primeiro a verificação de dois fatores e, em seguida, adicionando a conta à app. Não é preciso ativar a verificação de dois fatores para utilizar apenas o acesso telefónico sem palavras-passe para a sua conta, mas recomendamos vivamente que ligue a verificação de dois fatores para uma segurança adicional da conta.

## <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, vá à página [de bases](https://account.microsoft.com/security) da Segurança e inscreva-se utilizando a sua conta pessoal da Microsoft. Por exemplo, alain@outlook.com.

2. Na parte inferior da página básica de **Segurança,** escolha o link **mais opções** de segurança.

    ![Página básica de segurança com o link "mais opções de segurança" em destaque](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Vá à secção **de verificação de duas etapas** e opte por ligar a função **.** Também pode desligá-lo aqui se já não quiser usá-lo com a sua conta pessoal.

## <a name="add-your-microsoft-account-to-the-app"></a>Adicione a sua conta Microsoft à aplicação

1. Abra a aplicação Microsoft Authenticator no seu dispositivo móvel.

1. No Android, selecione **Adicionar conta** a partir do ícone Personalizar **e Controlar** no canto superior direito.

    ![Páginas de seleção de conta android](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    No iOS, selecione o ícone plus na parte superior direita.

    ![versão iOS da experiência de seleção de conta](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. Na página **Adicionar,** escolha **conta Pessoal**.

1. Selecione **Iniciar sessão com** a Microsoft para adicionar a sua conta. Um código QR pode ser usado quando disponível, mas pode sempre adicionar a sua conta fazendo sessão com o seu nome de utilizador e senha.

    ![Selecione uma conta Microsoft ou scaneie um código QR quando disponível](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Inscreva-se na sua conta pessoal, utilizando o endereço de e-mail apropriado alain@outlook.com (como), e depois selecione **Next**.

    >[!Note]
    >Se não tiver uma conta pessoal da Microsoft, [pode criar uma](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Introduza a sua palavra-passe e, em seguida, selecione **Iniciar sessão**. A sua conta pessoal é adicionada à aplicação Microsoft Authenticator.

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar as suas contas à aplicação, pode iniciar sessão utilizando a aplicação Autenticadora no seu dispositivo. Para mais informações, consulte [O Sign in usando a aplicação](user-help-auth-app-sign-in.md).

- Se tiver problemas em obter o seu código de verificação para a sua conta pessoal da Microsoft, consulte a secção de problemas de código de verificação de resolução de **problemas** da informação de segurança da conta da Microsoft & artigo de códigos de [verificação.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- Para dispositivos que executam o iOS, também pode fazer o back up as credenciais da sua conta e configurações de aplicações relacionadas, como a ordem das suas contas, para a nuvem. Para mais informações, consulte [Backup e recupere com a aplicação Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
