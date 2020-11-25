---
title: Resolver Problemas dos Conectores do Azure Data Factory
description: Saiba como resolver problemas de conector na Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 2e54c0b09c3dbe398b0522d0ad9ad2314e29ed26
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023845"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Resolver Problemas dos Conectores do Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para conectores na Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="error-code--azurebloboperationfailed"></a>Código de erro: AzureBlobOperationFailed

- **Mensagem:**`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa:** Problema de operação de armazenamento de bolhas.

- **Recomendação**: Verifique o erro em detalhes. Consulte o documento de ajuda blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Contacte a equipa de armazenamento se precisar de ajuda.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Código de erro: AzureBlobServiceNotReturnExpectedDataLength

- **Mensagem:**`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Código de erro: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Mensagem:**`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Código de erro: AzureStorageOperationFailedConcurrentWrite

- **Mensagem:**`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Error message: O tamanho do pedido é demasiado grande

- **Sintomas**: Copia dados em Azure Cosmos DB com o tamanho do lote de escrita predefinido, e o erro de impacto *" O tamanho do pedido é demasiado **grande**"*.

- **Causa:** Cosmos DB limita o tamanho de um único pedido a 2 MB. A fórmula é, Tamanho do Pedido = Tamanho único do documento * Escreva o tamanho do lote. Se o tamanho do seu documento for grande, o comportamento padrão resultará num tamanho de pedido demasiado grande. Pode sintonizar o tamanho do lote de escrita.

