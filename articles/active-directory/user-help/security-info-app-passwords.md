---
title: Criar palavras-passe de aplicativo sintetizar informações de Segurança (pré-visualização) - Azure AD
description: Crie senhas geradas automaticamente (palavras-passe de aplicação) para usar com qualquer aplicação não-navegador, ou qualquer aplicação que não suporte a verificação de dois fatores, na sua organização. Esta palavra-passe da aplicação é separada de uma senha normal e pode ser configurada a partir da página de informações de Segurança.
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
ms.openlocfilehash: d4076e379755518421ef79eca542ebdc99294a11
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704895"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Criar senhas de aplicação a partir da página informação de segurança (pré-visualização)

Determinados aplicativos, como o Outlook 2010, não dão suporte à verificação em duas etapas. Essa falta de suporte significa que, se você estiver usando a verificação em duas etapas em sua organização, o aplicativo não funcionará. Para contornar este problema, pode criar uma senha gerada automaticamente para usar com cada aplicação não-navegador, separada da sua senha normal.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>O seu administrador pode não permitir que utilize senhas de aplicação. Se não vir as **palavras-passe** da App como uma opção, não estão disponíveis na sua organização.

Ao usar senhas de aplicativo, é importante lembrar:

- As palavras-passe da aplicação são geradas automaticamente e devem ser criadas e introduzidas uma vez por app.

- Há um limite de 40 senhas por usuário. Se você tentar criar uma após esse limite, será solicitado que você exclua uma senha existente antes de ter permissão para criar a nova.

    >[!Note]
    >Os clientes do Office 2013 (incluindo o Outlook) suportam novos protocolos de autenticação e podem ser utilizados com verificação em duas etapas. Este suporte significa que, após a verificação em duas etapas, deixará de precisar de senhas de aplicação para clientes do Office 2013. Para mais informações, consulte o artigo de como a autenticação moderna funciona para o Office 2013 e artigo de [aplicações de clientes do Office 2016.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicação

Se utilizar a verificação em duas etapas com o seu trabalho ou conta escolar e o seu administrador tiver ligado a experiência de informação de segurança, pode criar e eliminar as suas palavras-passe da aplicação utilizando a página de informações de **Segurança.**

>[!Note]
>Se o seu administrador não tiver ligado a experiência de informação de segurança, deve seguir as instruções e informações nas [palavras-passe da aplicação Manage para a secção de verificação em duas etapas.](multi-factor-authentication-end-user-app-passwords.md)

### <a name="to-create-a-new-app-password"></a>Para criar uma nova senha de aplicação

1. Entre em sua conta corporativa ou de estudante e, em seguida, vá para a página https://myprofile.microsoft.com/.

    ![Minha página de perfil, mostrando links de informações de segurança realçadas](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações de segurança** no painel de navegação esquerdo ou no link no bloco **informações de segurança** e, em seguida, selecione **Adicionar método** na página **informações de segurança** .

    ![Página informações de segurança com a opção Adicionar método realçado](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página **Adicionar uma página de método,** selecione a **palavra-passe** da App a partir da lista de drop-down e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método, com senha de aplicação selecionada](media/security-info/securityinfo-myprofile-addpassword.png)

4. Digite o nome da aplicação que requer a palavra-passe da aplicação e, em seguida, selecione **Next**.

    ![Página de palavra-passe de aplicativo, com nome de app](media/security-info/securityinfo-myprofile-password-appname.png)

5. Copie o texto a partir da caixa **password,** cole a palavra-passe na área de palavra-passe da app (neste exemplo, Outlook 2010) e, em seguida, selecione **Done**.

    ![Página de palavra-passe de aplicativo, com nome de app](media/security-info/securityinfo-myprofile-password-copytext.png)

    A palavra-passe é adicionada e pode entrar com sucesso na sua aplicação.

## <a name="delete-your-app-passwords"></a>Elimine as suas palavras-passe da sua aplicação

Se já não precisar de utilizar uma aplicação que necessite de uma palavra-passe de aplicação, pode eliminar a palavra-passe da aplicação associada. A alugar a palavra-passe da aplicação liberta um dos pontos de senha de aplicação disponíveis para utilização no futuro.

>[!Important]
>Se eliminar uma palavra-passe da aplicação por engano, não há como desfazê-la. Terá de criar uma nova senha de aplicação e reinseri-la na aplicação, seguindo os passos na secção de [novas palavras-passe](#create-new-app-passwords) da aplicação Deste artigo.

### <a name="to-delete-an-app-password"></a>Para eliminar uma palavra-passe da aplicação

1. Na página de **informações** de Segurança, selecione o link **Delete** ao lado da opção de senha da **App** para a aplicação específica.

    ![Link para eliminar o método de palavra-passe da aplicação a partir de informações de segurança](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Selecione **Sim** da caixa de confirmação para eliminar a **palavra-passe**da App . Após a eliminação da palavra-passe da aplicação, é removida das suas informações de segurança e desaparece da página de informações de **Segurança.**

## <a name="for-more-information"></a>Para obter mais informações:

- Para mais informações sobre a página de **informações** de Segurança e como configurar, consulte a visão geral da [informação de segurança](user-help-security-info-overview.md)
