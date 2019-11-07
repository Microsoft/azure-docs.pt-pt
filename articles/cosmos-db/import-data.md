---
title: 'Tutorial: ferramenta de migração de banco de dados para Azure Cosmos DB'
description: 'Tutorial: saiba como usar as ferramentas de migração de dados de software livre Azure Cosmos DB para importar dados para o Azure Cosmos DB de várias fontes, incluindo MongoDB, SQL Server, armazenamento de tabelas, Amazon DynamoDB, CSV e arquivos JSON. Conversão de CSV para JSON.'
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: dech
ms.openlocfilehash: 1d25a2c9a3fda48c2f7de01563e01dd0c7de7762
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721152"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Tutorial: usar a ferramenta de migração de dados para migrar seus dados para Azure Cosmos DB

Este tutorial fornece instruções sobre como usar a ferramenta de migração de dados Azure Cosmos DB, que pode importar dados de várias fontes para contêineres e tabelas do cosmos do Azure. Pode importar a partir de ficheiros JSON, ficheiros CSV, SQL, MongoDB, Armazenamento de Tabelas do Azure, Amazon DynamoDB e até de coleções de APIs de SQL do Azure Cosmos DB. Você migra esses dados para coleções e tabelas para uso com Azure Cosmos DB. A ferramenta de Migração de Dados também pode ser utilizada ao migrar de uma coleção de uma partição individual para uma coleção de várias partições para a API de SQL.

Que API vai ser utilizada com o Azure Cosmos DB?

* **[API de SQL](documentdb-introduction.md)** - pode utilizar qualquer uma das opções de origem fornecidas na ferramenta de Migração de Dados para importar dados.
* **[API de Tabela](table-introduction.md)** - pode utilizar a ferramenta de Migração de Dados ou AzCopy para importar dados. Para obter mais informações, veja [Importar dados para utilização com a API de Tabela do Azure Cosmos DB](table-import.md).
* **[API do Azure Cosmos DB para MongoDB](mongodb-introduction.md)** -a ferramenta de migração de dados não dá suporte atualmente à api do Azure Cosmos DB para o MongoDB como uma origem ou como um destino. Se você quiser migrar os dados para dentro ou para fora das coleções no Azure Cosmos DB, consulte [como migrar dados do MongoDB para um cosmos Database com a API do Azure Cosmos DB para MongoDB](mongodb-migrate.md) para obter instruções. Pode também utilizar a ferramenta de Migração de Dados para exportar dados do MongoDB para coleções de APIs de SQL do Azure Cosmos DB, para utilização com a API de SQL.
* **[API do Gremlin](graph-introduction.md)** – a ferramenta de migração de dados não é uma ferramenta de importação com suporte para contas de API do Gremlin no momento.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Instalar a ferramenta de Migração de Dados
> * Importar dados de origens de dados diferentes
> * Exportar do Azure Cosmos DB para JSON

## <a id="Prerequisites"></a>Pré-requisitos

Antes de seguir as instruções neste artigo, certifique-se de executar as seguintes etapas:

