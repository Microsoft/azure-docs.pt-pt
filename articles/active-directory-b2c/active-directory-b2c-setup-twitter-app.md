---
title: Configurar a inscrição e a entrada com uma conta do Twitter usando Azure Active Directory B2C
description: Forneça inscrição e entrada para clientes com contas do Twitter em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ff795bbbd3cf136735499b571367cf5b8a6ec170
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622158"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do Twitter usando Azure Active Directory B2C

## <a name="create-an-application"></a>Criar uma aplicação

Para usar o Twitter como um provedor de identidade no Azure AD B2C, você precisa criar um aplicativo do Twitter. Se você ainda não tiver uma conta do Twitter, poderá se inscrever [https://twitter.com/signup](https://twitter.com/signup)em.

1. Entre no site de [desenvolvedores do Twitter](https://developer.twitter.com/en/apps) com suas credenciais de conta do Twitter.
1. Selecione **criar um aplicativo**.
1. Insira um **nome de aplicativo** e uma **Descrição de aplicativo**.
1. Na **URL do site**, `https://your-tenant.b2clogin.com`digite. Substitua `your-tenant` pelo nome do seu locatário. Por exemplo, https://contosob2c.b2clogin.com.
1. Para a **URL de retorno**de `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`chamada, digite. Substitua `your-tenant` pelo nome do seu nome do locatário e `your-user-flow-Id` pelo identificador do seu fluxo de usuário. Por exemplo, `b2c_1A_signup_signin_twitter`. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário, mesmo que o locatário seja definido com letras maiúsculas no Azure AD B2C.
1. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **criar**.
1. Na página **detalhes do aplicativo** , selecione **Editar > Editar detalhes**, marque a caixa **Habilitar entrada com o Twitter**e, em seguida, selecione **salvar**.
1. Selecione **chaves e tokens** e registre a **chave de API do consumidor** e os valores de **chave secreta da API do consumidor** a serem usados posteriormente.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurar o Twitter como um provedor de identidade em seu locatário

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Twitter**.
1. Insira um **nome**. Por exemplo, *Twitter*.
1. Para a **ID do cliente**, insira a chave de API do consumidor do aplicativo do Twitter que você criou anteriormente.
1. Para o **segredo do cliente**, insira a chave secreta da API do consumidor que você registrou.
1. Selecione **Guardar**.
