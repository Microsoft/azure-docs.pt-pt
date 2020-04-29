---
title: Configurar inscrição e iniciar sessão com uma conta no Twitter
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas do Twitter nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80051455"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurar e iniciar sessão com uma conta no Twitter utilizando o Diretório Ativo Azure B2C

## <a name="create-an-application"></a>Criar uma aplicação

Para utilizar o Twitter como fornecedor de identidade no Azure AD B2C, é necessário criar uma aplicação no Twitter. Se ainda não tem uma conta no Twitter, [https://twitter.com/signup](https://twitter.com/signup)pode inscrever-se em .

1. Inscreva-se no site do [Twitter Developers](https://developer.twitter.com/en/apps) com as credenciais da sua conta do Twitter.
1. Selecione **Criar uma aplicação**.
1. Introduza um **nome de aplicação** e uma descrição da **Aplicação.**
1. No URL do `https://your-tenant.b2clogin.com` **Site,** introduza . Substitua `your-tenant` pelo nome do seu inquilino. Por exemplo, `https://contosob2c.b2clogin.com`.
1. Para o URL de `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` **chamada,** introduza . Substitua-o `your-tenant` pelo nome `your-user-flow-Id` do seu nome de inquilino e pelo identificador do fluxo do utilizador. Por exemplo, `b2c_1A_signup_signin_twitter`. Você precisa usar todas as letras minúsculas ao introduzir o nome do seu inquilino e id de fluxo do utilizador mesmo que sejam definidos com letras maiúsculas em Azure AD B2C.
1. Na parte inferior da página, leia e aceite os termos e, em seguida, selecione **Criar**.
1. Na página de detalhes da **App,** selecione **Editar > Editar detalhes,** verifique a caixa para **ativar o 'Iniciar sessão' com**o Twitter e, em seguida, selecione **Guardar**.
1. Selecione **Teclas e fichas** e grave a **Chave API** do Consumidor e os valores-chave secretos da **API** do consumidor a utilizar mais tarde.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configure o Twitter como fornecedor de identidade no seu inquilino

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de Identidade**e, em seguida, selecione **o Twitter**.
1. Introduza um **Nome**. Por exemplo, *Twitter.*
1. Para o ID do **cliente**, insira a Chave API do Consumidor da aplicação do Twitter que criou anteriormente.
1. Para o segredo do **Cliente,** introduza a chave secreta da API do consumidor que gravou.
1. Selecione **Guardar**.