* **Instale** o [Microsoft .NET Framework 4,51](https://www.microsoft.com/download/developer-tools.aspx) ou superior.

* **Aumentar débito:** a duração da migração de dados depende da quantidade de débito que configurou para uma coleção individual ou um conjunto de coleções. Aumente o débito para migrações de dados maiores. Após concluir a migração, reduza o débito para reduzir os custos. Para obter mais informações sobre como aumentar a taxa de transferência na portal do Azure, consulte [níveis de desempenho](performance-levels.md) e [tipos de preço](https://azure.microsoft.com/pricing/details/cosmos-db/) no Azure Cosmos DB.

* **Criar recursos do Azure Cosmos DB:** antes de começar a migração de dados, crie previamente todas as suas coleções no portal do Azure. Para migrar para uma conta de Azure Cosmos DB com taxa de transferência no nível do banco de dados, forneça uma chave de partição ao criar os contêineres Cosmos do Azure.

## <a id="Overviewl"></a>Descrição Geral

A ferramenta de Migração de Dados é uma solução open source que importa dados para o Azure Cosmos DB a partir de uma variedade de origens, incluindo:

* Ficheiros JSON
* MongoDB
* SQL Server
* Ficheiros CSV
* Armazenamento de Tabelas do Azure
* Amazon DynamoDB
* HBase
* Contêineres de Cosmos do Azure

Embora a ferramenta de importação inclua uma interface gráfica (dtui.exe), também pode ser controlada a partir da linha de comandos (dt.exe). Na verdade, há uma opção para gerar o comando associado depois de configurar uma importação por meio da interface do usuário. Você pode transformar dados de origem tabulares, como arquivos SQL Server ou CSV, para criar relações hierárquicas (subdocumentos) durante a importação. Continue a ler para saber mais sobre as opções de origem, os comandos de exemplo para importar a partir de cada origem, as opções de destino e como visualizar os resultados da importação.

## <a id="Install"></a>Instalação

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
* [Contêineres de Cosmos do Azure](#SQLSource)
* [HBase](#HBaseSource)
* [Importação em volume do Azure Cosmos DB](#SQLBulkTarget)
* [Importação de registos sequenciais do Azure Cosmos DB](#SQLSeqTarget)

## <a id="JSON"></a>Importar ficheiros JSON

A opção de importador de origem de arquivo JSON permite importar um ou mais arquivos JSON de documento único ou arquivos JSON que têm uma matriz de documentos JSON. Ao adicionar pastas que têm arquivos JSON a serem importados, você tem a opção de Pesquisar recursivamente arquivos em subpastas.

![Captura de ecrã das opções de origem de ficheiro JSON - ferramentas de migração de bases de dados](./media/import-data/jsonsource.png)

A cadeia de conexão está no seguinte formato:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* O `<CosmosDB Endpoint>` é o URI do ponto de extremidade. Você pode obter esse valor do portal do Azure. Navegue até sua conta do Azure Cosmos. Abra o painel **visão geral** e copie o valor do **URI** .
* O `<AccountKey>` é a "senha" ou a **chave primária**. Você pode obter esse valor do portal do Azure. Navegue até sua conta do Azure Cosmos. Abra o painel **cadeias de conexão** ou **chaves** e copie o valor de "senha" ou **chave primária** .
* O `<CosmosDB Database>` é o nome do banco de dados CosmosDB.

Exemplo: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Use o comando VERIFY para garantir que a conta de Cosmos DB especificada no campo de cadeia de conexão possa ser acessada.

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

## <a id="MongoDB"></a>Importar do MongoDB

> [!IMPORTANT]
> Se você estiver importando para uma conta do cosmos configurada com a API do Azure Cosmos DB para MongoDB, siga estas [instruções](mongodb-migrate.md).

Com a opção de importador de origem do MongoDB, você pode importar de uma única coleção do MongoDB, opcionalmente filtrar documentos usando uma consulta e modificar a estrutura do documento usando uma projeção.  

![Captura de ecrã das opções de origem do MongoDB](./media/import-data/mongodbsource.png)

A cadeia de ligação está no formato MongoDB padrão:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do MongoDB especificada no campo de cadeia de ligação pode ser acedida.

Introduza o nome da coleção da qual os dados serão importados. Opcionalmente, você pode especificar ou fornecer um arquivo para uma consulta, como `{pop: {$gt:5000}}`, ou uma projeção, como `{loc:0}`, para filtrar e formatar os dados que você está importando.

Seguem-se alguns exemplos de linha de comandos para importar do MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a id="MongoDBExport"></a>Importar ficheiros de exportação do MongoDB

> [!IMPORTANT]
> Se você estiver importando para uma conta de Azure Cosmos DB com suporte para MongoDB, siga estas [instruções](mongodb-migrate.md).

A opção de importador de origem de ficheiros JSON de exportação do MongoDB permite importar um ou mais ficheiros JSON produzidos a partir do utilitário mongoexport.  

![Captura de ecrã das opções de origem de exportação do MongoDB](./media/import-data/mongodbexportsource.png)

Ao adicionar pastas que têm arquivos JSON de exportação do MongoDB para importação, você tem a opção de Pesquisar recursivamente arquivos em subpastas.

Eis um exemplo de linha de comandos para importação de ficheiros JSON de exportação do MongoDB:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a id="SQL"></a>Importar do SQL Server

A opção de importador de origem de SQL permite importar a partir de uma base de dados individual do SQL Server e, opcionalmente, filtrar os registos para importar através de uma consulta. Além disso, pode modificar a estrutura de documento, especificando um separador de aninhamento (mais informações em breve).  

![Captura de ecrã das opções de origem de SQL - ferramentas de migração de bases de dados](./media/import-data/sqlexportsource.png)

O formato da cadeia de ligação é o formato de cadeia de ligação SQL padrão.

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do SQL Server especificada no campo de cadeia de ligação pode ser acedida.

A propriedade do separador de aninhamento é utilizada para criar relações hierárquicas (subdocumentos) durante a importação. Considere a seguinte consulta SQL:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

São devolvidos os seguintes resultados (parciais):

![Captura de ecrã dos resultados da consulta SQL](./media/import-data/sqlqueryresults.png)

Tenha em atenção os aliases como Address.AddressType e Address.Location.StateProvinceName. Ao especificar um separador de aninhamento de “.”, a ferramenta de importação cria os subdocumentos Address e Address.Location durante a importação. Eis um exemplo de um documento resultante do Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Seguem-se alguns exemplos de linha de comandos para importar do SQL Server:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a id="CSV"></a>Importar ficheiros CSV e converter CSV para JSON

A opção de importador de origem de ficheiro CSV permite importar um ou mais ficheiros CSV. Ao adicionar pastas que têm arquivos CSV para importação, você tem a opção de Pesquisar recursivamente arquivos em subpastas.

![Captura de ecrã das opções de origem de CSV - CSV para JSON](media/import-data/csvsource.png)

Semelhante à origem de SQL, a propriedade do separador de aninhamento pode ser utilizada para criar relações hierárquicas (subdocumentos) durante a importação. Considere a seguinte linha de cabeçalho e linhas de dados em CSV:

![Captura de ecrã dos registos de exemplo de CSV - CSV para JSON](./media/import-data/csvsample.png)

Tenha em atenção os aliases como DomainInfo.Domain_Name e RedirectInfo.Redirecting. Ao especificar um separador de aninhamento de “.”, a ferramenta de importação irá criar os subdocumentos DomainInfo e RedirectInfo durante a importação. Eis um exemplo de um documento resultante do Azure Cosmos DB:

*{"DomainInfo": {"Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https:\//www.ACUS.GOV"}, "Federal Agency": "Conferência administrativa do Estados Unidos", "RedirectInfo": {"redirecionando": "0", "Redirect_Destination": ""}, "ID": "9cc565c5-EBCD-1c03-ebd3-cc3e2ecd814d"}*

A ferramenta de importação tenta inferir informações de tipo para valores sem aspas em arquivos CSV (os valores entre aspas são sempre tratados como cadeias de caracteres).  Os tipos são identificados pela seguinte ordem: número, data e hora, booleano.  

Existem dois outros aspetos a ter em atenção na importação de CSV:

1. Por predefinição, os valores sem aspas sempre são cortados relativamente a separadores e espaços, enquanto os valores entre aspas são preservados tal como estão. Este comportamento pode ser substituído com a caixa de verificação Cortar valores entre aspas ou a /s.TrimQuoted opção da linha de comandos.
2. Por predefinição, um nulo sem aspas é tratado como um valor nulo. Este comportamento pode ser substituído (ou seja, tratar um nulo sem aspas como uma cadeia “nula”), com a caixa de verificação Tratar NULO sem aspas como cadeia ou a opção /s.NoUnquotedNulls da linha de comandos.

Eis um exemplo de linha de comandos para a importação de CSV:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a id="AzureTableSource"></a>Importar do Armazenamento de Tabelas do Azure

A opção de importador de origem de armazenamento de Tabelas do Azure permite importar a partir de uma tabela individual do armazenamento de Tabelas do Azure. Opcionalmente, pode filtrar as entidades da tabela a importar.

Você pode gerar dados de saída que foram importados do armazenamento de tabelas do Azure para Azure Cosmos DB tabelas e entidades para uso com o API de Tabela. Os dados importados também podem ser saída para coleções e documentos para uso com a API do SQL. No entanto, API de Tabela só está disponível como um destino no utilitário de linha de comando. Não é possível exportar para API de Tabela usando a interface do usuário da ferramenta de migração de dados. Para obter mais informações, veja [Importar dados para utilização com a API de Tabela do Azure Cosmos DB](table-import.md).

![Captura de ecrã das opções de origem do armazenamento de Tabelas do Azure](./media/import-data/azuretablesource.png)

O formato da cadeia de ligação do armazenamento de Tabelas do Azure é:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância de armazenamento de Tabelas do Azure especificada no campo de cadeia de ligação pode ser acedida.

Introduza o nome da tabela do Azure da qual importar. Opcionalmente, pode especificar um [filtro](../vs-azure-tools-table-designer-construct-filter-strings.md).

A opção de importador de origem do armazenamento de Tabelas do Azure inclui as seguintes opções adicionais:

1. Incluir Campos Internos
   1. Todos - incluir todos os campos internos (PartitionKey, RowKey e Carimbo de data/hora)
   2. Nenhum - excluir todos os campos internos
   3. RowKey - incluir apenas o campo RowKey
2. Selecionar Colunas
   1. Os filtros de armazenamento de tabelas do Azure não dão suporte a projeções. Se pretender importar apenas propriedades específicas de entidade de Tabelas do Azure, adicione-as à lista Selecionar Colunas. Todas as outras propriedades de entidade são ignoradas.

Eis um exemplo de linha de comandos para importação do armazenamento de Tabelas do Azure:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a id="DynamoDBSource"></a>Importar do Amazon DynamoDB

A opção de importador de origem do Amazon DynamoDB permite que você importe de uma única tabela do Amazon DynamoDB. Opcionalmente, ele pode filtrar as entidades a serem importadas. São fornecidos vários modelos para que configurar uma importação seja tão simples quanto possível.

![Captura de ecrã das opções de origem do DynamoDB - ferramentas de migração de bases de dados](./media/import-data/dynamodbsource1.png)

![Captura de ecrã das opções de origem do DynamoDB - ferramentas de migração de bases de dados](./media/import-data/dynamodbsource2.png)

O formato da cadeia de ligação do Amazon DynamoDB é:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do Amazon DynamoDB especificada no campo de cadeia de ligação pode ser acedida.

Eis um exemplo de linha de comandos para importação do Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a id="BlobImport"></a>Importar do armazenamento de Blobs do Azure

O ficheiro JSON, o ficheiro de exportação do MongoDB e as opções do importador de origem de ficheiros CSV permitem importar um ou mais ficheiros do armazenamento de Blobs do Azure. Depois de especificar um URL do contentor de Blob e a Chave da Conta, forneça uma expressão regular para selecionar os ficheiros a importar.

![Captura de ecrã das opções de origem de ficheiros Blob](./media/import-data/blobsource.png)

Eis um exemplo da linha de comandos para importar ficheiros JSON do armazenamento de Blobs do Azure:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a id="SQLSource"></a>Importar de uma coleção de APIs de SQL

A opção de importador de origem Azure Cosmos DB permite que você importe dados de um ou mais contêineres de Cosmos do Azure e, opcionalmente, filtre documentos usando uma consulta.  

![Captura de ecrã das opções de origem do Azure Cosmos DB](./media/import-data/documentdbsource.png)

O formato da cadeia de ligação do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Você pode recuperar a cadeia de conexão da conta Azure Cosmos DB da página chaves do portal do Azure, conforme descrito em [como gerenciar uma conta de Azure Cosmos DB](manage-account.md). No entanto, o nome do banco de dados precisa ser anexado à cadeia de conexão no seguinte formato:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de ligação pode ser acedida.

Para importar de um único contêiner Cosmos do Azure, insira o nome da coleção da qual importar dados. Para importar de mais de um contêiner Cosmos do Azure, forneça uma expressão regular para corresponder a um ou mais nomes de coleção (por exemplo, collection01 | collection02 | collection03). Opcionalmente, você pode especificar ou fornecer um arquivo para, uma consulta para filtrar e formatar os dados que você está importando.

> [!NOTE]
> Como o campo de coleção aceita expressões regulares, se você estiver importando de uma única coleção cujo nome tem caracteres de expressão regular, esses caracteres deverão ter um escape de acordo.

A opção de importador de origem do Azure Cosmos DB inclui as seguintes opções avançadas:

1. Incluir Campos Internos: especifica se deve ou não incluir as propriedades do sistema de documentos do Azure Cosmos DB na exportação (por exemplo, _rid, _ts).
2. Número de Tentativas em Caso de Falha: especifica o número de vezes a tentar a ligação ao Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
3. Intervalo de Repetições: especifica o período de tempo de espera entre tentar estabelecer novamente a ligação ao Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
4. Modo de Ligação: especifica o modo de ligação a utilizar com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é mais amigável com a firewall, pois só utiliza a porta 443.

![Captura de ecrã das opções avançadas de origem do Azure Cosmos DB](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> A predefinição da ferramenta de importação é o modo de ligação de DirectTcp. Se ocorrerem problemas de firewall, mude para o modo de ligação de Gateway, pois só requer a porta 443.

Seguem-se alguns exemplos de linha de comandos para importação do Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500
```

> [!TIP]
> A Ferramenta de Importação de Dados do Azure Cosmos DB também suporta a importação de dados do [Emulador do Azure Cosmos DB](local-emulator.md). Ao importar dados de um emulador local, defina o ponto final para `https://localhost:<port>`.

## <a id="HBaseSource"></a>Importar do HBase

A opção de importador de origem do HBase permite importar dados de uma tabela do HBase e, opcionalmente, filtrar os dados. São fornecidos vários modelos para que configurar uma importação seja tão simples quanto possível.

![Captura de ecrã das opções de origem do HBase](./media/import-data/hbasesource1.png)

![Captura de ecrã das opções de origem do HBase](./media/import-data/hbasesource2.png)

O formato da cadeia de ligação do HBase Stargate é:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do HBase especificada no campo de cadeia de ligação pode ser acedida.

Eis um exemplo de linha de comandos para importação do HBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a id="SQLBulkTarget"></a>Importar para a API de SQL (Importação em Volume)

O importador em Volume do Azure Cosmos DB permite importar de qualquer uma das opções de origem disponíveis, utilizando um procedimento armazenado do Azure Cosmos DB para eficiência. A ferramenta dá suporte à importação para um contêiner Cosmos do Azure de partição única. Ele também dá suporte à importação fragmentada na qual os dados são particionados em mais de um contêiner de Cosmos do Azure de partição única. Para obter mais informações sobre a criação de partições de dados, veja [Partitioning and scaling in Azure Cosmos DB](partition-data.md) (Criar partições e dimensionar no Azure Cosmos DB). A ferramenta cria, executa e, em seguida, elimina o procedimento armazenado das coleções de destino.  

![Captura de ecrã das opções de volume do Azure Cosmos DB](./media/import-data/documentdbbulk.png)

O formato da cadeia de ligação do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

A cadeia de ligação da conta do Azure Cosmos DB pode ser obtida a partir da página Chaves do portal do Azure, conforme descrito em [Como gerir uma conta do Azure Cosmos DB](manage-account.md); no entanto, o nome da base de dados tem de ser anexado à cadeia de ligação no seguinte formato:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de ligação pode ser acedida.

Para importar para uma única coleção, introduza o nome da coleção da qual importar os dados e clique no botão Adicionar. Para importar para mais de uma coleção, insira cada nome de coleção individualmente ou use a seguinte sintaxe para especificar mais de uma coleção: *collection_prefix*[início do índice-fim do índice]. Ao especificar mais de uma coleção usando a sintaxe mencionada anteriormente, tenha em mente as seguintes diretrizes:

1. São suportados apenas padrões de nome de intervalo de números inteiros. Por exemplo, especificar a coleção [0-3] cria as seguintes coleções: collection0, collection1, collection2, collection3.
2. Pode utilizar uma sintaxe abreviada: collection[3] cria o mesmo conjunto de coleções mencionado no passo 1.
3. Pode ser fornecida mais do que uma substituição. Por exemplo, collection[0-1] [0-9] gera 20 nomes de coleção com zeros à esquerda (collection01, ..02, ..03).

Depois de os nomes das coleções terem sido especificados, escolha o débito pretendido das coleções (de 400 RUs a 10 000 RUs). Para um melhor desempenho de importação, escolha um maior débito. Para obter mais informações sobre os níveis de desempenho, veja [Performance levels in Azure Cosmos DB](performance-levels.md) (Níveis de desempenho no Azure Cosmos DB).

> [!NOTE]
> A definição de débito de desempenho aplica-se apenas à criação de coleções. Se a coleção especificada já existir, sua taxa de transferência não será modificada.

Quando você importa para mais de uma coleção, a ferramenta de importação oferece suporte à fragmentação baseada em hash. Nesse cenário, especifique a propriedade do documento que você deseja usar como a chave de partição. (Se a chave de partição for deixada em branco, os documentos serão fragmentados aleatoriamente nas coleções de destino.)

Opcionalmente, você pode especificar qual campo na fonte de importação deve ser usado como a propriedade de ID do documento Azure Cosmos DB durante a importação. Se os documentos não tiverem essa propriedade, a ferramenta de importação gerará um GUID como o valor da propriedade de ID.

Estão disponíveis várias opções avançadas durante a importação. Em primeiro lugar, embora a ferramenta inclua um procedimento armazenado predefinido de importação em volume (BulkInsert.js), pode optar por especificar o seu próprio procedimento armazenado de importação:

 ![Captura de ecrã da opção de sproc de inserção em volume do Azure Cosmos DB](./media/import-data/bulkinsertsp.png)

Além disso, ao importar os tipos de data (por exemplo, do SQL Server ou MongoDB), pode escolher entre três opções de importação:

 ![Captura de ecrã das opções de importação de data e hora do Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Cadeia: manter como um valor de cadeia
* Época: manter como um valor de número de Época
* Ambos: manter os valores de cadeia e de número de Época. Esta opção cria um subdocumento, por exemplo: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

O importador em Volume do Azure Cosmos DB inclui as seguintes opções avançadas adicionais:

1. Tamanho do Lote: a predefinição da ferramenta é um tamanho de lote de 50.  Se os documentos a importar forem grandes, considere reduzir o tamanho do lote. Por outro lado, se os documentos a importar forem pequenos, considere aumentar o tamanho do lote.
2. Tamanho Máximo de Script (bytes): a predefinição da ferramenta é um tamanho máximo de script de 512 KB.
3. Desabilitar a geração de ID automática: se cada documento a ser importado tiver um campo de ID, a seleção dessa opção poderá aumentar o desempenho. Documentos com um campo de ID exclusiva ausente não são importados.
4. Atualizar documentos existentes: a ferramenta assume o padrão de não substituir documentos existentes por conflitos de ID. A seleção dessa opção permite substituir documentos existentes por IDs correspondentes. Esta funcionalidade é útil para as migrações de dados agendadas que atualizam os documentos existentes.
5. Número de repetições em caso de falha: especifica a frequência de repetição da conexão para Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção da conectividade de rede).
6. Intervalo de Repetições: especifica o período de tempo de espera entre tentar estabelecer novamente a ligação ao Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção de conectividade de rede).
7. Modo de Ligação: especifica o modo de ligação a utilizar com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é mais amigável com a firewall, pois só utiliza a porta 443.

![Captura de ecrã das opções avançadas de importação em volume do Azure Cosmos DB](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> A predefinição da ferramenta de importação é o modo de ligação de DirectTcp. Se ocorrerem problemas de firewall, mude para o modo de ligação de Gateway, pois só requer a porta 443.

## <a id="SQLSeqTarget"></a>Importar para a API de SQL (Importação de Registos Sequenciais)

O importador de registro sequencial Azure Cosmos DB permite que você importe de uma opção de origem disponível de acordo com o registro. Poderá escolher esta opção se estiver a importar para uma coleção existente que atingiu a quota de procedimentos armazenados. A ferramenta dá suporte à importação para um único contêiner Cosmos do Azure (partição única e várias partições). Ele também dá suporte à importação fragmentada na qual os dados são particionados em mais de um contêiner de Cosmos do Azure de partição única ou de várias partições. Para obter mais informações sobre a criação de partições de dados, veja [Partitioning and scaling in Azure Cosmos DB](partition-data.md) (Criar partições e dimensionar no Azure Cosmos DB).

![Captura de ecrã das opções de importação de registos sequenciais do Azure Cosmos DB](./media/import-data/documentdbsequential.png)

O formato da cadeia de ligação do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Você pode recuperar a cadeia de conexão para a conta de Azure Cosmos DB da página chaves do portal do Azure, conforme descrito em [como gerenciar uma conta de Azure Cosmos DB](manage-account.md). No entanto, o nome do banco de dados precisa ser anexado à cadeia de conexão no seguinte formato:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Utilize o comando Verificar para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de ligação pode ser acedida.

Para importar para uma única coleção, insira o nome da coleção na qual importar dados e, em seguida, clique no botão Adicionar. Para importar para mais de uma coleção, insira cada nome de coleção individualmente. Você também pode usar a sintaxe a seguir para especificar mais de uma coleção: *collection_prefix*[start index-end index]. Ao especificar mais de uma coleção por meio da sintaxe mencionada anteriormente, tenha em mente as seguintes diretrizes:

1. São suportados apenas padrões de nome de intervalo de números inteiros. Por exemplo, especificar a coleção [0-3] cria as seguintes coleções: collection0, collection1, collection2, collection3.
2. Pode utilizar uma sintaxe abreviada: collection[3] cria o mesmo conjunto de coleções mencionado no passo 1.
3. Pode ser fornecida mais do que uma substituição. Por exemplo, a coleção [0-1], [0-9] cria 20 nomes de coleção com zeros à esquerda (collection01, ..02, ..03).

Depois de os nomes das coleções terem sido especificados, escolha o débito pretendido das coleções (de 400 RUs a 250 000 RUs). Para um melhor desempenho de importação, escolha um maior débito. Para obter mais informações sobre os níveis de desempenho, veja [Performance levels in Azure Cosmos DB](performance-levels.md) (Níveis de desempenho no Azure Cosmos DB). Qualquer importação para coleções com um débito >10 000 RUs requer uma chave de partição. Se optar por ter mais de 250 000 RUs, terá de fazer um pedido no portal para que a sua conta seja aumentada.

> [!NOTE]
> A definição de débito aplica-se apenas à coleção ou criação de base de dados. Se a coleção especificada já existir, sua taxa de transferência não será modificada.

Ao importar para mais de uma coleção, a ferramenta de importação oferece suporte à fragmentação baseada em hash. Nesse cenário, especifique a propriedade do documento que você deseja usar como a chave de partição. (Se a chave de partição for deixada em branco, os documentos serão fragmentados aleatoriamente nas coleções de destino.)

Opcionalmente, você pode especificar qual campo na fonte de importação deve ser usado como a propriedade de ID do documento Azure Cosmos DB durante a importação. (Se os documentos não tiverem essa propriedade, a ferramenta de importação gerará um GUID como o valor da propriedade de ID.)

Estão disponíveis várias opções avançadas durante a importação. Em primeiro lugar, ao importar os tipos de data (por exemplo, do SQL Server ou MongoDB), pode escolher entre três opções de importação:

 ![Captura de ecrã das opções de importação de data e hora do Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Cadeia: manter como um valor de cadeia
* Época: manter como um valor de número de Época
* Ambos: manter os valores de cadeia e de número de Época. Esta opção cria um subdocumento, por exemplo: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

O importador de registos sequenciais do Azure Cosmos DB inclui as seguintes opções avançadas adicionais:

1. Número de Pedidos Paralelos: a predefinição da ferramenta é dois pedidos paralelos. Se os documentos a importar forem pequenos, considere aumentar o número de pedidos paralelos. Se este número aumentar muito, a importação pode apresentar limite de velocidade.
2. Desabilitar a geração de ID automática: se cada documento a ser importado tiver um campo de ID, a seleção dessa opção poderá aumentar o desempenho. Documentos com um campo de ID exclusiva ausente não são importados.
3. Atualizar documentos existentes: a ferramenta assume o padrão de não substituir documentos existentes por conflitos de ID. A seleção dessa opção permite substituir documentos existentes por IDs correspondentes. Esta funcionalidade é útil para as migrações de dados agendadas que atualizam os documentos existentes.
4. Número de repetições em caso de falha: especifica a frequência de repetição da conexão para Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção da conectividade de rede).
5. Intervalo de repetição: especifica quanto tempo esperar entre repetir a conexão para Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção de conectividade de rede).
6. Modo de Ligação: especifica o modo de ligação a utilizar com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de ligação direta são mais rápidos, enquanto o modo de gateway é mais amigável com a firewall, pois só utiliza a porta 443.

![Captura de ecrã das opções avançadas de importação de registos sequenciais do Azure Cosmos DB](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> A predefinição da ferramenta de importação é o modo de ligação de DirectTcp. Se ocorrerem problemas de firewall, mude para o modo de ligação de Gateway, pois só requer a porta 443.

## <a id="IndexingPolicy"></a>Especificar uma política de indexação

Ao permitir que a ferramenta de migração crie coleções de API de SQL do Azure Cosmos DB durante a importação, pode especificar a política de indexação das coleções. Na secção de opções avançadas de importação, nas opções de Volume do Azure Cosmos DB e registos Sequenciais do Azure Cosmos DB, navegue para a secção Política de Indexação.

![Captura de ecrã das opções avançadas da Política de Indexação do Azure Cosmos DB](./media/import-data/indexingpolicy1.png)

Ao utilizar a opção avançada Política de Indexação, pode selecionar um ficheiro de política de indexação, introduzir manualmente uma política de indexação ou selecionar de um conjunto de modelos predefinidos (ao clicar na caixa de texto da política de indexação).

Os modelos de política que a ferramenta fornece são:

* Predefinição. Essa política é melhor quando você executa consultas de igualdade em cadeias de caracteres. Ele também funcionará se você usar consultas ORDENAdas, de intervalo e de igualdade para números. Esta política tem uma tolerância de armazenamento de índice inferior ao Intervalo.
* Intervalo. Essa política é melhor quando você usa consultas ORDENAr por, intervalo e igualdade em números e cadeias de caracteres. Esta política tem uma tolerância de armazenamento de índice maior do que a Predefinição ou o Hash.

![Captura de ecrã das opções avançadas da Política de Indexação do Azure Cosmos DB](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Se você não especificar uma política de indexação, a política padrão será aplicada. Para obter mais informações sobre as políticas de indexação, veja [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="export-to-json-file"></a>Exportar para ficheiro JSON

A Azure Cosmos DB o exportador JSON permite exportar qualquer uma das opções de origem disponíveis para um arquivo JSON que tenha uma matriz de documentos JSON. A ferramenta manipula a exportação para você. Como alternativa, você pode optar por exibir o comando de migração resultante e executar o comando por conta própria. O ficheiro JSON resultante pode ser armazenado localmente ou no armazenamento de Blobs do Azure.

![Captura de ecrã da opção de exportação de ficheiro JSON local do Azure Cosmos DB](./media/import-data/jsontarget.png)

![Captura de ecrã da opção de exportação de armazenamento de Blobs JSON do Azure do Azure Cosmos DB](./media/import-data/jsontarget2.png)

Opcionalmente, você pode optar por melhorar o JSON resultante. Essa ação aumentará o tamanho do documento resultante e, ao mesmo tempo, tornará o conteúdo mais legível.

* Exportação JSON padrão

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Exportação de JSON Prettified

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

1. Se um nome de arquivo não for fornecido, todos os erros serão retornados na página resultados.
2. Se um nome de ficheiro for fornecido sem um diretório, o ficheiro é criado (ou substituído) no diretório de ambiente atual.
3. Se você selecionar um arquivo existente, o arquivo será substituído, não haverá nenhuma opção de acréscimo.
4. Em seguida, escolha se pretende registar todas as mensagens, só as de erros críticos ou as de inexistência de erros. Por fim, decida a frequência com a mensagem de transferência no ecrã é atualizada com o respetivo progresso.

   ![Captura de ecrã do ecrã Configuração avançada](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Confirmar as configurações de importação e exibir a linha de comando

1. Depois de especificar as informações de origem, as informações de destino e a configuração avançada, examine o resumo da migração e exiba ou copie o comando de migração resultante, se desejar. (Copiar o comando é útil para automatizar operações de importação.)

    ![Captura de ecrã do ecrã de resumo](./media/import-data/summary.png)

    ![Captura de ecrã do ecrã de resumo](./media/import-data/summarycommand.png)

2. Quando estiver satisfeito com as opções de origem e destino, clique em **Importar**. O tempo decorrido, a contagem de itens transferidos e as informações das falhas (se não tiver fornecido um nome de ficheiro na configuração Avançada) são atualizados enquanto a importação está em curso. Depois de concluída, pode exportar os resultados (por exemplo, para lidar com eventuais falhas de importação).

    ![Captura de ecrã da opção de exportação de JSON do Azure Cosmos DB](./media/import-data/viewresults.png)

3. Você também pode iniciar uma nova importação redefinindo todos os valores ou mantendo as configurações existentes. (Por exemplo, você pode optar por manter as informações da cadeia de conexão, a escolha de origem e de destino e muito mais.)

    ![Captura de ecrã da opção de exportação de JSON do Azure Cosmos DB](./media/import-data/newimport.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez as seguintes tarefas:

> [!div class="checklist"]
> * Instalou a ferramenta de Migração de Dados
> * Importou dados de origens de dados diferentes
> * Exportou do Azure Cosmos DB para JSON

Agora pode avançar para o próximo tutorial e ficar a saber como consultar dados com o Azure Cosmos DB.

> [!div class="nextstepaction"]
>[Como consultar dados?](../cosmos-db/tutorial-query-sql-api.md)
