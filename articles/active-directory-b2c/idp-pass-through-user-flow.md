---
title: Passe um token de acesso por meio de um fluxo de usuário para o seu aplicativo-Azure Active Directory B2C
description: Saiba como passar um token de acesso para provedores de identidade OAuth 2,0 como uma declaração em um fluxo de usuário no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66b3dc4aba5d1b29cc0c6190877fbd6b26a11f0c
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510092"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Passe um token de acesso por meio de um fluxo de usuário para seu aplicativo no Azure Active Directory B2C

Um [fluxo de usuário](active-directory-b2c-reference-policies.md) no Azure Active Directory B2C (Azure ad B2C) fornece aos usuários de seu aplicativo uma oportunidade de se inscrever ou entrar com um provedor de identidade. Quando a jornada é iniciada, Azure AD B2C recebe um [token de acesso](active-directory-b2c-reference-tokens.md) do provedor de identidade. Azure AD B2C usa esse token para recuperar informações sobre o usuário. Você habilita uma declaração em seu fluxo de usuário para passar o token para os aplicativos que você registra no Azure AD B2C.

Atualmente, Azure AD B2C só dá suporte à passagem do token de acesso dos provedores de identidade [OAuth 2,0](active-directory-b2c-reference-oauth-code.md) , que incluem o [Facebook](active-directory-b2c-setup-fb-app.md) e o [Google](active-directory-b2c-setup-goog-app.md). Para todos os outros provedores de identidade, a declaração é retornada em branco.

## <a name="prerequisites"></a>Pré-requisitos

* Seu aplicativo deve estar usando um [fluxo de usuário v2](user-flow-versions.md).
* Seu fluxo de usuário está configurado com um provedor de identidade OAuth 2,0.

## <a name="enable-the-claim"></a>Habilitar a declaração

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório e o filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de usuário (políticas)** e, em seguida, selecione seu fluxo de usuário. Por exemplo, **B2C_1_signupsignin1**.
5. Selecione **Afirmações de aplicação**.
6. Habilite a declaração de **token de acesso do provedor de identidade** .

    ![Habilitar a declaração de token de acesso do provedor de identidade](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Clique em **salvar** para salvar o fluxo do usuário.

## <a name="test-the-user-flow"></a>Testar o fluxo do usuário

Ao testar seus aplicativos no Azure ad B2C, pode ser útil ter o token de Azure ad B2C retornado `https://jwt.ms` para examinar as declarações nele.

1. Na página Visão geral do fluxo de usuário, selecione **executar fluxo de usuário**.
2. Para **aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token no exemplo abaixo, a **URL de resposta** deve ser `https://jwt.ms`mostrada.
3. Clique em **executar fluxo de usuário**e entre com suas credenciais de conta. Você deve ver o token de acesso do provedor de identidade na Declaração **idp_access_token** .

    Você verá algo semelhante ao exemplo a seguir:

    ![Token decodificado em jwt.ms com bloco idp_access_token realçado](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Passos seguintes

Saiba mais na [visão geral de tokens de Azure ad B2C](active-directory-b2c-reference-tokens.md).
