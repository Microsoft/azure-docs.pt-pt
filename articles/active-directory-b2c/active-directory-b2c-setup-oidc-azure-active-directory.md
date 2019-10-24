---
title: Configurar a entrada para uma organização Azure Active Directory-Azure Active Directory B2C
description: Configure a entrada para uma organização de Azure Active Directory específica no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0c2e368b9c12d8ab673e5b8808632501de448b9a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755757"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar a entrada para uma organização de Azure Active Directory específica no Azure Active Directory B2C

Para usar um Azure Active Directory (AD do Azure) como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure ad B2C, você precisa criar um aplicativo que o represente. Este artigo mostra como habilitar a entrada para usuários de uma organização específica do Azure AD usando um fluxo de usuário no Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Criar um aplicativo do Azure AD

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo dentro do locatário organizacional do Azure AD, que não é o mesmo que seu locatário de Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário do Azure AD. Este não é o mesmo locatário que seu locatário de Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **registros de aplicativo**.
4. Selecione **novo registro**.
5. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
6. Aceite a seleção de **contas neste diretório organizacional somente** para este aplicativo.
7. Para o **URI de redirecionamento**, aceite o valor de **Web**e insira a URL a seguir em todas as letras minúsculas, em que `your-B2C-tenant-name` é substituído pelo nome do seu locatário Azure ad B2C. Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Todas as URLs agora devem estar usando [b2clogin.com](b2clogin.md).

8. Clique em **registrar**. Copie a **ID do aplicativo (cliente)** a ser usada posteriormente.
9. Selecione **certificados & segredos** no menu do aplicativo e, em seguida, selecione **novo segredo do cliente**.
10. Insira um nome para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
11. Selecione o período de validade. Para este aplicativo, aceite a seleção de **em 1 ano**.
12. Selecione **Adicionar** e copie o valor do novo segredo do cliente que será exibido para ser usado posteriormente.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como um provedor de identidade

1. Verifique se você está usando o diretório que contém Azure AD B2C locatário. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **novo provedor do OpenID Connect**.
1. Insira um **nome**. Por exemplo, digite *contoso Azure ad*.
1. Para **URL de metadados**, insira a URL a seguir substituindo `your-AD-tenant-domain` pelo nome de domínio do seu locatário do Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Não use o** ponto de extremidade de metadados do Azure ad v 2.0, por exemplo `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Fazer isso resulta em um erro semelhante a `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` ao tentar entrar.

1. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente.
1. Para **segredo do cliente**, insira o segredo do cliente que você registrou anteriormente.
1. Deixe os valores padrão para **escopo**, **tipo de resposta**e **modo de resposta**.
1. Adicional Insira um valor para **Domain_hint**. Por exemplo, *ContosoAD*. Esse é o valor a ser usado ao fazer referência a esse provedor de identidade usando *domain_hint* na solicitação.
1. Em **mapeamento de declarações do provedor de identidade**, insira os seguintes valores de mapeamento de declarações:

    * **ID de usuário**: *OID*
    * **Nome de exibição**: *nome*
    * **Nome fornecido**: *given_name*
    * **Sobrenome**: *family_name*
    * **Email**: *unique_name*

1. Selecione **Guardar**.
