---
title: Como gerenciar senhas de aplicativo-Azure Active Directory | Microsoft Docs
description: Saiba mais sobre as senhas de aplicativo e para que elas são usadas em relação à verificação em duas etapas.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: lizross
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9d997f9ced0fbd921abfb13cda5a4845eb3763
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616208"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gerenciar senhas de aplicativo para verificação em duas etapas

>[!Important]
>O administrador pode não permitir que você use senhas de aplicativo. Se você não vir **as senhas de aplicativo** como uma opção, elas não estão disponíveis em sua organização.

Ao usar senhas de aplicativo, é importante lembrar:

- As senhas de aplicativo são geradas automaticamente e inseridas apenas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar uma após esse limite, será solicitado que você exclua uma senha existente antes de ter permissão para criar a nova.

- Use uma senha de aplicativo por dispositivo, não por aplicativo. Por exemplo, crie uma única senha para todos os aplicativos em seu laptop e, em seguida, outra senha única para todos os aplicativos na área de trabalho.

    >[!Note]
    >Os clientes do Office 2013 (incluindo o Outlook) dão suporte a novos protocolos de autenticação e podem ser usados com a verificação em duas etapas. Esse suporte significa que, após a verificação em duas etapas ser ativada, você não precisará mais de senhas de aplicativo para clientes do Office 2013. Para obter mais informações, consulte o artigo [como funciona a autenticação moderna para aplicativos cliente do office 2013 e do office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicativo

Durante o processo inicial de registro de verificação de dois fatores, você receberá uma única senha de aplicativo. Se você precisar de mais de um, precisará criá-los por conta própria. Você pode criar senhas de aplicativo de várias áreas, dependendo de como a verificação de dois fatores é configurada em sua organização. Para obter mais informações sobre como registrar-se para usar a verificação de dois fatores com sua conta corporativa ou de estudante, consulte [visão geral para a verificação de dois fatores e sua conta corporativa ou de estudante](multi-factor-authentication-end-user-first-time.md) e seus artigos relacionados.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Onde criar e excluir suas senhas de aplicativo

Você pode criar e excluir senhas de aplicativo com base em como usar a verificação de dois fatores:

- **Sua organização usa a verificação de dois fatores e a página de verificação de segurança adicional.** Se você estiver usando sua conta corporativa ou de estudante (como, alain@contoso.com) com a verificação de dois fatores em sua organização, você poderá gerenciar suas senhas de aplicativo na [página de verificação de segurança adicional](https://account.activedirectory.windowsazure.com/Proofup.aspx). Para obter instruções detalhadas, consulte [criar e excluir senhas de aplicativo usando a página de verificação de segurança adicional](#create-and-delete-app-passwords-from-the-additional-security-verification-page) neste artigo.

- **Sua organização usa a verificação de dois fatores e o portal do Office 365.** Se você estiver usando sua conta corporativa ou de estudante (como, alain@contoso.com), verificação de dois fatores e aplicativos do Office 365 em sua organização, você poderá gerenciar suas senhas de aplicativo na [página do portal do Office 365](https://office.portal.com). Para obter instruções detalhadas, consulte [criar e excluir senhas de aplicativo usando o portal do Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) neste artigo.

- **Você está usando a verificação de dois fatores com um conta Microsoft pessoal.** Se você estiver usando um conta Microsoft pessoal (como, alain@outlook.com) com a verificação de dois fatores, poderá gerenciar suas senhas de aplicativo na [página noções básicas de segurança](https://account.microsoft.com/security/). Para obter instruções detalhadas, consulte [usando senhas de aplicativo com aplicativos que não dão suporte à verificação em duas etapas](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Criar e excluir senhas de aplicativo da página de verificação de segurança adicional

Você pode criar e excluir senhas de aplicativo da página de **verificação de segurança adicional** para sua conta corporativa ou de estudante.

1. Entre na [página verificação de segurança adicional](https://account.activedirectory.windowsazure.com/Proofup.aspx)e, em seguida, selecione **senhas de aplicativo**.

    ![Página senhas de aplicativo, com a guia senhas de aplicativo realçada](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Selecione **criar**, digite o nome do aplicativo que requer a senha do aplicativo e, em seguida, selecione **Avançar**.

    ![Página criar senhas de aplicativo, com o nome do aplicativo que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Copie a senha da página **sua senha de aplicativo** e selecione **fechar**.

    ![Sua página de senha de aplicativo com a senha para seu aplicativo especificado](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Na página **senhas de aplicativo** , verifique se seu aplicativo está listado.

     ![Página senhas de aplicativo, com o novo aplicativo mostrado na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Abra o aplicativo para o qual você criou a senha de aplicativo (por exemplo, Outlook 2010) e cole a senha do aplicativo quando solicitado. Você só deve fazer isso uma vez por aplicativo.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Para excluir uma senha de aplicativo usando a página senhas de aplicativo

1. Na página **senhas de aplicativo** , selecione **excluir** ao lado da senha de aplicativo que você deseja excluir.

   ![Excluir uma senha de aplicativo](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** para confirmar que deseja excluir a senha e, em seguida, selecione **fechar**.

    A senha do aplicativo foi excluída com êxito.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Criar e excluir senhas de aplicativo usando o portal do Office 365

Se você usar a verificação em duas etapas com sua conta corporativa ou de estudante e seus aplicativos do Office 365, você poderá criar e excluir suas senhas de aplicativo usando o portal do Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Para criar senhas de aplicativo usando o portal do Office 365

1. Entre no Office 365 e vá para a [página minha conta](https://portal.office.com), selecione **segurança & privacidade**e, em seguida, expanda **verificação de segurança adicional**.

    ![Portal do Office mostrando área de verificação de segurança adicional expandida](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Selecione o texto que diz, **crie e gerencie senhas de aplicativo** para abrir a página **senhas de aplicativo** .

    ![Página senhas de aplicativo, com a guia senhas de aplicativo realçada](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Selecione **criar**, digite o nome do aplicativo que requer a senha do aplicativo e, em seguida, selecione **Avançar**.

    ![Página criar senhas de aplicativo, com o nome do aplicativo que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Copie a senha da página **sua senha de aplicativo** e selecione **fechar**.

    ![Sua página de senha de aplicativo com a senha para seu aplicativo especificado](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Na página **senhas de aplicativo** , verifique se seu aplicativo está listado.

     ![Página senhas de aplicativo, com o novo aplicativo mostrado na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Abra o aplicativo para o qual você criou a senha de aplicativo (por exemplo, Outlook 2010) e cole a senha do aplicativo quando solicitado. Você só deve fazer isso uma vez por aplicativo.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Para excluir senhas de aplicativo usando a página senhas de aplicativo

1. Na página **senhas de aplicativo** , selecione **excluir** ao lado da senha de aplicativo que você deseja excluir.

   ![Excluir uma senha de aplicativo](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** na caixa de confirmação e, em seguida, selecione **fechar**.

    A senha do aplicativo foi excluída com êxito.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se suas senhas de aplicativo não estiverem funcionando corretamente

Certifique-se de que você digitou sua senha corretamente. Se tiver certeza de que digitou sua senha corretamente, você poderá tentar entrar novamente e criar uma nova senha de aplicativo. Se nenhuma dessas opções corrigir o problema, entre em contato com o suporte técnico de sua organização para que ele possa excluir suas senhas de aplicativo existentes, permitindo que você crie as novas marcas.

## <a name="next-steps"></a>Passos seguintes

- [Gerenciar suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)

- Experimente o [aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) para verificar suas entradas com notificações de aplicativo, em vez de receber textos ou chamadas.
