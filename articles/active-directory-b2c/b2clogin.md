---
title: Definir URLs de redirecionamento para b2clogin.com-Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como usar o b2clogin.com em suas URLs de redirecionamento para Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 080c1933f88d9e824969a42212de2eacd0f62e14
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927290"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Definir URLs de redirecionamento para b2clogin.com para Azure Active Directory B2C

Ao configurar um provedor de identidade para inscrição e entrada em seu aplicativo Azure Active Directory (Azure AD) B2C, você precisa especificar uma URL de redirecionamento. No passado, login.microsoftonline.com foi usado, agora você deveria estar usando b2clogin.com.

> [!NOTE]
> Você pode usar o código do lado do cliente JavaScript (atualmente em visualização) no b2clogin.com. Seu código JavaScript será removido de sua página personalizada se você usar login.microsoftonline.com. Restrições de segurança adicionais também são aplicadas a login.microsoftonline.com, como a remoção de elementos de formulário HTML de sua página personalizada. 

O uso do b2clogin.com oferece benefícios adicionais, como:

- O espaço consumido no cabeçalho do cookie pelos serviços da Microsoft é reduzido.
- Suas URLs não incluem mais uma referência à Microsoft. Por exemplo, `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

> [!NOTE]
> Você pode usar o nome do locatário e o GUID do locatário da seguinte maneira:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`(que ainda se refere `onmicrosoft.com`a)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid`(nesse caso, não há nenhuma referência à Microsoft)
>
> No entanto, você não pode usar um _domínio personalizado_ para seu locatário Azure Active Directory B2C `https://your-tenant-name.b2clogin.com/your-custom-domain-name` , por exemplo, _não_ funcionaria.

Considere estas configurações que talvez precisem ser alteradas ao usar b2clogin.com:

- Defina as URLs de redirecionamento em seus aplicativos de provedor de identidade para usar o b2clogin.com. 
- Defina seu aplicativo Azure AD B2C para usar b2clogin.com para referências de fluxo de usuário e pontos de extremidade de token. 
- Se você estiver usando MSAL, precisará definir a propriedade **ValidateAuthority** como `false`.
- Certifique-se de alterar as **origens permitidas** que você definiu nas configurações de CORS para [personalização da interface do usuário](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Alterar URLs de redirecionamento

Para usar o b2clogin.com, nas configurações do seu aplicativo de provedor de identidade, procure e altere a lista de URLs confiáveis para redirecionar de volta para Azure AD B2C.  No momento, você provavelmente o configurou para redirecionar de volta para algum site login.microsoftonline.com. 

Você precisará alterar a URL de redirecionamento `your-tenant-name.b2clogin.com` para que seja autorizado. Certifique-se de `your-tenant-name` substituir pelo nome do seu locatário Azure ad B2C e remova `/te` -o se ele existir na URL. Há pequenas variações para essa URL para cada provedor de identidade, portanto, verifique a página correspondente para obter a URL exata.

Você pode encontrar informações de configuração para provedores de identidade nos seguintes artigos:

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
- [OIDC personalizado](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Atualizar seu aplicativo

Seu aplicativo Azure ad B2C provavelmente se refere `login.microsoftonline.com` a vários lugares, como referências de fluxo de usuário e pontos de extremidade de token.  Verifique se o ponto de extremidade de autorização, o ponto de extremidade do token e o emissor `your-tenant-name.b2clogin.com`foram atualizados para uso.  

## <a name="set-the-validateauthority-property"></a>Definir a propriedade ValidateAuthority

Se você estiver usando MSAL, defina a propriedade **ValidateAuthority** como `false`. Quando **ValidateAuthority** é definido como `false`, os redirecionamentos são permitidos para b2clogin.com. 

O exemplo a seguir mostra como você pode definir a propriedade:

No [MSAL para .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

E no [MSAL para JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

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
