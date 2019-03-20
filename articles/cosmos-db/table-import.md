---
title: Migrar dados existentes para a conta da API de tabela no Azure Cosmos DB
description: Saiba como migrar ou importar no local ou na cloud de dados para a conta de API de tabela do Azure no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e709aa25a8fa0a8ff6c94bf2e813151e5ecbae65
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006238"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migre os dados para a conta da API de Tabela do Azure Cosmos DB

Este tutorial dá instruções sobre como importar dados para utilização com a [API de Tabelas](table-introduction.md) do Azure Cosmos DB. Se tiver dados armazenados no Armazenamento de Tabelas do Azure, poderá utilizar a Ferramenta de Migração de Dados ou AzCopy para importar os seus dados para a API de Tabela do Azure Cosmos DB. Se tiver dados armazenados numa conta API de Tabelas do Azure Cosmos DB (pré-visualização), terá de utilizar a ferramenta de Migração de Dados para migrar os dados. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Importar dados com a ferramenta de Migração de Dados
> * Importar dados com AzCopy
> * Migrar da API de Tabelas (pré-visualização) para API de Tabelas 

## <a name="prerequisites"></a>Pré-requisitos

* **Aumente o débito:** A duração da sua migração de dados depende da quantidade de débito que configura para um contentor individual ou um conjunto de contentores. Aumente o débito para migrações de dados maiores. Após concluir a migração, reduza o débito para reduzir os custos. Para obter mais informações sobre como aumentar o débito no portal do Azure, veja os níveis de Desempenho e escalões de preço do Azure Cosmos DB.

* **Crie recursos do Azure Cosmos DB:** Antes de começar a migração de dados, crie previamente todas as suas tabelas do portal do Azure. Se estiver a migrar para uma conta do Azure Cosmos DB com débito ao nível da base de dados, confirme que proporciona uma chave de partição quando criar as tabelas do Azure Cosmos DB.

## <a name="data-migration-tool"></a>Ferramenta de Migração de Dados

A ferramenta de Migração de Dados do Azure Cosmos DB de linha de comandos (dt.exe) pode ser utilizada para importar os dados de Armazenamento de Tabelas do Azure existente para uma conta GA da API de Tabelas ou para migrar dados de uma conta API de Tabelas (pré-visualização) para uma conta GA de API de Tabelas. Atualmente, não são suportadas outras origens. A ferramenta de Migração de Dado baseada em IU (dtui.exe) não é atualmente suportada para contas API de Tabelas. 

Para efetuar uma migração dos dados da tabela, faça as seguintes tarefas:

