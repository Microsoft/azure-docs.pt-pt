---
title: Autenticação
description: Explica como funciona a autenticação
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: dc325fdf68c5afbb122f9e77c5509a6a8053a12e
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427456"
---
# <a name="configure-authentication"></a>Configurar a autenticação

A Azure Remote Rendering utiliza o mesmo mecanismo de autenticação que [as âncoras espaciais Azure (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Os clientes precisam definir *um* dos seguintes para ligar para as APIs REST com sucesso:

* **ContaKey**: pode ser obtido no separador "Chaves" para a conta de renderização remota no portal Azure. As chaves de conta são apenas recomendados para desenvolvimento/prototipagem.
    ![ID da Conta](./media/azure-account-primary-key.png)

* **AutenticaçãoToken:** é um símbolo AD Azure, que pode ser obtido através da [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Existem vários fluxos diferentes disponíveis para aceitar credenciais de utilizador e usar essas credenciais para obter um token de acesso.

* **MRAccessToken:** é um token MR, que pode ser obtido a partir do Azure Mixed Reality Security Token Service (STS). Recuperado do `https://sts.mixedreality.azure.com` ponto final usando uma chamada REST semelhante à chamada abaixo:

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Quando o cabeçalho de autorização for formatado da seguinte forma: `Bearer <Azure_AD_token>` ou `Bearer <accoundId>:<accountKey>` . O primeiro é preferível para a segurança. O símbolo devolvido desta chamada REST é o sinal de acesso mr.

## <a name="authentication-for-deployed-applications"></a>Autenticação para aplicações implantadas

As chaves da conta são recomendadas para prototipagem rápida, apenas durante o desenvolvimento. Recomenda-se não enviar a sua aplicação para a produção usando uma chave de conta incorporada. A abordagem recomendada é utilizar uma abordagem de autenticação AZure AD baseada no utilizador ou em serviço.

 A autenticação Azure AD é descrita na secção de autenticação do [utilizador Azure AD](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) do serviço [Azure Spatial Anchors (ASA).](../../spatial-anchors/index.yml)

 Para mais informações, consulte o [Tutorial: Assegurar a renderização remota do Azure e o armazenamento de modelos - Autenticação do Azure Ative Directory](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

Para ajudar a controlar o nível de acesso concedido ao seu serviço, utilize as seguintes funções ao conceder acesso baseado em funções:

* **Administrador de renderização remota**: Fornece ao utilizador capacidades de conversão, gestão de sessão, renderização e diagnóstico para renderização remota do Azure.
* **Cliente de renderização remota**: Fornece ao utilizador capacidades de gestão de sessão, renderização e diagnóstico para renderização remota do Azure.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta](create-an-account.md)
* [Utilização das APIs frontendas Azure para autenticação](frontend-apis.md)
* [Scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md)