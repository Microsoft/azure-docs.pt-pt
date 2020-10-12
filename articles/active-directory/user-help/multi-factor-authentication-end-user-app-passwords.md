---
title: Como gerir as passwords das aplicações - Azure Ative Directory Microsoft Docs
description: Saiba mais sobre as palavras-passe das aplicações e para que são usadas no que diz respeito à verificação em duas etapas.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c9d8bb85aac87503ca641f6633668a6dc582882f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056086"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Gerir palavras-passe de aplicativos para verificação em duas etapas

>[!Important]
>O seu administrador pode não permitir que utilize senhas de aplicação. Se não vê **as palavras-passe da App** como uma opção, não estão disponíveis na sua organização.

Ao utilizar palavras-passe de aplicações, é importante lembrar:

- As palavras-passe da aplicação são geradas automaticamente e devem ser criadas e inseridas uma vez por aplicação.

- Há um limite de 40 palavras-passe por utilizador. Se tentar criar um após esse limite, será solicitado a apagar uma palavra-passe existente antes de ser autorizado a criar a nova.

    >[!Note]
    >Os clientes do Office 2013 (incluindo o Outlook) suportam novos protocolos de autenticação e podem ser utilizados com verificação em duas etapas. Este suporte significa que, após a verificação em duas etapas, deixará de precisar de senhas de aplicação para clientes do Office 2013. Para mais informações, consulte o artigo [de autenticação moderna para o Office 2013 e o Office 2016.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Criar novas senhas de aplicação

Durante o seu processo inicial de registo de verificação de dois fatores, é-lhe fornecida uma única senha de aplicação. Se precisar de mais do que um, terá que criá-los por si mesmo. Pode criar senhas de aplicação a partir de várias áreas, dependendo da configuração da verificação de dois fatores na sua organização. Para obter mais informações sobre o registo para utilizar a verificação de dois fatores com a sua conta de trabalho ou escola, consulte [a Visão Geral para verificação de dois fatores e a sua conta de trabalho ou escola](multi-factor-authentication-end-user-first-time.md) e seus artigos relacionados.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Onde criar e eliminar as suas palavras-passe de aplicação

Pode criar e eliminar palavras-passe de aplicações, com base na forma como utiliza a verificação de dois fatores:

- **A sua organização utiliza a verificação de dois fatores e a página adicional de verificação de segurança.** Se estiver a utilizar o seu trabalho ou conta escolar (como, por exemplo, alain@contoso.com ) com verificação de dois fatores na sua organização, pode gerir as suas palavras-passe da aplicação a partir da página adicional de verificação de [segurança.](https://account.activedirectory.windowsazure.com/Proofup.aspx) Para obter instruções detalhadas, consulte [Criar e eliminar palavras-passe de aplicações utilizando a página adicional de verificação de segurança](#create-and-delete-app-passwords-from-the-additional-security-verification-page) neste artigo.

- **A sua organização utiliza a verificação de dois fatores e o portal Office 365.** Se estiver a utilizar o seu trabalho ou conta escolar (como, por exemplo, alain@contoso.com a verificação de dois fatores e as aplicações microsoft 365 na sua organização, pode gerir as suas palavras-passe da aplicação a partir da página do [portal do Office 365](https://www.office.com). Para obter instruções detalhadas, consulte [Criar e eliminar palavras-passe de aplicações utilizando o portal Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) neste artigo.

- **Está a usar a verificação de dois fatores com uma conta pessoal da Microsoft.** Se estiver a utilizar uma conta pessoal da Microsoft (como, por exemplo, alain@outlook.com ) com verificação de dois fatores, pode gerir as suas palavras-passe da aplicação a partir da [página básica de Segurança](https://account.microsoft.com/security/). Para obter instruções detalhadas, consulte [utilizar palavras-passe de aplicações com aplicações que não suportem a verificação em duas etapas.](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Criar e eliminar palavras-passe de aplicações a partir da página adicional de verificação de segurança

Pode criar e eliminar palavras-passe de aplicações a partir da página adicional de **verificação de segurança** para o seu trabalho ou conta escolar.

1. Inscreva-se na [página de verificação de segurança adicional](https://account.activedirectory.windowsazure.com/Proofup.aspx)e, em seguida, selecione **palavras-passe da App**.

    ![Página de passwords da aplicação, com o separador de senhas da App em destaque](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. **Selecione Criar**, digite o nome da aplicação que requer a palavra-passe da aplicação e, em seguida, selecione **Next**.

    ![Crie a página de palavras-passe da aplicação, com nome de app que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Copie a palavra-passe a partir da página de senha da **aplicação** e, em seguida, **selecione Fechar**.

    ![A página de senha da sua aplicação com a palavra-passe para a sua aplicação especificada](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. A partir da página de **palavras-passe** da App, certifique-se de que a sua aplicação está listada.

     ![Página de passwords da aplicação, com nova app mostrada na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Abra a aplicação para a qual criou a palavra-passe da aplicação (por exemplo, Outlook 2010) e, em seguida, cole a palavra-passe da aplicação quando a pedida. Só deve fazer isso uma vez por aplicação.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Para eliminar uma palavra-passe da aplicação utilizando a página de palavras-passe da App

1. A partir da página **de palavras-passe** da App, selecione **Eliminar** ao lado da palavra-passe da aplicação que pretende eliminar.

   ![Eliminar uma senha de aplicação](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** para confirmar que pretende eliminar a palavra-passe e, em seguida, selecione **Fechar**.

    A palavra-passe da aplicação é eliminada com sucesso.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Criar e eliminar senhas de aplicação utilizando o portal Office 365

Se utilizar a verificação em duas etapas com o seu trabalho ou conta escolar e as suas aplicações Microsoft 365, pode criar e eliminar as suas palavras-passe de aplicação utilizando o portal Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Criar palavras-passe de aplicativos utilizando o portal Office 365

1. Faça o sômis à sua conta de trabalho ou escola, vá à [página minha conta,](https://portal.office.com)selecione **Segurança & privacidade,** e depois expanda **a verificação adicional de segurança**.

    ![Portal do office mostrando área de verificação de segurança adicional expandida](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Selecione o texto que diz, **Crie e gere senhas de aplicação** para abrir a página **de palavras-passe** da App.

    ![Página de passwords da aplicação, com o separador de senhas da App em destaque](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. **Selecione Criar**, digite o nome da aplicação que requer a palavra-passe da aplicação e, em seguida, selecione **Next**.

    ![Crie a página de palavras-passe da aplicação, com nome de app que precisa de senha](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Copie a palavra-passe a partir da página de senha da **aplicação** e, em seguida, **selecione Fechar**.

    ![A página de senha da sua aplicação com a palavra-passe para a sua aplicação especificada](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. A partir da página de **palavras-passe** da App, certifique-se de que a sua aplicação está listada.

     ![Página de passwords da aplicação, com nova app mostrada na lista](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Abra a aplicação para a qual criou a palavra-passe da aplicação (por exemplo, Outlook 2010) e, em seguida, cole a palavra-passe da aplicação quando a pedida. Só deve fazer isso uma vez por aplicação.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Para eliminar palavras-passe de aplicativos utilizando a página de palavras-passe da App

1. A partir da página **de palavras-passe** da App, selecione **Eliminar** ao lado da palavra-passe da aplicação que pretende eliminar.

   ![Eliminar uma senha de aplicação](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecione **Sim** na caixa de confirmação e, em seguida, selecione **Fechar**.

    A palavra-passe da aplicação é eliminada com sucesso.

## <a name="if-your-app-passwords-arent-working-properly"></a>Se as palavras-passe da sua aplicação não estiverem a funcionar corretamente

Certifique-se de que escreveu a sua palavra-passe corretamente. Se tiver a certeza de que introduziu a sua palavra-passe corretamente, pode tentar iniciar sessão novamente e criar uma nova palavra-passe da aplicação. Se nenhuma dessas opções corrigir o seu problema, contacte o balcão de ajuda da sua organização para que possam eliminar as suas palavras-passe de aplicações existentes, permitindo-lhe criar novas.

## <a name="next-steps"></a>Passos seguintes

- [Gerir as definições de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)

- Experimente a [aplicação Microsoft Authenticator](user-help-auth-app-download-install.md) para verificar os seus insusentus com notificações de aplicações, em vez de receber textos ou chamadas.
