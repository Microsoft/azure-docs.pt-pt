---
title: Configurar a inscrição e início de sessão com uma conta do Twitter com o Azure Active Directory B2C | Documentos da Microsoft
description: Forneça a inscrição e início de sessão para clientes com contas do Twitter nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a07f138461759ff0871df0c677ef36e0fb36ae34
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703961"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta do Twitter com o Azure Active Directory B2C

## <a name="create-an-application"></a>Criar uma aplicação

Para utilizar o Twitter como fornecedor de identidade no Azure AD B2C, terá de criar um aplicativo do Twitter. Se ainda não tiver uma conta do Twitter, pode obtê-la em [ https://twitter.com/signup ](https://twitter.com/signup).

1. Entrar para o [Twitter desenvolvedores](https://developer.twitter.com/en/apps) Web site com as suas credenciais de conta do Twitter.
2. Selecione **criar uma aplicação**.
3. Introduza um **nome da aplicação** e uma **descrição da aplicação**.
4. Na **URL do site**, introduza `https://your-tenant.b2clogin.com`. Substitua `your-tenant` com o nome do seu inquilino. Por exemplo, https://contosob2c.b2clogin.com.
5. Para o **URL de chamada de retorno**, introduza `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Substitua `your-tenant` com o nome do nome do seu inquilino e `your-user-flow-Id` com o identificador do seu fluxo de utilizador. Por exemplo, `b2c_1A_signup_signin_twitter`. Tem de utilizar todas as letras minúsculas, ao introduzir o nome do seu inquilino, mesmo que o inquilino está definido com letras maiúsculas no Azure AD B2C.
6. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **criar**.
7. Na **detalhes da aplicação** página, selecione **Editar > Editar detalhes**, marque a caixa **ativar o início de sessão com o Twitter**e, em seguida, selecione **guardar**.
8. Selecione **chaves e tokens** e registe o **chave de API de consumidor** e o **chave secreta da API de consumidor** valores a utilizar mais tarde.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como fornecedor de identidade no seu inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Fornecer um **nome**. Por exemplo, introduza *Twitter*.
6. Selecione **tipo de fornecedor de identidade**, selecione **Twitter**e clique em **OK**.
7. Selecione **configurar este fornecedor de identidade** e introduza a chave de API para o **ID de cliente** e a chave de segredo de API para o **segredo do cliente**.
8. Clique em **OK**e, em seguida, clique em **criar** para guardar a configuração do Twitter.
