---
title: Adicionar um conta Microsoft pessoal a um aplicativo autenticador-Azure AD
description: Como adicionar suas contas pessoais da Microsoft, como para Outlook.com ou Xbox LIVE ao aplicativo Microsoft Authenticator para verificação de dois fatores.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca01aad76bfbeba0cf56be8ee74287a79f6346e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820246"
---
# <a name="add-your-personal-microsoft-accounts"></a>Adicionar suas contas pessoais da Microsoft

Adicione suas contas pessoais da Microsoft, como para Outlook.com e Xbox LIVE ao aplicativo Microsoft Authenticator para o processo de verificação de dois fatores padrão e o método de entrada de telefone sem senha.

- **Método de verificação de dois fatores padrão.** Digite seu nome de usuário e senha no dispositivo no qual você está fazendo logon e, em seguida, escolha se o aplicativo Microsoft Authenticator envia uma notificação ou se você prefere copiar o código de verificação associado da tela **contas** da Microsoft Aplicativo autenticador.

- **Método de entrada sem senha.** Digite seu nome de usuário no dispositivo no qual você está fazendo logon para seu conta Microsoft pessoal e, em seguida, use seu dispositivo móvel para verificar se é você usando sua impressão digital, face ou PIN. Para esse método, você não precisa inserir sua senha.

>[!Important]
>Para poder adicionar sua conta, você deve baixar e instalar o aplicativo Microsoft Authenticator. Se ainda não tiver feito isso, siga as etapas no artigo [baixar e instalar o aplicativo](user-help-auth-app-download-install.md) .

## <a name="add-your-personal-microsoft-account"></a>Adicionar seu conta Microsoft pessoal

Você pode adicionar seus conta Microsoft pessoais primeiro ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

>[!Note]
>Se você planeja usar apenas a entrada por telefone sem senha para seu conta Microsoft pessoal, não precisará ativar a verificação de dois fatores. No entanto, para segurança de conta adicional, recomendamos que você ative a verificação de dois fatores.

### <a name="turn-on-two-factor-verification"></a>Ativar a verificação de dois fatores

1. Em seu computador, acesse sua página de [noções básicas de segurança](https://account.microsoft.com/security) e entre usando seu conta Microsoft pessoal. Por exemplo, alain@outlook.com.

2. Na parte inferior da página **noções básicas de segurança** , escolha o link **mais opções de segurança** .

    ![Página noções básicas de segurança com o link "mais opções de segurança" realçado](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Vá para a seção de verificação **em** **duas etapas** e escolha Ativar o recurso. Você também pode desativá-lo aqui se não quiser mais usá-lo com sua conta pessoal.

### <a name="add-your-microsoft-account-to-the-app"></a>Adicionar seu conta Microsoft ao aplicativo

1. Abra o aplicativo Microsoft Authenticator em seu dispositivo móvel.

2. Selecione **adicionar conta** no ícone **Personalizar e controlar** no canto superior direito.

    ![Página contas, com o ícone Personalizar e controlar realçado](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. Na página **adicionar conta** , escolha **conta pessoal**.

4. Entre em sua conta pessoal, usando o endereço de email apropriado (como alain@outlook.com) e, em seguida, escolha **Avançar**.

    >[!Note]
    >Se você não tiver um conta Microsoft pessoal, poderá criar um aqui.

5. Insira sua senha e, em seguida, escolha **entrar**.

    Sua conta pessoal é adicionada ao aplicativo Microsoft Authenticator.

## <a name="next-steps"></a>Passos seguintes

- Depois de adicionar suas contas ao aplicativo, você pode entrar usando o aplicativo autenticador em seu dispositivo. Para obter mais informações, consulte [entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Se você estiver tendo problemas para obter seu código de verificação para seu conta Microsoft pessoal, consulte a seção **Solucionando** problemas de código de verificação do artigo [conta Microsoft informações de segurança & códigos de verificação](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- Para dispositivos que executam o iOS, você também pode fazer backup de suas credenciais de conta e configurações de aplicativo relacionadas, como a ordem de suas contas, para a nuvem. Para obter mais informações, consulte [fazer backup e recuperar com Microsoft Authenticator aplicativo](user-help-auth-app-backup-recovery.md).
