---
title: Adicione uma conta pessoal da Microsoft à aplicação Microsoft Authenticator - Azure AD
description: Adicione contas pessoais da Microsoft, como para Outlook.com ou Xbox LIVE à aplicação Microsoft Authenticator para verificar a sua identidade enquanto utiliza a verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 8945f7a49f4c04b3265cb79c88c9acb287c50d10
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704753"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Adicione contas pessoais da Microsoft à aplicação Microsoft Authenticator

Adicione as suas contas pessoais da Microsoft, como para Outlook.com e Xbox LIVE à aplicação Microsoft Authenticator para o processo padrão de verificação de dois fatores e o método de entrada de telefone sem palavras-passe.

- **Método padrão de verificação de dois fatores.** Digite o seu nome de utilizador e palavra-passe no dispositivo para o qual está a iniciar sessão e, em seguida, escolha se a aplicação Microsoft Authenticator envia uma notificação ou se prefere copiar o código de verificação associado a partir do ecrã de **Contas** da aplicação Microsoft Authenticator.

- **Método de entrada sem palavras-passe.** Digite o seu nome de utilizador no dispositivo em que está a iniciar sessão para a sua conta pessoal da Microsoft e, em seguida, use o seu dispositivo móvel para verificar se é você usando a sua impressão digital, rosto ou PIN. Para esse método, você não precisa inserir sua senha.

>[!Important]
>Para poder adicionar sua conta, você deve baixar e instalar o aplicativo Microsoft Authenticator. Se ainda não tiver feito isso, siga as etapas no artigo [baixar e instalar o aplicativo](user-help-auth-app-download-install.md) .

## <a name="add-your-personal-microsoft-account"></a>Adicione a sua conta pessoal da Microsoft

Pode adicionar a sua conta pessoal da Microsoft, ligando primeiro a verificação de dois fatores e, em seguida, adicionando a conta à app.

>[!Note]
>se planeia utilizar apenas um acesso telefónico sem palavras-passe para a sua conta pessoal da Microsoft, não tem de ativar a verificação de dois fatores. No entanto, para segurança adicional da conta, recomendamos que ligue a verificação de dois fatores.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. No seu computador, vá à página [de bases](https://account.microsoft.com/security) da Segurança e inscreva-se utilizando a sua conta pessoal da Microsoft. Por exemplo, alain@outlook.com.

2. Na parte inferior da página básica de **Segurança,** escolha o link **mais opções** de segurança.

    ![Página básica de segurança com o link "mais opções de segurança" em destaque](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Vá à secção **de verificação de duas etapas** e opte por ligar a função **.** Também pode desligá-lo aqui se já não quiser usá-lo com a sua conta pessoal.

### <a name="add-your-microsoft-account-to-the-app"></a>Adicione a sua conta Microsoft à aplicação

1. Abra a aplicação Microsoft Authenticator no seu dispositivo móvel.

2. **Selecione Adicionar conta** a partir do ícone Personalizar e **controlar** no canto superior direito.

    ![Página de contas, com o ícone Personalizar e controlar em destaque](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Na página **Adicionar,** escolha **conta Pessoal**.

4. Inscreva-se na sua conta pessoal, utilizando o endereço de e-mail apropriado (como alain@outlook.com), e depois escolha **A Seguinte**.

    >[!Note]
    >Se não tiver uma conta pessoal da Microsoft, pode criar uma aqui.

5. Insira a sua palavra-passe e, em seguida, escolha **Iniciar sessão**.

    A sua conta pessoal é adicionada à aplicação Microsoft Authenticator.

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar suas contas ao aplicativo, você pode entrar usando o aplicativo autenticador em seu dispositivo. Para obter mais informações, consulte [entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Se você estiver tendo problemas para obter seu código de verificação para seu conta Microsoft pessoal, consulte a seção **Solucionando** problemas de código de verificação do artigo [conta Microsoft informações de segurança & códigos de verificação](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Para dispositivos que executam o iOS, você também pode fazer backup de suas credenciais de conta e configurações de aplicativo relacionadas, como a ordem de suas contas, para a nuvem. Para obter mais informações, consulte [fazer backup e recuperar com Microsoft Authenticator aplicativo](user-help-auth-app-backup-recovery.md).
