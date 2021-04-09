---
title: Aceder a informações dos utilizadores em Azure Static Web Apps
description: Aprenda a ler dados de utilizadores devolvidos pelo fornecedor de autorização.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: d5a1d810c357aa83b8069023b00d76352da124df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94844800"
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

As funções API disponíveis em Aplicações Web Estáticas através do backend Azure Functions têm acesso à mesma informação do utilizador que uma aplicação do cliente. Embora a API receba informações identificáveis pelo utilizador, não efetua as suas próprias verificações se o utilizador for autenticado ou se corresponder a uma função necessária. As regras de controlo de acesso são definidas no [`routes.json`](routes.md) ficheiro.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

Numa função C#, as informações do utilizador estão disponíveis a partir do `x-ms-client-principal` cabeçalho que pode ser deseralizado num `ClaimsPrincipal` objeto ou no seu próprio tipo personalizado. O código que se segue demonstra como desempacotar o cabeçalho num tipo intermediário, `ClientPrincipal` que é depois transformado num `ClaimsPrincipal` caso.

```csharp
  public static class StaticWebAppsAuth
  {
    private class ClientPrincipal
    {
        public string IdentityProvider { get; set; }
        public string UserId { get; set; }
        public string UserDetails { get; set; }
        public IEnumerable<string> UserRoles { get; set; }
    }

    public static ClaimsPrincipal Parse(HttpRequest req)
    {
        var principal = new ClientPrincipal();

        if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
        {
            var data = header[0];
            var decoded = Convert.FromBase64String(data);
            var json = Encoding.ASCII.GetString(decoded);
            principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
        }

        principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

        if (!principal.UserRoles?.Any() ?? true)
        {
            return new ClaimsPrincipal();
        }

        var identity = new ClaimsIdentity(principal.IdentityProvider);
        identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
        identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
        identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

        return new ClaimsPrincipal(identity);
    }
  }
```

---

<sup>1</sup> A [API fetch](https://caniuse.com/#feat=fetch) e o operador [de espera](https://caniuse.com/#feat=mdn-javascript_operators_await) não são suportados no Internet Explorer.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar as definições da aplicação](application-settings.md)
