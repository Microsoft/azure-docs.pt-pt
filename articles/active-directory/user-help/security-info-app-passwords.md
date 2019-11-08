---
title: Criar senhas de aplicativo em informações de segurança (versão prévia) – Azure AD
description: Configure senhas geradas automaticamente (senhas de aplicativo) para usar com qualquer aplicativo sem navegador ou qualquer aplicativo que não ofereça suporte à verificação de dois fatores em sua organização. Essa senha de aplicativo é separada de uma senha normal e pode ser configurada na página informações de segurança.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb26d90772fc9e3e3e506946363c76cf02e6b2ef
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820329"
---
# <a name="create-app-passwords-from-your-security-info-preview-page"></a>Criar senhas de aplicativo de sua página de informações de segurança (versão prévia)

Determinados aplicativos, como o Outlook 2010, não dão suporte à verificação em duas etapas. Essa falta de suporte significa que, se você estiver usando a verificação em duas etapas em sua organização, o aplicativo não funcionará. Para contornar esse problema, você pode criar uma senha gerada automaticamente para usar com cada aplicativo sem navegador, separado da sua senha normal.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>O administrador pode não permitir que você use senhas de aplicativo. Se você não vir **as senhas de aplicativo** como uma opção, elas não estão disponíveis em sua organização.

Ao usar senhas de aplicativo, é importante lembrar:

- As senhas de aplicativo são geradas automaticamente e devem ser criadas e inseridas uma vez por aplicativo.

- Há um limite de 40 senhas por usuário. Se você tentar criar uma após esse limite, será solicitado que você exclua uma senha existente antes de ter permissão para criar a nova.

    >[!Note]
    >Os clientes do Office 2013 (incluindo o Outlook) dão suporte a novos protocolos de autenticação e podem ser usados com a verificação em duas etapas. Esse suporte significa que, após a verificação em duas etapas ser ativada, você não precisará mais de senhas de aplicativo para clientes do Office 2013. Para obter mais informações, consulte o artigo [como funciona a autenticação moderna para aplicativos cliente do office 2013 e do office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicativo

Se você usar a verificação em duas etapas com sua conta corporativa ou de estudante e o administrador tiver ativado a experiência de informações de segurança, você poderá criar e excluir suas senhas de aplicativo usando a página **informações de segurança** .

>[!Note]
>Se o administrador não ativou a experiência de informações de segurança, você deve seguir as instruções e informações na seção [gerenciar senhas de aplicativo para a verificação em duas etapas](multi-factor-authentication-end-user-app-passwords.md) .

### <a name="to-create-a-new-app-password"></a>Para criar uma nova senha de aplicativo

1. Entre em sua conta corporativa ou de estudante e, em seguida, vá para a página https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou no link no bloco **informações de segurança** e, em seguida, selecione **Adicionar método** na página **informações de segurança** .

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar um método** , selecione **senha do aplicativo** na lista suspensa e, em seguida, selecione **Adicionar**.

    ![Caixa Adicionar método, com a senha de aplicativo selecionada](media/security-info/securityinfo-myprofile-addpassword.png)

4. Digite o nome do aplicativo que requer a senha do aplicativo e, em seguida, selecione **Avançar**.

    ![Página senha do aplicativo, com o nome do aplicativo](media/security-info/securityinfo-myprofile-password-appname.png)

5. Copie o texto da caixa **senha** , Cole a senha na área senha do aplicativo (neste exemplo, Outlook 2010) e, em seguida, selecione **concluído**.

    ![Página senha do aplicativo, com o nome do aplicativo](media/security-info/securityinfo-myprofile-password-copytext.png)

    A senha é adicionada e você pode fazer logon com êxito em seu aplicativo no futuro.

## <a name="delete-your-app-passwords"></a>Excluir suas senhas de aplicativo

Se você não precisar mais usar um aplicativo que exija uma senha de aplicativo, poderá excluir a senha de aplicativo associada. A exclusão da senha do aplicativo libera um dos pontos de senha de aplicativo disponíveis para uso no futuro.

>[!Important]
>Se você excluir uma senha de aplicativo por engano, não há como desfazê-la. Você precisará criar uma nova senha de aplicativo e inseri-la novamente no aplicativo, seguindo as etapas na seção [criar novas senhas de aplicativo](#create-new-app-passwords) deste artigo.

### <a name="to-delete-an-app-password"></a>Para excluir uma senha de aplicativo

1. Na página **informações de segurança** , selecione o link **excluir** ao lado da opção **senha de aplicativo** para o aplicativo específico.

    ![Link para excluir o método de senha de aplicativo das informações de segurança](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Selecione **Sim** na caixa de confirmação para excluir a **senha do aplicativo**. Depois que a senha do aplicativo for excluída, ela será removida das informações de segurança e desaparecerá da página **informações de segurança** .

## <a name="for-more-information"></a>Para obter mais informações:

- Para obter mais informações sobre a página **informações de segurança** e como configurá-la, consulte [visão geral das informações de segurança](user-help-security-info-overview.md)
