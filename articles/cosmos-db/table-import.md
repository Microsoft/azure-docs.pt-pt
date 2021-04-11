---
title: Migrar os dados existentes para uma conta API de tabela em Azure Cosmos DB
description: Saiba como migrar ou importar dados no local ou em nuvem para uma conta API da Tabela Azure em Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443343"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Migrar os seus dados para uma conta API de Tabela DB da Azure Cosmos
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Este tutorial fornece instruções sobre a importação de dados para uso com a API da Tabela DB [AZure](table-introduction.md)Cosmos . Se tiver dados armazenados no Azure Table Storage, pode utilizar a ferramenta de migração de dados ou a AzCopy para importar os seus dados para a API da Tabela DB AZure Cosmos. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com AzCopy

## <a name="prerequisites"></a>Pré-requisitos

* **Aumento da produção:** A duração da migração dos seus dados depende da quantidade de produção que criou para um recipiente individual ou um conjunto de contentores. Aumente o débito para migrações de dados maiores. Após concluir a migração, reduza o débito para reduzir os custos.

* **Criar recursos DB da Azure Cosmos:** Antes de começar a migrar os dados, crie todas as suas tabelas a partir do portal Azure. Se estiver a migrar para uma conta DB Azure Cosmos que tenha uma produção ao nível da base de dados, certifique-se de fornecer uma chave de partição quando criar as tabelas DB do Azure Cosmos.

## <a name="data-migration-tool"></a>Ferramenta de migração de dados

Pode utilizar a ferramenta de migração de dados de linha de comando (dt.exe) na Azure Cosmos DB para importar os seus dados de armazenamento de mesa azure existentes para uma conta API de tabela. 

Para migrar dados de tabela:

1. Transfira a ferramenta de migração do [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Executada `dt.exe` usando os argumentos da linha de comando para o seu cenário. `dt.exe` executa um comando no seguinte formato:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

As opções suportadas para este comando são:

* **/ErrorLog:** É opcional. Nome do ficheiro CSV para redirecionar falhas de transferência de dados.
* **/OverwriteErrorLog:** É opcional. Substitua o ficheiro de registo de erros.
* **/ProgressUpdateInterval:** Opcional, o padrão é `00:00:01` . O intervalo de tempo para atualizar o progresso da transferência de dados no ecrã.
* **/ErrorDetails:** Opcional, o padrão é `None` . Especifica que devem ser apresentadas informações detalhadas sobre erros para os seguintes erros: `None` `Critical` , ou `All` .
* **/EnableCosmosTableLog:** É opcional. Direcione o registo para uma conta de tabela Azure Cosmos DB. Se for definido, este incumprimento é o fio de ligação da conta de destino, a menos que `/CosmosTableLogConnectionString` também seja fornecido. Isto é útil se várias instâncias da ferramenta estiverem a ser executadas simultaneamente.
* **/CosmosTableLogConnectionString:** É opcional. O fio de ligação para direcionar o log para uma conta de tabela Azure Cosmos DB remota.

### <a name="command-line-source-settings"></a>Definições de origem da linha de comandos

Utilize as seguintes opções de origem quando definir O Armazenamento da Mesa Azure como a fonte da migração.

* **/s:AzureTable:** Lê dados do Armazenamento de Mesa.
* **/s.ConnectionString:** Fio de ligação para o ponto final da mesa. Pode recuperar isto do portal Azure.
* **/s.LocationMode:** Opcional, o padrão é `PrimaryOnly` . Especifica qual o modo de localização a utilizar ao ligar ao Armazenamento de Mesa: `PrimaryOnly` , , . . `PrimaryThenSecondary` `SecondaryOnly` `SecondaryThenPrimary` .
* **/s.Tabela:** Nome da mesa Azure.
* **/s.Campos Internos:** Definido para a `All` migração de mesa, porque `RowKey` e são `PartitionKey` necessários para importação.
* **/s.Filtro:** É opcional. Fio de filtro para aplicar.
* **/s.Projeção:** É opcional. Lista de colunas a selecionar,

Para recuperar a cadeia de ligação de origem quando importar do Armazenamento de Mesa, abra o portal Azure. Selecione **as contas de armazenamento** As  >    >  **teclas de acesso à** conta e copie a cadeia de **ligação**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Screenshot que mostra as contas de Armazenamento > Opções de chaves de acesso > conta e realça o ícone da cópia.":::

### <a name="command-line-target-settings"></a>Definições de destino da linha de comandos

Utilize as seguintes opções-alvo quando definir a API da Tabela DB Azure Cosmos como o alvo da migração.

* **/t:TableAPIBulk:** Envia dados para a Azure Cosmos DB Table API em lotes.
* **/t.ConnectionString:** A corda de ligação para o ponto final da mesa.
* **/t.TableName:** Especifica o nome da tabela para escrever.
* **/t.Overwrite:** Opcional, o padrão é `false` . Especifica se os valores existentes devem ser substituídos.
* **/t.MaxInputBufferSize:** Opcional, o padrão é `1GB` . Estimativa aproximada dos bytes de entrada para tampão antes de lavar os dados para afundar.
* **/t.Produção:** Opcional, o serviço predefinido se não for especificado. Especifica a produção para configurar para a tabela.
* **/t.MaxBatchSize:** Opcional, o padrão é `2MB` . Especifique o tamanho do lote em bytes.

### <a name="sample-command-source-is-table-storage"></a>Comando da amostra: Fonte é armazenamento de mesa

Aqui está uma amostra de linha de comando que mostra como importar do Armazenamento de Mesa para a Tabela API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrar dados utilizando o AzCopy

Também pode utilizar o utilitário de linha de comando AzCopy para migrar dados do Armazenamento de Mesa para a API da Tabela DB AZure Cosmos. Para utilizar o AzCopy, exporta primeiro os seus dados como descrito nos [dados de exportação do Armazenamento de Mesa.](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) Em seguida, importa os dados para Azure Cosmos DB, conforme descrito na [Azure Cosmos DB Table API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Consulte a seguinte amostra quando estiver a importar para a Azure Cosmos DB. Note que o `/Dest` valor `cosmosdb` utiliza, não `core` .

Exemplo de comando de importação:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Passos seguintes

Saiba como consultar os dados utilizando a API da Tabela DB Azure Cosmos. 

> [!div class="nextstepaction"]
>[Como consultar dados?](../cosmos-db/tutorial-query-table.md)




