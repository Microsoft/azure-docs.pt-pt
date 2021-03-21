---
title: Migrar os dados existentes para a conta API de tabela em Azure Cosmos DB
description: Saiba como migrar ou importar dados no local ou em nuvem para a conta AZure Table API em Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096085"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Migre os dados para a conta da API de Tabela do Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Este tutorial fornece instruções sobre a importação de dados para uso com a API da Tabela DB [AZure](table-introduction.md)Cosmos . Se tiver dados armazenados no Armazenamento de Tabelas do Azure, poderá utilizar a Ferramenta de Migração de Dados ou AzCopy para importar os seus dados para a API de Tabela do Azure Cosmos DB. Se tiver dados armazenados numa conta API de Tabelas do Azure Cosmos DB (pré-visualização), terá de utilizar a ferramenta de Migração de Dados para migrar os dados. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Importar dados com a ferramenta de Migração de Dados
> * Importar dados com AzCopy
> * Migrar da API de Tabelas (pré-visualização) para API de Tabelas 

## <a name="prerequisites"></a>Pré-requisitos

* **Aumento da produção:** A duração da migração dos seus dados depende da quantidade de produção que criou para um recipiente individual ou um conjunto de contentores. Aumente o débito para migrações de dados maiores. Após concluir a migração, reduza o débito para reduzir os custos. Para obter mais informações sobre como aumentar o débito no portal do Azure, veja os níveis de Desempenho e escalões de preço do Azure Cosmos DB.

* **Criar recursos do Azure Cosmos DB:** antes de começar a migração de dados, crie previamente todas as suas tabelas no portal do Azure. Se estiver a migrar para uma conta do Azure Cosmos DB com débito ao nível da base de dados, confirme que proporciona uma chave de partição quando criar as tabelas do Azure Cosmos DB.

## <a name="data-migration-tool"></a>Ferramenta de Migração de Dados

A ferramenta de Migração de Dados do Azure Cosmos DB de linha de comandos (dt.exe) pode ser utilizada para importar os dados de Armazenamento de Tabelas do Azure existente para uma conta GA da API de Tabelas ou para migrar dados de uma conta API de Tabelas (pré-visualização) para uma conta GA de API de Tabelas. Atualmente, não são suportadas outras origens. A ferramenta de Migração de Dado baseada em IU (dtui.exe) não é atualmente suportada para contas API de Tabelas. 

Para efetuar uma migração dos dados da tabela, faça as seguintes tarefas:

