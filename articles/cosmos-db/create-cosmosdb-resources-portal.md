---
title: Quickstart - Criar recursos DB da Azure Cosmos a partir do portal Azure
description: Este quickstart mostra como criar uma base de dados, contentor e itens Azure Cosmos utilizando o portal Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/10/2020
ms.openlocfilehash: baeb5fbadfaf128c2c491a1fdb7e880b413878d6
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491091"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Quickstart: Criar uma conta Azure Cosmos, base de dados, contentor e itens a partir do portal Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Portal do Azure](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Pode utilizar a Azure Cosmos DB para criar e consultar rapidamente bases de dados chave/valor, bases de dados de documentos e bases de dados de gráficos, que beneficiam da distribuição global e das capacidades de escala horizontal no núcleo da Azure Cosmos DB. 

Este quickstart demonstra como usar o portal Azure para criar uma conta AZure Cosmos [DB SQL API,](./introduction.md) criar uma base de dados de documentos e contentor, e adicionar dados ao recipiente. 

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição da Azure ou conta de teste gratuita da Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Aceda ao [portal do Azure](https://portal.azure.com/) para criar um conta do Azure Cosmos DB. Procure e selecione **Azure Cosmos DB**.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="O painel Bases de dados do portal do Azure":::

1. Selecione **Adicionar**.
1. Na página **Criar Conta do Azure Cosmos DB** , introduza as definições básicas da nova conta do Azure Cosmos DB. 

    |Definição|Valor|Descrição |
    |---|---|---|
    |Subscrição|Nome da subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos. |
    |Grupo de Recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **Criar novo** e, em seguida, introduza um nome exclusivo para o novo grupo de recursos. |
    |Nome da Conta|Um nome exclusivo|Introduza um nome para identificar a conta do Azure Cosmos. Uma vez que *documents.azure.com* é anexado ao nome que indicar para criar o URI, utilize um nome exclusivo.<br><br>O nome só pode conter letras minúsculas, números e o caráter de hífen (-). Tem de ter entre 3 e 31 carateres.|
    |API|O tipo de conta a criar|Selecione **Núcleo (SQL)** para criar uma base de dados de documentos e consultar com a sintaxe SQL. <br><br>A API determina o tipo de conta a criar. A Azure Cosmos DB fornece cinco APIs: Core (SQL) e MongoDB para dados de documentos, Gremlin para dados gráficos, Tabela Azure e Cassandra. De momento, deve criar uma conta separada para cada API. <br><br>[Saiba mais sobre a API SQL](introduction.md).|
    |Modo de capacidade|Produção provisida ou sem servidor|**Selecione Provisão para** criar uma conta no modo [de produção previsto.](set-throughput.md) Selecione **Serverless** para criar uma conta no modo [sem servidor.](serverless.md)|
    |Aplicar Desconto de Escalão Gratuito|Aplicar ou não aplicar|Com o nível livre Azure Cosmos DB, você receberá os primeiros 400 RU/s e 5 GB de armazenamento gratuitamente numa conta. Saiba mais sobre [o free tier](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Localização|A região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos utilizadores para lhes dar o acesso mais rápido aos dados.|
    |Tipo de Conta|Produção ou Não Produção|Selecione **Produção** se a conta for utilizada para uma carga de trabalho de produção. Selecione **Não-Produção** se a conta for utilizada para não produção, por exemplo, desenvolvimento, teste, QA ou encenação. Esta é uma definição de marcação de recursos Azure que afina a experiência portal mas não afeta a conta DB Azure Cosmos subjacente. Pode alterar este valor a qualquer momento.|
    |Redundância Geográfica|Ativar ou Desativar|Ativar ou desativar a distribuição global na sua conta, emparelhando a sua região com uma região de pares. Pode adicionar mais regiões à sua conta mais tarde.|
    |Escritas de várias regiões|Ativar ou Desativar|A capacidade de escrita multi-região permite-lhe tirar partido da produção prevista para as suas bases de dados e contentores em todo o mundo.|
    |Zonas de Disponibilidade|Ativar ou Desativar|Zonas de disponibilidade ajudam-no a melhorar ainda mais a disponibilidade e a resiliência da sua aplicação.|

> [!NOTE]
> Pode ter até uma conta DB Azure Cosmos de nível gratuito por subscrição Azure e deve optar pela criação da conta. Se não vir a opção de aplicar o desconto de nível livre, isto significa que outra conta na subscrição já foi ativada com nível gratuito.

> [!NOTE]
> As seguintes opções não estão disponíveis se selecionar **Serverless** como **o modo Capacidade** :
> - Aplicar Desconto de Escalão Gratuito
> - Georredundância
> - Escritas de várias regiões
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png" alt-text="A página da nova conta do Azure Cosmos DB":::

1. Selecione **Rever + criar**. Pode ignorar as secções **Rede** e **Etiquetas**.

1. Reveja as definições da conta e, em seguida, selecione **Criar**. A criação da conta demora alguns minutos. Aguarde até que a página do portal apresente **A implementação está concluída**. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="O painel de Notificações do portal do Azure":::

1. Selecione **Ir para recurso** para aceder à página da conta do Azure Cosmos DB. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="A página da conta do Azure Cosmos DB":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Adicione uma base de dados e um recipiente 

Pode utilizar o Data Explorer no portal Azure para criar uma base de dados e um recipiente. 

1.  Selecione Data **Explorer** a partir da navegação à esquerda na sua página de conta DB Azure Cosmos e, em seguida, selecione **Novo Recipiente**. 
    
    Pode ser necessário rolar para a direita para ver a janela **do Recipiente Adicionar.**
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="O Data Explorer no portal do Azure, painel Adicionar Contentor":::
    
1.  No painel de **recipiente Adicionar,** introduza as definições para o novo recipiente.
    
    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|ToDoList|*Insira o ToDoList* como o nome da nova base de dados. Os nomes da base de dados devem conter de 1 a 255 caracteres, não podendo conter `/, \\, #, ?` , ou um espaço de fuga. Consulte a opção **de produção da base de dados Provision,** que lhe permite partilhar o produto que está previsto na base de dados em todos os contentores da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a produção a 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar verticalmente o débito mais tarde.<br><br>**Nota:** Esta definição não está disponível ao criar um novo recipiente numa conta sem servidor.| 
    |**ID do Contentor**|Itens|*Insira os Itens* como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Chave de partição**| /categoria| A amostra descrita neste artigo *utiliza/categoria* como chave de partição.|

    
    Não adicione **chaves únicas** para este exemplo. As teclas únicas permitem adicionar uma camada de integridade de dados à base de dados, garantindo a singularidade de um ou mais valores por chave de partição. Para mais informações, consulte [as teclas Únicas em Azure Cosmos DB](unique-keys.md).
    
1.  Selecione **OK**. O Data Explorer apresenta a nova base de dados e o recipiente que criou.

## <a name="add-data-to-your-database"></a>Adicione dados à sua base de dados

Adicione dados à sua nova base de dados utilizando o Data Explorer.

1. No **Data Explorer,** expanda a base **de dados ToDoList** e expanda o recipiente **de Itens.** Em seguida, selecione **Itens** e, em seguida, selecione **Novo Item**. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Criar documentos novos no Data Explorer no portal do Azure":::
   
1. Adicione a seguinte estrutura ao documento no lado direito do painel **documento:**

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Selecione **Guardar**.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Copie em dados json e selecione Guardar no Data Explorer no portal Azure":::
   
1. Selecione **Novo Documento** novamente, e crie e guarde outro documento com um `id` exclusivo , e quaisquer outras propriedades e valores que pretenda. Os seus documentos podem ter qualquer estrutura, porque a Azure Cosmos DB não impõe qualquer esquema nos seus dados.

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Se pretender eliminar apenas a base de dados e utilizar a conta Azure Cosmos no futuro, pode eliminar a base de dados com os seguintes passos:

* Cheguei à sua conta do Azure Cosmos.
* Abra o **Data Explorer,** clique na base de dados que pretende eliminar e selecione **Eliminar Base de Dados**.
* Introduza o nome de ID/base de dados da base de dados para confirmar a operação de eliminação. 

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta DB Azure Cosmos, criar uma base de dados e um contentor utilizando o Data Explorer. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).