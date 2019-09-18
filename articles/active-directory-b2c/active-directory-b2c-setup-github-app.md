---
title: Configure a inscrição e a entrada com uma conta do GitHub-Azure Active Directory B2C
description: Forneça a inscrição e início de sessão para os clientes com contas do GitHub nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74c663d8847c2829a5d9466f8e601dd44593a6f8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065205"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta do GitHub através do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Criar uma aplicação de OAuth do GitHub

Para usar uma conta do GitHub como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do GitHub, poderá se inscrever [https://www.github.com/](https://www.github.com/)em.

1. Entrar para o [GitHub desenvolvedor](https://github.com/settings/developers) Web site com as suas credenciais do GitHub.
1. Selecione **aplicações de OAuth** e, em seguida, selecione **nova aplicação de OAuth**.
1. Introduza um **nome da aplicação** e a sua **URL da home page**.
1. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` no **URL de chamada de retorno de autorização**. Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C. Utilize só letras minúsculas, ao introduzir o nome do seu inquilino, mesmo que o inquilino está definido com letras maiúsculas no Azure AD B2C.
1. Clique em **registar a aplicação**.
1. Copie os valores da **ID de cliente** e **segredo do cliente**. Precisa para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configurar uma conta do GitHub como um fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e selecione **GitHub (versão prévia)** .
1. Insira um **nome**. Por exemplo, *GitHub*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo GitHub que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Selecione **Guardar**.
