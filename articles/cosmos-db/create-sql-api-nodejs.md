---
title: Quickstart- Use Node.js para consultar a conta API API da Azure Cosmos DB SQL
description: Como usar Node.js para criar uma app que se conecta à conta API API da Azure Cosmos DB SQL e consulta dados.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: b4ed27e1515e898a71fb503bb0f260c608ef9f6f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090210"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Quickstart: Utilize Node.js para ligar e consultar dados da conta API API da Azure Cosmos DB SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [SDK v4 de Java](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

Neste quickstart, você cria e gere uma conta API API AZURE Cosmos DB SQL a partir do portal Azure, e usando uma aplicação de Node.js clonada do GitHub. Azure Cosmos DB é um serviço de base de dados multi-modelo que permite criar e consultar rapidamente documentos, tabelas, valor-chave e bases de dados de gráficos com capacidades de distribuição global e escala horizontal.

## <a name="walkthrough-video"></a>Vídeo walkthrough

Veja este vídeo para uma completa passagem pelo conteúdo deste artigo.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Ou [experimente a Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição do Azure. Também pode utilizar o [Emulador Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) com um URI de `https://localhost:8081` e a chave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git.](https://www.git-scm.com/downloads)

## <a name="create-an-azure-cosmos-account"></a>Criar uma conta do Azure Cosmos

Para este fim de arranque rápido, pode utilizar a [tentativa Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) opção gratuita para criar uma conta Azure Cosmos.

1. Navegue para a [tentativa Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) página gratuita.

1. Escolha a conta API **SQL** e selecione **Criar.** Iniciar sôms usando a sua conta Microsoft.

1. Depois de o sin-in ter sido bem sucedido, a sua conta Azure Cosmos deve estar pronta. Selecione **Abrir no portal Azure** para abrir a conta recém-criada.

A opção "experimente a Azure Cosmos DB gratuitamente" não requer uma subscrição do Azure e oferece-lhe uma conta Azure Cosmos por um período limitado de 30 dias. Se quiser utilizar a conta Azure Cosmos por um período mais longo, [deverá,](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) em vez disso, criar a conta dentro da sua subscrição Azure.

## <a name="add-a-container"></a>Adicionar um contentor

Pode agora utilizar a ferramenta Data Explorer no portal Azure para criar uma base de dados e um recipiente.

1. Selecione **data explorer**  >  **novo recipiente** .

   A área **do Recipiente Adicionar** é apresentada na extrema direita, pode ser necessário deslocar-se para a frente para o ver.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="O Data Explorer no portal do Azure, painel Adicionar Contentor":::

2. Na página **do recipiente Adicionar,** introduza as definições para o novo recipiente.

   | Definição           | Valor sugerido | Descrição                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **ID da Base de Dados**   | Tarefas           | Designe a nova base de dados como _Tarefas_ . Os nomes da base de dados devem conter de 1 a 255 caracteres, não podendo conter `/, \\, #, ?` , ou um espaço de fuga. Consulte a opção **de produção da base de dados Provision,** que lhe permite partilhar o produto que está previsto na base de dados em todos os contentores da base de dados. Esta opção também ajuda na poupança de custos. |
   | **Débito**    | 400             | Deixe a produção a 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar verticalmente o débito mais tarde.                                                                                                                                                                                                                                                    |
   | **ID do Contentor**  | Itens           | _Insira os Itens_ como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.                                                                                                                                                                                                                                                               |
   | **Chave de partição** | /categoria       | A amostra descrita neste artigo _utiliza/categoria_ como chave de partição.                                                                                                                                                                                                                                                                                                           |

   Além das definições anteriores, pode opcionalmente adicionar **teclas Únicas** para o recipiente. Vamos deixar o campo vazio neste exemplo. As chaves exclusivas oferecem aos programadores a capacidade de adicionar uma camada de integridade dos dados na base de dados. Ao criar uma política chave única ao criar um recipiente, garante a singularidade de um ou mais valores por chave de partição. Para saber mais, consulte o artigo [Chaves exclusivas no Azure Cosmos DB](unique-keys.md).

   Selecione **OK** . O Data Explorer mostra a base de dados e o contentor novos.

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

Este passo é opcional. Se estiver interessado em saber como os recursos da base de dados Azure Cosmos são criados no código, pode rever os seguintes excertos. Caso contrário, pode avançar diretamente para [Update your connection string (Atualizar a cadeia de ligação)](#update-your-connection-string).

Se estiver familiarizado com a versão anterior do SQL JavaScript SDK, poderá estar habituado a ver a _recolha_ e _o documento_ dos termos. Como o Azure Cosmos DB suporta [vários modelos API](introduction.md), [a versão 2.0+ do JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) utiliza o _recipiente_ de termos genéricos , que pode ser uma coleção, gráfico ou tabela, e _item_ para descrever o conteúdo do recipiente.

O Cosmos DB JavaScript SDK chama-se @azure/cosmos " " e pode ser instalado a partir de npm...

```bash
npm install @azure/cosmos
```

Os seguintes cortes são todos retirados do ficheiro _app.js._

- O `CosmosClient` é importado do pacote `@azure/cosmos` npm.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Um novo `CosmosClient` objeto é inicializado.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Selecione a base de dados "Tarefas".

  ```javascript
  const database = client.database(databaseId);
  ```

- Selecione o recipiente/recolha "Itens".

  ```javascript
  const container = database.container(containerId);
  ```

- Selecione todos os itens no recipiente "Itens".

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
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
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Eliminar um item

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> Tanto nos métodos de "atualização" como "apagar", o item tem de ser selecionado a partir da base de dados, chamando `container.item()` . Os dois parâmetros passados são o id do item e a chave de partição do item. Neste caso, a chave de parição é o valor do campo "categoria".

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora volte ao portal Azure para obter os detalhes da cadeia de ligação da sua conta Azure Cosmos. Copie o fio de ligação na aplicação para que possa ligar-se à sua base de dados.

1. Na sua conta DB Azure Cosmos no [portal Azure,](https://portal.azure.com/)selecione **Keys** da navegação esquerda e, em seguida, selecione **'Teclas de leitura'.** Utilize os botões de cópia no lado direito do ecrã para copiar a URI e a Chave Primária para o ficheiro _app.js_ no passo seguinte.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="O Data Explorer no portal do Azure, painel Adicionar Contentor":::

2. Em Abrir o ficheiro _config.js._

3. Copie o seu valor URI a partir do portal (utilizando o botão de cópia) e faça com que o valor da tecla de ponto final _config.js_ .

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Em seguida, copie o seu valor PRINCIPAL do portal e faça dele o valor do `config.key` _inconfig.js_ . Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Executar a aplicação

1. Corra `npm install` em um terminal para instalar o pacote " @azure/cosmos npm "

2. Execute `node app.js` num terminal para iniciar a aplicação Node.js.

3. Os dois itens que criou mais cedo neste quickstart estão listados. Um novo item é criado. A bandeira "isComplete" nesse item é atualizada para "verdadeiro" e, finalmente, o item é eliminado.

Pode continuar a experimentar esta aplicação de amostra ou voltar ao Data Explorer, modificar e trabalhar com os seus dados.

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta DB Azure Cosmos, criar um recipiente usando o Data Explorer e executar uma aplicação Node.js. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [dados de importação em azure cosmos db](import-data.md)
