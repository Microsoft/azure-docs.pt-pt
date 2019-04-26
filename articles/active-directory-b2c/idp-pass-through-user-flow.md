---
title: Passar um token de acesso através de um fluxo de utilizador à sua aplicação - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como pode passar por meio de um token de acesso para fornecedores de identidade de OAuth2.0 como uma afirmação num fluxo de utilizador no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7863bea9f3fe6ef146dc1e1f2b29bbfda09a9d6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397248"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Passar um token de acesso através de um fluxo de utilizador à sua aplicação no Azure Active Directory B2C

> [!NOTE]
> Esta funcionalidade está atualmente em pré-visualização pública.

R [fluxo de utilizador](active-directory-b2c-reference-policies.md) no Azure Active Directory (Azure AD) B2C fornece a utilizadores da sua aplicação uma oportunidade de se inscrever ou iniciar sessão com um fornecedor de identidade. Quando a jornada é iniciado, o Azure AD B2C recebe uma [token de acesso](active-directory-b2c-reference-tokens.md) do fornecedor de identidade. O Azure AD B2C utiliza esse token para obter informações sobre o utilizador. Ative uma afirmação no seu fluxo de utilizador para transmitir o token por meio para os aplicativos que Registre-se no Azure AD B2C.

O Azure AD B2C atualmente suporta apenas passando o token de acesso de [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) fornecedores de identidade, que incluem [Facebook](active-directory-b2c-setup-fb-app.md) e [Google](active-directory-b2c-setup-goog-app.md). Para todos os outros fornecedores de identidade, a afirmação é devolvida em branco.

## <a name="prerequisites"></a>Pré-requisitos

- A aplicação tem de utilizar um [fluxo de utilizador do v2](user-flow-versions.md).
- O fluxo de utilizador está configurado com um fornecedor de identidade do OAuth 2.0.

## <a name="enable-the-claim"></a>Permitir que a declaração

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (diretivas)** e, em seguida, selecione o fluxo de utilizador. Por exemplo, **B2C_1_signupsignin1**.
5. Selecione **Afirmações de aplicação**.
6. Ativar a **Token de acesso da fornecedor de identidade** de afirmação.

    ![Permitir que a declaração de Token de acesso da fornecedor de identidade](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Clique em **guardar** para guardar o fluxo de utilizador.

## <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

Ao testar as suas aplicações no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado para `https://jwt.ms` para rever as afirmações no mesmo.

1. Na página de descrição geral do fluxo de utilizador, selecione **executar o fluxo de utilizador**.
2. Para **aplicativo**, selecione a aplicação que registou anteriormente. Para ver o token no exemplo abaixo, o **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **executar o fluxo de utilizador**e, em seguida, inicie sessão com as credenciais da conta. Deverá ver o token de acesso, o fornecedor de identidade na **idp_access_token** de afirmação.

    Deverá ver algo semelhante ao seguinte exemplo:

    ![Token descodificada](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais no [descrição geral do Azure AD B2C tokens](active-directory-b2c-reference-tokens.md).




