---
title: Conjunto redirecionar URLs para b2clogin.com - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre como utilizar b2clogin.com no seu redirecionamento URLs para o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8188e36278bad9c93f709a5d7d9f831d1c19e6b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60314587"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Conjunto redirecionar URLs b2clogin.com para o Azure Active Directory B2C

Quando configurar um fornecedor de identidade para inscrição e início de sessão na sua aplicação do Azure Active Directory (Azure AD) B2C, tem de especificar um URL de redirecionamento. No passado, login.microsoftonline.com foi utilizado, agora deve usar b2clogin.com.

Utilizar b2clogin.com dá-lhe benefícios adicionais, tais como:

- Espaço consumido no cabeçalho de cookie por serviços da Microsoft é reduzido.
- Suas URLs deixarão de incluir uma referência à Microsoft. Por exemplo, `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

>[!NOTE]
> Pode utilizar o nome do inquilino e o GUID do inquilino da seguinte forma:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` (que ainda se refere à `onmicrosoft.com`)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid` (caso em que não há nenhuma referência para a Microsoft pelo)
>
> No entanto, não é possível utilizar um _domínio personalizado_ para o seu Azure Active Directory B2C de inquilino, por exemplo, `https://your-tenant-name.b2clogin.com/your-custom-domain-name` seria _não_ trabalhar.

Considere estas definições que poderão ter de alterar quando utilizar b2clogin.com:

- Definir o redirecionamento URLs em seus aplicativos de fornecedor de identidade para utilizar b2clogin.com. 
- Defina a sua aplicação do Azure AD B2C para utilizar b2clogin.com para referências de fluxo de utilizador e de pontos finais de token. 
- Se estiver a utilizar a MSAL, terá de definir o **ValidateAuthority** propriedade `false`.
- Certifique-se de que alterar quaisquer **origens permitidas** definidas nas definições de CORS para [personalização da interface do usuário](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>URLs de redirecionamento de alteração

Para utilizar b2clogin.com, nas definições para a sua aplicação de fornecedor de identidade, procure e alterar a lista de URLs fidedignos para redirecionar novamente para o Azure AD B2C.  Atualmente, provavelmente terá de configurá-lo até redirecionar novamente para algum site login.microsoftonline.com. 

Terá de alterar o URL de redirecionamento para que `your-tenant-name.b2clogin.com` está autorizado. Certifique-se de substituir `your-tenant-name` com o nome do seu Azure AD B2C inquilino e remover `/te` se existir no URL. Existem pequenas variações para este URL para cada fornecedor de identidade por isso, verifique a página correspondente para obter o URL exato.

Pode encontrar informações de configuração para fornecedores de identidade nos seguintes artigos:

- [Conta Microsoft](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Custom OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Atualizar a sua aplicação

A aplicação do Azure AD B2C refere-se provavelmente a `login.microsoftonline.com` em vários locais, como suas referências de fluxo de utilizador e os pontos finais de token.  Certifique-se de que o ponto final de autorização, o ponto final do token e o emissor foram atualizados para utilizar `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Defina a propriedade ValidateAuthority

Se estiver a utilizar a MSAL, defina o **ValidateAuthority** propriedade `false`. Quando **ValidateAuthority** está definida como `false`, redirecionamentos têm permissão para b2clogin.com. 

O exemplo seguinte mostra como pode definir a propriedade:

Na [MSAL para .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

E, em [MSAL para Javascript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
