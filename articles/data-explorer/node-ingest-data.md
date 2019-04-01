---
title: 'Início rápido: Ingestão de dados usando a biblioteca de nó de Explorador de dados do Azure'
description: Neste início rápido, irá aprender a ingerir (carregar) dados para o Azure Data Explorer com o Node.js.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: 0a23c171d99d46eb29dd589867ce70ca2739ff29
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756451"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>Início rápido: Ingestão de dados usando a biblioteca de nó de Explorador de dados do Azure

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece duas bibliotecas de cliente para o Node: uma [biblioteca de ingestão](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) e [uma biblioteca de dados](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Estas bibliotecas permitem ingerir (carregar) dados para um cluster e consultar dados a partir do código. Neste início rápido, primeiro vai criar uma tabela e o mapeamento de dados num cluster de teste. Em seguida, vai colocar em fila a ingestão para o cluster e validar os resultados.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Além de uma subscrição do Azure, precisa do seguinte para concluir este início rápido:

* [Um cluster e uma base de dados de teste](create-cluster-database-portal.md)

* [Node.js](https://nodejs.org/en/download/) instalado no seu computador de desenvolvimento

## <a name="install-the-data-and-ingest-libraries"></a>Instalar as bibliotecas de dados e de ingestão

Instalar o *azure-kusto-ingest* e o *azure-kusto-data*

```bash
npm i --save azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Adicionar declarações e constantes de importação

Importar classes das bibliotecas

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
Para autenticar uma aplicação, o Azure Data Explorer utiliza o ID de inquilino do Azure Active Directory. Para encontrar o ID de inquilino, siga [Encontrar o seu ID de inquilino do Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

Defina os valores para `authorityId`, `kustoUri`, `kustoIngestUri` e `kustoDatabase` antes de executar este código.

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

Agora construa a cadeia de ligação. Este exemplo utiliza a autenticação do dispositivo para aceder ao cluster. Também pode utilizar o certificado de aplicação do Azure Active Directory, a chave de aplicação, e o utilizador e a palavra-passe.

Irá criar a tabela de destino e o mapeamento num passo posterior.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Definir as informações do ficheiro de origem

Importe classes adicionais e defina as constantes para o ficheiro de origem de dados. Este exemplo utiliza um ficheiro de exemplo alojado no Armazenamento de Blobs do Azure. O conjunto de dados de exemplo **StormEvents** contém dados relacionados com Meteorologia dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>Criar uma tabela no cluster de teste

Crie uma tabela que corresponda ao esquema dos dados no ficheiro `StormEvents.csv`. Quando esse código é executado, ele retorna uma mensagem semelhante ao seguinte: *Para iniciar sessão, utilize um browser para abrir a página https://microsoft.com/devicelogin e introduza o código XXXXXXXXX para autenticar*. Siga os passos para iniciar sessão e regresse para executar o próximo bloco de código. Os blocos de código subsequentes que estabelecerem uma ligação terão de iniciar sessão novamente.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>Definir o mapeamento de ingestão

Mapeie os dados recebidos do CSV para os nomes de coluna e tipos de dados utilizados ao criar a tabela.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>Colocar uma mensagem em fila para ingestão

Coloque uma mensagem em fila para extrair dados do armazenamento de blobs e ingerir esses dados para o Azure Data Explorer.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Valide se a tabela contém dados

Valide se os dados foram ingeridos para a tabela. Aguarde cinco a dez minutos para que a ingestão colocada em fila agende a ingestão e carregue os dados para o Azure Data Explorer. Em seguida, execute o seguinte código para obter a contagem de registos na tabela `StormEvents`.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>Executar consultas de resolução de problemas

Inicie sessão no [https://dataexplorer.azure.com](https://dataexplorer.azure.com) e ligue ao cluster. Execute o seguinte comando na base de dados para ver se ocorreram quaisquer falhas de ingestão nas últimas quatro horas. Substitua o nome da base de dados antes de executar.
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Execute o seguinte comando para ver o estado de todas as operações de ingestão nas últimas quatro horas. Substitua o nome da base de dados antes de executar.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Limpar recursos

Se tenciona seguir os nossos inícios rápidos e tutoriais, mantenha os recursos que criou. Caso contrário, execute o seguinte comando na base de dados para limpar a tabela `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Escrever consultas](write-queries.md)
