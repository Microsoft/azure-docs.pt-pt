---
title: Solucionar problemas de conectores Azure Data Factory
description: Saiba como solucionar problemas de conector no Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778231"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Solucionar problemas de conectores Azure Data Factory

Este artigo explora métodos comuns de solução de problemas para conectores no Azure Data Factory.
  

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="error-code--azurebloboperationfailed"></a>Código de erro: AzureBlobOperationFailed

- **Mensagem**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa**: problema de impacto na operação do armazenamento de BLOBs.

- **Recomendação**: Verifique o erro em detalhes. Consulte o documento de ajuda de blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Contate a equipe de armazenamento se precisar de ajuda.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Código de erro: AzureBlobServiceNotReturnExpectedDataLength

- **Mensagem**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Código de erro: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Mensagem**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Código de erro: AzureStorageOperationFailedConcurrentWrite

- **Mensagem**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Mensagem de erro: o tamanho da solicitação é muito grande

- **Sintomas**: você copia dados em Azure Cosmos DB com tamanho de lote de gravação padrão e erro de clique *"o tamanho da**solicitação é muito grande**"* .

- **Causa**: Cosmos DB limita o tamanho de uma única solicitação a 2 MB. A fórmula é, tamanho da solicitação = tamanho do documento único * gravar tamanho do lote. Se o tamanho do documento for grande, o comportamento padrão resultará em um tamanho de solicitação muito grande. Você pode ajustar o tamanho do lote de gravação.

