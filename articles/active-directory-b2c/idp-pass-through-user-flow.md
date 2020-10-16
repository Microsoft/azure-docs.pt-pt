---
title: Passe um token de acesso através de um fluxo de utilizador para a sua app
titleSuffix: Azure AD B2C
description: Saiba como passar um token de acesso para os fornecedores de identidade OAuth 2.0 como uma reivindicação num fluxo de utilizador em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b834dda926b7da1241a325e1453143eccafaf30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87488776"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Passe um token de acesso através de um fluxo de utilizador para a sua aplicação no Azure Ative Directory B2C

Um [fluxo de utilizador](user-flow-overview.md) no Azure Ative Directory B2C (Azure AD B2C) proporciona aos utilizadores da sua aplicação a oportunidade de se inscreverem ou se inscreverem com um fornecedor de identidade. Quando a viagem começa, o Azure AD B2C recebe um sinal de [acesso](tokens-overview.md) do fornecedor de identidade. O Azure AD B2C utiliza esse símbolo para obter informações sobre o utilizador. Você ativa uma reclamação no fluxo do seu utilizador para passar o token através das aplicações que regista no Azure AD B2C.

Atualmente, o Azure AD B2C apenas suporta a passagem do token de acesso de fornecedores de identidade [OAuth 2.0,](authorization-code-flow.md) que incluem [o Facebook](identity-provider-facebook.md) e [o Google.](identity-provider-google.md) Para todos os outros fornecedores de identidade, a reclamação é devolvida em branco.

## <a name="prerequisites"></a>Pré-requisitos

* A sua aplicação deve estar a utilizar um [fluxo de utilizador recomendado.](user-flow-versions.md)
* O fluxo do utilizador está configurado com um fornecedor de identidade OAuth 2.0.

## <a name="enable-the-claim"></a>Ativar a reclamação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **os fluxos do Utilizador (políticas)** e, em seguida, selecione o fluxo do utilizador. Por exemplo, **B2C_1_signupsignin1.**
5. Selecione **Afirmações de aplicação**.
6. Ativar a reclamação **do Fornecedor de Identidade Access Token.**

    ![Ativar a reclamação do Access ToKen do Fornecedor de Identidade](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Clique **em Guardar** para salvar o fluxo do utilizador.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

Ao testar as suas aplicações em Azure AD B2C, pode ser útil ter o token AD B2C Azure devolvido `https://jwt.ms` para rever as reclamações no mesmo.

1. Na página geral do fluxo do utilizador, selecione Executar o **fluxo do utilizador**.
2. Para **Candidatura**, selecione a sua candidatura que já se registou anteriormente. Para ver o símbolo no exemplo abaixo, o **URL de resposta** deve mostrar `https://jwt.ms` .
3. Clique **no fluxo do utilizador executar**e, em seguida, iniciar scontabilidade com as credenciais da sua conta. Deve ver o sinal de acesso do fornecedor de identidade na **reclamação idp_access_token.**

    Deve ver algo semelhante ao seguinte exemplo:

    ![Ficha descodificada em jwt.ms com bloco idp_access_token em destaque](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [visão geral dos tokens Azure AD B2C](tokens-overview.md).
