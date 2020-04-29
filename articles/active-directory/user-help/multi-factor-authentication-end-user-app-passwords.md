---
title: Como gerir as palavras-passe das apps - Diretório Ativo Azure / Microsoft Docs
description: Saiba mais sobre as palavras-passe das aplicações e para que são usadas no que diz respeito à verificação em duas etapas.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253224"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gerir senhas de aplicação para verificação em duas etapas

>[!Important]
>O seu administrador pode não permitir que utilize senhas de aplicação. Se não vir as **palavras-passe** da App como uma opção, não estão disponíveis na sua organização.

Ao utilizar palavras-passe de aplicativos, é importante lembrar:

- As palavras-passe da aplicação são geradas automaticamente e devem ser criadas e introduzidas uma vez por app.

- Há um limite de 40 senhas por utilizador. Se tentar criar uma após esse limite, será-lhe solicitado que apague uma palavra-passe existente antes de ser autorizada a criar a nova.

    >[!Note]
    >Os clientes do Office 2013 (incluindo o Outlook) suportam novos protocolos de autenticação e podem ser utilizados com verificação em duas etapas. Este suporte significa que, após a verificação em duas etapas, deixará de precisar de senhas de aplicação para clientes do Office 2013. Para mais informações, consulte o artigo de como a autenticação moderna funciona para o Office 2013 e artigo de [aplicações de clientes do Office 2016.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicação

Durante o seu processo inicial de registo de verificação de dois fatores, é-lhe fornecida uma única senha de aplicação. Se precisar de mais do que um, terá que criá-los. Pode criar senhas de aplicação de várias áreas, dependendo de como a verificação de dois fatores é configurada na sua organização. Para obter mais informações sobre o registo para utilizar a verificação de dois fatores com o seu trabalho ou conta escolar, consulte [a visão geral para verificação de dois fatores e](multi-factor-authentication-end-user-first-time.md) a sua conta de trabalho ou escola e os seus artigos relacionados.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Onde criar e eliminar as suas palavras-passe da aplicação

Pode criar e eliminar palavras-passe de aplicações, com base na forma como utiliza a verificação de dois fatores:

- **A sua organização utiliza verificação de dois fatores e a página adicional de verificação de segurança.** Se estiver a utilizar o seu trabalho alain@contoso.comou conta escolar (por exemplo, ) com verificação de dois fatores na sua organização, pode gerir as suas palavras-passe da sua aplicação a partir da página adicional de [verificação](https://account.activedirectory.windowsazure.com/Proofup.aspx)de segurança . Para obter instruções detalhadas, consulte [Criar e eliminar palavras-passe](#create-and-delete-app-passwords-from-the-additional-security-verification-page) da aplicação utilizando a página adicional de verificação de segurança neste artigo.

- **A sua organização usa a verificação de dois fatores e o portal office 365.** Se estiver a usar o seu trabalho alain@contoso.comou conta escolar (como, por exemplo), verificação de dois fatores e aplicações do Office 365 na sua organização, pode gerir as suas palavras-passe da sua aplicação a partir da página do [portal Office 365](https://www.office.com). Para obter instruções detalhadas, consulte Criar e eliminar senhas de [aplicação utilizando o portal Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) neste artigo.

- **Está a usar a verificação de dois fatores com uma conta pessoal da Microsoft.** Se estiver a utilizar uma conta pessoal alain@outlook.comda Microsoft (por exemplo, ) com verificação de dois fatores, pode gerir as suas palavras-passe da aplicação a partir da [página de bases](https://account.microsoft.com/security/)da Segurança . Para obter instruções detalhadas, consulte [A utilização de palavras-passe de aplicativos com aplicações que não suportam a verificação em duas etapas](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Criar e eliminar senhas de aplicação a partir da página adicional de verificação de segurança

Pode criar e eliminar senhas de aplicação a partir da página adicional de **verificação** de segurança para o seu trabalho ou conta escolar.

1. Inscreva-se na página adicional de [verificação](https://account.activedirectory.windowsazure.com/Proofup.aspx)de segurança e, em seguida, selecione **as palavras-passe**da App .

    ![Página de passwords de aplicativos, com o separador de palavras-passe da App em destaque](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Selecione **Criar,** digite o nome da aplicação que requer a palavra-passe da aplicação e, em seguida, selecione **Next**.

    ![Crie página de passwords de aplicativos, com nome de app que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Copie a palavra-passe da página de **palavra-passe** da sua aplicação e, em seguida, selecione **Fechar**.

    ![A página da sua palavra-passe da sua aplicação com a palavra-passe para a sua aplicação especificada](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Na página de passwords da **App,** certifique-se de que a sua aplicação está listada.

     ![Página de passwords de aplicativos, com nova app mostrada na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Abra a aplicação para a sua criação (por exemplo, Outlook 2010) e, em seguida, repasse a palavra-passe da aplicação quando lhe for pedida. Só deve fazer isto uma vez por app.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Para eliminar uma palavra-passe da aplicação utilizando a página de passwords da App

1. A partir da página de passwords da **App,** selecione **Excluir** ao lado da palavra-passe da aplicação que pretende eliminar.

   ![Eliminar uma palavra-passe de aplicação](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** para confirmar que pretende eliminar a palavra-passe e, em seguida, selecione **Fechar**.

    A palavra-passe da aplicação é eliminada com sucesso.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Criar e eliminar senhas de aplicação utilizando o portal Office 365

Se utilizar a verificação em duas etapas com o seu trabalho ou conta escolar e as suas aplicações office 365, pode criar e eliminar as suas palavras-passe da aplicação através do portal Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Para criar senhas de aplicação usando o portal Office 365

1. Inscreva-se no Office 365 e depois vá à [página da minha conta,](https://portal.office.com)selecione **Segurança & privacidade,** e depois expanda a **verificação adicional**de segurança.

    ![Portal de escritório mostrando área adicional de verificação de segurança alargada](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Selecione o texto que diz, **Crie e gere as palavras-passe** da aplicação para abrir a página de passwords da **App.**

    ![Página de passwords de aplicativos, com o separador de palavras-passe da App em destaque](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Selecione **Criar,** digite o nome da aplicação que requer a palavra-passe da aplicação e, em seguida, selecione **Next**.

    ![Crie página de passwords de aplicativos, com nome de app que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Copie a palavra-passe da página de **palavra-passe** da sua aplicação e, em seguida, selecione **Fechar**.

    ![A página da sua palavra-passe da sua aplicação com a palavra-passe para a sua aplicação especificada](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Na página de passwords da **App,** certifique-se de que a sua aplicação está listada.

     ![Página de passwords de aplicativos, com nova app mostrada na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Abra a aplicação para a sua criação (por exemplo, Outlook 2010) e, em seguida, repasse a palavra-passe da aplicação quando lhe for pedida. Só deve fazer isto uma vez por app.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Para eliminar as palavras-passe da aplicação utilizando a página de passwords da App

1. A partir da página de passwords da **App,** selecione **Excluir** ao lado da palavra-passe da aplicação que pretende eliminar.

   ![Eliminar uma palavra-passe de aplicação](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** na caixa de confirmação e, em seguida, selecione **Fechar**.

    A palavra-passe da aplicação é eliminada com sucesso.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se as suas palavras-passe da sua aplicação não estiverem funcionando corretamente

Certifique-se de que escreveu corretamente a sua palavra-passe. Se tiver a certeza de que inseriu corretamente a sua palavra-passe, pode tentar iniciar sessão e criar uma nova senha de aplicação. Se nenhuma dessas opções corrigir o seu problema, contacte o balcão de Ajuda da sua organização para que possam eliminar as suas senhas de aplicação existentes, permitindo-lhe criar novas.

## <a name="next-steps"></a>Passos seguintes

- [Gerencie as definições de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)

- Experimente a [aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) para verificar os seus sessão com notificações de aplicações, em vez de receber textos ou chamadas.
