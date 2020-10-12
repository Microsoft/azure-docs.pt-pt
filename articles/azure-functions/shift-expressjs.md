---
title: Passando de Express.js para Funções Azure
description: Aprenda a refactor Express.js pontos finais para as Funções Azure.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87810229"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Passando de Express.js para Funções Azure

Express.js é um dos quadros de Node.js mais populares para desenvolvedores web e continua a ser uma excelente escolha para a construção de apps que servem pontos finais da API.

Ao migrar o código para uma arquitetura sem servidor, a refacagem Express.js pontos finais afeta as seguintes áreas:

- **Middleware**: Express.js possui uma coleção robusta de middleware. Muitos módulos de middleware já não são necessários à luz das funções Azure e das capacidades de [Gestão API Azure.](../api-management/api-management-key-concepts.md) Certifique-se de que pode replicar ou substituir qualquer lógica manuseada por middleware essencial antes de migrar pontos finais.

- **APIs diferentes**: A API utilizada para processar tanto pedidos como respostas difere entre Funções Azure e Express.js. O exemplo a seguir detalha as alterações necessárias.

- **Rota predefinida**: Por predefinição, os pontos finais das funções Azure são expostos ao abrigo da `api` rota. As regras de encaminhamento são configuráveis através [ `routePrefix` dohost.js_em_ ficheiro](./functions-bindings-http-webhook-output.md#hostjson-settings).

- **Configuração e convenções**: Uma aplicação de funções utiliza o _function.jsem_ ficheiro para definir verbos HTTP, definir políticas de segurança e pode configurar a entrada e saída da [função](./functions-triggers-bindings.md). Por predefinição, o nome da pasta que contém os ficheiros de função define o nome do ponto final, mas pode alterar o nome através da `route` propriedade nofunction.js[ no](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) ficheiro.

> [!TIP]
> Saiba mais através do Node.js de refactor tutorial interativo [e APIs expressos para APIs sem servidor com funções Azure](/learn/modules/shift-nodejs-express-apis-serverless/).

## <a name="example"></a>Exemplo

### <a name="expressjs"></a>Express.js

O exemplo a seguir mostra um ponto final típico `GET` Express.js.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

Quando um `GET` pedido é enviado `/hello` para, uma resposta contendo é `HTTP 200` `Success` devolvida. Se o ponto final encontrar um erro, a resposta é um `HTTP 500` com os detalhes de erro.

### <a name="azure-functions"></a>Funções do Azure

As Funções Azure organizam ficheiros de configuração e código numa única pasta para cada função. Por predefinição, o nome da pasta dita o nome da função.

Por exemplo, uma função nomeada `hello` tem uma pasta com os seguintes ficheiros.

``` files
| - hello
|  - function.json
|  - index.js
```

O exemplo a seguir implementa o mesmo resultado que o ponto final acima Express.js, mas com funções Azure.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

Ao deslocar-se para Funções, são feitas as seguintes alterações:

- **Módulo:** O código de função é implementado como um módulo JavaScript.

- **Objeto de contexto e resposta**: [`context`](./functions-reference-node.md#context-object) Permite-lhe comunicar com o tempo de funcionamento da Função. A partir do contexto, pode ler os dados do pedido e definir a resposta da função. O código sincronizado requer que ligue `context.done()` para completar a execução, enquanto `asyc` as funções resolvem o pedido implicitamente.

- **Convenção de nomeação**: O nome da pasta utilizado para conter os ficheiros Azure Functions é utilizado como nome de ponto final por defeito (isto pode ser ultrapassado no [function.jsem](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)).

- **Configuração**: Define os verbos HTTP no [function.jsem](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) ficheiros como `POST` ou `PUT` .

A _ seguintefunction.jsno_ ficheiro contém informações de configuração para a função.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Ao definir `get` na `methods` matriz, a função está disponível para `GET` pedidos HTTP. Se quiser que a sua API aceite pedidos de `POST` apoio, também pode adicionar `post` à matriz.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais com o Node.js tutorial interativo [e APIs expressos para APIs sem servidor com funções Azure](/learn/modules/shift-nodejs-express-apis-serverless/)