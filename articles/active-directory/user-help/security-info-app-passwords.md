---
title: Criar palavras-passe de aplicativo a partir da página de informações de segurança (pré-visualização) - Azure AD
description: Crie senhas geradas automaticamente (passwords de aplicação) para usar com qualquer aplicação não-navegador, ou qualquer aplicação que não suporte a verificação de dois fatores, na sua organização. Esta palavra-passe da aplicação é separada de uma senha normal e pode ser configurada a partir da página de informações de Segurança.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 84588130788e9be8d3be52a8ea0f3988dce7b952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537009"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Criar palavras-passe da aplicação na página Informações de segurança (pré-visualização)

Algumas aplicações, como o Outlook 2010, não suportam a verificação em duas etapas. Esta falta de suporte significa que se estiver a utilizar uma verificação em duas etapas na sua organização, a aplicação não funcionará. Para contornar este problema, pode criar uma palavra-passe gerada automaticamente para usar com cada aplicação não-browser, separada da sua senha normal.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>O seu administrador pode não permitir que utilize senhas de aplicação. Se não vê **as palavras-passe da App** como uma opção, não estão disponíveis na sua organização.

Ao utilizar palavras-passe de aplicações, é importante lembrar:

- As palavras-passe da aplicação são geradas automaticamente e devem ser criadas e inseridas uma vez por aplicação.

- Há um limite de 40 palavras-passe por utilizador. Se tentar criar um após esse limite, será solicitado a apagar uma palavra-passe existente antes de ser autorizado a criar a nova.

    >[!Note]
    >Os clientes do Office 2013 (incluindo o Outlook) suportam novos protocolos de autenticação e podem ser utilizados com verificação em duas etapas. Este suporte significa que, após a verificação em duas etapas, deixará de precisar de senhas de aplicação para clientes do Office 2013. Para mais informações, consulte o artigo [de autenticação moderna para o Office 2013 e o Office 2016.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicação

Se utilizar a verificação em duas etapas com o seu trabalho ou conta escolar e o seu administrador tiver ligado a experiência de informação de segurança, pode criar e eliminar as suas palavras-passe de aplicação utilizando a página **de informações de Segurança.**

>[!Note]
>Se o seu administrador não tiver ligado a experiência de informação de segurança, deve seguir as instruções e informações nas palavras-passe da aplicação Manage para a secção de verificação em [duas etapas.](multi-factor-authentication-end-user-app-passwords.md)

### <a name="to-create-a-new-app-password"></a>Para criar uma nova senha de aplicação

1. Inscreva-se na sua conta de trabalho ou escola e depois vá para a sua https://myaccount.microsoft.com/ página.

    ![A minha página de perfil, mostrando links de informações de segurança destacados](media/security-info/securityinfo-myprofile-page.png)

2. Selecione **informações** de segurança a partir do painel de navegação à esquerda ou do link no bloco **de informações de Segurança** e, em seguida, selecione Adicionar **método** a partir da página **de informações de Segurança.**

    ![Página de informações de segurança com opção de método adicionar realçada](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na página de método Adicionar uma página de **método,** selecione **a palavra-passe** da App a partir da lista de drop-down e, em seguida, selecione **Adicionar**.

    ![Adicionar caixa de método, com senha de aplicação selecionada](media/security-info/securityinfo-myprofile-addpassword.png)

4. Digite o nome da aplicação que requer a palavra-passe da aplicação e, em seguida, selecione **Next**.

    ![Screenshot que mostra a página "App password", com o nome da aplicação inserida.](media/security-info/securityinfo-myprofile-password-appname.png)

5. Copie o texto da caixa **palavra-passe,** cole a palavra-passe na área da palavra-passe da aplicação (neste exemplo, Outlook 2010) e, em seguida, selecione **Fazer**.

    ![Página de senha de aplicação, com nome de app](media/security-info/securityinfo-myprofile-password-copytext.png)

    A palavra-passe é adicionada e pode iniciar sessão com sucesso na sua aplicação em curso.

## <a name="delete-your-app-passwords"></a>Elimine as palavras-passe da sua aplicação

Se já não precisar de usar uma aplicação que exija uma palavra-passe da aplicação, pode eliminar a palavra-passe da aplicação associada. A eliminação da palavra-passe da aplicação liberta um dos pontos de senha de aplicação disponíveis para utilização no futuro.

>[!Important]
>Se eliminar uma palavra-passe da aplicação por engano, não há como desfazê-la. Terá de criar uma nova senha de aplicação e reintrodurá-la na aplicação, seguindo os passos na secção [de novas palavras-passe](#create-new-app-passwords) da aplicação deste artigo.

### <a name="to-delete-an-app-password"></a>Para eliminar uma palavra-passe de aplicação

1. Na página **de informações de Segurança,** selecione o link **Eliminar** ao lado da opção **de senha de aplicação** para a aplicação específica.

    ![Link para eliminar o método da palavra-passe da aplicação a partir de informações de segurança](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Selecione **Sim** da caixa de confirmação para eliminar a **palavra-passe da App.** Após a aplicação ser eliminada, é removida da sua informação de segurança e desaparece da página de informações de **Segurança.**

## <a name="for-more-information"></a>Para obter mais informações:

- Para obter mais informações sobre a página **de informações de Segurança** e como configurar, consulte a [visão geral da informação de Segurança](./security-info-setup-signin.md)