---
title: Incorporar interface de utilizador B2C do Azure Ative Directory B2C na sua app com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba como incorporar a interface de utilizador do Azure Ative Directory B2C na sua app com uma política personalizada
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 526d8bfa0124fba75eb33518acd83169ff020698
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555434"
---
# <a name="embedded-sign-in-experience"></a>Experiência de inscrição incorporada

Para uma experiência de sing-in mais simples, pode evitar redirecionar os utilizadores para uma página de sing-in separada ou gerar uma janela pop-up. Ao utilizar o elemento de moldura `<iframe>` inline, pode incorporar a interface de utilizador de entrada Ad B2C Azure AD B2C diretamente na sua aplicação web.

## <a name="web-application-embedded-sign-in"></a>Inscrição em incorporação de aplicação web

O elemento de moldura inline `<iframe>` é usado para incorporar um documento numa página web HTML5. Pode utilizar o elemento iframe para incorporar a interface de utilizador de entrada Azure AD B2C diretamente na sua aplicação web, como mostra no seguinte exemplo:

![Faça login com experiência div pairando](media/embedded-login/login-hovering.png)

Ao utilizar o iframe, considere o seguinte:

- O insinusado siná-in suporta apenas as contas locais. A maioria dos fornecedores de identidade social (por exemplo, Google e Facebook) bloqueiam as suas páginas de inscrição de serem renderizadas em quadros inline.
- Como os cookies de sessão AZure AD B2C dentro de um iframe são considerados cookies de terceiros, certos navegadores (por exemplo, Safari ou Chrome em modo incógnito) bloqueiam ou limpam esses cookies, resultando numa experiência indesejável do utilizador. Para evitar este problema, certifique-se de que o nome do domínio da sua aplicação e o seu domínio Azure AD B2C têm a *mesma origem*. Por exemplo, uma aplicação acolhida https://app.contoso.com tem a mesma origem que o Azure AD B2C em funcionamento https://login.contoso.com .
 
## <a name="configure-your-policy"></a>Configure a sua política

Para permitir que a interface de utilizador Azure AD B2C seja incorporada num iframe, uma política de segurança de conteúdos `Content-Security-Policy` e opções de fotogramas `X-Frame-Options` devem ser incluídas nos cabeçalhos de resposta Azure AD B2C HTTP. Estes cabeçalhos permitem que a interface de utilizador Azure AD B2C seja executada sob o nome de domínio da sua aplicação.

Adicione um elemento **JourneyFraming** dentro do elemento [RelyingParty.](relyingparty.md)  O elemento **UserJourneyBehaviors** deve seguir o **DefaultUserJourney**. O elemento **UserJourneyBehaviors** deve parecer-se com este exemplo:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

O atributo **Sources** contém o URI da sua aplicação web. Adicione um espaço entre URIs. Cada URI deve satisfazer os seguintes requisitos:

- O URI deve ser confiável e propriedade da sua aplicação.
- O URI deve utilizar o sistema https.  
- O URI completo da aplicação web deve ser especificado. Não são suportados carateres universais.

Além disso, recomendamos que também bloqueie o facto de o seu próprio nome de domínio ser incorporado num iframe, definindo os cabeçalhos de Política de Segurança de Conteúdo e X-Frame-Options, respectivamente, nas suas páginas de aplicação. Isto irá mitigar as preocupações de segurança em torno de navegadores mais antigos relacionados com a incorporação de iframes.

## <a name="adjust-policy-user-interface"></a>Ajustar interface de utilizador de política

Com a personalização da [interface de utilizador](customize-ui.md)Azure AD B2C, tem quase total controlo sobre o conteúdo HTML e CSS apresentados aos utilizadores. Siga os passos para personalizar uma página HTML utilizando definições de conteúdo. Para encaixar a interface de utilizador Azure AD B2C no tamanho do iframe, forneça uma página HTML limpa sem fundo e espaços extra.  

O seguinte código CSS esconde os elementos HTML Azure AD B2C e ajusta o tamanho do painel para preencher o iframe.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

Em alguns casos, é melhor notificar a sua aplicação da página Azure AD B2C que está a ser apresentada. Por exemplo, quando um utilizador seleciona a opção de inscrição, pode querer que a aplicação responda escondendo os links para iniciar sessão com uma conta social ou ajustar o tamanho do iframe.

Para notificar a sua aplicação da atual página Azure AD B2C, [ative a sua política para JavaScript](javascript-samples.md)e, em seguida, utilize mensagens posts HTML5. O seguinte código JavaScript envia uma mensagem postal para a aplicação `signUp` com:

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Configure uma aplicação web

Quando um utilizador seleciona o botão de inserção, a [aplicação web](code-samples.md#web-apps-and-apis) gera um pedido de autorização que leva o utilizador à experiência de sinstrução AD B2C do Azure. Após a sação estar concluída, o Azure AD B2C devolve um token de ID, ou código de autorização, ao URI de redirecionamento configurado dentro da sua aplicação.

Para suportar o login incorporado, a propriedade do iframe **SRC** aponta para o controlador de entrada, `/account/SignUpSignIn` como, por exemplo, que gera o pedido de autorização e redireciona o utilizador para a política Azure AD B2C.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Após o token de ID ser recebido e validado pela aplicação, o fluxo de autorização está completo e a aplicação reconhece e confia no utilizador. Como o fluxo de autorização acontece dentro do iframe, é preciso recarregar a página principal. Após a recarga da página, o botão de inscrição muda para "assinar" e o nome de utilizador é apresentado na UI.  

Segue-se um exemplo que mostra como o reorientar o URI pode refrescar a página principal:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Adicione o s-in com contas sociais a uma aplicação web

Os fornecedores de identidade social bloqueiam as suas páginas de inscrição de renderização em quadros inline. Pode utilizar uma política separada para contas sociais, ou pode utilizar uma única política para iniciar sação e inscrever-se em contas locais e sociais. Então pode usar o `domain_hint` parâmetro de cadeia de consulta. O parâmetro de sugestão de domínio leva o utilizador diretamente à página de entrada do fornecedor de identidade social.

Na sua aplicação, adicione o sômin com botões de conta social. Quando um utilizador clica num dos botões da conta social, o controlo precisa de alterar o nome da política ou definir o parâmetro de sugestão de domínio.

<!-- TBD: add a diagram -->

O URI de redirecionamento pode ser o mesmo URI de redirecionamento usado pelo iframe. Pode saltar a recarga da página.

## <a name="configure-a-single-page-application"></a>Configure uma aplicação de uma página

Para uma aplicação de uma página, também precisará de uma segunda página HTML "iniciar s-in" que se carregue no iframe. Esta página de inscrição acolhe o código da biblioteca de autenticação que gera o código de autorização e devolve o token.

Quando a aplicação de uma página precisa do token de acesso, use o código JavaScript para obter o token de acesso a partir do iframe e objeto que o contém.

> [!NOTE]
> A execução do MSAL 2.0 num iframe não é suportada atualmente.

O seguinte código é um exemplo que corre na página principal e chama o código JavaScript de um iframe:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos relacionados:

- [Personalização da interface de utilizador](customize-ui.md)
- Referência do elemento [departe de suporte](relyingparty.md)
- [Ativar a sua política para JavaScript](javascript-samples.md)
- [Amostras de código](code-samples.md)