1. Transfira a ferramenta de migração do [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Execute `dt.exe` utilizando os argumentos da linha de comandos do seu cenário. `dt.exe` executa um comando no seguinte formato:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

As opções suportadas para este comando são:

* **/ErrorLog:** É opcional. Nome do ficheiro CSV para redirecionar falhas de transferência de dados
* **/OverwriteErrorLog:** É opcional. Ficheiro de registo de erro de substituição
* **/ProgressUpdateInterval:** Opcional, o padrão é 00:00:01. Intervalo de tempo para atualizar o progresso da transferência de dados no ecrã
* **/ErrorDetails:** Opcional, o padrão é nenhum. Especifica que informações detalhadas de erro devem ser exibidas para os seguintes erros: Nenhum, Crítico, Todos
* **/EnableCosmosTableLog:** É opcional. Direcione o log para uma conta de mesa cosmos. Se for definido, isto predefine a cadeia de ligação da conta de destino a menos que /CosmosTableLogConnectionString também seja fornecido. Isto é útil se várias instâncias de DT estiverem sendo executadas simultaneamente.
* **/CosmosTableLogConnectionString:** É opcional. ConnectionString para direcionar o registo para uma conta de tabela cosmos remota.

### <a name="command-line-source-settings"></a>Definições de origem da linha de comandos

Utilize as seguintes opções de origem ao definir o Armazenamento de Tabelas do Azure ou pré-visualização da API de Tabelas como a origem da migração.

* **/s:AzureTable:** Lê dados do armazenamento da Tabela Azure
* **/s.ConnectionString:** Fio de ligação para o ponto final da mesa. Isto pode ser recuperado do portal Azure
* **/s.LocationMode:** Opcional, o padrão é PrimárioOnly. Especifica qual o modo de localização a utilizar ao ligar ao armazenamento da tabela Azure: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
* **/s.Tabela:** Nome da Tabela Azure
* **/s.Campos Internos:** Definido para All para migração de mesa como RowKey e PartitionKey são necessários para importação.
* **/s.Filtro:** É opcional. Cadeia de filtro para aplicar
* **/s.Projeção:** É opcional. Lista de colunas para selecionar

Para recuperar a cadeia de ligação de origem ao importar do armazenamento da tabela Azure, abra o portal Azure e clique nas **teclas de** Acesso à conta de armazenamento  >    >  e, em seguida, utilize o botão de cópia para copiar a **cadeia De ligação**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Screenshot que mostra contas de Armazenamento > Opções de > De acesso de conta e realça o botão de cópia.":::

Para recuperar a cadeia de ligação de origem ao importar de uma conta API de tabela API (pré-visualização) da Tabela Azure Cosmos, abra o portal Azure, clique na Cadeia de Ligação da Conta **Azure Cosmos**  >    >   DB e utilize o botão de cópia para copiar a **Cadeia de Ligação**.

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Captura de ecrã das opções de origem do HBase":::

[Comando de Armazenamento de Tabelas do Azure](#azure-table-storage)

[Comando API de Tabelas do Azure Cosmos DB (pré-visualização)](#table-api-preview)

### <a name="command-line-target-settings"></a>Definições de destino da linha de comandos

Utilize as seguintes opções de destino ao definir a API de Tabelas do Azure Cosmos DB como o destino da migração.

* **/t:TableAPIBulk:** Envia dados para a tabela Azure CosmosDB em lotes
* **/t.ConnectionString:** Fio de ligação para o ponto final da mesa
* **/t.TableName:** Especifica o nome da tabela para escrever
* **/t.Overwrite:** Opcional, o padrão é falso. Especifica se os valores existentes devem ser substituídos
* **/t.MaxInputBufferSize:** Opcional, o padrão é de 1GB. Estimativa aproximada dos bytes de entrada para tampão antes de lavar dados para afundar
* **/t.Produção:** Opcional, o serviço predefinido se não for especificado. Especifica a produção para configurar para a tabela
* **/t.MaxBatchSize:** Opcional, o padrão é de 2MB. Especificar o tamanho do lote em bytes

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Comando de exemplo: a origem é o Armazenamento de Tabelas do Azure

Eis um exemplo de linha de comandos a mostrar como importar do Armazenamento de Tabelas do Azure para a API de Tabelas:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Comando de exemplo: a origem é uma API de Tabelas do Azure Cosmos DB (pré-visualização)

Eis um exemplo de linha de comandos para importar da pré-visualização de API de Tabelas para GA de API de Tabelas:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migrar dados utilizando o AzCopy

Utilizar o utilitário da linha de comandos AzCopy é a outra opção para migrar dados do Armazenamento de Tabelas do Azure para a API de Tabelas do Azure Cosmos DB. Para utilizar AzCopy, primeiro exporta os dados conforme descrito em [Exportar dados do Armazenamento de Tabelas](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) e, em seguida, importa os dados para o Azure Cosmos DB conforme descrito em [API de Tabelas do Azure Cosmos DB](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Ao efetuar a importação para o Azure Cosmos DB, consulte o seguinte exemplo. Tenha em atenção que o valor /Dest utiliza cosmosdb, não core.

Exemplo de comando de importação:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrar da API de Tabelas (pré-visualização) para a API de Tabelas

> [!WARNING]
> Se pretender usufruir imediatamente dos benefícios das tabelas geralmente disponíveis, migre as tabelas de pré-visualização existentes conforme especificado nesta secção. Caso contrário, iremos efetuar migrações automáticas para os clientes de pré-visualização existentes nas próximas semanas. No entanto, tenha em atenção que as tabelas de pré-visualização migradas automaticamente terão determinadas restrições que as tabelas criadas de novo não terão.

A API de Tabelas está agora disponível globalmente (GA). Há diferenças entre a pré-visualização e as versões GA das tabelas no código que é executado na cloud como no código que é executado no cliente. Portanto, não é aconselhável tentar combinar um cliente SDK de pré-visualização com uma conta API de Tabelas GA e vice-versa. Os clientes de pré-visualização de API de Tabelas que pretendam continuar a utilizar as tabelas existentes, mas num ambiente de produção, necessitam de migrar da pré-visualização para o ambiente GA ou aguardar pela migração automática. Se aguardar pela migração automática, será notificado sobre as restrições nas tabelas migradas. Depois da migração, poderá criar novas tabelas na conta existente sem restrições (só as tabelas migradas terão restrições).

Para migrar da API de Tabelas (pré-visualização) para API de Tabelas disponíveis globalmente:

1. Crie uma nova conta do Azure Cosmos DB e defina o tipo de API como Tabelas do Azure conforme descrito em [Criar uma conta de base de dados](create-table-dotnet.md#create-a-database-account).

2. Altere os clientes para utilização de uma versão GA de [SDK de API de Tabelas](table-sdk-dotnet.md).

3. Migre os dados de cliente das tabelas de pré-visualização para tabelas GA utilizando a ferramenta de Migração de Dados. As instruções sobre a utilização da ferramenta de migração de dados para este fim estão descritas em [ferramenta de Migração de Dados](#data-migration-tool). 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar dados com a ferramenta de Migração de Dados
> * Importar dados com AzCopy
> * Migrar da API de Tabelas (pré-visualização) para a API de Tabelas

Agora pode avançar para o próximo tutorial e ficar a saber como consultar dados com a API de Tabelas do Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar dados?](../cosmos-db/tutorial-query-table.md)