- **Resolução**: no coletor da atividade de cópia, reduza o valor de ' tamanho do lote de gravação ' (o valor padrão é 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Mensagem de erro: violação de restrição de índice exclusivo

- **Sintomas**: ao copiar dados para o cosmos DB, você encontra o seguinte erro:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa**: há duas causas possíveis:

    - Se você usar o comportamento **Inserir** como gravação, esse erro significa que os dados de origem têm linhas/objetos com a mesma ID.

    - Se você usar **Upsert** como comportamento de gravação e definir outra chave exclusiva para o contêiner, esse erro significa que os dados de origem têm linhas/objetos com IDs diferentes, mas o mesmo valor para a chave exclusiva definida.

- **Resolução**: 

    - Para cause1, defina **Upsert** como comportamento de gravação.
    - Para a causa 2, verifique se cada documento tem um valor diferente para a chave exclusiva definida.

### <a name="error-message-request-rate-is-large"></a>Mensagem de erro: a taxa de solicitação é grande

- **Sintomas**: ao copiar dados para o cosmos DB, você encontra o seguinte erro:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Causa**: as unidades de solicitação usadas são maiores do que a ru disponível configurada em Cosmos DB. Saiba como Cosmos DB calcula RU [aqui](../cosmos-db/request-units.md#request-unit-considerations).

- **Resolução**: aqui estão duas soluções:

    1. **Aumente o contêiner de ru** para maior valor em Cosmos DB, o que melhorará o desempenho da atividade de cópia, embora incorra mais custo em Cosmos DB. 

    2. Diminua o **writeBatchSize** para um valor menor (como 1000) e defina **parallelCopies** como um valor menor, como 1, o que tornará o desempenho de execução de cópia pior do que o atual, mas não incorrerá mais custo em Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Coluna ausente no mapeamento de coluna

- **Sintomas**: quando você importa o esquema para Cosmos DB para mapeamento de coluna, algumas colunas estão ausentes. 

- **Causa**: o ADF infere o esquema dos primeiros 10 documentos Cosmos DB. Se algumas colunas/Propriedades não tiverem valor nesses documentos, elas não serão detectadas pelo ADF, portanto, não aparecerão.

- **Resolução**: você pode ajustar a consulta como abaixo para impor a coluna a ser exibida no conjunto de resultados com um valor vazio: (Suponha que a coluna "impossível" esteja ausente nos 10 primeiros documentos). Como alternativa, você pode adicionar manualmente a coluna para mapeamento.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Mensagem de erro: o GuidRepresentation para o leitor é CSharpLegacy

- **Sintomas**: ao copiar dados do cosmos DB MongoAPI/MongoDB com o campo UUID, você clica no seguinte erro:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Causa**: há duas maneiras de representar o UUID em BSON-UuidStardard e UuidLegacy. Por padrão, UuidLegacy é usado para ler dados. Você encontrará um erro se os dados do UUID no MongoDB forem UuidStandard.

- **Resolução**: na cadeia de conexão do MongoDB, adicione a opção "**uuidRepresentation = Standard**". Para obter mais informações, consulte [cadeia de conexão do MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Ger2 de Armazenamento do Azure Data Lake

### <a name="error-code--adlsgen2operationfailed"></a>Código de erro: AdlsGen2OperationFailed

- **Mensagem**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa**: ADLS Gen2 gera o erro indicando que a operação falhou.

- **Recomendação**: Verifique a mensagem de erro detalhada lançada por ADLS Gen2. Se ele for causado por uma falha transitória, tente novamente. Se precisar de ajuda adicional, entre em contato com o suporte do armazenamento do Azure e forneça a ID da solicitação na mensagem de erro.

- **Causa**: quando a mensagem de erro contém ' proibido ', a entidade de serviço ou a identidade gerenciada que você usa pode não ter permissão suficiente para acessar o ADLS Gen2.

- **Recomendação**: consulte o documento de ajuda: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Causa**: quando a mensagem de erro contém ' InternalServerError ', o erro é retornado por ADLS Gen2.

- **Recomendação**: pode ser causada por uma falha transitória, tente novamente. Se o problema persistir, entre em contato com o suporte do armazenamento do Azure e forneça a ID da solicitação na mensagem de erro.


### <a name="error-code--adlsgen2invalidurl"></a>Código de erro: AdlsGen2InvalidUrl

- **Mensagem**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Código de erro: AdlsGen2InvalidFolderPath

- **Mensagem**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Código de erro: AdlsGen2OperationFailedConcurrentWrite

- **Mensagem**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Código de erro: AdlsGen2TimeoutError

- **Mensagem**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Armazenamento Ger1 do Azure Data Lake

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Mensagem de erro: o servidor remoto retornou um erro: (403) proibido

- **Sintomas**: falha na atividade de cópia com o seguinte erro: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Causa**: uma possível causa é que a entidade de serviço ou a identidade gerenciada que você usa não tem permissão para acessar a pasta/arquivo específico.

- **Resolução**: conceda permissões correspondentes em todas as pastas e subpastas que você precisa copiar. Consulte [este documento](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Mensagem de erro: falha ao obter o token de acesso usando a entidade de serviço. Erro de ADAL: service_unavailable

- **Sintomas**: falha na atividade de cópia com o seguinte erro:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Causa**: quando o servidor de token de serviço (STS) de propriedade do Azure Active Directory não está disponível, ou seja, muito ocupado para lidar com solicitações, ele retorna um erro HTTP 503. 

- **Resolução**: execute novamente a atividade de cópia após alguns minutos.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/banco de dados SQL do Azure/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Código de erro: SqlFailedToConnect

- **Mensagem**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Causa**: se a mensagem de erro contiver "SqlException", o banco de dados SQL lançará o erro indicando que uma operação específica falhou.

- **Recomendação**: Pesquise por código de erro do SQL neste documento de referência para obter mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Se precisar de mais ajuda, entre em contato com o suporte do Azure SQL.

- **Causa**: se a mensagem de erro contiver "cliente com endereço IP"... " Não tem permissão para acessar o servidor ", e você está tentando se conectar ao banco de dados SQL do Azure, geralmente ele é causado pelo problema do firewall do banco de dados SQL do Azure.

- **Recomendação**: na configuração do firewall do SQL Server do Azure, habilite a opção "permitir que os serviços e recursos do Azure acessem este servidor". Doc de referência: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Código de erro: SqlOperationFailed

- **Mensagem**: `A database operation failed. Please search error to get more details.`

- **Causa**: se a mensagem de erro contiver "SqlException", o banco de dados SQL lançará o erro indicando que uma operação específica falhou.

- **Recomendação**: se o erro de SQL não for claro, tente alterar o banco de dados para o nível de compatibilidade mais recente ' 150 '. Ele pode gerar erros de SQL da versão mais recente. Veja o documento de detalhes: https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat.
        Para solucionar problemas de SQL, pesquise por código de erro do SQL neste documento de referência para obter mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Se precisar de mais ajuda, entre em contato com o suporte do Azure SQL.

- **Causa**: se a mensagem de erro contiver "PdwManagedToNativeInteropException", geralmente ela é causada por incompatibilidade entre os tamanhos de coluna de origem e de coletor.

- **Recomendação**: Verifique o tamanho das colunas de origem e do coletor. Se precisar de mais ajuda, entre em contato com o suporte do Azure SQL.

- **Causa**: se a mensagem de erro contiver "InvalidOperationException", geralmente ela será causada por dados de entrada inválidos.

- **Recomendação**: para identificar qual linha encontra o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar as linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Doc de referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Código de erro: SqlUnauthorizedAccess

- **Mensagem**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: a credencial está incorreta ou a conta de logon não pode acessar o banco de dados SQL.

- **Recomendação**: Verifique se a conta de logon tem permissão suficiente para acessar o banco de dados SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Código de erro: SqlOpenConnectionTimeout

- **Mensagem**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa**: pode ser uma falha transitória do banco de dados SQL.

- **Recomendação**: tente atualizar a cadeia de conexão de serviço vinculado com um valor de tempo limite de conexão maior.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Código de erro: SqlAutoCreateTableTypeMapFailed

- **Mensagem**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: a tabela de criação automática não pode atender ao requisito de origem.

- **Recomendação**: Atualize o tipo de coluna em "mapeamentos" ou crie manualmente a tabela de coletor no servidor de destino.


### <a name="error-code--sqldatatypenotsupported"></a>Código de erro: SqlDataTypeNotSupported

- **Mensagem**: `A database operation failed. Check the SQL errors.`

- **Causa**: se o problema ocorrer na fonte SQL e o erro estiver relacionado ao estouro de SqlDateTime, o valor de dados será sobre o intervalo de tipo lógico (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Recomendação**: Converta o tipo para cadeia de caracteres na consulta SQL de origem ou no mapeamento de coluna da atividade de cópia, altere o tipo de coluna para ' String '.

- **Causa**: se o problema ocorrer no coletor SQL e o erro estiver relacionado ao estouro de SqlDateTime, o valor dos dados estará acima do intervalo permitido na tabela de coletor.

- **Recomendação**: Atualize o tipo de coluna correspondente para o tipo "datetime2" na tabela de coletor.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Código de erro: SqlInvalidDbStoredProcedure

- **Mensagem**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: o procedimento armazenado especificado não é válido. Pode ser causado por que o procedimento armazenado não retorna nenhum dado.

- **Recomendação**: valide o procedimento armazenado pelas ferramentas do SQL. Verifique se o procedimento armazenado pode retornar dados.


### <a name="error-code--sqlinvaliddbquerystring"></a>Código de erro: SqlInvalidDbQueryString

- **Mensagem**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa**: a consulta SQL especificada não é válida. Pode ser causado por que a consulta não retorna nenhum dado

- **Recomendação**: valide a consulta SQL por ferramentas SQL. Verifique se a consulta pode retornar dados.


### <a name="error-code--sqlinvalidcolumnname"></a>Código de erro: SqlInvalidColumnName

- **Mensagem**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa**: não é possível localizar a coluna. Configuração possível incorreta.

- **Recomendação**: Verifique a coluna na consulta, ' estrutura ' no conjunto de dados e ' mapeamentos ' na atividade.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Código de erro: SqlColumnNameMismatchByCaseSensitive

- **Mensagem**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Código de erro: SqlBatchWriteTimeout

- **Mensagem**: `Timeouts in SQL write operation.`

- **Causa**: pode ser uma falha transitória do banco de dados SQL.

- **Recomendação**: tente novamente. Se o problema for reproduzido, contate o suporte do SQL do Azure.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Código de erro: SqlBatchWriteTransactionFailed

- **Mensagem**: `SQL transaction commits failed`

- **Causa**: se os detalhes da exceção informarem constantemente o tempo limite da transação, a latência de rede entre o tempo de execução de integração e o banco de dados será maior do que o limite padrão

- **Recomendação**: Atualize a cadeia de conexão do serviço vinculado do SQL com o valor ' tempo limite de conexão ' igual a 120 ou superior e execute a atividade novamente.

- **Causa**: se os detalhes da exceção diferenciarem o SqlConnection de forma intermitente, ele poderá ser uma falha de rede transitória ou um problema do lado do banco de dados SQL

- **Recomendação**: repita a atividade e examine as métricas do lado do banco de dados SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Código de erro: SqlBulkCopyInvalidColumnLength

- **Mensagem**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: falha na cópia em massa do SQL devido ao recebimento de um comprimento de coluna inválido do cliente bcp.

- **Recomendação**: para identificar qual linha encontra o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar as linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Doc de referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Código de erro: SqlConnectionIsClosed

- **Mensagem**: `The connection is closed by SQL Database.`

- **Causa**: a conexão SQL é fechada pelo banco de dados SQL quando a execução simultânea alta e a conexão do servidor terminam.

- **Recomendação**: o servidor remoto fechou a conexão SQL. Tente novamente. Se o problema for reproduzido, contate o suporte do SQL do Azure.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Código de erro: SqlCreateTableFailedUnsupportedType

- **Mensagem**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Mensagem de erro: falha na conversão ao converter de uma cadeia de caracteres em uniqueidentifier

- **Sintomas**: quando você copia dados de uma fonte de dados tabulares (como SQL Server) para o Azure SQL data warehouse usando cópia preparada e polybase, você clica no seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Causa**: o polybase do Azure SQL data warehouse não pode converter uma cadeia de caracteres vazia em GUID.

- **Resolução**: no coletor de atividade de cópia, em configurações do polybase, defina a opção "**usar padrão de tipo**" como false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensagem de erro: tipo de dados esperado: DECIMAL (x, x), valor incorreto

- **Sintomas**: quando você copia dados de uma fonte de dados tabulares (como SQL Server) para o SQL DW usando cópia preparada e polybase, você clica no seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Causa**: o polybase do Azure SQL data warehouse não pode inserir uma cadeia de caracteres vazia (valor nulo) em uma coluna decimal.

- **Resolução**: no coletor de atividade de cópia, em configurações do polybase, defina a opção "**usar padrão de tipo**" como false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Mensagem de erro: mensagem de exceção Java: HdfsBridge:: CreateRecordReader

- **Sintomas**: você copia dados para o Azure SQL data warehouse usando o polybase e clica no seguinte erro:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Causa**: a possível causa é que o esquema (largura total da coluna) é muito grande (maior que 1 MB). Verifique o esquema da tabela de destino do SQL DW adicionando o tamanho de todas as colunas:

    - Int-> 4 bytes
    - Bigint-> 8 bytes
    - Varchar (n), Char (n), binary (n), varbinary (n)-> n bytes
    - Nvarchar (n), nchar (n)-> n * 2 bytes
    - Data-> 6 bytes
    - DateTime/(2), smalldatetime-> 16 bytes
    - DateTimeOffset-> 20 bytes
    - Decimal-> 19 bytes
    - Float-> 8 bytes
    - Dinheiro-> 8 bytes
    - Smallmoney-> 4 bytes
    - Real-> 4 bytes
    - Smallint-> 2 bytes
    - Tempo-> 12 bytes
    - Tinyint-> 1 byte

- **Resolução**: Reduza a largura da coluna para menos de 1 MB

- Ou use a abordagem de inserção em massa desabilitando o polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Mensagem de erro: a condição especificada usando cabeçalho (s) condicional HTTP não foi atendida

- **Sintomas**: Use a consulta SQL para efetuar pull de dados do Azure SQL data warehouse e pressione o seguinte erro:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa**: problema de acesso de SQL data warehouse do Azure consultando a tabela externa no armazenamento do Azure.

- **Resolução**: execute a mesma consulta no SSMS e verifique se você vê o mesmo resultado. Em caso afirmativo, abra um ticket de suporte para o Azure SQL Data Warehouse e indique o nome da base de dados e do servidor SQL DW para continuar a resolução de problemas.
            

## <a name="delimited-text-format"></a>Formato de Texto Delimitado

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Código de erro: DelimitedTextColumnNameNotAllowNull

- **Mensagem**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: quando definir ' firstRowAsHeader ' na atividade, a primeira linha será usada como nome da coluna. Esse erro significa que a primeira linha contém um valor vazio. Por exemplo: ' ColumnA,, coluna b '.

- **Recomendação**: Verifique a primeira linha e corrija o valor se houver um valor vazio.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Código de erro: DelimitedTextMoreColumnsThanDefined

- **Mensagem**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Causa**: a contagem de colunas da linha problemática é grande do que a contagem de colunas da primeira linha. Pode ser causado por problemas de dados ou configurações de caractere delimitador de coluna/cotação incorreta.

- **Recomendação**: Obtenha a contagem de linhas na mensagem de erro, verifique a coluna da linha e corrija os dados.

- **Causa**: se a contagem de colunas esperada for "1" na mensagem de erro, é possível que você tenha especificado uma compactação incorreta ou configurações de formato, o que fez com que o ADF analisasse erroneamente seus arquivos.

- **Recomendação**: Verifique as configurações de formato para ter certeza de que ele corresponde ao (s) arquivo (ns) de origem.

- **Causa**: se sua origem for uma pasta, é possível que os arquivos na pasta especificada tenham um esquema diferente.

- **Recomendação**: Verifique se os arquivos na pasta especificada têm esquema idêntico.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Código de erro: DelimitedTextIncorrectRowDelimiter

- **Mensagem**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Código de erro: DelimitedTextTooLargeColumnCount

- **Mensagem**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Código de erro: DelimitedTextInvalidSettings

- **Mensagem**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Código de erro: DynamicsCreateServiceClientError

- **Mensagem**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa**: esse é um problema transitório no lado do servidor do Dynamics.

- **Recomendação**: execute o pipeline novamente. Se continuar falhando, tente reduzir o paralelismo. Se ainda falhar, entre em contato com o suporte do Dynamics.



## <a name="json-format"></a>Formato JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Código de erro: JsonInvalidArrayPathDefinition

- **Mensagem**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Código de erro: JsonEmptyJObjectData

- **Mensagem**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Código de erro: JsonNullValueInPathDefinition

- **Mensagem**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Código de erro: JsonUnsupportedHierarchicalComplexValue

- **Mensagem**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Código de erro: JsonConflictPartitionDiscoverySchema

- **Mensagem**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Código de erro: JsonInvalidDataFormat

- **Mensagem**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Código de erro: JsonInvalidDataMixedArrayAndObject

- **Mensagem**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Formato parquet

### <a name="error-code--parquetjavainvocationexception"></a>Código de erro: ParquetJavaInvocationException

- **Mensagem**: `An error occurred when invoking java, message: %javaException;.`

- **Causa**: quando a mensagem de erro contém ' Java. lang. OutOfMemory ', ' Java heap Space ' e ' doubleCapacity ', geralmente é um problema de gerenciamento de memória na versão antiga do Integration Runtime.

- **Recomendação**: se você estiver usando Integration Runtime de hospedagem interna e a versão for anterior à 3.20.7159.1, sugira a atualização para a versão mais recente.

- **Causa**: quando a mensagem de erro contém ' Java. lang. OutOfMemory ', o Integration Runtime não tem recursos suficientes para processar os arquivos.

- **Recomendação**: limite as execuções simultâneas no Integration Runtime. Para Integration Runtime auto-hospedados, escale verticalmente para um computador avançado com memória igual ou maior que 8 GB.

- **Causa**: quando a mensagem de erro contém ' NullPointerReference ', é possível que seja um erro transitório.

- **Recomendação**: tente novamente. Se o problema persistir, entre em contato com o suporte.


### <a name="error-code--parquetinvalidfile"></a>Código de erro: ParquetInvalidFile

- **Mensagem**: `File is not a valid parquet file.`

- **Causa**: problema no arquivo parquet.

- **Recomendação**: Verifique se a entrada é um arquivo parquet válido.


### <a name="error-code--parquetnotsupportedtype"></a>Código de erro: ParquetNotSupportedType

- **Mensagem**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: o formato parquet não tem suporte no Azure data Factory.

- **Recomendação**: Verifique os dados de origem. Consulte o documento: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Código de erro: ParquetMissedDecimalPrecisionScale

- **Mensagem**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: Tente analisar a precisão e a escala do número, mas nenhuma informação é fornecida.

- **Recomendação**: ' Source ' não retorna precisão e escala corretas. Verifique a precisão e a escala da coluna do problema.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Código de erro: ParquetInvalidDecimalPrecisionScale

- **Mensagem**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa**: o esquema é inválido.

- **Recomendação**: Verifique a precisão e a escala da coluna do problema.


### <a name="error-code--parquetcolumnnotfound"></a>Código de erro: ParquetColumnNotFound

- **Mensagem**: `Column %column; does not exist in Parquet file.`

- **Causa**: o esquema de origem é incompatível com o esquema de coletor.

- **Recomendação**: Verifique a the'mappings ' in ' Activity '. Verifique se a coluna de origem pode ser mapeada para a coluna de coletor correta.


### <a name="error-code--parquetinvaliddataformat"></a>Código de erro: ParquetInvalidDataFormat

- **Mensagem**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: os dados não podem ser convertidos no tipo especificado em mapeamentos. origem

- **Recomendação**: Verifique os dados de origem ou especifique o tipo de dados correto para essa coluna no mapeamento de coluna da atividade de cópia. Consulte o documento: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Código de erro: ParquetDataCountNotMatchColumnCount

- **Mensagem**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**: contagem de colunas de origem e contagem de colunas de coletor incompatíveis

- **Recomendação**: a contagem de colunas de origem de verificação dupla é igual à contagem de colunas do coletor em ' mapeamento '.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Código de erro: ParquetDataTypeNotMatchColumnType

- **Mensagem**: o tipo de dados% srcType; não corresponde ao tipo de coluna% dstType; especificado na coluna '% columnIndex; '.

- **Causa**: os dados da origem não podem ser convertidos em tipos definidos no coletor

- **Recomendação**: especifique um tipo correto no Mapping. Sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Código de erro: ParquetBridgeInvalidData

- **Mensagem**: `%message;`

- **Causa**: valor de dados acima da limitação

- **Recomendação**: tente novamente. Se o problema persistir, entre em contato conosco.


### <a name="error-code--parquetunsupportedinterpretation"></a>Código de erro: ParquetUnsupportedInterpretation

- **Mensagem**: `The given interpretation '%interpretation;' of parquet format is not supported.`

- **Causa**: cenário sem suporte

- **Recomendação**: ' ParquetInterpretFor ' não deve ser ' sparkSql '.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Código de erro: ParquetUnsupportFileLevelCompressionOption

- **Mensagem**: `File level compression is not supported for Parquet.`

- **Causa**: cenário sem suporte

- **Recomendação**: Remova ' CompressionType ' na carga.



## <a name="general-copy-activity-error"></a>Erro geral da atividade de cópia

### <a name="error-code--jrenotfound"></a>Código de erro: JreNotFound

- **Mensagem**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa**: o Integration Runtime de hospedagem interna não pode localizar o tempo de execução do Java. O tempo de execução Java é necessário para ler a origem específica.

- **Recomendação**: Verifique seu ambiente de tempo de execução de integração, o documento de referência: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Código de erro: WildcardPathSinkNotSupported

- **Mensagem**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa**: o conjunto de coletas não dá suporte a curinga.

- **Recomendação**: Verifique o conjunto de valores do coletor e corrija o caminho sem o valor de curinga.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Código de erro: MappingInvalidPropertyWithEmptyValue

- **Mensagem**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Código de erro: MappingInvalidPropertyWithNamePathAndOrdinal

- **Mensagem**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Código de erro: MappingDuplicatedOrdinal

- **Mensagem**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Código de erro: MappingInvalidOrdinalForSinkColumn

- **Mensagem**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para solução de problemas, Experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
            
