---
title: Passe um sinal de acesso através de um fluxo de utilizador para a sua aplicação
titleSuffix: Azure AD B2C
description: Saiba como passar um sinal de acesso para os fornecedores de identidade OAuth 2.0 como uma reclamação num fluxo de utilizador no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187791"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Passe um sinal de acesso através de um fluxo de utilizador para a sua aplicação no Azure Ative Directory B2C

Um fluxo de [utilizador](user-flow-overview.md) no Azure Ative Directory B2C (Azure AD B2C) proporciona aos utilizadores da sua aplicação a oportunidade de se inscreverem ou inscreverem-se com um fornecedor de identidade. Quando a viagem começa, o Azure AD B2C recebe um sinal de [acesso](tokens-overview.md) do fornecedor de identidade. O Azure AD B2C utiliza esse símbolo para recuperar informações sobre o utilizador. Permite que uma reclamação no fluxo do utilizador passe o símbolo para as aplicações que regista no Azure AD B2C.

Atualmente, o Azure AD B2C apenas suporta a passagem do sinal de acesso dos fornecedores de identidade [OAuth 2.0,](authorization-code-flow.md) que incluem o [Facebook](identity-provider-facebook.md) e o [Google.](identity-provider-google.md) Para todos os outros fornecedores de identidade, a reclamação é devolvida em branco.

## <a name="prerequisites"></a>Pré-requisitos

* A sua aplicação deve estar a utilizar um fluxo de [utilizador v2](user-flow-versions.md).
* O fluxo do utilizador está configurado com um fornecedor de identidade OAuth 2.0.

## <a name="enable-the-claim"></a>Ativar a reclamação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (políticas)** e, em seguida, selecione o fluxo do utilizador. Por exemplo, **B2C_1_signupsignin1.**
5. Selecione **Afirmações de aplicação**.
6. Ativar a alegação de Acesso ao Fornecedor de **Identidade.**

    ![Ativar a alegação de acesso ao fornecedor de identidade](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Clique em **Guardar** para salvar o fluxo do utilizador.

## <a name="test-the-user-flow"></a>Testar o fluxo do utilizador

Ao testar as suas aplicações em Azure AD B2C, pode ser útil que `https://jwt.ms` o token Azure AD B2C seja devolvido para rever as reclamações nele.

1. Na página 'Visão Geral' do fluxo do utilizador, selecione **Executar o fluxo do utilizador**.
2. Para **Aplicação,** selecione a sua aplicação que registou anteriormente. Para ver o símbolo no exemplo abaixo, `https://jwt.ms`o URL de **resposta** deve mostrar .
3. Clique no **fluxo do utilizador executar**e, em seguida, inscreva-se com as credenciais da sua conta. Deve ver o sinal de acesso do fornecedor de identidade na **idp_access_token** reivindicação.

    Deve ver algo semelhante ao seguinte exemplo:

    ![Ficha descodificada em jwt.ms com bloco idp_access_token em destaque](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Passos seguintes

Saiba mais na [visão geral dos tokens Azure AD B2C](tokens-overview.md).
