---
title: Configurar o sedundo para uma organização AD Azure
titleSuffix: Azure AD B2C
description: Confiúdico de inscrição para uma organização específica do Azure Ative Directory em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e4fa4b64c6519df90d5883e8c5760b3ed2ce0337
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004464"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar o sº de inscrição para uma organização específica do Azure Ative Directory em Azure Ative Directory B2C

Para utilizar um Diretório Ativo Azure (Azure AD) como fornecedor de [identidade](authorization-code-flow.md) em Azure AD B2C, precisa de criar uma aplicação que o represente. Este artigo mostra-lhe como ativar o início de saúde dos utilizadores de uma organização específica da AZure AD utilizando um fluxo de utilizador em Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configure Azure AD como fornecedor de identidade

1. Certifique-se de que está a usar o diretório que contém o inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione novo fornecedor **OpenID Connect**.
1. Insira um **nome**. Por exemplo, *insira Contoso Azure AD*.
1. Para **o url metadados,** introduza o seguinte URL substituindo `{tenant}` pelo nome de domínio do seu inquilino AZure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Por exemplo, `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Para **identificação do cliente,** introduza o ID da aplicação que gravou anteriormente.
1. Para **segredo do Cliente,** insira o segredo do cliente que gravou anteriormente.
1. Para o **Âmbito,** insira o `openid profile` .
1. Deixe os valores predefinidos para o **tipo de resposta**e modo **resposta**.
1. (Opcional) Para a **sugestão de Domínio,** insira `contoso.com` . Para obter mais informações, consulte [Configurar o insusimento direto utilizando o Azure Ative Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. No âmbito **do mapeamento de reclamações do fornecedor de identidade,** selecione as seguintes reclamações:

    * **ID do utilizador**: *oid*
    * **Nome do visor**: *nome*
    * **Nome próprio**: *given_name*
    * **Apelido**: *family_name*
    * **E-mail**: *unique_name*

1. Selecione **Guardar**.
