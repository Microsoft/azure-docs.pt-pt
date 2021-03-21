---
title: Configure pontos finais privados para a loja analítica Azure Cosmos DB.
description: Saiba como configurar pontos finais privados geridos para a loja analítica Azure Cosmos DB para restringir o acesso à rede.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 2f15b397fbceb9e097d94080ba03fba50a96ed06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048510"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Configurar pontos finais privados para a loja analítica Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Neste artigo, você vai aprender como configurar pontos finais privados geridos para a loja analítica Azure Cosmos DB. Se estiver a utilizar a loja transacional, consulte [os pontos finais privados para o artigo da loja transacional.](how-to-configure-private-endpoints.md) Utilizando pontos finais privados geridos, pode restringir o acesso à rede da loja analítica Azure Cosmos DB à rede virtual gerida pela Azure Synapse. Os pontos finais privados geridos estabelecem uma ligação privada à sua loja analítica.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>Ativar o ponto final privado para a loja analítica

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Crie um espaço de trabalho Azure Synapse Analytics com uma rede virtual gerida

[Criar um espaço de trabalho em Azure Synapse Analytics com exfiltração de dados ativado.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Com [a proteção de exfiltração de dados,](../synapse-analytics/security/workspace-data-exfiltration-protection.md)pode garantir que utilizadores maliciosos não podem copiar ou transferir dados dos seus recursos Azure para locais fora do âmbito da sua organização.

As seguintes restrições de acesso são aplicáveis quando a proteção contra a exfiltração de dados é ligada para um espaço de trabalho Azure Synapse Analytics:

* Se estiver a utilizar o Azure Spark para a Azure Synapse Analytics, o acesso só é permitido aos pontos finais privados geridos aprovados para a loja analítica Azure Cosmos DB.

* Se estiver a utilizar piscinas SQL sem servidor sinapse, pode consultar qualquer conta DB da Azure Cosmos utilizando o Azure Synapse Link. No entanto, os pedidos de escrita que [criam tabelas externas como select (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) só são permitidos aos pontos finais privados de gestão aprovados na rede virtual do espaço de trabalho.

> [!NOTE]
> Não é possível alterar a configuração de rede virtual gerida e de exfiltração de dados após a criação do espaço de trabalho.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Adicione um ponto final privado gerido para a loja analítica Azure Cosmos DB

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. A partir do portal Azure, navegue até ao seu espaço de trabalho Synapse Analytics e abra o **painel de visão** geral.

1. Lançar o Synapse Studio navegando para obter o painel **"Getting Started"** e selecione **Open** under **Open Synapse Studio**.

1. No Synapse Studio, abra o **separador Manage.**

1. Navegue para **pontos finais privados geridos** e selecione **Novos**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Crie um novo ponto final privado para a loja analítica." border="true":::

1. Selecione **Azure Cosmos DB (SQL API)** tipo de conta > **Continue**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Selecione Azure Cosmos DB SQL API para criar um ponto final privado." border="true":::

1. Preencha o **novo formulário de ponto final privado gerido** com os seguintes detalhes:

   * **Nome** - Nome para o seu ponto final privado gerido. Este nome não pode ser atualizado depois de ser criado.
   * **Descrição** - Forneça uma descrição amigável para identificar o seu ponto final privado.
   * **Subscrição Azure** - Selecione uma conta DB Azure Cosmos da lista de contas disponíveis nas suas subscrições Azure.
   * **Nome da conta DB Azure Cosmos** - Selecione uma conta DB Azure Cosmos existente do tipo SQL ou MongoDB.
   * **Sub-resouce do alvo** - Selecione uma das seguintes opções: **Analítica**: Se pretender adicionar o ponto final privado para a loja analítica Azure Cosmos DB.
     **Sql** (ou **MongoDB):** Se pretender adicionar OLTP ou ponto final de conta transacional.

   > [!NOTE]
   > Pode adicionar tanto a loja transacional como os pontos finais privados da loja analítica à mesma conta DB da Azure Cosmos num espaço de trabalho Azure Synapse Analytics. Se quiser apenas fazer consultas analíticas, só pode querer mapear o ponto final privado analítico.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Escolha analítico para o subresource alvo." border="true":::

1. Depois de criar, vá ao nome de ponto final privado e **selecione Obter aprovações no portal Azure**.

1. Navegue na sua conta DB Azure Cosmos, selecione o ponto final privado e **selecione Aprovar**.

1. Volte ao espaço de trabalho Synapse Analytics e clique em **Refresh** no painel **de pontos finais privados geridos.** Verifique se o ponto final privado está no estado **aprovado.**

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Verifique se o ponto final privado está aprovado." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Use a faísca Apache para a Azure Synapse Analytics

Se criou um espaço de trabalho Azure Synapse com proteção de exfiltração de dados ligado, o acesso de saída das contas Sinapse Spark a Azure Cosmos DB será bloqueado, por padrão. Além disso, se o Azure Cosmos DB já tiver um ponto final privado existente, a Synapse Spark será impedida de aceder ao mesmo.

Para permitir o acesso aos dados do Azure Cosmos DB:

* Se estiver a utilizar o Azure Synapse Link para consultar os dados do Azure Cosmos DB, adicione um ponto final privado **analítico** gerido para a conta DB Azure Cosmos.

* Se estiver a utilizar as gravações/leituras//ou leituras de streaming de lotes para a loja transacional, adicione um ponto final privado gerido *sql* ou *MongoDB* para a conta DB Azure Cosmos. Além disso, também deve definir *a ligaçãoMode* a *Gateway,* como mostrado no seguinte corte de código:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Usando piscinas SQL sem servidor sinapse

As piscinas SQL sem servidor sinapse utilizam capacidades multi-inquilinos que não são implantadas em rede virtual gerida. Se a conta DB da Azure Cosmos tiver um ponto final privado existente, a piscina SQL sem servidor synapse será bloqueada de aceder à conta, devido a verificações de isolamento de rede na conta DB do Azure Cosmos.

Para configurar o isolamento da rede para esta conta a partir de um espaço de trabalho synapse:

1. Permitir que o espaço de trabalho da Sinaapse aceda à conta DB do Azure Cosmos especificando `NetworkAclBypassResourceId` a definição na conta.

   **Utilizar o PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Utilizar a CLI do Azure**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > A conta DB da Azure Cosmos e o espaço de trabalho Azure Synapse Analytics devem estar sob o mesmo inquilino do Azure Ative Directory (AD).

2. Agora pode aceder à conta a partir de piscinas SQL sem servidor, utilizando consultas T-SQL sobre Azure Synapse Link. No entanto, para garantir o isolamento da rede para os dados na loja analítica, deve adicionar um ponto final privado gerido **analítico** para esta conta. Caso contrário, os dados na loja analítica não serão bloqueados do acesso público.

> [!IMPORTANT]
> Se estiver a utilizar o Azure Synapse Link e precisar de isolamento de rede para os seus dados na loja analítica, deve mapear a conta DB do Azure Cosmos no espaço de trabalho da Synapse utilizando o ponto final privado gerido **pela Analytical.**

## <a name="next-steps"></a>Passos seguintes

* Começar com [consulta de loja analítica com Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Começar com [consulta de loja analítica com piscinas SQL sem servidor Azure Synapse](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
