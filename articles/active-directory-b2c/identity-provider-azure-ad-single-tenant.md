---
title: Configurar o início de uma organização da AD Azure
titleSuffix: Azure AD B2C
description: Configurar o início de inscrição para uma organização específica de Diretório Ativo Azure no Azure Ative Directory B2C.
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
ms.openlocfilehash: b0ff1c2d913d0a4402b491f3c84ce0d35cd081df
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847592"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar o início de inscrição para uma organização específica de Diretório Ativo Azure no Azure Ative Directory B2C

Para utilizar um Diretório Ativo Azure (Azure AD) como fornecedor de [identidade](authorization-code-flow.md) no Azure AD B2C, é necessário criar uma aplicação que o represente. Este artigo mostra-lhe como ativar o início de sessão para utilizadores de uma organização específica da AD Azure utilizando um fluxo de utilizador em Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Criar uma aplicação Azure AD

Para permitir o início de sessão para utilizadores de uma organização específica da AD Azure, é necessário registar uma aplicação dentro do inquilino da AD Azure organizacional, que não é o mesmo que o seu inquilino Azure AD B2C.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD. Este não é o mesmo inquilino que o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
4. Selecione **novo registro**.
5. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
6. Aceite a seleção de **Contas neste diretório organizacional apenas** para esta aplicação.
7. Para o **Redirect URI,** aceite o valor da **Web**e introduza o seguinte URL em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu inquilino Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Todas as URLs agora devem estar usando [b2clogin.com](b2clogin.md).

8. Clique em **registrar**. Copie o ID do **Pedido (cliente)** para ser usado mais tarde.
9. Selecione **Certificados e segredos** no menu de candidaturas e, em seguida, selecione **novo segredo do cliente**.
10. Insira um nome para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
11. Selecione o período de validade. Para esta candidatura, aceite a seleção de **In 1 ano**.
12. Selecione **Adicionar** e copiar o valor do novo segredo do cliente que é apresentado para ser usado mais tarde.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configure Azure AD como fornecedor de identidade

1. Certifique-se de que está a usar o diretório que contém inquilino Azure AD B2C. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **o fornecedor New OpenID Connect**.
1. Insira um **nome**. Por exemplo, introduza *Contoso Azure AD*.
1. Para url **de Metadados,** introduza o seguinte URL substituindo `your-AD-tenant-domain` com o nome de domínio do seu inquilino Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Não** utilize o ponto final dos metadados Azure AD V2.0, por exemplo `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Fazê-lo resulta num erro semelhante ao `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` ao tentar iniciar sessão.

1. Para **ID do Cliente,** insira o ID da aplicação que gravou anteriormente.
1. Para **o segredo do Cliente,** insira o segredo do cliente que gravou anteriormente.
1. Deixe os valores predefinidos para o tipo de **alcance,** **tipo de resposta**e modo **resposta**.
1. (Opcional) Introduza um valor para **Domain_hint.** Por exemplo, *ContosoAD.* Este é o valor a utilizar quando se refere a este fornecedor de identidade usando *domain_hint* no pedido.
1. No âmbito do mapeamento de sinistros do **fornecedor de identidade,** insira os seguintes valores de mapeamento de sinistros:

    * **ID do utilizador**: *oid*
    * **Nome do mostrador**: *nome*
    * **Nome dado**: *given_name*
    * **Sobrenome**: *family_name*
    * **E-mail**: *unique_name*

1. Selecione **Guardar**.
