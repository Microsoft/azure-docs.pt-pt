---
title: 'Quickstart: A sua primeira consulta JavaScript'
description: Neste arranque rápido, segue os passos para ativar a biblioteca de Gráficos de Recursos para JavaScript e executar a sua primeira consulta.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 194097594340e2f34433c07185c6132f8a13c845
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057421"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Quickstart: Executar a sua primeira consulta de Gráfico de Recursos usando JavaScript

Este quickstart acompanha-o através do processo de adição das bibliotecas à sua instalação JavaScript. O primeiro passo para a utilização do Azure Resource Graph é inicializar uma aplicação JavaScript com as bibliotecas necessárias.

No final deste processo, terá adicionado as bibliotecas à sua instalação JavaScript e executará a sua primeira consulta de Gráfico de Recursos.

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição Azure**: Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- **Node.js**: [Node.js](https://nodejs.org/) versão 12 ou superior é necessária.

## <a name="application-initialization"></a>Inicialização de aplicações

Para permitir que o JavaScript questione o Azure Resource Graph, o ambiente deve ser configurado. Esta configuração funciona onde o JavaScript pode ser utilizado, incluindo [a bash no Windows 10](/windows/wsl/install-win10).

1. Inicialize um novo projeto de Node.js executando o seguinte comando.

   ```bash
   npm init -y
   ```

1. Adicione uma referência ao módulo yargs.

   ```bash
   npm install yargs
   ```

1. Adicione uma referência ao módulo Azure Resource Graph.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Adicione uma referência à biblioteca de autenticação Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Verifique em _package.jsem é a_ versão `@azure/arm-resourcegraph` **2.0.0** ou superior e `@azure/ms-rest-nodeauth` é a versão **3.0.3** ou superior.

## <a name="query-the-resource-graph"></a>Consulta do Gráfico de Recursos

1. Crie um novo ficheiro chamado _index.js_ e introduza o seguinte código.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Introduza o seguinte comando no terminal:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   Certifique-se de substituir o `<YOUR_SUBSCRIPTION_ID_LIST>` espaço reservado pela sua lista separada por vírgula de IDs de subscrição Azure.

   > [!NOTE]
   > Como este exemplo de consulta não fornece um tipo modificador `order by` como, executar esta consulta várias vezes é provável que produza um conjunto diferente de recursos por pedido.

1. Altere o primeiro parâmetro para `index.js` e altere a consulta para `order by` a propriedade **Nome.** `<YOUR_SUBSCRIPTION_ID_LIST>`Substitua-o pelo seu ID de subscrição:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   À medida que o script tenta autenticar, uma mensagem semelhante à seguinte mensagem é exibida no terminal:

   > Para iniciar sposição, utilize um navegador web para abrir a página https://microsoft.com/devicelogin e introduza o código FGB56WJUGK para autenticar.

   Uma vez autenticado no navegador, o script continua a ser executado.

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Altere o primeiro parâmetro para `index.js` e altere a consulta para primeiro `order by` a propriedade **Nome** e, em seguida, para os `limit` cinco melhores resultados. `<YOUR_SUBSCRIPTION_ID_LIST>`Substitua-o pelo seu ID de subscrição:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

## <a name="clean-up-resources"></a>Limpar recursos

Se desejar remover as bibliotecas instaladas da sua aplicação, execute o seguinte comando.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, adicionou as bibliotecas de Gráficos de Recursos ao seu ambiente JavaScript e executou a sua primeira consulta. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)