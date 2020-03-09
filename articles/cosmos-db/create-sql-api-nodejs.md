---
title: Quickstart- Use Node.js para consultar a conta API Da Azure Cosmos DB SQL
description: Como usar o Node.js para criar uma app que se conecta à conta API Da Azure Cosmos DB SQL e consulta dados.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 729fd776321a90257289dcf92f13079a8206d9d9
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927388"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Quickstart: Use Node.js para ligar e consultar dados da conta API Da Azure Cosmos DB SQL

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java](create-sql-api-java.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste arranque rápido, cria e gere uma conta API Azure Cosmos DB SQL a partir do portal Azure, e utilizando uma aplicação Node.js clonada do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, basede-chaves e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure. Você também pode usar o [Emulador Db Azure Cosmos](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Azure Cosmos

Para este fim de arranque rápido, você pode usar o [experiment o Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) a opção gratuita para criar uma conta Azure Cosmos.

1. Navegue para a [prova Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) página gratuita.

1. Escolha a conta **SQL** API e selecione **Criar**. Inscreva-se na sua conta Microsoft.

1. Depois de o início de inscrição, a sua conta Azure Cosmos deve estar pronta. Selecione **Open no portal Azure** para abrir a conta recém-criada.

A opção "try Azure Cosmos DB gratuitamente" não requer uma subscrição Azure e oferece-lhe uma conta Azure Cosmos por um período limitado de 30 dias. Se quiser utilizar a conta Azure Cosmos por um período mais longo, [deverá,](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) em vez disso, criar a conta dentro da sua subscrição Azure.

## <a name="add-a-container"></a>Adicione um recipiente

Agora pode utilizar a ferramenta Data Explorer no portal Azure para criar uma base de dados e um recipiente.

1. Selecione **Data Explorer** > **Novo Recipiente**.

   A área **add container** é exibida na extrema-direita, pode ser necessário rolar para a direita para vê-lo.

   ![O portal Azure Data Explorer, Adicionar painel de contentores](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. Na página **'Adicionar',** introduza as definições para o novo recipiente.

   | Definição           | Valor sugerido | Descrição                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **ID da Base de Dados**   | Tarefas           | Designe a nova base de dados como _Tarefas_. Os nomes da base de dados devem conter de 1 a 255 caracteres, e não podem conter `/, \\, #, ?`, ou um espaço de fuga. Consulte a opção de entrada da base de **dados Provision,** permite-lhe partilhar a entrada disponibilizada na base de dados em todos os recipientes da base de dados. Esta opção também ajuda na poupança de custos. |
   | **Débito**    | 400             | Deixe a entrada em 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.                                                                                                                                                                                                                                                    |
   | **ID do contentor**  | Itens           | Introduza _itens_ como o nome do seu novo recipiente. Os IDs de contentores têm os mesmos requisitos de caracteres que os nomes da base de dados.                                                                                                                                                                                                                                                               |
   | **Chave de partição** | /categoria       | A amostra descrita neste artigo _utiliza/categoria_ como chave de partição.                                                                                                                                                                                                                                                                                                           |

   Além das definições anteriores, pode adicionar **opcionalmente chaves Únicas** para o recipiente. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política chave única ao criar um recipiente, você garante a singularidade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](unique-keys.md).

   Selecione **OK**. O Data Explorer exibe a nova base de dados e o recipiente.

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora vamos clonar uma aplicação Node.js do GitHub, definir a cadeia de ligação e executá-la.

1. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em saber como os recursos da base de dados Azure Cosmos são criados no código, pode rever os seguintes cortes. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string).

Se estiver familiarizado com a versão anterior do SQL JavaScript SDK, poderá estar habituado a ver os termos _de recolha_ e _documento_. Uma vez que o Azure Cosmos DB suporta [vários modelos API](introduction.md), a [versão 2.0+ do JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) utiliza o _recipiente_de termos genéricos , que pode ser uma coleção, gráfico ou tabela, e _item_ para descrever o conteúdo do recipiente.

O Cosmos DB JavaScript SDK chama-se "@azure/cosmos" e pode ser instalado a partir da npm...

```bash
npm install @azure/cosmos
```

Os seguintes fragmentos são retirados do ficheiro _app.js_.

- O `CosmosClient` é importado do pacote `@azure/cosmos` npm.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Um novo objeto `CosmosClient` é inicializado.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Selecione a base de dados "Tarefas".

  ```javascript
  const database = await client.databases(databaseId);
  ```

- Selecione o recipiente/recolha "Itens".

  ```javascript
  const container = await client.databases(containerId);
  ```

- Selecione todos os itens do recipiente "Itens".

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: results } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Criar um novo item

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Atualizar um item

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: itemToUpdate } = await container
    .item(id, category)
    .replace(itemToUpdate);
  ```

- Apagar um item

  ```javascript
  const { resource: result } = await this.container.item(id, category).delete();
  ```

> [!NOTE]
> Tanto nos métodos de "atualização" como de "apagar", o item tem de ser selecionado a partir da base de dados, ligando para `container.item()`. Os dois parâmetros passados são a identificação do item e a chave de partição do item. Neste caso, a chave de parição é o valor do campo "categoria".

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora volte ao portal Azure para obter os detalhes da linha de ligação da sua conta Azure Cosmos. Copie a cadeia de ligação para a aplicação de modo a que possa ligar-se à sua base de dados.

1. Na sua conta Azure Cosmos DB no [portal Azure,](https://portal.azure.com/)selecione **Chaves** da navegação à esquerda e, em seguida, selecione **Teclas de leitura .** Utilize os botões de cópia no lado direito do ecrã para copiar o uri e a Chave Primária no ficheiro _app.js_ no próximo passo.

   ![Ver e copiar uma chave de acesso no portal do Azure, painel Chaves](./media/create-sql-api-dotnet/keys.png)

2. Em Abrir o ficheiro _config.js._

3. Copie o seu valor URI a partir do portal (utilizando o botão de cópia) e faça-o com o valor da chave de ponto final em _config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Em seguida, copie o seu valor PRINCIPAL CHAVE do portal e faça dele o valor do `config.key` em _config.js_. Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Executar a aplicação

1. Executar `npm install` em um terminal para instalar o pacote "@azure/cosmos" npm

2. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

3. Os dois itens que criou no início deste quickstart estão listados. Um novo item é criado. A bandeira "isComplete" nesse item é atualizada para "verdadeiro" e, finalmente, o item é apagado.

Pode continuar a experimentar esta aplicação de amostraou voltar ao Data Explorer, modificar e trabalhar com os seus dados.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos DB, criar um recipiente usando o Data Explorer e executar uma app Node.js. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [dados de importação em azure cosmos db](import-data.md)
