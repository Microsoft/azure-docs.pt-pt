---
title: Configurar inscrição e iniciar sôm-in com uma conta no Twitter
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas de Twitter nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e07be01a0fb6d74b4dcef5cbc6ec129f95fd2e7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387937"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta no Twitter utilizando o Azure Ative Directory B2C

## <a name="create-an-application"></a>Criar uma aplicação

Para utilizar o Twitter como fornecedor de identidade no Azure AD B2C, precisa de criar uma aplicação no Twitter. Se ainda não tiver uma conta no Twitter, pode inscrever-se em [https://twitter.com/signup](https://twitter.com/signup) .

1. Inscreva-se no site do [Twitter Developers](https://developer.twitter.com/en/apps) com as suas credenciais de conta no Twitter.
1. Selecione **Criar uma aplicação.**
1. Introduza um **nome de Aplicação** e uma **descrição da Aplicação.**
1. No **URL do site,** insira `https://your-tenant.b2clogin.com` . `your-tenant`Substitua-o pelo nome do seu inquilino. Por exemplo, `https://contosob2c.b2clogin.com`.
1. Para o **URL callback,** insira `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . `your-tenant`Substitua-o pelo nome do seu inquilino e `your-user-flow-Id` pelo identificador do seu fluxo de utilizador. Por exemplo, `b2c_1A_signup_signin_twitter`. Você precisa usar todas as letras minúsculas ao introduzir o seu nome de inquilino e id fluxo de utilizador mesmo que sejam definidas com letras maiúsculas em Azure AD B2C.
1. Na parte inferior da página, leia e aceite os termos e, em seguida, **selecione Criar**.
1. Na página de detalhes da **App,** **selecione Editar > Editar detalhes**, verifique a caixa para **ativar o registo no Twitter**e, em seguida, selecione **Save**.
1. Selecione **Chaves e fichas** e registe a **Chave API do Consumidor** e os valores-chave **secretos da API do consumidor** a serem usados mais tarde.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configure o Twitter como fornecedor de identidade no seu inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione o **Twitter**.
1. Insira um **nome**. Por exemplo, *o Twitter.*
1. Para o ID do **Cliente,** insira a Chave API do Consumidor da aplicação Twitter que criou anteriormente.
1. Para o segredo do **Cliente,** insira a chave secreta da API do consumidor que gravou.
1. Selecione **Guardar**.
