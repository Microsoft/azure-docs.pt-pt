---
title: Aceder a informações dos utilizadores em Azure Static Web Apps
description: Aprenda a ler dados de utilizadores devolvidos pelo fornecedor de autorização.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 273ab5c20cf41b7305d72f1bfef5deeeb046c49a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83597752"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Aceder a informações dos utilizadores em Azure Static Web Apps Preview

A Azure Static Web Apps fornece informações de utilizadores relacionadas com a autenticação através de um [ponto final de acesso direto](#direct-access-endpoint) e para [funções API](#api-functions).

Muitas interfaces de utilizador dependem fortemente dos dados de autenticação do utilizador. O ponto final de acesso direto é uma API de utilidade que expõe a informação do utilizador sem ter de implementar uma função personalizada. Além da conveniência, o ponto final de acesso direto não está sujeito a atrasos de arranque a frio que estejam associados à arquitetura sem servidor.

## <a name="client-principal-data"></a>Dados principais do cliente

O principal objeto de dados do cliente expõe a informação identificável do utilizador à sua aplicação. As seguintes propriedades estão em destaque no objeto principal do cliente:

| Propriedade  | Descrição |
|-----------|---------|
| `identityProvider` | O nome do [fornecedor de identidade.](authentication-authorization.md) |
| `userId` | Um identificador exclusivo específico de Aplicações Web Azure Estático para o utilizador. <ul><li>O valor é único numa base por aplicação. Por exemplo, o mesmo utilizador devolve um valor diferente `userId` num recurso de Aplicações Web estáticas diferente.<li>O valor persiste durante toda a vida de um utilizador. Se eliminar e adicionar o mesmo utilizador de volta à aplicação, é gerado um `userId` novo.</ul>|
| `userDetails` | Nome de utilizador ou endereço de e-mail do utilizador. Alguns fornecedores devolvem o [endereço de e-mail do utilizador,](authentication-authorization.md)enquanto outros enviam o [manípulo](authentication-authorization.md)do utilizador. |
| `userRoles`     | Uma série de [funções atribuídas pelo utilizador](authentication-authorization.md). |

Segue-se um exemplo principal do cliente da amostra:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Ponto final de acesso direto

Pode enviar um `GET` pedido para a rota e ter acesso direto aos `/.auth/me` dados principais do cliente. Quando o estado da sua visão depender de dados de autorização, utilize esta abordagem para o melhor desempenho.

Para os utilizadores com sessão iniciada, a resposta contém um objeto JSON principal do cliente. Pedidos de devoluções de utilizadores não `null` autenticados.

Utilizando a [api](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)de busca<sup>1,</sup> pode aceder aos dados principais do cliente utilizando a seguinte sintaxe.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>Funções de API

Os dados principais do cliente são transmitidos para funções de API no cabeçalho de `x-ms-client-principal` pedido. Os dados principais do cliente são enviados como uma cadeia codificada [base64](https://www.wikipedia.org/wiki/Base64)contendo um objeto JSON serializado.

A função exemplo a seguir mostra como ler e devolver informações do utilizador.

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

Assumindo que a função acima é `user` nomeada, pode utilizar a API do navegador [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> para aceder à resposta da API utilizando a seguinte sintaxe.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> A [API fetch](https://caniuse.com/#feat=fetch) e o operador [de espera](https://caniuse.com/#feat=mdn-javascript_operators_await) não são suportados no Internet Explorer.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](application-settings.md)
