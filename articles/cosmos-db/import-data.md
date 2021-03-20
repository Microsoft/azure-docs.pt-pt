---
title: 'Tutorial: Ferramenta de migração de bases de dados para Azure Cosmos DB'
description: 'Tutorial: Saiba como usar as ferramentas de migração de dados DB do Azure Cosmos de código aberto para importar dados para a Azure Cosmos DB de várias fontes, incluindo mongoDB, SQL Server, armazenamento de mesa, Amazon DynamoDB, CSV e ficheiros JSON. Conversão de CSV para JSON.'
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: dech
ms.openlocfilehash: 1cee4d2ad1bc7f362a045a5991624ec43521b8d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341653"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Tutorial: Utilizar a ferramenta de Migração de dados para migrar os dados para o Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este tutorial fornece instruções sobre como utilizar a ferramenta de Migração de Dados do Azure Cosmos DB, que pode importar dados de várias origens para contentores e tabelas do Azure Cosmos. Pode importar a partir de ficheiros JSON, ficheiros CSV, SQL, MongoDB, Armazenamento de Tabelas do Azure, Amazon DynamoDB e até de coleções de APIs de SQL do Azure Cosmos DB. Migra esses dados para coleções e tabelas para serem utilizados com o Azure Cosmos DB. A ferramenta de Migração de Dados também pode ser utilizada ao migrar de uma coleção de uma partição individual para uma coleção de várias partições para a API de SQL.

> [!NOTE]
> A ferramenta Azure Cosmos DB Data Migration é uma ferramenta de código aberto projetada para pequenas migrações. Para maiores migrações, consulte o nosso [guia para ingerir dados.](cosmosdb-migrationchoices.md)

