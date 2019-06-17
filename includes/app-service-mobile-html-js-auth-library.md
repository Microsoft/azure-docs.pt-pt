---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140880"
---
### <a name="server-auth"></a>Como: Autenticar com um fornecedor (fluxo de servidor)
Para que as Aplicações Móveis giram o processo de autenticação na sua aplicação, tem de registar a aplicação com o seu fornecedor de identidade. Em seguida, no seu Serviço de Aplicações do Azure, tem de configurar o ID da aplicação e o segredo fornecidos pelo seu fornecedor.
Para obter mais informações, consulte o tutorial [Add authentication to your app (Adicionar autenticação à sua aplicação)](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

Depois de registar o seu fornecedor de identidade, chame o método `.login()` com o nome do fornecedor. Por exemplo, para iniciar sessão com o Facebook utilize o seguinte código:

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Os valores válidos para o fornecedor são "aad", "facebook", "google", "microsoftaccount" e "twitter".

> [!NOTE]
> A Autenticação da Google não funciona atualmente através do Fluxo de Servidor.  Para se autenticar com o Google, tem de utilizar um [método de fluxo de cliente](#client-auth).

Neste caso, o Serviço de Aplicações do Azure gere o fluxo de autenticação OAuth 2.0.  Apresenta a página de início de sessão do fornecedor selecionado e gera um token de autenticação do serviço de aplicações após o início de sessão com êxito com o fornecedor de identidade. A função de início de sessão, quando concluída, devolve um objeto JSON que expõe o ID de utilizador e o token de autenticação do Serviço de Aplicações nos campos userId e authenticationToken, respetivamente. Este token pode ser colocado em cache e reutilizado até expirar.

### <a name="client-auth"></a>Como: Autenticar com um fornecedor (fluxo de cliente)

A sua aplicação também pode contactar o fornecedor de identidade independentemente e, em seguida, fornecer o token devolvido ao seu Serviço de Aplicações para autenticação. Este fluxo de cliente permite-lhe fornecer uma experiência de início de sessão único para os utilizadores ou para obter dados de utilizador adicionais do fornecedor de identidade.

#### <a name="social-authentication-basic-example"></a>Exemplo básico de Autenticação nas Redes Sociais

Este exemplo utiliza o SDK cliente do Facebook para autenticação:

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
Este exemplo assume que o token fornecido pelo SDK do fornecedor respetivo é armazenado na variável do token.

### <a name="auth-getinfo"></a>Como: Obter informações sobre o utilizador autenticado

As informações de autenticação podem ser obtidas a partir do ponto final `/.auth/me`, ao utilizar uma chamada HTTP com qualquer biblioteca de AJAX.  Certifique-se de que definiu o cabeçalho `X-ZUMO-AUTH` para o token de autenticação.  O token de autenticação é armazenado no `client.currentUser.mobileServiceAuthenticationToken`.  Por exemplo, para utilizar a API de obtenção:

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

A obtenção está disponível como [um pacote de npm](https://www.npmjs.com/package/whatwg-fetch) ou para transferência de browser a partir do [CDNJS](https://cdnjs.com/libraries/fetch). Pode também utilizar jQuery ou outra API de AJAX para obter as informações.  Os dados são recebidos como um objeto JSON.
