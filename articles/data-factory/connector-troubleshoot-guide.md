---
title: Resolver Problemas dos Conectores do Azure Data Factory
description: Saiba como resolver problemas de problemas com os problemas de conector na Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: a1b2f74af02db1560dbcdd0bf0c72976dc6dcea8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022338"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Resolver Problemas dos Conectores do Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para conectores na Azure Data Factory.
  

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="error-code--azurebloboperationfailed"></a>Código de erro: AzureBlobOperationFailed

- **Mensagem:**`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa:** Operação de armazenamento blob atingiu problema.

- **Recomendação**: Verifique o erro nos detalhes. Consulte o documento de ajuda blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Contacte a equipa de armazenamento se precisar de ajuda.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Código de erro: AzureBlobServiceNotReturnExpectedDataLength

- **Mensagem:**`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Código de erro: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Mensagem:**`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Código de erro: AzureStorageOperationFalhadaConcurrentWrite

- **Mensagem:**`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Mensagem de erro: O tamanho do pedido é demasiado grande

- **Sintomas:** Copia dados para O BD do Azure Cosmos com o tamanho padrão do lote de escrita, e o erro de impacto " O tamanho do *pedido é demasiado**grande**"*.

- **Causa**: Cosmos DB limita o tamanho de um único pedido a 2 MB. A fórmula é, Tamanho de Pedido = Tamanho de documento único * Tamanho do lote de escrita. Se o tamanho do documento for grande, o comportamento padrão resultará num tamanho de pedido demasiado grande. Pode afinar o tamanho do lote de escrita.