* **[SQL API](./introduction.md)** - Pode utilizar qualquer uma das opções de origem fornecidas na ferramenta migração de dados para importar dados em pequena escala. [Saiba mais sobre as opções de migração para importar dados em larga escala.](cosmosdb-migrationchoices.md)
* **[Tabela API](table-introduction.md)** - Pode utilizar a ferramenta migração de dados ou [a AzCopy](table-import.md#migrate-data-by-using-azcopy) para importar dados. Para obter mais informações, veja [Importar dados para utilização com a API de Tabela do Azure Cosmos DB](table-import.md).
* **[A API da Azure Cosmos DB para a MongoDB](mongodb-introduction.md)** - A ferramenta de migração de dados não suporta a API da Azure Cosmos DB para a MongoDB, quer como fonte, quer como alvo. Se quiser migrar os dados dentro ou fora das coleções em Azure Cosmos DB, consulte [como migrar os dados do MongoDB para uma base de dados cosmos com a API da Azure Cosmos DB para a MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) para obter instruções. Pode também utilizar a ferramenta de Migração de Dados para exportar dados do MongoDB para coleções de APIs de SQL do Azure Cosmos DB, para utilização com a API de SQL.
* **[Cassandra API](graph-introduction.md)** - A ferramenta de migração de dados não é uma ferramenta de importação suportada para contas da API cassandra. [Conheça as opções de migração para importar dados para a API de Cassandra](cosmosdb-migrationchoices.md#azure-cosmos-db-cassandra-api)
* **[Gremlin API](graph-introduction.md)** - A ferramenta de migração de dados não é uma ferramenta de importação suportada para contas da API gremlin neste momento. [Conheça as opções de migração para importar dados para a API gremlin](cosmosdb-migrationchoices.md#other-apis) 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a ferramenta de Migração de Dados
> * Importar dados de origens de dados diferentes
> * Exportar do Azure Cosmos DB para JSON

## <a name="prerequisites"></a><a id="Prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, certifique-se de que faz os seguintes passos:

* **Instale** [o Quadro Microsoft .NET 4.51](https://www.microsoft.com/download/developer-tools.aspx) ou superior.

* **Aumentar débito:** a duração da migração de dados depende da quantidade de débito que configurou para uma coleção individual ou um conjunto de coleções. Aumente o débito para migrações de dados maiores. Após concluir a migração, reduza o débito para reduzir os custos. Para obter mais informações sobre o aumento da produção no portal Azure, consulte os [níveis](performance-levels.md) de desempenho e [preços](https://azure.microsoft.com/pricing/details/cosmos-db/) em Azure Cosmos DB.

* **Criar recursos do Azure Cosmos DB:** antes de começar a migração de dados, crie previamente todas as suas coleções no portal do Azure. Para migrar para uma conta DB Azure Cosmos que tenha produção de nível de base de dados, forneça uma chave de partição quando criar os recipientes Azure Cosmos.

> [!IMPORTANT]
> Para se certificar de que a ferramenta de migração de dados utiliza a Segurança da Camada de Transporte (TLS) 1.2 ao ligar-se às suas contas Azure Cosmos, utilize a versão .NET Framework 4.7 ou siga as instruções encontradas [neste artigo](/dotnet/framework/network-programming/tls).

## <a name="overview"></a><a id="Overviewl"></a>Descrição geral

A ferramenta de Migração de Dados é uma solução open source que importa dados para o Azure Cosmos DB a partir de uma variedade de origens, incluindo:

* Ficheiros JSON
* MongoDB
* SQL Server
* Ficheiros CSV
* Armazenamento de Tabelas do Azure
* Amazon DynamoDB
* HBase
* Contentores do Azure Cosmos

Embora a ferramenta de importação inclua uma interface gráfica (dtui.exe), também pode ser controlada a partir da linha de comandos (dt.exe). Na verdade, há uma opção para o comando associado depois de configurar uma importação através da UI. Pode transformar dados de origem tabular, como ficheiros SQL Server ou CSV, para criar relações hierárquicas (subdocuments) durante a importação. Continue a ler para saber mais sobre as opções de origem, os comandos de exemplo para importar a partir de cada origem, as opções de destino e como visualizar os resultados da importação.

> [!NOTE]
> Só deve utilizar a ferramenta de migração Azure Cosmos DB para pequenas migrações. Para grandes migrações, consulte o nosso [guia para ingerir dados.](cosmosdb-migrationchoices.md)

## <a name="installation"></a><a id="Install"></a>Instalação

O código de origem da ferramenta de migração está disponível no GitHub [neste repositório](https://github.com/azure/azure-documentdb-datamigrationtool). Pode transferir e compilar a solução localmente ou [transferir um binário previamente compilado](https://aka.ms/csdmtool) e, em seguida, executar:

* **Dtui.exe**: versão de interface gráfica da ferramenta
* **Dt.exe**: versão de linha de comandos da ferramenta

## <a name="select-data-source"></a>Selecionar origem de dados

Assim que instalar a ferramenta, está na altura de importar os seus dados. Que tipo de dados pretende importar?

* [Ficheiros JSON](#JSON)
* [MongoDB](#MongoDB)
* [Ficheiros de Exportação do MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Ficheiros CSV](#CSV)
* [Armazenamento de tabelas do Azure](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Contentores do Azure Cosmos](#SQLSource)
* [HBase](#HBaseSource)
* [Importação em volume do Azure Cosmos DB](#SQLBulkTarget)
* [Importação de registos sequenciais do Azure Cosmos DB](#SQLSeqTarget)

## <a name="import-json-files"></a><a id="JSON"></a>Importar ficheiros JSON

A opção de importador de fontes de ficheiros JSON permite importar um ou mais ficheiros JSON de documento único ou ficheiros JSON que cada um tem uma série de documentos JSON. Ao adicionar pastas que tenham ficheiros JSON para importar, tem a opção de pesquisar novamente ficheiros em sub-dobradeiras.

:::image type="content" source="./media/import-data/jsonsource.png" alt-text="Captura de ecrã das opções de origem de ficheiro JSON - ferramentas de migração de bases de dados":::

A cadeia de ligação encontra-se no seguinte formato:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* O `<CosmosDB Endpoint>` ponto final URI. Pode obter este valor a partir do portal Azure. Navegue para a sua conta Azure Cosmos. Abra o **painel de visão** geral e copie o valor **URI.**
* A `<AccountKey>` chave principal é a "Password" ou a CHAVE **PRINCIPAL.** Pode obter este valor a partir do portal Azure. Navegue para a sua conta Azure Cosmos. Abra o painel de cordas ou **teclas** de **ligação** e copie o valor "Password" ou **"CHAVE PRIMÁRIA".**
* O `<CosmosDB Database>` nome da base de dados cosmosdb.

Exemplo: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Utilize o comando Verificar para garantir que a conta Cosmos DB especificada no campo de cordas de ligação pode ser acedida.

Seguem-se alguns exemplos de linha de comandos para importar ficheiros JSON:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a name="import-from-mongodb"></a><a id="MongoDB"></a>Importar do MongoDB

> [!IMPORTANT]
> Se estiver a importar para uma conta Cosmos configurada com a API da Azure Cosmos DB para a MongoDB, siga estas [instruções.](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)

Com a opção de importador de origem MongoDB, pode importar a partir de uma única coleção MongoDB, filtrar opcionalmente documentos usando uma consulta, e modificar a estrutura do documento usando uma projeção.  

:::image type="content" source="./media/import-data/mongodbsource.png" alt-text="Captura de ecrã das opções de origem do MongoDB":::

A cadeia de ligação está no formato MongoDB padrão:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do MongoDB especificada no campo de cadeia de ligação pode ser acedida.

Introduza o nome da coleção da qual os dados serão importados. Pode especificar ou fornecer um ficheiro para uma consulta, `{pop: {$gt:5000}}` como, por exemplo, ou uma projeção, `{loc:0}` como, por exemplo, para filtrar e moldar os dados que está a importar.

Seguem-se alguns exemplos de linha de comandos para importar do MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a name="import-mongodb-export-files"></a><a id="MongoDBExport"></a>Importar ficheiros de exportação do MongoDB

> [!IMPORTANT]
> Se você está importando para uma conta DB Azure Cosmos com apoio para a MongoDB, siga estas [instruções](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

A opção de importador de origem de ficheiros JSON de exportação do MongoDB permite importar um ou mais ficheiros JSON produzidos a partir do utilitário mongoexport.  

:::image type="content" source="./media/import-data/mongodbexportsource.png" alt-text="Captura de ecrã das opções de origem de exportação do MongoDB":::

Ao adicionar pastas que tenham ficheiros JSON de exportação de MongoDB para importação, tem a opção de pesquisar novamente ficheiros em sub-dobradres.

Eis um exemplo de linha de comandos para importação de ficheiros JSON de exportação do MongoDB:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a name="import-from-sql-server"></a><a id="SQL"></a>Importar do SQL Server

A opção de importador de origem de SQL permite importar a partir de uma base de dados individual do SQL Server e, opcionalmente, filtrar os registos para importar através de uma consulta. Além disso, pode modificar a estrutura de documento, especificando um separador de aninhamento (mais informações em breve).  

:::image type="content" source="./media/import-data/sqlexportsource.png" alt-text="Captura de ecrã das opções de origem de SQL - ferramentas de migração de bases de dados":::

O formato da cadeia de ligação é o formato de cadeia de ligação SQL padrão.

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do SQL Server especificada no campo de cadeia de ligação pode ser acedida.

A propriedade do separador de aninhamento é utilizada para criar relações hierárquicas (subdocumentos) durante a importação. Considere a seguinte consulta SQL:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

São devolvidos os seguintes resultados (parciais):

:::image type="content" source="./media/import-data/sqlqueryresults.png" alt-text="Captura de ecrã dos resultados da consulta SQL":::

Tenha em atenção os aliases como Address.AddressType e Address.Location.StateProvinceName. Ao especificar um separador de aninhamento de “.”, a ferramenta de importação cria os subdocumentos Address e Address.Location durante a importação. Eis um exemplo de um documento resultante do Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Seguem-se alguns exemplos de linha de comandos para importar do SQL Server:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a name="import-csv-files-and-convert-csv-to-json"></a><a id="CSV"></a>Importar ficheiros CSV e converter CSV para JSON

A opção de importador de origem de ficheiro CSV permite importar um ou mais ficheiros CSV. Ao adicionar pastas que tenham ficheiros CSV para importação, tem a opção de procurar novamente ficheiros em sub-dobradeiras.

:::image type="content" source="media/import-data/csvsource.png" alt-text="Captura de ecrã das opções de origem de CSV - CSV para JSON":::

Semelhante à origem de SQL, a propriedade do separador de aninhamento pode ser utilizada para criar relações hierárquicas (subdocumentos) durante a importação. Considere a seguinte linha de cabeçalho e linhas de dados em CSV:

:::image type="content" source="./media/import-data/csvsample.png" alt-text="Captura de ecrã dos registos de exemplo de CSV - CSV para JSON":::

Tenha em atenção os aliases como DomainInfo.Domain_Name e RedirectInfo.Redirecting. Ao especificar um separador de aninhamento de “.”, a ferramenta de importação irá criar os subdocumentos DomainInfo e RedirectInfo durante a importação. Eis um exemplo de um documento resultante do Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https: \/ /www.ACUS.GOV" }, "Agência Federal": "Conferência Administrativa dos Estados Unidos", "RedirectInfo": { "Redirecionamento": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

A ferramenta de importação tenta inferir informações de tipo para valores não citados em ficheiros CSV (os valores citados são sempre tratados como cordas).  Os tipos são identificados pela seguinte ordem: número, data e hora, booleano.  

Existem dois outros aspetos a ter em atenção na importação de CSV:

1. Por predefinição, os valores sem aspas sempre são cortados relativamente a separadores e espaços, enquanto os valores entre aspas são preservados tal como estão. Este comportamento pode ser substituído com a caixa de verificação Cortar valores entre aspas ou a /s.TrimQuoted opção da linha de comandos.
2. Por predefinição, um nulo sem aspas é tratado como um valor nulo. Este comportamento pode ser substituído (ou seja, tratar um nulo sem aspas como uma cadeia “nula”), com a caixa de verificação Tratar NULO sem aspas como cadeia ou a opção /s.NoUnquotedNulls da linha de comandos.

Eis um exemplo de linha de comandos para a importação de CSV:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a name="import-from-azure-table-storage"></a><a id="AzureTableSource"></a>Importar do Armazenamento de Tabelas do Azure

A opção de importador de origem de armazenamento de Tabelas do Azure permite importar a partir de uma tabela individual do armazenamento de Tabelas do Azure. Opcionalmente, pode filtrar as entidades da tabela a importar.

Pode obter dados de produção que foram importados do Azure Table Storage para as tabelas DB da Azure Cosmos para utilização com a Tabela API. Os dados importados também podem ser produzidos para recolhas e documentos para utilização com a API SQL. No entanto, a Tabela API só está disponível como alvo na utilidade da linha de comando. Não é possível exportar para a Tabela API utilizando a interface de utilizador da ferramenta migração de dados. Para obter mais informações, veja [Importar dados para utilização com a API de Tabela do Azure Cosmos DB](table-import.md).

:::image type="content" source="./media/import-data/azuretablesource.png" alt-text="Captura de ecrã das opções de origem do armazenamento de Tabelas do Azure":::

O formato da cadeia de ligação do armazenamento de Tabelas do Azure é:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância de armazenamento de Tabelas do Azure especificada no campo de cadeia de ligação pode ser acedida.

Introduza o nome da tabela do Azure da qual importar. Opcionalmente, pode especificar um [filtro](/visualstudio/azure/vs-azure-tools-table-designer-construct-filter-strings).

A opção de importador de origem do armazenamento de Tabelas do Azure inclui as seguintes opções adicionais:

1. Incluir Campos Internos
   1. Todos - incluir todos os campos internos (PartitionKey, RowKey e Carimbo de data/hora)
   2. Nenhum - excluir todos os campos internos
   3. RowKey - incluir apenas o campo RowKey
2. Selecionar Colunas
   1. Os filtros de armazenamento da mesa Azure não suportam projeções. Se pretender importar apenas propriedades específicas de entidade de Tabelas do Azure, adicione-as à lista Selecionar Colunas. Todas as outras propriedades de entidade são ignoradas.

Eis um exemplo de linha de comandos para importação do armazenamento de Tabelas do Azure:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a name="import-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Importar do Amazon DynamoDB

A opção de importador de fontes da Amazon DynamoDB permite-lhe importar a partir de uma única tabela Do Dínamo da Amazon. Pode filtrar opcionalmente as entidades a serem importadas. São fornecidos vários modelos para que configurar uma importação seja tão simples quanto possível.

:::image type="content" source="./media/import-data/dynamodbsource1.png" alt-text="Screenshot das opções de origem Do Dínamo da Amazon - ferramentas de migração de bases de dados.":::

:::image type="content" source="./media/import-data/dynamodbsource2.png" alt-text="Screenshot das opções de origem do Amazon DynamoDB com modelo - ferramentas de migração de bases de dados.":::

O formato da cadeia de ligação do Amazon DynamoDB é:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do Amazon DynamoDB especificada no campo de cadeia de ligação pode ser acedida.

Eis um exemplo de linha de comandos para importação do Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a name="import-from-azure-blob-storage"></a><a id="BlobImport"></a>Importar do armazenamento de Blobs do Azure

O ficheiro JSON, o ficheiro de exportação do MongoDB e as opções do importador de origem de ficheiros CSV permitem importar um ou mais ficheiros do armazenamento de Blobs do Azure. Depois de especificar um URL do contentor de Blob e a Chave da Conta, forneça uma expressão regular para selecionar os ficheiros a importar.

:::image type="content" source="./media/import-data/blobsource.png" alt-text="Captura de ecrã das opções de origem de ficheiros Blob":::

Eis um exemplo da linha de comandos para importar ficheiros JSON do armazenamento de Blobs do Azure:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a name="import-from-a-sql-api-collection"></a><a id="SQLSource"></a>Importar de uma coleção de APIs de SQL

A opção de importador de fontes da Azure Cosmos permite importar dados de um ou mais contentores da Azure Cosmos e filtrar opcionalmente documentos usando uma consulta.  

:::image type="content" source="./media/import-data/documentdbsource.png" alt-text="Captura de ecrã das opções de origem do Azure Cosmos DB":::

O formato da cadeia de ligação do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Pode recuperar a cadeia de conexão de conta Azure Cosmos DB a partir da página Keys do portal Azure, conforme descrito em [Como gerir uma conta DB Azure Cosmos](./how-to-manage-database-account.md). No entanto, o nome da base de dados deve ser anexado à cadeia de ligação no seguinte formato:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de ligação pode ser acedida.

Para importar de um único contentor Azure Cosmos, insira o nome da coleção para importar dados de. Para importar de mais de um contentor Azure Cosmos, forneça uma expressão regular para combinar com um ou mais nomes de coleção (por exemplo, coleção01 | coleção02 | coleção03). Pode especificar opcionalmente, ou fornecer um ficheiro para, uma consulta para filtrar e moldar os dados que está a importar.

> [!NOTE]
> Uma vez que o campo de recolha aceita expressões regulares, se você está importando de uma única coleção cujo nome tem caracteres de expressão regular, então esses caracteres devem ser escapados em conformidade.

A opção de importador de origem do Azure Cosmos DB inclui as seguintes opções avançadas:

1. Incluir Campos Internos: especifica se deve ou não incluir as propriedades do sistema de documentos do Azure Cosmos DB na exportação (por exemplo, _rid, _ts).
2. Número de Tentativas em Caso de Falha: especifica o número de vezes a tentar a ligação ao Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
3. Intervalo de Repetições: especifica o período de tempo de espera entre tentar estabelecer novamente a ligação ao Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
4. Modo de Ligação: especifica o modo de ligação a utilizar com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é mais amigável com a firewall, pois só utiliza a porta 443.

:::image type="content" source="./media/import-data/documentdbsourceoptions.png" alt-text="Captura de ecrã das opções avançadas de origem do Azure Cosmos DB":::

> [!TIP]
> A predefinição da ferramenta de importação é o modo de ligação de DirectTcp. Se ocorrerem problemas de firewall, mude para o modo de ligação de Gateway, pois só requer a porta 443.

Seguem-se alguns exemplos de linha de comandos para importação do Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite
```

> [!TIP]
> A Ferramenta de Importação de Dados do Azure Cosmos DB também suporta a importação de dados do [Emulador do Azure Cosmos DB](local-emulator.md). Ao importar dados de um emulador local, defina o ponto final para `https://localhost:<port>`.

## <a name="import-from-hbase"></a><a id="HBaseSource"></a>Importar do HBase

A opção de importador de origem do HBase permite importar dados de uma tabela do HBase e, opcionalmente, filtrar os dados. São fornecidos vários modelos para que configurar uma importação seja tão simples quanto possível.

:::image type="content" source="./media/import-data/hbasesource1.png" alt-text="Screenshot das opções de origem HBase.":::

:::image type="content" source="./media/import-data/hbasesource2.png" alt-text="Screenshot das opções de origem HBase com o menu contextual filter expandido.":::

O formato da cadeia de ligação do HBase Stargate é:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do HBase especificada no campo de cadeia de ligação pode ser acedida.

Eis um exemplo de linha de comandos para importação do HBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a name="import-to-the-sql-api-bulk-import"></a><a id="SQLBulkTarget"></a>Importar para a API de SQL (Importação em Volume)

O importador em Volume do Azure Cosmos DB permite importar de qualquer uma das opções de origem disponíveis, utilizando um procedimento armazenado do Azure Cosmos DB para eficiência. A ferramenta suporta a importação para um recipiente Azure Cosmos de uma única partição. Também suporta a importação de fragmentos, através do qual os dados são divididos em mais de um recipiente Azure Cosmos de uma única partição. Para obter mais informações sobre a criação de partições de dados, veja [Partitioning and scaling in Azure Cosmos DB](partitioning-overview.md) (Criar partições e dimensionar no Azure Cosmos DB). A ferramenta cria, executa e, em seguida, elimina o procedimento armazenado das coleções de destino.  

:::image type="content" source="./media/import-data/documentdbbulk.png" alt-text="Captura de ecrã das opções de volume do Azure Cosmos DB":::

O formato da cadeia de ligação do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

A cadeia de ligação da conta do Azure Cosmos DB pode ser obtida a partir da página Chaves do portal do Azure, conforme descrito em [Como gerir uma conta do Azure Cosmos DB](./how-to-manage-database-account.md); no entanto, o nome da base de dados tem de ser anexado à cadeia de ligação no seguinte formato:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de ligação pode ser acedida.

Para importar para uma única coleção, introduza o nome da coleção da qual importar os dados e clique no botão Adicionar. Para importar para mais de uma coleção, introduza cada nome de recolha individualmente ou utilize a seguinte sintaxe para especificar mais do que uma coleção: *collection_prefix*[índice inicial - índice final]. Ao especificar mais do que uma coleção utilizando a sintaxe acima mencionada, tenha em mente as seguintes orientações:

1. São suportados apenas padrões de nome de intervalo de números inteiros. Por exemplo, especificar a coleção [0-3] cria as seguintes coleções: collection0, collection1, collection2, collection3.
2. Pode utilizar uma sintaxe abreviada: collection[3] cria o mesmo conjunto de coleções mencionado no passo 1.
3. Pode ser fornecida mais do que uma substituição. Por exemplo, collection[0-1] [0-9] gera 20 nomes de coleção com zeros à esquerda (collection01, ..02, ..03).

Depois de os nomes das coleções terem sido especificados, escolha o débito pretendido das coleções (de 400 RUs a 10 000 RUs). Para um melhor desempenho de importação, escolha um maior débito. Para obter mais informações sobre os níveis de desempenho, veja [Performance levels in Azure Cosmos DB](performance-levels.md) (Níveis de desempenho no Azure Cosmos DB).

> [!NOTE]
> A definição de débito de desempenho aplica-se apenas à criação de coleções. Se a coleção especificada já existir, a sua produção não será modificada.

Quando importa para mais de uma coleção, a ferramenta de importação suporta o fragmento à base de haxixe. Neste cenário, especifique a propriedade do documento que pretende utilizar como Chave de Partição. (Se a chave de partição ficar em branco, os documentos são fragmentos aleatoriamente através das coleções-alvo.)

Pode especificar opcionalmente qual o campo na fonte de importação que deve ser usado como propriedade de ID documento Azure Cosmos durante a importação. Se os documentos não têm esta propriedade, então a ferramenta de importação gera um GUID como o valor da propriedade ID.

Estão disponíveis várias opções avançadas durante a importação. Em primeiro lugar, embora a ferramenta inclua um procedimento armazenado predefinido de importação em volume (BulkInsert.js), pode optar por especificar o seu próprio procedimento armazenado de importação:

 :::image type="content" source="./media/import-data/bulkinsertsp.png" alt-text="Captura de ecrã da opção de sproc de inserção em volume do Azure Cosmos DB":::

Além disso, ao importar os tipos de data (por exemplo, do SQL Server ou MongoDB), pode escolher entre três opções de importação:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Captura de ecrã das opções de importação de data e hora do Azure Cosmos DB":::

* Cadeia: manter como um valor de cadeia
* Época: manter como um valor de número de Época
* Ambos: manter os valores de cadeia e de número de Época. Esta opção cria um subdocumento, por exemplo: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

O importador em Volume do Azure Cosmos DB inclui as seguintes opções avançadas adicionais:

1. Tamanho do Lote: a predefinição da ferramenta é um tamanho de lote de 50.  Se os documentos a importar forem grandes, considere reduzir o tamanho do lote. Por outro lado, se os documentos a importar forem pequenos, considere aumentar o tamanho do lote.
2. Tamanho Máximo de Script (bytes): a predefinição da ferramenta é um tamanho máximo de script de 512 KB.
3. Desativar a Geração automática de identificação: Se cada documento a ser importado tiver um campo de ID, então a seleção desta opção pode aumentar o desempenho. Documentos que faltam um campo de identificação único não são importados.
4. Atualizar documentos existentes: A ferramenta não substitui os documentos existentes por conflitos de identificação. A seleção desta opção permite a sobreescrita de documentos existentes com IDs correspondentes. Esta funcionalidade é útil para as migrações de dados agendadas que atualizam os documentos existentes.
5. Número de Retrações no Insucesso: Especifica com que frequência voltar a tentar a ligação ao Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção da conectividade da rede).
6. Intervalo de Repetições: especifica o período de tempo de espera entre tentar estabelecer novamente a ligação ao Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
7. Modo de Ligação: especifica o modo de ligação a utilizar com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é mais amigável com a firewall, pois só utiliza a porta 443.

:::image type="content" source="./media/import-data/docdbbulkoptions.png" alt-text="Captura de ecrã das opções avançadas de importação em volume do Azure Cosmos DB":::

> [!TIP]
> A predefinição da ferramenta de importação é o modo de ligação de DirectTcp. Se ocorrerem problemas de firewall, mude para o modo de ligação de Gateway, pois só requer a porta 443.

## <a name="import-to-the-sql-api-sequential-record-import"></a><a id="SQLSeqTarget"></a>Importar para a API de SQL (Importação de Registos Sequenciais)

O importador de registos sequenciais Azure Cosmos DB permite-lhe importar de uma opção de origem disponível numa base record-by-record. Poderá escolher esta opção se estiver a importar para uma coleção existente que atingiu a quota de procedimentos armazenados. A ferramenta suporta a importação para um único (único-partição e multipartições) recipiente Azure Cosmos. Também suporta a importação de fragmentos, através do qual os dados são divididos em mais de uma única partição ou multi-partição do recipiente Azure Cosmos. Para obter mais informações sobre a criação de partições de dados, veja [Partitioning and scaling in Azure Cosmos DB](partitioning-overview.md) (Criar partições e dimensionar no Azure Cosmos DB).

:::image type="content" source="./media/import-data/documentdbsequential.png" alt-text="Captura de ecrã das opções de importação de registos sequenciais do Azure Cosmos DB":::

O formato da cadeia de ligação do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Pode recuperar a cadeia de ligação para a conta DB do Azure Cosmos a partir da página Keys do portal Azure, conforme descrito em [Como gerir uma conta DB Azure Cosmos](./how-to-manage-database-account.md). No entanto, o nome da base de dados deve ser anexado à cadeia de ligação no seguinte formato:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de ligação pode ser acedida.

Para importar para uma única coleção, insira o nome da coleção para importar dados e, em seguida, clique no botão Adicionar. Para importar para mais de uma coleção, insira cada nome de coleção individualmente. Também pode utilizar a seguinte sintaxe para especificar mais do que uma coleção: *collection_prefix*[índice inicial - índice final]. Ao especificar mais do que uma coleção através da sintaxe acima mencionada, tenha em mente as seguintes orientações:

1. São suportados apenas padrões de nome de intervalo de números inteiros. Por exemplo, especificar a coleção [0-3] cria as seguintes coleções: collection0, collection1, collection2, collection3.
2. Pode utilizar uma sintaxe abreviada: collection[3] cria o mesmo conjunto de coleções mencionado no passo 1.
3. Pode ser fornecida mais do que uma substituição. Por exemplo, a coleção [0-1], [0-9] cria 20 nomes de coleção com zeros à esquerda (collection01, ..02, ..03).

Depois de os nomes das coleções terem sido especificados, escolha o débito pretendido das coleções (de 400 RUs a 250 000 RUs). Para um melhor desempenho de importação, escolha um maior débito. Para obter mais informações sobre os níveis de desempenho, veja [Performance levels in Azure Cosmos DB](performance-levels.md) (Níveis de desempenho no Azure Cosmos DB). Qualquer importação para coleções com um débito >10 000 RUs requer uma chave de partição. Se optar por ter mais de 250 000 RUs, terá de fazer um pedido no portal para que a sua conta seja aumentada.

> [!NOTE]
> A definição de débito aplica-se apenas à coleção ou criação de base de dados. Se a coleção especificada já existir, a sua produção não será modificada.

Ao importar para mais de uma coleção, a ferramenta de importação suporta o fragmento à base de haxixe. Neste cenário, especifique a propriedade do documento que pretende utilizar como Chave de Partição. (Se a chave de partição ficar em branco, os documentos são fragmentos aleatoriamente através das coleções-alvo.)

Pode especificar opcionalmente qual o campo na fonte de importação que deve ser usado como propriedade de ID documento Azure Cosmos durante a importação. (Se os documentos não tiverem esta propriedade, então a ferramenta de importação gera um GUID como o valor da propriedade ID.)

Estão disponíveis várias opções avançadas durante a importação. Em primeiro lugar, ao importar os tipos de data (por exemplo, do SQL Server ou MongoDB), pode escolher entre três opções de importação:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Captura de ecrã das opções de importação de data e hora do Azure Cosmos DB":::

* Cadeia: manter como um valor de cadeia
* Época: manter como um valor de número de Época
* Ambos: manter os valores de cadeia e de número de Época. Esta opção cria um subdocumento, por exemplo: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

O importador de registos sequenciais do Azure Cosmos DB inclui as seguintes opções avançadas adicionais:

1. Número de Pedidos Paralelos: a predefinição da ferramenta é dois pedidos paralelos. Se os documentos a importar forem pequenos, considere aumentar o número de pedidos paralelos. Se este número aumentar muito, a importação pode apresentar limite de velocidade.
2. Desativar a Geração automática de identificação: Se cada documento a ser importado tiver um campo de ID, então a seleção desta opção pode aumentar o desempenho. Documentos que faltam um campo de identificação único não são importados.
3. Atualizar documentos existentes: A ferramenta não substitui os documentos existentes por conflitos de identificação. A seleção desta opção permite a sobreescrita de documentos existentes com IDs correspondentes. Esta funcionalidade é útil para as migrações de dados agendadas que atualizam os documentos existentes.
4. Número de Retrações no Insucesso: Especifica com que frequência voltar a tentar a ligação ao Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção da conectividade da rede).
5. Intervalo de retenção: Especifica quanto tempo demorará entre voltar a tentar a ligação ao Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção da conectividade da rede).
6. Modo de Ligação: especifica o modo de ligação a utilizar com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é mais amigável com a firewall, pois só utiliza a porta 443.

:::image type="content" source="./media/import-data/documentdbsequentialoptions.png" alt-text="Captura de ecrã das opções avançadas de importação de registos sequenciais do Azure Cosmos DB":::

> [!TIP]
> A predefinição da ferramenta de importação é o modo de ligação de DirectTcp. Se ocorrerem problemas de firewall, mude para o modo de ligação de Gateway, pois só requer a porta 443.

## <a name="specify-an-indexing-policy"></a><a id="IndexingPolicy"></a>Especificar uma política de indexação

Ao permitir que a ferramenta de migração crie coleções de API de SQL do Azure Cosmos DB durante a importação, pode especificar a política de indexação das coleções. Na secção de opções avançadas de importação, nas opções de Volume do Azure Cosmos DB e registos Sequenciais do Azure Cosmos DB, navegue para a secção Política de Indexação.

:::image type="content" source="./media/import-data/indexingpolicy1.png" alt-text="Screenshot de Azure Cosmos DB Indexing Policy opções avançadas.":::

Ao utilizar a opção avançada Política de Indexação, pode selecionar um ficheiro de política de indexação, introduzir manualmente uma política de indexação ou selecionar de um conjunto de modelos predefinidos (ao clicar na caixa de texto da política de indexação).

Os modelos de política que a ferramenta fornece são:

* Predefinição. Esta política é melhor quando se realizam consultas de igualdade contra cordas. Também funciona se utilizar pedidos DE ORDEM BY, gama e consultas de igualdade para números. Esta política tem uma tolerância de armazenamento de índice inferior ao Intervalo.
* Intervalo. Esta política é melhor quando se utiliza pedidos DE ORDEM BY, perguntas de alcance e igualdade tanto em números como em cordas. Esta política tem uma tolerância de armazenamento de índice maior do que a Predefinição ou o Hash.

:::image type="content" source="./media/import-data/indexingpolicy2.png" alt-text="Screenshot de Azure Cosmos DB Indexing Policy opções avançadas especificando informações-alvo.":::

> [!NOTE]
> Se não especificar uma política de indexação, então a política por defeito é aplicada. Para obter mais informações sobre as políticas de indexação, veja [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="export-to-json-file"></a>Exportar para ficheiro JSON

O exportador Azure Cosmos DB JSON permite-lhe exportar qualquer uma das opções de origem disponíveis para um ficheiro JSON que tenha uma série de documentos JSON. A ferramenta trata da exportação para si. Em alternativa, pode optar por ver o comando de migração resultante e executar o comando por si mesmo. O ficheiro JSON resultante pode ser armazenado localmente ou no armazenamento de Blobs do Azure.

:::image type="content" source="./media/import-data/jsontarget.png" alt-text="Captura de ecrã da opção de exportação de ficheiro JSON local do Azure Cosmos DB":::

:::image type="content" source="./media/import-data/jsontarget2.png" alt-text="Captura de ecrã da opção de exportação de armazenamento de Blobs JSON do Azure do Azure Cosmos DB":::

Pode optar opcionalmente por prettificar o JSON resultante. Esta ação aumentará a dimensão do documento resultante, tornando o conteúdo mais legível.

* Exportação padrão JSON

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Exportação de JSON pretificado

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Veja a seguir um exemplo da linha de comandos para exportar o ficheiro JSON para o Armazenamento de blobs do Azure:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Configuração avançada

No ecrã de configuração Avançada, especifique a localização do ficheiro de registo no qual gostaria de escrever quaisquer erros. As seguintes regras aplicam-se a esta página:

1. Se um nome de ficheiro não for fornecido, todos os erros são devolvidos na página Resultados.
2. Se um nome de ficheiro for fornecido sem um diretório, o ficheiro é criado (ou substituído) no diretório de ambiente atual.
3. Se selecionar um ficheiro existente, então o ficheiro é substituído, não há opção de apêndice.
4. Em seguida, escolha se pretende registar todas as mensagens, só as de erros críticos ou as de inexistência de erros. Por fim, decida a frequência com a mensagem de transferência no ecrã é atualizada com o respetivo progresso.

   :::image type="content" source="./media/import-data/AdvancedConfiguration.png" alt-text="Captura de ecrã do ecrã Configuração avançada":::

## <a name="confirm-import-settings-and-view-command-line"></a>Confirmar definições de importação e ver linha de comando

1. Depois de especificar as informações de origem, informações de destino e configuração avançada, reveja o resumo da migração e veja ou copie o comando de migração resultante, se assim o desejar. (Copiar o comando é útil para automatizar operações de importação.)

    :::image type="content" source="./media/import-data/summary.png" alt-text="Screenshot do ecrã sumário.":::

    :::image type="content" source="./media/import-data/summarycommand.png" alt-text="Screenshot do ecrã sumário com pré-visualização da linha de comando.":::

2. Quando estiver satisfeito com as opções de origem e destino, clique em **Importar**. O tempo decorrido, a contagem de itens transferidos e as informações das falhas (se não tiver fornecido um nome de ficheiro na configuração Avançada) são atualizados enquanto a importação está em curso. Depois de concluída, pode exportar os resultados (por exemplo, para lidar com eventuais falhas de importação).

    :::image type="content" source="./media/import-data/viewresults.png" alt-text="Screenshot da opção de exportação da Azure Cosmos DB JSON.":::

3. Também pode iniciar uma nova importação repondo todos os valores ou mantendo as definições existentes. (Por exemplo, pode optar por manter a informação de ligação, a origem e a escolha do alvo, e muito mais.)

    :::image type="content" source="./media/import-data/newimport.png" alt-text="Screenshot da opção de exportação da Azure Cosmos DB JSON com a caixa de diálogo de confirmação New Import.":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez as seguintes tarefas:

> [!div class="checklist"]
> * Instalou a ferramenta de Migração de Dados
> * Importou dados de origens de dados diferentes
> * Exportou do Azure Cosmos DB para JSON

Agora pode avançar para o próximo tutorial e ficar a saber como consultar dados com o Azure Cosmos DB.

> [!div class="nextstepaction"]
>[Como consultar dados?](../cosmos-db/tutorial-query-sql-api.md)
