---
title: Serviço de Confiança JavaScript
description: Esta é a versão JavaScript da criação de um Serviço Fidedigno para Serviços de Comunicação.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 6790335e5aa63f515cd125f31a8ccd7877132c10
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661349"
---
## <a name="download-code"></a>Código de Descarregamento

Encontre o código finalizado para este arranque rápido no [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/Trusted%20Authentication%20Service)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. Para mais detalhes, consulte [Criar uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Visual Studio Code](https://code.visualstudio.com/) numa das [plataformas suportadas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), Versões LTS ativas e de manutenção (10.14.1 recomendado). Utilize o `node --version` comando para verificar a sua versão.
- A [extensão das Funções do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para o Visual Studio Code.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../../quickstarts/create-communication-resource.md)

## <a name="overview"></a>Descrição Geral

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="Diagrama para arquitetura de serviço de confiança":::

Para este tutorial, vamos criar uma Função Azure que servirá como um serviço de fornecimento de símbolos fidedignos. Você pode usar este tutorial para arrancar o seu próprio serviço de fornecimento simbólico.

Este serviço é responsável pela autenticação dos utilizadores nos Serviços de Comunicação Azure. Os utilizadores das suas aplicações de Serviços de Comunicação exigirão uma `Access Token` participação nas linhas de chat e chamadas VoIP. A Função Azure funcionará como intermediário de confiança entre o utilizador e os Serviços de Comunicação. Isto permite-lhe obter fichas de acesso sem expor o seu fio de ligação de recursos aos seus utilizadores.

Para mais informações, consulte a arquitetura do [servidor cliente](../../concepts/client-and-server-architecture.md) e [a autenticação e autorização](../../concepts/authentication.md) de documentação conceptual.

## <a name="setting-up"></a>Configuração

### <a name="azure-functions-set-up"></a>Funções Azure configurada

Vamos primeiro configurar a estrutura básica para a nossa função Azure. Instruções passo a passo sobre a configuração podem ser encontradas aqui: [Criar uma função usando Código de Estúdio Visual](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)

A nossa função Azure requer a seguinte configuração:

- Idioma: JavaScript
- Modelo: HTTP Trigger
- Nível de Autorização: Anónimo (Isto pode ser trocado mais tarde se preferir um modelo de autorização diferente)
- Nome da função: Definido pelo utilizador

Depois de seguir as [instruções de Funções Azure](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript) com a configuração acima, deverá ter um projeto no Código do Estúdio Visual para a Função Azure com um `index.js` ficheiro que contenha a função em si. O código dentro deste ficheiro deve ser o seguinte:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

Vamos agora proceder à instalação de bibliotecas dos Serviços de Comunicação Azure.

### <a name="install-communication-services-libraries"></a>Instalar bibliotecas de serviços de comunicação

Usaremos a `Identity` biblioteca para `User Access Tokens` gerar.

Utilize o `npm install` comando para instalar a biblioteca de clientes de identidade de serviços de comunicação Azure para o JavaScript.

```console

npm install @azure/communication-identity --save

```

A `--save` opção lista a biblioteca como uma dependência do seu **package.jsarquivado.**

No topo do `index.js` ficheiro, importe a interface para o `CommunicationIdentityClient`

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');
```

## <a name="access-token-generation"></a>Geração simbólica de acesso

Para permitir que a nossa Função Azure `User Access Tokens` gere, primeiro precisamos de usar a cadeia de ligação para o nosso recurso de Serviços de Comunicação.

Visite o [quickstart de fornecimento de recursos](../../quickstarts/create-communication-resource.md) para obter mais informações sobre a recuperação da sua cadeia de ligação.

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

Em seguida, vamos modificar a nossa função original para `User Access Tokens` gerar.

`User Access Tokens` são gerados criando um utilizador a partir do `createUser` método. Uma vez criado o utilizador, podemos utilizar o `issueToken` método para gerar um símbolo para o utilizador que a Função Azure retorna.

Para este exemplo, vamos configurar o âmbito simbólico para `voip` . Outros âmbitos podem ser necessários para a sua aplicação. Saiba mais sobre [âmbitos](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    context.res = {
        body: userToken
    };
}
```

Para os Serviços de Comunicação `CommunicationUser` existentes, pode saltar o passo de criação e apenas gerar um token de acesso. Mais detalhes [encontrados](../../quickstarts/access-tokens.md)no início rápido de acesso do utilizador Create .

## <a name="test-the-azure-function"></a>Teste a Função Azure

Executar a Função Azure localmente utilizando `F5` . Isto rubricará a Função Azure localmente e torná-la-á acessível através de: `http://localhost:7071/api/FUNCTION_NAME` . Consulte documentação adicional sobre [o funcionamento local](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-locally)

Abra o URL no seu navegador e deverá ver um corpo de resposta com o ID do utilizador de comunicação, token e expiração para o token.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Screenshot mostrando um exemplo de resposta para a função Azure criada.":::

## <a name="deploy-the-function-to-azure"></a>Implementar a Função para Azure

Para implementar a sua Função Azure, pode seguir [as instruções passo a passo](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#sign-in-to-azure)

Em resumo, terá de:
1. Inscreva-se no Azure do Visual Studio
2. Publique o seu projeto na sua conta Azure. Aqui terá de escolher uma subscrição existente.
3. Crie um novo recurso Azure Function utilizando o assistente do Estúdio Visual ou utilize um recurso existente. Para um novo recurso, terá de o configurar para a sua região desejada, tempo de execução e identificador único.
4. Aguarde a implantação para finalizar
5. Executar a função 🎉

## <a name="run-azure-function"></a>Executar função Azure

Executar a função Azure usando o url `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`

Pode encontrar o URL clicando corretamente na função no Código do Estúdio Visual e copiando o URL de função.

Para mais informações sobre [a execução da sua função Azure](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-in-azure)
