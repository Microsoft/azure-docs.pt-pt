---
title: Configurar o início de uma organização da AD Azure
titleSuffix: Azure AD B2C
description: Configurar o início de inscrição para uma organização específica de Diretório Ativo Azure no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678048"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar o início de inscrição para uma organização específica de Diretório Ativo Azure no Azure Ative Directory B2C

Para utilizar um Diretório Ativo Azure (Azure AD) como fornecedor de [identidade](authorization-code-flow.md) no Azure AD B2C, é necessário criar uma aplicação que o represente. Este artigo mostra-lhe como ativar o início de sessão para utilizadores de uma organização específica da AD Azure utilizando um fluxo de utilizador em Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configure Azure AD como fornecedor de identidade

1. Certifique-se de que está a usar o diretório que contém inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **o fornecedor New OpenID Connect**.
1. Introduza um **Nome**. Por exemplo, introduza *Contoso Azure AD*.
1. Para url **de Metadados,** introduza o seguinte URL substituindo `{tenant}` pelo nome de domínio do seu inquilino Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. Para **ID do Cliente,** insira o ID da aplicação que gravou anteriormente.
1. Para **o segredo do Cliente,** insira o segredo do cliente que gravou anteriormente.
1. Para o **Âmbito,** insira o `openid profile`.
1. Deixe os valores predefinidos para o **tipo de resposta**e modo **resposta**.
1. (Opcional) Para a dica `contoso.com`de **domínio,** insira . Para mais informações, consulte Configurar o início de sessão direta utilizando o [Diretório Ativo Azure B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. No âmbito do mapeamento de reclamações do **fornecedor de identidade,** selecione as seguintes reclamações:

    * **ID do utilizador**: *oid*
    * **Nome do mostrador**: *nome*
    * **Nome dado**: *given_name*
    * **Sobrenome**: *family_name*
    * **E-mail**: *unique_name*

1. Selecione **Guardar**.
