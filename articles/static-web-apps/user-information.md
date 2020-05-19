---
title: Aceder à informação dos utilizadores em Aplicações Web Estáticas do Azure
description: Aprenda a ler dados de utilizadores devolvidos pelo fornecedor de autorização.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 273ab5c20cf41b7305d72f1bfef5deeeb046c49a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597752"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Aceder à informação dos utilizadores na pré-visualização de aplicações da Web Da Web Do Azure

As Aplicações Web Estáticas Azure fornecem informações de utilizador relacionadas com a autenticação através de um [ponto final de acesso direto](#direct-access-endpoint) e para [funções API](#api-functions).

Muitas interfaces de utilizador dependem fortemente dos dados de autenticação do utilizador. O ponto final de acesso direto é um Utilitário API que expõe informações do utilizador sem ter de implementar uma função personalizada. Além da conveniência, o ponto final de acesso direto não está sujeito a atrasos de arranque a frio que estão associados à arquitetura sem servidores.

## <a name="client-principal-data"></a>Dados principais do cliente

O principal objeto de dados do cliente expõe informações identificáveis pelo utilizador à sua aplicação. As seguintes propriedades estão em destaque no objeto principal do cliente:

| Propriedade  | Descrição |
|-----------|---------|
| `identityProvider` | O nome do fornecedor de [identidade.](authentication-authorization.md) |
| `userId` | Um identificador único específico de Aplicações Web Azure Static para o utilizador. <ul><li>O valor é único numa base por app. Por exemplo, o mesmo utilizador devolve um valor diferente `userId` num recurso de Aplicações Web Estáticas diferentes.<li>O valor persiste durante a vida de um utilizador. Se eliminar e adicionar o mesmo utilizador de volta à aplicação, gera-se um `userId` novo.</ul>|
| `userDetails` | Nome de utilizador ou endereço de e-mail do utilizador. Alguns fornecedores devolvem o [endereço de e-mail do utilizador,](authentication-authorization.md)enquanto outros enviam o [manípulo](authentication-authorization.md)do utilizador . |
| `userRoles`     | Uma série de [funções atribuídas](authentication-authorization.md)pelo utilizador. |

O exemplo seguinte é um objeto principal do cliente da amostra:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Ponto final de acesso direto

Pode enviar um `GET` pedido para a rota e receber acesso direto aos `/.auth/me` principais dados do cliente. Quando o estado da sua opinião depender de dados de autorização, use esta abordagem para o melhor desempenho.

Para os utilizadores de login, a resposta contém um objeto JSON principal do cliente. Os pedidos de devoluções de utilizadores não `null` autenticados.

Utilizando a [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> API, pode aceder aos dados principais do cliente utilizando a seguinte sintaxe.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>Funções API

Os dados principais do cliente são transmitidos às funções DaPI no cabeçalho do `x-ms-client-principal` pedido. Os dados principais do cliente são enviados como uma cadeia codificada [base64](https://www.wikipedia.org/wiki/Base64)contendo um objeto JSON serializado.

A função exemplo seguinte mostra como ler e devolver as informações dos utilizadores.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Assumindo que a função acima é `user` nomeada, pode utilizar a API do navegador [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> para aceder à resposta da API utilizando a seguinte sintaxe.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> A [Fetch](https://caniuse.com/#feat=fetch) API e [o operador à espera](https://caniuse.com/#feat=mdn-javascript_operators_await) não são suportados no Internet Explorer.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](application-settings.md)