- **Resolução**: No sumidouro da atividade de cópia, reduza o valor do 'Tamanho do lote de escrita' (o valor predefinido é de 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Error message: Unique index constraint violation

- **Sintomas**: Ao copiar dados para cosmos DB, você atinge o seguinte erro:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa:** Existem duas causas possíveis:

    - Se utilizar o **Insert** como comportamento de escrita, este erro significa que os dados de origem têm linhas/objetos com o mesmo ID.

    - Se utilizar o **Upsert** como comportamento de escrita e definir outra chave única para o recipiente, este erro significa que os dados de origem têm linhas/objetos com diferentes IDs, mas o mesmo valor para a chave única definida.

- **Resolução:** 

    - Para a causa1, configura **o Upsert** como comportamento de escrita.
    - Para a causa 2, certifique-se de que cada documento tem um valor diferente para uma chave única definida.

### <a name="error-message-request-rate-is-large"></a>Mensagem de erro: A taxa de pedido é grande

- **Sintomas**: Ao copiar dados para cosmos DB, você atinge o seguinte erro:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Causa**: As unidades de pedido utilizadas são maiores do que a RU disponível configurada em Cosmos DB. Saiba como cosmos DB calcula ru a partir [daqui.](../cosmos-db/request-units.md#request-unit-considerations)

- **Resolução**: Aqui estão duas soluções:

    1. **Aumente o recipiente RU** para maior valor em Cosmos DB, o que melhorará o desempenho da atividade de cópia, embora incorra mais custo em Cosmos DB. 

    2. Diminua **o writeBatchSize** para um valor menor (como 1000) e coloque **paraleloSCópias** para um valor menor, como 1, o que tornará o desempenho da execução de cópia pior do que o atual, mas não incorrerá mais custo em Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Coluna em falta no mapeamento da coluna

- **Sintomas**: Quando se importa esquema para Cosmos DB para mapeamento de colunas, faltam algumas colunas. 

- **Causa:** ADF infere o esquema dos primeiros 10 documentos da Cosmos DB. Se algumas colunas/propriedades não tiverem valor nesses documentos, não serão detetadas pela ADF, pelo que não aparecerão.

- **Resolução**: Pode sintonizar a consulta como abaixo para impor a coluna para aparecer em resultado definido com valor vazio: (assumir: a coluna "impossível" está em falta nos primeiros 10 documentos). Em alternativa, pode adicionar manualmente a coluna para mapeamento.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Mensagem de erro: A Representação Guida para o leitor é CSharpLegacy

- **Sintomas**: Ao copiar dados do Cosmos DB MongoAPI/MongoDB com o campo UUID, atingiu o seguinte erro:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Causa:** Existem duas formas de representar o UUID em BSON - UuidStardard e UuidLegacy. Por padrão, o UuidLegacy é usado para ler dados. Irá cometer um erro se os seus dados uUID no MongoDB forem UuidStandard.

- **Resolução**: Na cadeia de ligação MongoDB, adicione a opção "**uuidRepresentation=standard**". Para mais informações, consulte a cadeia de [ligação MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

### <a name="error-code--adlsgen2operationfailed"></a>Código de erro: AdlsGen2OperationFailed

- **Mensagem:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa**: ADLS Gen2 lança o erro indicando que a operação falhou.

- **Recomendação**: Verifique a mensagem de erro detalhada lançada pela ADLS Gen2. Se for causado por uma falha transitória, por favor, tente novamente. Se precisar de mais ajuda, contacte o suporte de armazenamento Azure e forneça o ID de pedido na mensagem de erro.

- **Causa**: Quando a mensagem de erro contiver 'Proibido', o diretor de serviço ou a identidade gerida que utiliza pode não ter autorização suficiente para aceder ao ADLS Gen2.

- **Recomendação**: Consulte o documento de ajuda: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Causa**: Quando a mensagem de erro contém 'InternalServerError', o erro é devolvido por ADLS Gen2.

- **Recomendação**: Pode ser causado por falha transitória, por favor tente novamente. Se o problema persistir, contacte o suporte de armazenamento Azure e forneça o ID de pedido na mensagem de erro.


### <a name="error-code--adlsgen2invalidurl"></a>Código de erro: AdlsGen2InvalidUrl

- **Mensagem:**`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Código de erro: AdlsGen2InvalidFolderPath

- **Mensagem:**`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Código de erro: AdlsGen2OperationFailedConcurrentWrite

- **Mensagem:**`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Código de erro: AdlsGen2TimeoutError

- **Mensagem:**`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Mensagem de erro: O servidor remoto devolveu um erro: (403) Proibido

- **Sintomas**: Falha de atividade de cópia com o seguinte erro: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Causa**: Uma das causas possíveis é que o diretor de serviço ou a identidade gerida que utiliza não tem permissão para aceder à determinada pasta/ficheiro.

- **Resolução**: Conceda permissões correspondentes em todas as pastas e subpastas que precisa de copiar. Consulte [este doc](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Mensagem de erro: Não conseguiu obter o sinal de acesso utilizando o diretor de serviço. Erro ADAL: service_unavailable

- **Sintomas**: Falha de atividade de cópia com o seguinte erro:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Causa**: Quando o Servidor de Token de Serviço (STS) propriedade do Azure Ative Directory não está indisponível, ou seja, demasiado ocupado para lidar com pedidos, devolve um erro HTTP 503. 

- **Resolução**: Reexecutar a atividade da cópia após vários minutos.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Armazém de dados Azure SQL/Azure SQL Database/Servidor SQL

### <a name="error-code--sqlfailedtoconnect"></a>Código de erro: SqlFailedToConnect

- **Mensagem:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Causa**: Se a mensagem de erro contiver "SqlException", a Base de Dados SQL lança o erro indicando que alguma operação específica falhou.

- **Recomendação**: Por favor, procure por Código de Erro SQL neste doc de referência para obter mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Se precisar de mais ajuda, contacte o suporte Azure SQL.

- **Causa**: Se a mensagem de erro contiver "Cliente com endereço IP '..." não está autorizado a aceder ao servidor", e está a tentar ligar-se à Base de Dados Azure SQL, normalmente é causada por problemas de firewall da Base de Dados Azure SQL.

- **Recomendação**: Na configuração lógica da firewall do servidor SQL, permita que os serviços e recursos do Azure acedam a este servidor. Doutoramento em referência: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Código de erro: SqlOperationFailed

- **Mensagem:**`A database operation failed. Please search error to get more details.`

- **Causa**: Se a mensagem de erro contiver "SqlException", a Base de Dados SQL lança o erro indicando que alguma operação específica falhou.

- **Recomendação**: Se o erro SQL não for claro, tente alterar a base de dados para o nível de compatibilidade mais recente '150'. Pode lançar erros SQL da versão mais recente. Por favor, consulte o médico detalhado: https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat .
        Para resolver problemas de Resolução de Problemas SQL, procure por código de erro SQL neste doc de referência para obter mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Se precisar de mais ajuda, contacte o suporte Azure SQL.

- **Causa**: Se a mensagem de erro contiver "PdwManagedToNativeInteropException", normalmente é causada por uma incompatibilidade entre os tamanhos da coluna de origem e pia.

- **Recomendação**: Verifique o tamanho das colunas de origem e pia. Se precisar de mais ajuda, contacte o suporte Azure SQL.

- **Causa**: Se a mensagem de erro contiver "InvalidaoperationException", normalmente é causada por dados de entrada inválidos.

- **Recomendação**: Para identificar qual a linha que encontra o problema, por favor, permita a funcionalidade de tolerância à falha na atividade de cópia, que pode redirecionar a linha problemática para o armazenamento para posterior investigação. Doutoramento em referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlunauthorizedaccess"></a>Código de erro: SqlUnauthorizedAccess

- **Mensagem:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa:** A credencial está incorreta ou a conta de login não pode aceder à Base de Dados SQL.

- **Recomendação**: Verifique se a conta de login tem autorização suficiente para aceder à Base de Dados SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Código de erro: SqlOpenConnectionTimeout

- **Mensagem:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa:** Pode ser falha transitória da Base de Dados SQL.

- **Recomendação**: Tente voltar a atualizar a cadeia de ligação de serviço ligada com um maior valor de tempo de ligação.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Código de erro: SqlAutoCreateTableTypeMapFailed

- **Mensagem:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: Tabela de criação automática não pode satisfazer o requisito de origem.

- **Recomendação**: Atualize o tipo de coluna em 'mapeamentos' ou crie manualmente a tabela de sumidouros no servidor-alvo.


### <a name="error-code--sqldatatypenotsupported"></a>Código de erro: SqlDataTypeNotSupported

- **Mensagem:**`A database operation failed. Check the SQL errors.`

- **Causa**: Se o problema ocorrer na fonte SQL e o erro estiver relacionado com o transbordo do SqlDateTime, o valor dos dados está acima da gama de tipo lógico (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Recomendação**: Molde o tipo para cadeiar na consulta SQL de origem ou no mapeamento da coluna de atividade de cópia, altere o tipo de coluna para 'String'.

- **Causa**: Se o problema ocorrer na pia SQL e o erro estiver relacionado com o transbordo Do SqlDateTime, o valor dos dados é sobre o intervalo permitido na tabela de sumidouros.

- **Recomendação**: Atualizar o tipo de coluna correspondente ao tipo 'datetime2' na tabela de sumidouros.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Código de erro: Procedimento SqlInvalidDbStored

- **Mensagem:**`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: O procedimento armazenado especificado não é válido. Pode ser causado por o procedimento armazenado não devolver nenhum dado.

- **Recomendação**: Validar o procedimento armazenado pelas Ferramentas SQL. Certifique-se de que o procedimento armazenado pode devolver os dados.


### <a name="error-code--sqlinvaliddbquerystring"></a>Código de erro: SqlInvalidDbQueryString

- **Mensagem:**`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa**: A consulta SQL especificada não é válida. Pode ser causado por que a consulta não devolva nenhum dado

- **Recomendação**: Validar a Consulta SQL por Ferramentas SQL. Certifique-se de que a consulta pode devolver os dados.


### <a name="error-code--sqlinvalidcolumnname"></a>Código de erro: SqlInvalidColumnName

- **Mensagem:**`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa:** Não consigo encontrar coluna. Possível configuração errada.

- **Recomendação**: Verifique a coluna na consulta, "estrutura" no conjunto de dados e "mapeamentos" na atividade.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Código de erro: SqlColumnNameMismatchByCaseSensitive

- **Mensagem:**`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Código de erro: SqlBatchWriteTimeout

- **Mensagem:**`Timeouts in SQL write operation.`

- **Causa:** Pode ser falha transitória da Base de Dados SQL.

- **Recomendação**: Por favor, tente novamente. Se o problema se reproduzir, contacte o suporte Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Código de erro: SqlBatchWriteTransactionFailed

- **Mensagem:**`SQL transaction commits failed`

- **Causa**: Se os detalhes de exceção indirem constantemente o tempo limite de transação, a latência da rede entre o tempo de integração e a base de dados é superior ao limiar de incumprimento em 30 segundos.

- **Recomendação**: Atualizar a cadeia de ligação de serviço sql com valor de 'tempo limite de ligação' igual a 120 ou superior e reexecutar a atividade.

- **Causa**: Se os detalhes de exceção intermitentemente disserem a sqlconnection quebrada, pode ser apenas falha de rede transitória ou problema lateral da Base de Dados SQL

- **Recomendação**: Por favor, tente novamente a atividade e reveja as métricas laterais da Base de Dados SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Código de erro: SqlBulkCopyInvalidColumnLength

- **Mensagem:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: SQL Copy a granel falhou devido a receber um comprimento de coluna inválido do cliente bcp.

- **Recomendação**: Para identificar qual a linha que encontra o problema, por favor, permita a funcionalidade de tolerância à falha na atividade de cópia, que pode redirecionar a linha problemática para o armazenamento para posterior investigação. Doutoramento em referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Código de erro: SqlConnectionIsClosed

- **Mensagem:**`The connection is closed by SQL Database.`

- **Causa**: A ligação SQL é fechada pela Base de Dados SQL quando a ligação de alta corrente e de terminação do servidor.

- **Recomendação**: O servidor remoto fechou a ligação SQL. Tente novamente. Se o problema se reproduzir, contacte o suporte Azure SQL.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Código de erro: SqlCreateTableFailedUnedUnsupportedType

- **Mensagem:**`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Error message: Conversão falhou ao converter de uma cadeia de caracteres para identificador único

- **Sintomas**: Quando copia dados da fonte de dados tabular (como o SQL Server) para o Armazém de Dados Azure SQL utilizando cópia encenada e PolyBase, atingiu o seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Causa**: Azure SQL Data Warehouse PolyBase não pode converter corda vazia para GUID.

- **Resolução**: No sink de atividade da Cópia, sob as definições da Polybase, delineie a opção "padrão de**utilização**do tipo " para falsa.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensagem de erro: Tipo de dados esperado: DECIMAL(x,x), Valor ofensivo

- **Sintomas**: Quando copia dados da fonte de dados tabular (como o SQL Server) para o DW SQL utilizando cópia encenada e PolyBase, atinge o seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Causa**: Azure SQL Data Warehouse Polybase não pode inserir cadeia vazia (valor nulo) na coluna decimal.

- **Resolução**: No sink de atividade da Cópia, sob as definições da Polybase, delineie a opção "padrão de**utilização**do tipo " para falsa.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Mensagem de erro: Mensagem de exceção java:HdfsBridge::CreateRecordReader

- **Sintomas:** Copia dados para o Armazém de Dados Azure SQL utilizando a PolyBase e atinge o seguinte erro:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Causa**: A causa possível é que o esquema (largura total da coluna) seja demasiado grande (maior que 1 MB). Verifique o esquema da tabela DW SQL alvo adicionando o tamanho de todas as colunas:

    - Int -> 4 bytes
    - Bigint -> 8 bytes
    - Varchar(n), char(n),binary n, varbinary(n) -> n bytes
    - Nvarchar(n), nchar(n) -> n*2 bytes
    - Data -> 6 bytes
    - Data/(2), data pequena -> 16 bytes
    - Datatimeoffset -> 20 bytes
    - Decimal -> 19 bytes
    - Flutuação -> 8 bytes
    - Dinheiro -> 8 bytes
    - Smallmoney -> 4 bytes
    - Real -> 4 bytes
    - Pequenos -> 2 bytes
    - Tempo - > 12 bytes
    - Tinyint -> 1 byte

- **Resolução**: Reduzir a largura da coluna para ser inferior a 1 MB

- Ou usar a abordagem de inserção a granel, desativando a Polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Mensagem de erro: A condição especificada com cabeçalho condicional HTTP não é satisfeita

- **Sintomas:** Utiliza a consulta SQL para retirar dados do Armazém de Dados Azure SQL e atingir o seguinte erro:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa**: Azure SQL Data Warehouse acerta em problemas consultando a tabela externa no Armazenamento Azure.

- **Resolução**: Faça a mesma consulta no SSMS e verifique se vê o mesmo resultado. Em caso afirmativo, abra um ticket de suporte para o Azure SQL Data Warehouse e indique o nome da base de dados e do servidor SQL DW para continuar a resolução de problemas.
            

## <a name="delimited-text-format"></a>Formato de texto delimitado

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Código de erro: DelimitedTextColumnNameNotAllowNull

- **Mensagem:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: Quando definido 'firstRowAsHeader' em atividade, a primeira linha será usada como nome de coluna. Este erro significa que a primeira linha contém valor vazio. Por exemplo: "ColunaA,ColunaB".

- **Recomendação**: Verifique a primeira linha e fixe o valor se houver valor vazio.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Código de erro: DelimitedTextMoreColumnsThanDefined

- **Mensagem:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Causa**: A contagem de colunas da linha problemática é grande do que a contagem de colunas da primeira linha. Pode ser causado por problemas de dados ou definições incorretas de carvão de delimitador/citação.

- **Recomendação**: Por favor, faça a contagem da linha na mensagem de erro, verifique a coluna da linha e corrija os dados.

- **Causa**: Se a contagem de colunas esperada for "1" na mensagem de erro, é possível que tenha especificado as definições erradas de compressão ou formato, o que fez com que a ADF analisasse erradamente os seus ficheiros.

- **Recomendação**: Verifique as definições do formato para se certificar de que corresponde ao ou aos ficheiros de origem.

- **Causa**: Se a sua fonte for uma pasta, é possível que os ficheiros sob a pasta especificada tenham esquemas diferentes.

- **Recomendação**: Certifique-se de que os ficheiros sob a pasta dada têm esquemas idênticos.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Código de erro: DelimitedTextIncorrectRowDelimiter

- **Mensagem:**`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Código de erro: DelimitedTextTooLargeColumnCount

- **Mensagem:**`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Código de erro: Definições detextodesinválidas

- **Mensagem:**`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dinâmica 365/Serviço Comum de Dados/CRM dinâmica

### <a name="error-code--dynamicscreateserviceclienterror"></a>Código de erro: DynamicsCreateServiceClientError

- **Mensagem:**`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa:** Este é um problema transitório no lado do servidor de dinâmica.

- **Recomendação**: Reexecutar o gasoduto. Se continuar a falhar, tente reduzir o paralelismo. Se ainda falhar, contacte o suporte da dinâmica.



## <a name="json-format"></a>Formato JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Código de erro: JsonInvalidArrayPathDefinition

- **Mensagem:**`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Código de erro: JsonEmptyJObjectData

- **Mensagem:**`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Código de erro: JsonNullValueValueInPathDefinition

- **Mensagem:**`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Código de erro: JsonUnsupportedHierarchicalComplexValue

- **Mensagem:**`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Código de erro: JsonConflictPartitionDiscoverySchema

- **Mensagem:**`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Código de erro: JsonInvalidDataFormat

- **Mensagem:**`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Código de erro: JsonInvalidDataMixedArrayAndObject

- **Mensagem:**`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Formato Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Código de erro: ParquetJavaInvocationException

- **Mensagem:**`An error occurred when invoking java, message: %javaException;.`

- **Causa**: Quando a mensagem de erro contém 'java.lang.OutOfMemory', 'Java heap space' e 'doubleCapacity', normalmente é um problema de gestão de memória na versão antiga do tempo de funcionamento da integração.

- **Recomendação**: Se estiver a utilizar o Tempo de Execução de Integração Auto-hospedado e a versão for superior a 3.20.7159.1, sugira o upgrade para a versão mais recente.

- **Causa**: Quando a mensagem de erro contém 'java.lang.OutOfMemory', o tempo de funcionamento da integração não tem recursos suficientes para processar os ficheiros.

- **Recomendação**: Limitar as corridas simultâneas no tempo de funcionação da integração. Para o Tempo de Execução de Integração Auto-hospedado, esforce-se até uma máquina poderosa com memória igual ou superior a 8 GB.

- **Causa**: Quando a mensagem de erro contém 'NullPointerReference', é possível que seja um erro transitório.

- **Recomendação**: Por favor, tente novamente. Se o problema persistir, contacte o suporte.


### <a name="error-code--parquetinvalidfile"></a>Código de erro: ParquetInvalidFile

- **Mensagem:**`File is not a valid parquet file.`

- **Causa:** Problema de ficheiro sinuoso.

- **Recomendação**: Verifique se a entrada é um ficheiro parquet válido.


### <a name="error-code--parquetnotsupportedtype"></a>Código de erro: ParquetNotSupportedType

- **Mensagem:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: O formato parquet não é suportado na Azure Data Factory.

- **Recomendação**: Verifique duas vezes os dados de origem. Consulte o médico: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Código de erro: Escala de Precisão ParquetMissedDecimal

- **Mensagem:**`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: Tente analisar a precisão e a escala do número, mas nenhuma informação é fornecida.

- **Recomendação**: 'Fonte' não devolve precisão e escala corretas. Verifique a precisão e a escala da coluna de emissão.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Código de erro: ParquetInvalidDecimalPrecisionScale

- **Mensagem:**`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa:** O esquema é inválido.

- **Recomendação**: Verifique a precisão e a escala da coluna de emissão.


### <a name="error-code--parquetcolumnnotfound"></a>Código de erro: ParquetColumnNotFound

- **Mensagem:**`Column %column; does not exist in Parquet file.`

- **Causa**: O esquema de origem é incompatível com o esquema da pia.

- **Recomendação**: Verifique os 'mapeamentos' na 'atividade'. Certifique-se de que a coluna de origem pode ser mapeada para a coluna do lavatório direito.


### <a name="error-code--parquetinvaliddataformat"></a>Código de erro: ParquetInvalidDataFormatForma

- **Mensagem:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: Os dados não podem ser convertidos em tipo especificado em mapeamentos.source

- **Recomendação**: Verifique os dados de origem ou especifique o tipo de dados correto para esta coluna no mapeamento da coluna de atividade de cópia. Consulte o médico: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Código de erro: ParquetDataCountNotMatchColumnCount

- **Mensagem:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**: Contagem de colunas de origem e desfasamento da contagem da coluna do lavatório

- **Recomendação**: A contagem de colunas de origem de verificação dupla é a mesma que a contagem da coluna do lavatório em 'mapeamento'.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Código de erro: ParquetDataTypeNotMatchColumnType

- **Mensagem**: O tipo de dados %srcType; não é compatível com o tipo de coluna %dstType; na coluna '%columnIndex;'.

- **Causa**: Os dados provenientes da fonte não podem ser convertidos para dactilografados definidos em pia

- **Recomendação**: Por favor, especifique um tipo correto em mapeamento.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Código de erro: ParquetBridgeInvalidData

- **Mensagem:**`%message;`

- **Causa**: Valor dos dados sobre a limitação

- **Recomendação**: Por favor, tente novamente. Se o problema persistir, contacte-nos.


### <a name="error-code--parquetunsupportedinterpretation"></a>Código de erro: Interpretação Não Suportada Parquet

- **Mensagem:**`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Causa**: Cenário não suportado

- **Recomendação**: 'ParquetInterpretFor' não deve ser 'sparkSql'.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Código de erro: ParquetUnsupportFileLevelCompressionOption

- **Mensagem:**`File level compression is not supported for Parquet.`

- **Causa**: Cenário não suportado

- **Recomendação**: Remova o 'CompressionType' na carga útil.



## <a name="general-copy-activity-error"></a>Erro geral de atividade de cópia

### <a name="error-code--jrenotfound"></a>Código de erro: JreNotFound

- **Mensagem:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa:** O tempo de execução da integração auto-hospedado não pode encontrar Java Runtime. O Tempo de Execução de Java é necessário para ler uma fonte específica.

- **Recomendação**: Verifique o ambiente de execução da sua integração, o doc de referência:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Código de erro: WildcardPathSinkNotSupported

- **Mensagem:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa:** Conjunto de dados do lavatório não suporta wildcard.

- **Recomendação**: Verifique o conjunto de dados do lavatório e fixe o caminho sem o valor wildcard.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Código de erro: MappingInvalidPropertyWithEmptyValue

- **Mensagem:**`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Código de erro: MappingInvalidPropertyWithNamePathAnddinal

- **Mensagem:**`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Código de erro: MappingDuplicatedOrdinal

- **Mensagem:**`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Código de erro: MappingInvalidOrdinalForSinkColumn

- **Mensagem:**`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Próximos passos

Para obter mais ajuda para resolução de problemas, experimente estes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de funcionalidade sinuosos da Fábrica de Dados](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum para fábrica de dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre data factory](https://twitter.com/hashtag/DataFactory)
            
