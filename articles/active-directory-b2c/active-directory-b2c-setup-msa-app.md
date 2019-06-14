---
title: Configurar a inscrição e início de sessão com uma conta Microsoft - Azure Active Directory B2C
description: Forneça a inscrição e início de sessão para os clientes com contas Microsoft nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 63aa93e72d76d430fc7e026478d91e496f27eae6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055110"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta Microsoft através do Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Criar uma aplicação da conta Microsoft

Para utilizar uma conta Microsoft como uma [fornecedor de identidade](active-directory-b2c-reference-oidc.md) no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação no seu inquilino que a representa. Se ainda não tiver uma conta Microsoft, pode obtê-la em [ https://www.live.com/ ](https://www.live.com/).

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **registos das aplicações**.
1. Selecione **novo registo**
1. Introduza um **nome** para a sua aplicação. Por exemplo, *MSAapp1*.
1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, o Skype, Xbox, Outlook.com)** . Esta opção destina-se o conjunto mais ampla de identidades da Microsoft.

   Para obter mais informações sobre as seleções de tipo de conta diferente, consulte [início rápido: Registar uma aplicação com a plataforma de identidade do Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Sob **URI de redirecionamento (opcional)** , selecione **Web** e introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C.
1. Selecione **registar**
1. Registo a **ID da aplicação (cliente)** mostrado na página de descrição geral do aplicativo. Precisará disto quando configurar o fornecedor de identidade na secção seguinte.
1. Selecione **certificados e segredos**
1. Clique em **novo segredo do cliente**
1. Introduza um **Descrição** para o segredo, por exemplo *palavra-passe de aplicação 1*e, em seguida, clique em **Add**.
1. Registrar a palavra-passe de aplicação mostrada os **valor** coluna. Precisará disto quando configurar o fornecedor de identidade na secção seguinte.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurar uma conta Microsoft como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
1. Fornecer um **nome**. Por exemplo, introduza *MSA*.
1. Selecione **tipo de fornecedor de identidade**, selecione **Account Microsoft**e clique em **OK**.
1. Selecione **configurar este fornecedor de identidade** e introduza o ID da aplicação (cliente) que registou anteriormente no **ID de cliente** texto caixa e introduza o segredo do cliente que registou o **cliente segredo** caixa de texto.
1. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração da conta Microsoft.