- **Resolução**: Na atividade de cópia, reduza o valor do "tamanho do lote de escrita" (o valor padrão é de 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Error message: Unique index constraint violation

- **Sintomas**: Ao copiar dados para Cosmos DB, atinge o seguinte erro:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Causa**: Existem duas causas possíveis:

    - Se utilizar **Insira** como comportamento de escrita, este erro significa que os dados de origem têm linhas/objetos com o mesmo ID.

    - Se utilizar **o Upsert** como comportamento de escrita e definir outra chave única para o recipiente, este erro significa que os dados de origem têm linhas/objetos com diferentes IDs mas o mesmo valor para a chave única definida.

- **Resolução:** 

    - Por causa1, coloque **Upsert** como comportamento de escrita.
    - Para a causa 2, certifique-se de que cada documento tem um valor diferente para chave única definida.

### <a name="error-message-request-rate-is-large"></a>Error message: A taxa de pedido é grande

- **Sintomas**: Ao copiar dados para Cosmos DB, atinge o seguinte erro:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Causa**: As unidades de pedido utilizadas são maiores do que a RU disponível configurada na Cosmos DB. Saiba como cosmos DB calcula RU [daqui.](../cosmos-db/request-units.md#request-unit-considerations)

- **Resolução**: Eis duas soluções:

    1. **Aumente o contentor RU** para um maior valor em Cosmos DB, o que melhorará o desempenho da atividade da cópia, embora incorre em mais custos em Cosmos DB. 

    2. Diminuir **a escreverBatchSize** para valor menor (como 1000) e definir **paralelosCopias** para um valor menor como 1, o que tornará o desempenho da cópia pior do que o atual, mas não incorrerá em mais custos em Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Coluna em falta no mapeamento de coluna

- **Sintomas**: Quando importa esquemas para Cosmos DB para mapeamento de colunas, algumas colunas estão em falta. 

- **Causa**: ADF infere o esquema dos primeiros 10 documentos da Cosmos DB. Se algumas colunas/propriedades não tiverem valor nesses documentos, não serão detetadas pela ADF, portanto, não aparecerão.

- **Resolução**: Pode sintonizar a consulta como abaixo para impor a coluna para aparecer no resultado definido com valor vazio: (assumir: "impossível" coluna está em falta nos primeiros 10 documentos). Em alternativa, pode adicionar manualmente a coluna para mapeamento.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Error message: The GuidRepresentation for the reader is CSharpLegacy

- **Sintomas**: Ao copiar dados da Cosmos DB MongoAPI/MongoDB com o campo UUID, atingiu o seguinte erro:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Causa**: Existem duas formas de representar a UUID em BSON - UuidStardard e UuidLegacy. Por padrão, o UuidLegacy é utilizado para ler dados. Atingirá um erro se os seus dados UUID em MongoDB forem UuidStandard.

- **Resolução**: Na cadeia de ligação MongoDB, adicione opção "**uuidRepresentation=standard**". Para obter mais informações, consulte a [cadeia de ligação MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

### <a name="error-code--adlsgen2operationfailed"></a>Código de erro: AdlsGen2OperationFailed

- **Mensagem:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa**: A ADLS Gen2 lança o erro indicando que a operação falhou.

- **Recomendação**: Verifique a mensagem de erro detalhada lançada pela ADLS Gen2. Se for causado por falha transitória, por favor, redaça. Se precisar de mais ajuda, contacte o suporte do Azure Storage e forneça o ID do pedido por mensagem de erro.

- **Causa**: Quando a mensagem de erro contiver 'Proibido', o principal de serviço ou identidade gerida que utiliza pode não ter autorização suficiente para aceder à ADLS Gen2.

- **Recomendação**: Consulte o documento de ajuda: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Causa**: Quando a mensagem de erro contém 'InternalServerError', o erro é devolvido pela ADLS Gen2.

- **Recomendação**: Pode ser causado por falha transitória, por favor, redaça. Se o problema persistir, contacte o suporte do Azure Storage e forneça o ID do pedido por mensagem de erro.


### <a name="error-code--adlsgen2invalidurl"></a>Código de erro: AdlsGen2InvalidUrl

- **Mensagem:**`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Código de erro: AdlsGen2InvalidFolderPath

- **Mensagem:**`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Código de erro: AdlsGen2OperationFailedConcurrentWrite

- **Mensagem:**`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>Código de erro: AdlsGen2TimeoutError

- **Mensagem:**`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Mensagem de erro: A ligação subjacente foi fechada: Não foi possível estabelecer uma relação de confiança para o canal seguro SSL/TLS.

- **Sintomas**: A atividade de cópia falha com o seguinte erro: 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **Causa**: A validação do certificado falhou durante o aperto de mão TLS.

- **Resolução**: Resolução : Resolução: Utilize cópia encenada para saltar a validação TLS para a ADLS Gen1. Você precisa reproduzir este problema e recolher vestígios de netmon, e em seguida, envolver a sua equipa de rede para verificar a configuração da rede local.

    ![Resolução de problemas ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Error message: The remote server retornou um erro: (403) Proibido

- **Sintomas**: A atividade de cópia falha com o seguinte erro: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Causa**: Uma causa possível é que o principal de serviço ou identidade gerida que utiliza não tem permissão para aceder a determinada pasta/ficheiro.

- **Resolução**: Conceda permissões correspondentes em todas as pastas e sub-dobradores que necessita de copiar. Consulte [este doc](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Error message: Failed to access token using service principal. Erro ADAL: service_unavailable

- **Sintomas**: A atividade de cópia falha com o seguinte erro:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Causa**: Quando o Service Token Server (STS) propriedade do Azure Ative Directory não está indisponível, ou seja, demasiado ocupado para lidar com pedidos, devolve um erro HTTP 503. 

- **Resolução**: Reenexsitar a atividade da cópia após vários minutos.
                  

## <a name="azure-synapse-analytics-formerly-sql-data-warehouseazure-sql-databasesql-server"></a>Azure Synapse Analytics (anteriormente SQL Data Warehouse)/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Código de erro: SqlFailedToConnect

- **Mensagem:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Causa**: Se a mensagem de erro contiver "SqlException", a Base de Dados SQL lança o erro indicando que alguma operação específica falhou.

- **Recomendação**: Procure por código de erro SQL neste doc de referência para mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Se precisar de mais ajuda, contacte o suporte da Azure SQL.

- **Causa**: Se a mensagem de erro contiver "Cliente com endereço IP '...' não é permitido aceder ao servidor", e está a tentar ligar-se à Base de Dados Azure SQL, normalmente é causada por um problema de firewall da Base de Dados Azure SQL.

- **Recomendação**: Na configuração lógica da firewall do servidor SQL, permita que os serviços e recursos do Azure acedam a este servidor. Doc de referência: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Código de erro: SqlOperationFailed

- **Mensagem:**`A database operation failed. Please search error to get more details.`

- **Causa**: Se a mensagem de erro contiver "SqlException", a Base de Dados SQL lança o erro indicando que alguma operação específica falhou.

- **Recomendação**: Se o erro sql não for claro, tente alterar a base de dados para o nível de compatibilidade mais recente '150'. Pode lançar erros SQL de versão mais recentes. Consulte o [doc de pormenor](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Para obter problemas de resolução de problemas de SQL, procure por código de erro SQL neste doc de referência para mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Se precisar de mais ajuda, contacte o suporte da Azure SQL.

- **Causa**: Se a mensagem de erro contiver "PdwManagedToNativeInteropException", normalmente é causada por desfasamento entre os tamanhos das colunas de origem e pia.

- **Recomendação**: Verifique o tamanho das colunas de origem e de lavatório. Se precisar de mais ajuda, contacte o suporte da Azure SQL.

- **Causa**: Se a mensagem de erro contiver "InvalidOperationException", normalmente é causada por dados de entrada inválidos.

- **Recomendação**: Para identificar qual a linha que encontra o problema, por favor, permita a função de tolerância à falha na atividade da cópia, que pode redirecionar linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Doc de referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .



### <a name="error-code--sqlunauthorizedaccess"></a>Código de erro: SqlUnauthorizedAccess

- **Mensagem:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa:** A credencial está incorreta ou a conta de login não pode aceder à Base de Dados SQL.

- **Recomendação**: Verifique se a conta de login tem permissão suficiente para aceder à Base de Dados SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Código de erro: SqlOpenConnectionTimeout

- **Mensagem:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa**: Pode ser falha transitória da Base de Dados SQL.

- **Recomendação**: Retentou atualizar o fio de ligação de serviço ligado com maior valor de tempo de ligação.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Código de erro: SqlAutoCreateTableTypeMapFailed

- **Mensagem:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: A tabela de criação automática não pode satisfazer os requisitos de origem.

- **Recomendação**: Atualize o tipo de coluna em 'mapeamentos', ou crie manualmente a tabela da pia no servidor-alvo.


### <a name="error-code--sqldatatypenotsupported"></a>Código de erro: SqlDataTypeNotSupported

- **Mensagem:**`A database operation failed. Check the SQL errors.`

- **Causa**: Se o problema acontecer na fonte SQL e o erro estiver relacionado com o transbordo sqlDateTime, o valor dos dados está sobre a gama de tipo lógica (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Recomendação**: Lançar o tipo de corda na consulta SQL de origem, ou na cópia de mapeamento de colunas de atividade alterar o tipo de coluna para 'String'.

- **Causa**: Se o problema acontecer na pia SQL e o erro estiver relacionado com o transbordo sqlDateTime, o valor dos dados está sobre a gama permitida na tabela do lavatório.

- **Recomendação**: Atualizar o tipo de coluna correspondente ao tipo 'datatime2' na tabela do lavatório.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Código de erro: SqlInvalidDbStoredProcedure

- **Mensagem:**`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: O procedimento armazenado especificado não é válido. Pode ser causado por o procedimento armazenado não devolver quaisquer dados.

- **Recomendação**: Valide o procedimento armazenado por Ferramentas SQL. Certifique-se de que o procedimento armazenado pode devolver os dados.


### <a name="error-code--sqlinvaliddbquerystring"></a>Código de erro: SqlInvalidDbQueryString

- **Mensagem:**`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa**: A consulta SQL especificada não é válida. Pode ser causado por que a consulta não devolva quaisquer dados

- **Recomendação**: Validar a Consulta SQL por Ferramentas SQL. Certifique-se de que a consulta pode devolver os dados.


### <a name="error-code--sqlinvalidcolumnname"></a>Código de erro: SqlInvalidColumnName

- **Mensagem:**`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa:** Não consigo encontrar coluna. Possível configuração errada.

- **Recomendação**: Verifique a coluna na consulta, 'estrutura' no conjunto de dados e 'mapeamentos' na atividade.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Código de erro: SqlColumnNameMismatchByCaseSensitive

- **Mensagem:**`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Código de erro: SqlBatchWriteTimeout

- **Mensagem:**`Timeouts in SQL write operation.`

- **Causa**: Pode ser falha transitória da Base de Dados SQL.

- **Recomendação**: Retry. Se o problema voltar a ser repro, contacte o suporte da Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Código de erro: SqlBatchWriteTransactionFailed

- **Mensagem:**`SQL transaction commits failed`

- **Causa**: Se os detalhes da exceção disserem constantemente o tempo limite de transação, a latência da rede entre o tempo de funcionação da integração e a base de dados é superior ao limiar padrão em 30 segundos.

- **Recomendação**: Atualizar a cadeia de ligação de serviço ligada sql com o valor 'intervalo de ligação' é igual a 120 ou mais e reexame a atividade.

- **Causa:** Se os detalhes da exceção disserem intermitentemente que a ligação sql se partiu, pode ser apenas uma falha de rede transitória ou um problema lateral da Base de Dados SQL

- **Recomendação**: Recandidexar a atividade e rever as métricas laterais da Base de Dados SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Código de erro: SqlBulkCopyInvalidColumnLength

- **Mensagem:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: SQL Bulk Copy falhou devido a receber um comprimento de coluna inválido do cliente bcp.

- **Recomendação**: Para identificar qual a linha que encontra o problema, por favor, permita a função de tolerância à falha na atividade da cópia, que pode redirecionar linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Doc de referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Código de erro: SqlConnectionIsClosed

- **Mensagem:**`The connection is closed by SQL Database.`

- **Causa**: A ligação SQL é fechada pela SQL Database quando a execução e a ligação terminam o servidor.

- **Recomendação:** Servidor remoto fechou a ligação SQL. Redaçar. Se o problema voltar a ser repro, contacte o suporte da Azure SQL.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Código de erro: SqlCreateTableFailedUnsupportedType

- **Mensagem:**`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Error message: Conversion failed when conversioning from a character string to uniqueidentifier

- **Sintomas**: Ao copiar dados de fonte de dados tabulares (como o SQL Server) para a Azure Synapse Analytics utilizando cópia encenada e PolyBase, acertou no seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Causa**: Azure Synapse Analytics PolyBase não pode converter corda vazia para GUID.

- **Resolução**: Na pia de atividade de cópia, sob as definições de Polybase, defina a opção "**use opção padrão do tipo**" para falso.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensagem de erro: Tipo de dados esperado: DECIMAL(x,x), Valor ofensivo

- **Sintomas**: Ao copiar dados de fonte de dados tabulares (como o SQL Server) para a Azure Synapse Analytics utilizando cópia encenada e PolyBase, acertou no seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Causa**: Azure Synapse Analytics Polybase não pode inserir a corda vazia (valor nulo) na coluna decimal.

- **Resolução**: Na pia de atividade de cópia, sob as definições de Polybase, defina a opção "**use opção padrão do tipo**" para falso.

### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Mensagem de erro: Mensagem de exceção Java: HdfsBridge::CreateRecordReader

- **Sintomas**: Copia dados em Azure Synapse Analytics utilizando a PolyBase e atinge o seguinte erro:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Causa**: A causa possível é que o esquema (largura total da coluna) seja demasiado grande (superior a 1 MB). Verifique o esquema da tabela target Azure Synapse Analytics adicionando o tamanho de todas as colunas:

    - Int -> 4 bytes
    - Bigint -> 8 bytes
    - Varchar(n),char(n),binário(n), varbinário(n) -> n bytes
    - Nvarchar(n), nchar(n) -> n*2 bytes
    - Data -> 6 bytes
    - Data/((2), horário de data -> 16 bytes
    - Datatimeoffset -> 20 bytes
    - Decimal -> 19 bytes
    - Boia -> 8 bytes
    - Dinheiro -> 8 bytes
    - Smallmoney -> 4 bytes
    - Real -> 4 bytes
    - Smallint -> 2 bytes
    - Tempo -> 12 bytes
    - Tinyint -> 1 byte

- **Resolução**: Reduzir a largura da coluna para ser inferior a 1 MB

- Ou use a abordagem de inserção a granel desativando a Base Polibase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Error message: A condição especificada através do ou dos cabeçalho condicional HTTP não é cumprida

- **Sintomas**: Utilize a consulta SQL para retirar dados da Azure Synapse Analytics e atingir o seguinte erro:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa**: Azure Synapse Analytics hit issue consultando a tabela externa no Azure Storage.

- **Resolução**: Faça a mesma consulta em SSMS e verifique se vê o mesmo resultado. Se sim, abra um bilhete de suporte para a Azure Synapse Analytics e forneça o seu servidor Esporão Azure Synapse e o nome da base de dados para uma nova resolução de problemas.
            

## <a name="delimited-text-format"></a>Formato de texto delimitado

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Código de erro: DelimitedTextColumnNameNotAllowNull

- **Mensagem:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: Quando definir 'firstRowAsHeader' em atividade, a primeira linha será usada como nome de coluna. Este erro significa que a primeira linha contém valor vazio. Por exemplo: 'ColunaA,, ColunaB'.

- **Recomendação**: Verifique a primeira linha e fixe o valor se houver valor vazio.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Código de erro: DelimitedTextMoreColumnsThanDefined

- **Mensagem:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Causa:** A contagem de colunas problemáticas é grande do que a contagem de colunas da primeira linha. Pode ser causado por problemas de dados ou configurações incorretas de delimiter/citação de colunas.

- **Recomendação**: Obtenha a contagem de linha em mensagem de erro, verifique a coluna da linha e corrija os dados.

- **Causa**: Se a contagem de colunas esperada for "1" na mensagem de erro, é possível que tenha especificado configurações erradas de compressão ou formato, o que fez com que a ADF analisasse indevidamente os seus ficheiros.

- **Recomendação**: Verifique as definições do formato para se certificar de que corresponde ao(s) dos seus ficheiros de origem.

- **Causa**: Se a sua fonte for uma pasta, é possível que os ficheiros sob a pasta especificada tenham esquemas diferentes.

- **Recomendação**: Certifique-se de que os ficheiros sob a pasta dada têm esquemas idênticos.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Código de erro: DelimitedTextIncorrectRowDelimiter

- **Mensagem:**`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Código de erro: DelimitedTextTooLargeColumnCount

- **Mensagem:**`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Código de erro: DelimitedTextInvalidSettings

- **Mensagem:**`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dinâmica 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Código de erro: DynamicsCreateServiceClientError

- **Mensagem:**`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa**: Este é um problema transitório no lado do servidor dinâmico.

- **Recomendação:** Reencaúndio do gasoduto. Se continuar a falhar, tente reduzir o paralelismo. Se ainda falhar, contacte o suporte da dinâmica.



## <a name="json-format"></a>Formato JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Código de erro: JsonInvalidArrayPathDefinition

- **Mensagem:**`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Código de erro: JsonEmptyJObjectData

- **Mensagem:**`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Código de erro: JsonNullValueInPathDefinition

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

### <a name="error-code--parquetjavainvocationexception"></a>Código de erro: ParquetJavaInvocationExcepção

- **Mensagem:**`An error occurred when invoking java, message: %javaException;.`

- **Causa**: Quando a mensagem de erro contém 'java.lang.OutOfMemory', 'Java heap space' e 'doubleCapacity', normalmente é um problema de gestão da memória na versão antiga do tempo de execução da integração.

- **Recomendação**: Se estiver a utilizar o Tempo de Execução de Integração Auto-hospedado e a versão tiver um prazo anterior a 3.20.7159.1, é sugerido que atualize para a versão mais recente.

- **Causa**: Quando a mensagem de erro contém 'java.lang.OutOfMemory', o tempo de execução da integração não tem recursos suficientes para processar os ficheiros.

- **Recomendação**: Limitar os percursos simultâneos no tempo de integração. Para o tempo de funcionação de integração auto-hospedado, dimensione até uma máquina poderosa com memória igual ou superior a 8 GB.

- **Causa**: Quando a mensagem de erro contém 'NullPointerReference', é possível que seja um erro transitório.

- **Recomendação**: Retry. Se o problema persistir, contacte o suporte.


### <a name="error-code--parquetinvalidfile"></a>Código de erro: ParquetInvalidFile

- **Mensagem:**`File is not a valid parquet file.`

- **Causa**: Problema de arquivo parquet.

- **Recomendação**: Verifique se a entrada é um ficheiro parquet válido.


### <a name="error-code--parquetnotsupportedtype"></a>Código de erro: ParquetNotSupportedType

- **Mensagem:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: O formato parquet não é suportado na Azure Data Factory.

- **Recomendação:** Verifique novamente os dados de origem. Consulte o doc: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Código de erro: ParquetMissedDecimalPrecisionScale

- **Mensagem:**`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: Tente analisar a precisão e a escala do número, mas não é fornecida essa informação.

- **Recomendação**: 'Fonte' não devolve a precisão e a escala corretas. Verifique a precisão e a escala da coluna de emissão.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Código de erro: ParquetInvalidDecimalPrecisionScale

- **Mensagem:**`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa**: O esquema é inválido.

- **Recomendação**: Verifique a precisão e a escala da coluna de emissão.


### <a name="error-code--parquetcolumnnotfound"></a>Código de erro: ParquetColumnNotFound

- **Mensagem:**`Column %column; does not exist in Parquet file.`

- **Causa**: O esquema de origem é incompatível com o esquema da pia.

- **Recomendação**: Verifique os 'mapeamentos' em "atividade". Certifique-se de que a coluna de origem pode ser mapeada para a coluna da pia direita.


### <a name="error-code--parquetinvaliddataformat"></a>Código de erro: ParquetInvalidDataFormat

- **Mensagem:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: Os dados não podem ser convertidos em tipo especificado em mapeamentos.source

- **Recomendação:** Verifique novamente os dados de origem ou especifique o tipo de dados correto para esta coluna no mapeamento da coluna de funções de cópia. Consulte o doc: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Código de erro: ParquetDataCountNotMatchColumnCount

- **Mensagem:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**: Contagem de colunas de origem e incompatibilidade da contagem de colunas de sumidouro

- **Recomendação**: A contagem de colunas de verificação dupla é a mesma que a contagem de colunas de pia em 'mapeamento'.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Código de erro: ParquetDataTypeNotMatchColumnType

- **Mensagem**: O tipo de dados %srcType; não corresponde ao tipo de coluna %dstType; na coluna '%colunaIndex;'.

- **Causa**: Os dados da fonte não podem ser convertidos para dactilografado definidos na pia

- **Recomendação**: Especifique um tipo correto em mapeamento.pia.


### <a name="error-code--parquetbridgeinvaliddata"></a>Código de erro: ParquetBridgeInvalidData

- **Mensagem:**`%message;`

- **Causa**: Valor dos dados sobre a limitação

- **Recomendação**: Retry. Se o problema persistir, contacte-nos.


### <a name="error-code--parquetunsupportedinterpretation"></a>Código de erro: ParquetUnsupportedInterpretation

- **Mensagem:**`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Causa**: Cenário não apoiado

- **Recomendação**: "ParquetInterpretFor" não deve ser 'sparkSql'.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Código de erro: ParquetUnsupportFileLevelCompressionOp

- **Mensagem:**`File level compression is not supported for Parquet.`

- **Causa**: Cenário não apoiado

- **Recomendação**: Remova 'CompressionType' na carga útil.



## <a name="general-copy-activity-error"></a>Erro de atividade de cópia geral

### <a name="error-code--jrenotfound"></a>Código de erro: JreNotFound

- **Mensagem:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa:** O tempo de integração auto-hospedado não encontra o tempo de funcionação de Java. O Tempo de Execução de Java é necessário para a leitura de uma fonte específica.

- **Recomendação**: Verifique o seu ambiente de tempo de execução de integração, o doc de referência: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Código de erro: WildcardPathSinkNotSupy

- **Mensagem:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa:** Conjunto de dados de afundanço não suporta wildcard.

- **Recomendação**: Verifique o conjunto de dados da pia e corrija o caminho sem valor wildcard.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Código de erro: MappingInvalidPropertyWithEmptyValue

- **Mensagem:**`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Código de erro: MappingInvalidPropertyWithNamePathAndOrdinal

- **Mensagem:**`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Código de erro: MappingDuplicatedOrdinal

- **Mensagem:**`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Código de erro: MappingInvalidOrdinalForSinkColumn

- **Mensagem:**`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](/answers/topics/azure-data-factory.html)
*  [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
