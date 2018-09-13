---
title: Configurar a inscrição e início de sessão com uma conta Microsoft através do Azure Active Directory B2C | Documentos da Microsoft
description: Forneça a inscrição e início de sessão para os clientes com contas Microsoft nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 911f8df6aa513a289cd15ea7fc96d7fc8806bb4a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719516"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta Microsoft através do Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Criar uma aplicação da conta Microsoft

Para utilizar uma conta Microsoft como um fornecedor de identidade no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação no seu inquilino que a representa. Se ainda não tiver uma conta Microsoft, pode obtê-la em [ https://www.live.com/ ](https://www.live.com/).

1. Inicie sessão para o [Portal de registo de aplicação do Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) com as credenciais da conta Microsoft.
2. No canto superior direito, selecione **adicionar uma aplicação**.
3. Introduza um **nome** para a sua aplicação. Por exemplo, *MSAapp1*.
4. Selecione **gerar nova palavra-passe** e certifique-se de que copia a palavra-passe para utilizar quando configurar o fornecedor de identidade. Também copiar a **Id da aplicação**. 
5. Selecione **adicionar plataforma**e, em seguida e escolha **Web**.
4. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` no **redirecionar URLs**. Substitua `your-tenant-name` com o nome do seu inquilino.
5. Selecione **Guardar**.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurar uma conta Microsoft como fornecedor de identidade

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.  

    ![Mudar para o inquilino do Azure AD B2C](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Fornecer um **nome**. Por exemplo, introduza *MSA*.
6. Selecione **tipo de fornecedor de identidade**, selecione **Account Microsoft**e clique em **OK**.
7. Selecione **configurar este fornecedor de identidade** e introduza o Id da aplicação que registou anteriormente como a **ID de cliente** e introduza a palavra-passe que registou como o **segredo do cliente**da aplicação de conta Microsoft que criou anteriormente.
8. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração da conta Microsoft.