1. Transfira a ferramenta de migração do [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Execute `dt.exe` utilizando os argumentos da linha de comandos do seu cenário. `dt.exe` executa um comando no seguinte formato:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
```

As opções do comando são:

    /ErrorLog: Optional. Name of the CSV file to redirect data transfer failures
    /OverwriteErrorLog: Optional. Overwrite error log file
    /ProgressUpdateInterval: Optional, default is 00:00:01. Time interval to refresh on-screen data transfer progress
    /ErrorDetails: Optional, default is None. Specifies that detailed error information should be displayed for the following errors: None, Critical, All
    /EnableCosmosTableLog: Optional. Direct the log to a cosmos table account. If set, this defaults to destination account connection string unless /CosmosTableLogConnectionString is also provided. This is useful if multiple instances of DT are being run simultaneously.
    /CosmosTableLogConnectionString: Optional. ConnectionString to direct the log to a remote cosmos table account. 

### <a name="command-line-source-settings"></a>Definições de origem da linha de comandos

Utilize as seguintes opções de origem ao definir o Armazenamento de Tabelas do Azure ou pré-visualização da API de Tabelas como a origem da migração.

    /s:AzureTable: Reads data from Azure Table storage
    /s.ConnectionString: Connection string for the table endpoint. This can be retrieved from the Azure portal
    /s.LocationMode: Optional, default is PrimaryOnly. Specifies which location mode to use when connecting to Azure Table storage: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
    /s.Table: Name of the Azure Table
    /s.InternalFields: Set to All for table migration as RowKey and PartitionKey are required for import.
    /s.Filter: Optional. Filter string to apply
    /s.Projection: Optional. List of columns to select

Para obter a cadeia de ligação da origem ao importar do Armazenamento de Tabelas do Azure, abra o portal do Azure e clique em **Contas de armazenamento** > **Conta** > **Chaves de acesso** e, em seguida, utilize o botão Copiar para copiar a **Cadeia de ligação**.

![Captura de ecrã das opções de origem do HBase](./media/table-import/storage-table-access-key.png)

Para obter a cadeia da ligação de origem ao importar de uma conta API de Tabelas do Azure Cosmos DB (pré-visualização), abra o portal do Azure, clique em **Azure Cosmos DB** > **Conta** > **Cadeia de ligação** e utilize o botão Copiar para copiar a **Cadeia de ligação**.

![Captura de ecrã das opções de origem do HBase](./media/table-import/cosmos-connection-string.png)

[Comando de Armazenamento de Tabelas do Azure](#azure-table-storage)

[Comando API de Tabelas do Azure Cosmos DB (pré-visualização)](#table-api-preview)

### <a name="command-line-target-settings"></a>Definições de destino da linha de comandos

Utilize as seguintes opções de destino ao definir a API de Tabelas do Azure Cosmos DB como o destino da migração.

    /t:TableAPIBulk: Uploads data into Azure CosmosDB Table in batches
    /t.ConnectionString: Connection string for the table endpoint
    /t.TableName: Specifies the name of the table to write to
    /t.Overwrite: Optional, default is false. Specifies if existing values should be overwritten
    /t.MaxInputBufferSize: Optional, default is 1GB. Approximate estimate of input bytes to buffer before flushing data to sink
    /t.Throughput: Optional, service defaults if not specified. Specifies throughput to configure for table
    /t.MaxBatchSize: Optional, default is 2MB. Specify the batch size in bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Comando de exemplo: Origem é o armazenamento de tabelas do Azure

Eis um exemplo de linha de comandos a mostrar como importar do Armazenamento de Tabelas do Azure para a API de Tabelas:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```
<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Comando de exemplo: Origem é a API de tabela do Azure Cosmos DB (pré-visualização)

Eis um exemplo de linha de comandos para importar da pré-visualização de API de Tabelas para GA de API de Tabelas:

```
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrar dados utilizando o AzCopy

Utilizar o utilitário da linha de comandos AzCopy é a outra opção para migrar dados do Armazenamento de Tabelas do Azure para a API de Tabelas do Azure Cosmos DB. Para utilizar AzCopy, primeiro exporta os dados conforme descrito em [Exportar dados do Armazenamento de Tabelas](../storage/common/storage-use-azcopy.md#export-data-from-table-storage) e, em seguida, importa os dados para o Azure Cosmos DB conforme descrito em [API de Tabelas do Azure Cosmos DB](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

Ao efetuar a importação para o Azure Cosmos DB, consulte o seguinte exemplo. Tenha em atenção que o valor /Dest utiliza cosmosdb, não core.

Exemplo de comando de importação:

```
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrar da API de Tabelas (pré-visualização) para a API de Tabelas

> [!WARNING]
> Se pretender usufruir imediatamente dos benefícios das tabelas geralmente disponíveis, migre as tabelas de pré-visualização existentes conforme especificado nesta secção. Caso contrário, iremos efetuar migrações automáticas para os clientes de pré-visualização existentes nas próximas semanas. No entanto, tenha em atenção que as tabelas de pré-visualização migradas automaticamente terão determinadas restrições que as tabelas criadas de novo não terão.
> 

A API de Tabelas está agora disponível globalmente (GA). Há diferenças entre a pré-visualização e as versões GA das tabelas no código que é executado na cloud como no código que é executado no cliente. Portanto, não é aconselhável tentar combinar um cliente SDK de pré-visualização com uma conta API de Tabelas GA e vice-versa. Os clientes de pré-visualização de API de Tabelas que pretendam continuar a utilizar as tabelas existentes, mas num ambiente de produção, necessitam de migrar da pré-visualização para o ambiente GA ou aguardar pela migração automática. Se aguardar pela migração automática, será notificado sobre as restrições nas tabelas migradas. Depois da migração, poderá criar novas tabelas na conta existente sem restrições (só as tabelas migradas terão restrições).

Para migrar da API de Tabelas (pré-visualização) para API de Tabelas disponíveis globalmente:

1. Crie uma nova conta do Azure Cosmos DB e defina o tipo de API como Tabelas do Azure conforme descrito em [Criar uma conta de base de dados](create-table-dotnet.md#create-a-database-account).

2. Altere os clientes para utilização de uma versão GA de [SDK de API de Tabelas](table-sdk-dotnet.md).

3. Migre os dados de cliente das tabelas de pré-visualização para tabelas GA utilizando a ferramenta de Migração de Dados. As instruções sobre a utilização da ferramenta de migração de dados para este fim estão descritas em [ferramenta de Migração de Dados](#data-migration-tool). 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar dados com a ferramenta de Migração de Dados
> * Importar dados com AzCopy
> * Migrar da API de Tabelas (pré-visualização) para a API de Tabelas

Agora pode avançar para o próximo tutorial e ficar a saber como consultar dados com a API de Tabelas do Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar dados?](../cosmos-db/tutorial-query-table.md)
