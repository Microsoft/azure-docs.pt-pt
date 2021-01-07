---
title: Resolver Problemas dos Conectores do Azure Data Factory
description: Saiba como resolver problemas de conector na Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 68547b8fb673cd54b7c21963ede122553bbbc390
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967128"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Resolver Problemas dos Conectores do Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para conectores na Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="error-code-azurebloboperationfailed"></a>Código de erro: AzureBlobOperationFailed

- **Mensagem:**`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa:** Problema de operação de armazenamento de bolhas.

- **Recomendação**: Verifique o erro em detalhes. Consulte o documento de ajuda blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Contacte a equipa de armazenamento se precisar de ajuda.


### <a name="invalid-property-during-copy-activity"></a>Propriedade inválida durante a atividade de cópia

- **Mensagem:**`Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **Causa**: O tipo definido no conjunto de dados é inconsistente com o tipo de fonte/pia definido na atividade da cópia.

- **Resolução**: Edite o conjunto de dados ou a definição de JSON do pipeline para tornar os tipos consistentes e refazer a implementação.


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

    - **Aumente o contentor RU** para um maior valor em Cosmos DB, o que melhorará o desempenho da atividade da cópia, embora incorre em mais custos em Cosmos DB. 
    - Diminuir **a escreverBatchSize** para valor menor (como 1000) e definir **paralelosCopias** para um valor menor como 1, o que tornará o desempenho da cópia pior do que o atual, mas não incorrerá em mais custos em Cosmos DB.

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
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>Código de erro: CosmosDbSqlApiOperaçãoFailada

- **Mensagem:**`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Causa**: CosmosDbSqlApi operação atingiu problema.

- **Recomendação**: Verifique o erro em detalhes. Consulte o [documento de ajuda cosmosDb](https://docs.microsoft.com/azure/cosmos-db/troubleshoot-dot-net-sdk). Contacte a equipa da CosmosDb se precisar de ajuda.


## <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Mensagem de erro: A ligação subjacente foi fechada: Não foi possível estabelecer uma relação de confiança para o canal seguro SSL/TLS.

- **Sintomas**: A atividade de cópia falha com o seguinte erro: 

    ```
    Message: ErrorCode = `UserErrorFailedFileOperation`, Error Message = `The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`.
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


## <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

### <a name="error-code-adlsgen2operationfailed"></a>Código de erro: ADLSGen2OperationFailed

- **Mensagem:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa**: A ADLS Gen2 lança o erro indicando que a operação falhou.

- **Recomendação**: Verifique a mensagem de erro detalhada lançada pela ADLS Gen2. Se for causado por falha transitória, por favor, redaça. Se precisar de mais ajuda, contacte o suporte do Azure Storage e forneça o ID do pedido por mensagem de erro.

- **Causa**: Quando a mensagem de erro contiver 'Proibido', o principal de serviço ou identidade gerida que utiliza pode não ter autorização suficiente para aceder à ADLS Gen2.

- **Recomendação**: Consulte o documento de ajuda: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Causa**: Quando a mensagem de erro contém 'InternalServerError', o erro é devolvido pela ADLS Gen2.

- **Recomendação**: Pode ser causado por falha transitória, por favor, redaça. Se o problema persistir, contacte o suporte do Azure Storage e forneça o ID do pedido por mensagem de erro.

### <a name="request-to-adls-gen2-account-met-timeout-error"></a>Pedido à conta da ADLS Gen2 cumpriu erro de tempo limite

- **Mensagem**: Código de erro = `UserErrorFailedBlobFSOperation` , Mensagem de Erro = `BlobFS operation failed for: A task was canceled` .

- **Causa**: O problema é causado pelo erro de tempo limite de sumidouro da ADLS Gen2, que acontece principalmente na máquina de infravermelhos auto-hospedada.

- **Recomendação:** 

    - Coloque a sua máquina de INFRAVERMELHO auto-hospedada e direcione a conta ADLS Gen2 na mesma região, se possível. Isto pode evitar erros de tempo limite aleatórios e ter um melhor desempenho.

    - Verifique se existe alguma definição de rede especial como o ExpressRoute e certifique-se de que a rede tem largura de banda suficiente. Sugere-se que reduza a definição de postos de trabalho simultâneos em termos de IR, quando a largura de banda global é baixa, através da qual pode evitar a concorrência de recursos de rede em vários postos de trabalho simultâneos.

    - Utilize um tamanho de bloco menor para uma cópia não binária para atenuar tal erro de tempo limite se o tamanho do ficheiro for moderado ou pequeno. Consulte o [Bloco de Colocação de Armazenamento blob](https://docs.microsoft.com/rest/api/storageservices/put-block).

       Para especificar o tamanho do bloco personalizado, pode editar a propriedade em .json editor:
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-file-storage"></a>Armazenamento de Ficheiros do Azure

### <a name="error-code--azurefileoperationfailed"></a>Código de erro: AzureFileOperationFailed

- **Mensagem:**`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Causa**: Problema de funcionamento da operação de armazenamento de ficheiros Azure.

- **Recomendação**: Verifique o erro em detalhes. Consulte o documento de ajuda do Ficheiro Azure: https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes . Contacte a equipa de armazenamento se precisar de ajuda.


## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse Analytics/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Código de erro: SqlFailedToConnect

- **Mensagem:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Causa**: Azure SQL: Se a mensagem de erro contiver "SqlErrorNumber=47073", significa que o acesso à rede pública é negado na definição de conectividade.

- **Recomendação**: Na firewall Azure SQL, desconfiem da opção "Negar o acesso à rede pública" para "Não". Saiba mais https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access com.

- **Causa**: Azure SQL: Se a mensagem de erro contiver código de erro SQL, como "SqlErrorNumber=[errorcode]", consulte o guia de resolução de problemas do Azure SQL.

- **Recomendação**: Saiba mais a partir de https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues .

- **Causa**: Verifique se a porta 1433 está na lista de autorizações de firewall.

- **Recomendação**: Siga com este documento de referência: https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by- .

- **Causa**: Se a mensagem de erro contiver "SqlException", a Base de Dados SQL lança o erro indicando que alguma operação específica falhou.

- **Recomendação**: Procure por código de erro SQL neste doc de referência para mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Se precisar de mais ajuda, contacte o suporte da Azure SQL.

- **Causa**: Se se trata de um problema transitório (por exemplo, ligação de rede instáctica), adicione novamente a política de atividade para atenuar.

- **Recomendação**: Siga este documento de referência: https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy .

- **Causa**: Se a mensagem de erro contiver "Cliente com endereço IP '...' não é permitido aceder ao servidor", e está a tentar ligar-se à Base de Dados Azure SQL, normalmente é causada por um problema de firewall da Base de Dados Azure SQL.

- **Recomendação**: Na configuração de firewall do Azure SQL Server, permita que os serviços e recursos do Azure acedam a esta opção de servidor. Doc de referência: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


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

- **Resolução:** 
    - Reduza a largura da coluna para ser inferior a 1 MB.
    - Ou utilize a abordagem de inserção em massa ao desativar o Polybase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Error message: A condição especificada através do ou dos cabeçalho condicional HTTP não é cumprida

- **Sintomas**: Utilize a consulta SQL para retirar dados da Azure Synapse Analytics e atingir o seguinte erro:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Causa**: Azure Synapse Analytics hit issue consultando a tabela externa no Azure Storage.

- **Resolução**: Faça a mesma consulta em SSMS e verifique se vê o mesmo resultado. Se sim, abra um bilhete de suporte para a Azure Synapse Analytics e forneça o seu servidor Esporão Azure Synapse e o nome da base de dados para uma nova resolução de problemas.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>O nível de desempenho é baixo e leva a falha de cópia

- **Sintomas**: Abaixo a mensagem de erro ocorreu ao copiar dados na Base de Dados Azure SQL: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Causa**: Está a ser utilizada a Base de Dados Azure SQL S1, que atingiu os limites de IO neste caso.

- **Resolução**: Atualizar o nível de desempenho da Base de Dados Azure SQL para corrigir o problema. 


### <a name="sql-table-cannot-be-found"></a>A Mesa SQL não pode ser encontrada 

- **Sintomas**: Ocorreu erro ao copiar dados da Hybrid para a tabela do Servidor SQL on-prem:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Causa**: A conta atual sql não tem permissão suficiente para executar pedidos emitidos por .NET SqlBulkCopy.WriteToServer.

- **Resolução**: Mude para uma conta SQL mais privilegiada.


### <a name="error-message-string-or-binary-data-would-be-truncated"></a>Error message: String or binary data would be truncated

- **Sintomas**: Ocorreu erro ao copiar dados para a tabela do Servidor SQL On-prem/Azure: 

- **Causa**: A definição de esquema de mesa Cx Sql tem uma ou mais colunas com menos comprimento do que o esperado.

- **Resolução**: Tentar seguir os passos para corrigir o problema:

    1. Aplicar a [tolerância à falha do](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)lavatório SQL, especialmente "redirecionar As Sobrancelhas Incompatíveis" para a resolução de problemas que as linhas têm o problema.

        > [!NOTE]
        > Por favor, note-se que a tolerância a falhas pode introduzir um tempo adicional de execução, o que pode levar a custos mais elevados.

    2. Verifique novamente os dados redirecionados com o comprimento da coluna de esquemas de tabela SQL para ver quais colunas precisam de ser atualizadas.

    3. Atualizar o esquema da tabela em conformidade.


## <a name="azure-table-storage"></a>Table Storage do Azure

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>Código de erro: AzureTableDuplicateColumnsFromSource

- **Mensagem:**`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink`

- **Causa**: Pode ser comum para consulta sql com junta, ou ficheiros csv não estruturados

- **Recomendação:** verifique duas vezes as colunas de origem e fixe em conformidade.


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>Código de erro: DB2DriverRunFailed

- **Mensagem:**`Error thrown from driver. Sql code: '%code;'`

- **Causa**: Se a mensagem de erro contiver "SQLSTATE=51002 SQLCODE=-805", consulte a Ponta neste documento: https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties

- **Recomendação**: Tente definir "NULLID" em propriedade "packageCollection"


## <a name="delimited-text-format"></a>Formato de texto delimitado

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Código de erro: DelimitedTextColumnNameNotAllowNull

- **Mensagem:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: Quando definir 'firstRowAsHeader' em atividade, a primeira linha será usada como nome de coluna. Este erro significa que a primeira linha contém valor vazio. Por exemplo: 'ColunaA, ColunaB'.

- **Recomendação**: Verifique a primeira linha e fixe o valor se houver valor vazio.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Código de erro: DelimitedTextMoreColumnsThanDefined

- **Mensagem:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Causa:** A contagem de colunas problemáticas é maior do que a contagem de colunas da primeira linha. Pode ser causado por problemas de dados ou configurações incorretas de delimiter/citação de colunas.

- **Recomendação**: Obtenha a contagem de linha em mensagem de erro, verifique a coluna da linha e corrija os dados.

- **Causa**: Se a contagem de colunas esperada for "1" na mensagem de erro, talvez tenha especificado configurações erradas de compressão ou formato. Assim, a ADF analisou incorretamente os seus ficheiros.

- **Recomendação**: Verifique as definições do formato para se certificar de que corresponde ao(s) dos seus ficheiros de origem.

- **Causa**: Se a sua fonte for uma pasta, é possível que os ficheiros sob a pasta especificada tenham esquemas diferentes.

- **Recomendação**: Certifique-se de que os ficheiros sob a pasta dada têm esquemas idênticos.


## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dinâmica 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Código de erro: DynamicsCreateServiceClientError

- **Mensagem:**`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Causa**: Este é um problema transitório no lado do servidor dinâmico.

- **Recomendação:** Reencaúndio do gasoduto. Se continuar a falhar, tente reduzir o paralelismo. Se ainda falhar, contacte o suporte da dinâmica.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>Faltam colunas ao visualizar/importar esquemas

- **Sintomas**: Algumas das colunas acabam por faltar quando se importam esquemas ou pré-visualização de dados. Mensagem de erro: `The valid structure information (column name and type) are required for Dynamics source.`

- **Causa**: Esta questão é basicamente by-design, uma vez que a ADF não é capaz de mostrar colunas que não têm valor nos primeiros 10 registos. Certifique-se de que as colunas adicionadas estão no formato correto. 

- **Recomendação**: Adicione manualmente as colunas no separador de mapeamento.


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>Código de erro: DynamicsMissingTargetForMultiTargetLookupField

- **Mensagem:**`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Causa**: A coluna-alvo não existe na fonte ou no mapeamento de colunas.

- **Recomendação:** 1. Certifique-se de que a fonte contém a coluna-alvo. 2. Adicione a coluna-alvo no mapeamento da coluna. Certifique-se de que a coluna da pia está no padrão de "{fieldName} @EntityReference ".


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>Código de erro: DynamicsInvalidTargetForMultiTargetLookupField

- **Mensagem:**`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;"`

- **Causa**: Um nome de entidade errada é fornecido como entidade-alvo de um campo de procuração multi-alvo.

- **Recomendação**: Forneça um nome de entidade válido para o campo de procuração multi-alvo.


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>Código de erro: DynamicsInvalidTypeForMultiTargetLookupField

- **Mensagem:**`The provided target type is not a valid string. Field: '%fieldName;'.`

- **Causa:** O valor na coluna-alvo não é uma corda

- **Recomendação**: Forneça uma cadeia válida na coluna alvo de procuração multi-alvo.


### <a name="error-code--dynamicsfailedtorequetserver"></a>Código de erro: DynamicsFailedToRequetServer

- **Mensagem:**`The dynamics server or the network is experiencing issues. Check network connectivity or check dynamics server log for more details.`

- **Causa**: O servidor dinâmico é instável ou inacessível ou a rede está a ter problemas.

- **Recomendação**: Verifique a conectividade da rede ou verifique o registo do servidor dinâmico para obter mais detalhes. Suporte de dinâmica de contato para mais ajuda.
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>Código de erro: FtpFailedToConnectToFtpServer

- **Mensagem:**`Failed to connect to FTP server. Please make sure the provided server informantion is correct, and try again.`

- **Causa**: O tipo de serviço ligado incorreto pode ser utilizado para o servidor FTP, como utilizar o SFTP Linked Service para ligar a um servidor FTP.

- **Recomendação:** Verifique a porta do servidor alvo. Por defeito, a FTP utiliza a porta 21.


## <a name="http"></a>Http

### <a name="error-code--httpfilefailedtoread"></a>Código de erro: HttpFileFailedToRead

- **Mensagem:**`Failed to read data from http server. Check the error from http server：%message;`

- **Causa:** Este erro ocorre quando a Azure Data Factory fala com o servidor http, mas a operação de pedido de http falha.

- **Recomendação**: Verifique o código de estado http \ mensagem na mensagem de erro e corrija o problema do servidor remoto.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Código de erro: ArgumentOutOfRangeException

- **Mensagem:**`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Causa**: Em ADF, os valores da DataTime são suportados no intervalo de 0001-01-01 00:00:00-999-12-31 23:59:59. No entanto, a Oracle suporta uma ampla gama de valor DateTime (como o século BC ou min/seg>59), o que leva ao fracasso na ADF.

- **Recomendação:** 

    Corra `select dump(<column name>)` para verificar se o valor na Oracle está na gama da ADF. 

    Se desejar saber a sequência de byte no resultado, verifique https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="orc-format"></a>Formato Orc

### <a name="error-code--orcjavainvocationexception"></a>Código de erro: OrcJavaInvocationExcepção

- **Mensagem:**`An error occurred when invoking java, message: %javaException;.`

- **Causa**: Quando a mensagem de erro contém 'java.lang.OutOfMemory', 'Java heap space' e 'doubleCapacity', normalmente é um problema de gestão da memória na versão antiga do tempo de execução da integração.

- **Recomendação**: Se estiver a utilizar o tempo de execução de integração auto-hospedado, sugira o upgrade para a versão mais recente.

- **Causa**: Quando a mensagem de erro contém 'java.lang.OutOfMemory', o tempo de execução da integração não tem recursos suficientes para processar os ficheiros.

- **Recomendação**: Limitar os percursos simultâneos no tempo de integração. Para o tempo de funcionação de integração auto-hospedado, dimensione até uma máquina poderosa com memória igual ou superior a 8 GB.

- **Causa**: Quando a mensagem de erro contém 'NullPointerReference', é possível que seja um erro transitório.

- **Recomendação**: Retry. Se o problema persistir, contacte o suporte.

- **Causa**: Quando a mensagem de erro contém 'BufferOverflowException', é possível que seja um erro transitório.

- **Recomendação**: Retry. Se o problema persistir, contacte o suporte.

- **Causa**: Quando a mensagem de erro contém "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable não pode ser lançado para org.apache.hadoop.io.Text", esta é a questão da conversão do tipo dentro do Tempo de Execução de Java. Normalmente, é causado por dados de origem que não podem ser bem tratados em java.

- **Recomendação**: Trata-se de uma questão de dados. Tente utilizar a corda em vez de char/varchar em dados de formato orc.

### <a name="error-code--orcdatetimeexceedlimit"></a>Código de erro: OrcDateTimeExceedLimit

- **Mensagem:**`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Causa**: Se o valor da data for '0001-01-01 00:00:00', pode ser causado pela diferença entre o Calendário Juliano e o Calendário Gregoriano. https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates.

- **Recomendação**: Verifique o valor dos carrapatos e evite utilizar o valor da data '0001-01-01 00:00:00'.


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

- **Mensagem:**`File is not a valid Parquet file.`

- **Causa**: Problema de arquivo parquet.

- **Recomendação**: Verifique se a entrada é um ficheiro Parquet válido.


### <a name="error-code--parquetnotsupportedtype"></a>Código de erro: ParquetNotSupportedType

- **Mensagem:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: O formato Parquet não é suportado na Azure Data Factory.

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

- **Mensagem:**`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Causa**: Cenário não apoiado

- **Recomendação**: "ParquetInterpretFor" não deve ser 'sparkSql'.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Código de erro: ParquetUnsupportFileLevelCompressionOp

- **Mensagem:**`File level compression is not supported for Parquet.`

- **Causa**: Cenário não apoiado

- **Recomendação**: Remova 'CompressionType' na carga útil.


### <a name="error-code--usererrorjniexception"></a>Código de erro: UserErrorJniException

- **Mensagem:**`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Causa**: JVM não pode ser criado porque alguns argumentos ilegais (globais) são definidos.

- **Recomendação**: Inicie sessão na máquina que acolhe **cada nó** do seu IR auto-hospedado. Verifique se a variável do sistema está corretamente definida desta forma: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Reinicie todos os nós iv e, em seguida, reencando o gasoduto.


### <a name="arithmetic-overflow"></a>Transbordo aritmético

- **Sintomas**: Ocorreu uma mensagem de erro ao copiar ficheiros Parquet: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Causa**: Atualmente apenas decimal de precisão <= 38 e comprimento de parte inteiro <= 20 é suportado ao copiar ficheiros da Oracle para Parquet. 

- **Resolução**: Pode converter colunas com este problema em VARCHAR2 como uma solução alternativa.


### <a name="no-enum-constant"></a>Nenhuma constante enum

- **Sintomas**: Ocorreu uma mensagem de erro ao copiar dados para o formato Parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , ou: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Causa:** 

    A questão pode ser causada por espaços brancos ou caracteres não suportados (tais como,; {} ()\n\t=) em nome da coluna, uma vez que Parquet não suporta tal formato. 

    Por exemplo, o nome da coluna como *contoso (teste)* analisará o tipo nos parênteses a partir do [código](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . O erro será levantado uma vez que não existe tal tipo de "teste".

    Para verificar os tipos suportados, pode verificá-los [aqui.](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)

- **Resolução:** 

    - Verifique se há espaços brancos no nome da coluna da pia.

    - Verifique duas vezes se a primeira linha com espaços brancos é usada como nome de coluna.

    - Verifique duas vezes se o tipo OriginalType está suportado ou não. Tente evitar estes símbolos `,;{}()\n\t=` especiais. 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>Código de erro: RestSinkCallFailed

- **Mensagem:**`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Causa:** Este erro acontece quando a Azure Data Factory fala com o Rest Endpoint sobre o protocolo http e o pedido de funcionamento falha.

- **Recomendação**: Verifique o código de estado http \ mensagem na mensagem de erro e corrija o problema do servidor remoto.

### <a name="unexpected-network-response-from-rest-connector"></a>Resposta inesperada da rede do conector REST

- **Sintomas**: O ponto final recebe por vezes uma resposta inesperada (400 / 401 / 403 / 500) do conector REST.

- **Causa**: O conector de fonte REST utiliza o método URL e HTTP/cabeçalho/corpo a partir de serviço/conjunto de dados/fonte de cópia ligado como parâmetros ao construir um pedido HTTP. A questão é provavelmente causada por alguns erros em um ou mais parâmetros especificados.

- **Resolução:** 
    - Utilize 'caracóis' na janela cmd para verificar se o parâmetro é a causa ou não **(Os** cabeçalhos aceitar e **do agente de utilizador** devem ser sempre incluídos):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Se o comando retornar a mesma resposta inesperada, fixe acima dos parâmetros com 'curl' até que retorne a resposta esperada. 

      Também pode usar 'curl--help' para uma utilização mais avançada do comando.

    - Se apenas o conector ADF REST retornar uma resposta inesperada, contacte o suporte da Microsoft para uma resolução de problemas.
    
    - Por favor, note que o 'curl' pode não ser adequado para reproduzir a emissão de validação de certificadoS SSL. Em alguns cenários, o comando 'curl' foi executado com sucesso sem atingir qualquer problema de validação de cert SSL. Mas quando o mesmo URL é executado no navegador, nenhum cert SSL é realmente devolvido em primeiro lugar para que o cliente estabeleça confiança com o servidor.

      Ferramentas como **Carteiro** e **Violinista** são recomendadas para o caso acima.


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>Código de erro: SftpOperaçãoFail

- **Mensagem:**`Failed to '%operation;'. Check detailed error from SFTP.`

- **Causa:** Problema de impacto da operação Sftp.

- **Recomendação**: Verifique erro detalhado da SFTP.


### <a name="error-code--sftprenameoperationfail"></a>Código de erro: SftpRenameOperationFail

- **Mensagem:**`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, please set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Causa**: O seu servidor SFTP não suporta renomear o ficheiro temporário.

- **Recomendação**: Desconte "useTempFileRename" como falso na pia de cópia para desativar o upload para o ficheiro temporário.


### <a name="error-code--sftpinvalidsftpcredential"></a>Código de erro: SftpInvalidSftpCredential

- **Mensagem:**`Invalid Sftp credential provided for '%type;' authentication type.`

- **Causa**: O conteúdo de chaves privadas é recolhido da AKV/SDK, mas não está codificado corretamente.

- **Recomendação:**  

    Se o conteúdo da chave privada for de AKV e o ficheiro chave original puder funcionar se o cliente o enviar diretamente para o serviço ligado à SFTP

    Consulte https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication , o conteúdo privateKey é um conteúdo de chave SSH codificado na Base64.

    Por favor, codifique **todo o conteúdo do ficheiro original da chave privada** com codificação base64 e guarde a cadeia codificada para AKV. O ficheiro chave original é o que pode funcionar no serviço ligado sFTP se clicar no Upload a partir de ficheiro.

    Aqui estão algumas amostras usadas para gerar a corda:

    - Use o código C# :
    ```
    byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
    string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
    ```

    - Use o código Python:
    ```
    import base64
    rfd = open(r'{Private Key Path}', 'rb')
    keyContent = rfd.read()
    rfd.close()
    print base64.b64encode(Key Content)
    ```

    - Utilize a ferramenta de conversão base64 de terceiros

        Ferramentas como https://www.base64encode.org/ são recomendadas.

- **Causa**: É escolhido um formato de conteúdo chave errado

- **Recomendação:**  

    A tecla privada SSH em formato PKCS#8 (a partir de "-----BEGIN ENCRYPTED PRIVATE KEY-----") não é suportada para aceder ao servidor SFTP em ADF. 

    Executar abaixo os comandos para converter a chave para o formato chave SSH tradicional (comece com "-----BEGIN RSA PRIVATE KEY-----"):

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Causa**: Conteúdo de chaves de credencial ou privado inválido ou privado

- **Recomendação**: Verifique duas vezes com ferramentas como o WinSCP para ver se o ficheiro de chave ou a palavra-passe está correto.

### <a name="sftp-copy-activity-failed"></a>A atividade de cópia SFTP falhou

- **Sintomas**: Código de erro: UserErrorInvalidColumnMappingColumnNotFound. Mensagem de erro: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **Causa**: A fonte não inclui uma coluna chamada "AccMngr".

- **Resolução**: Verifique duas vezes como o seu conjunto de dados configurado mapeando a coluna de dataset de destino para confirmar se existe tal coluna "AccMngr".


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>Código de erro: SftpFailedToConnectToSftpServer

- **Mensagem:**`Failed to connect to Sftp server '%server;'.`

- **Causa**: Se a mensagem de erro contiver 'A operação de leitura da tomada foi cronometrada após 30000 milissegundos', uma das causas possíveis é que o tipo de serviço ligado incorreto é utilizado para o servidor SFTP, como utilizar o FtP Linked Service para ligar a um servidor SFTP.

- **Recomendação:** Verifique a porta do servidor alvo. Por predefinição, a SFTP utiliza a porta 22.

- **Causa**: Se a mensagem de erro contiver 'A resposta do servidor não contém identificação do protocolo SSH', uma das causas possíveis é que o servidor SFTP tenha acelerado a ligação. A ADF criará várias ligações para descarregar a partir do servidor SFTP em paralelo, e por vezes atinge o estrangulamento do servidor SFTP. Praticamente, um servidor diferente retornará erros diferentes quando atingidos.

- **Recomendação:**  

    Especifique a ligação máxima simultânea do conjunto de dados SFTP para 1 e reexplique a cópia. Se conseguir passar, podemos ter a certeza que estrangular é a causa.

    Se pretender promover a baixa produção, contacte o administrador da SFTP para aumentar o limite de contagem de ligação simultânea ou adicione IP abaixo para permitir a lista:

    - Se estiver a utilizar o IR Gerido, adicione [intervalos IP do Centro de Dados Azure](https://www.microsoft.com/download/details.aspx?id=41653).
      Ou pode instalar o IR auto-hospedado se não quiser adicionar uma grande lista de gamas IP na lista de autorizações do servidor SFTP.

    - Se estiver a utilizar o IR auto-hospedado, adicione o IP da máquina que instalou o SHIR para permitir a lista.


## <a name="sharepoint-online-list"></a>Lista do SharePoint Online

### <a name="error-code--sharepointonlineauthfailed"></a>Código de erro: SharePointOnlineAuthFailed

- **Mensagem:**`The access token generated failed, status code: %code;, error message: %message;.`

- **Causa**: O iD e a chave do serviço podem não estar corretamente definidos.

- **Recomendação:** Verifique a sua aplicação registada (ID principal de serviço) e faça uma chave se está corretamente definida.


## <a name="xml-format"></a>Formato XML

### <a name="error-code--xmlsinknotsupported"></a>Código de erro: XmlSinkNotSupportado

- **Mensagem:**`Write data in xml format is not supported yet, please choose a different format!`

- **Causa**: Usou um conjunto de dados Xml como conjunto de dados de sumidouro na sua atividade de cópia

- **Recomendação**: Utilize um conjunto de dados em formato diferente como pia de cópia.


### <a name="error-code--xmlattributecolumnnameconflict"></a>Código de erro: XmlAttributeColumnNameConflict

- **Mensagem:**`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Causa:** Usou um prefixo de atributo, que causou o conflito.

- **Recomendação**: Desacorra um valor diferente da propriedade "atributoPrefix".


### <a name="error-code--xmlvaluecolumnnameconflict"></a>Código de erro: XmlValueColumnNameConflict

- **Mensagem:**`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Causa**: Usou um dos nomes do elemento da criança como nome da coluna para o valor do elemento.

- **Recomendação**: Desacorda um valor diferente da propriedade "valueColumn".


### <a name="error-code--xmlinvalid"></a>Código de erro: XmlInvalid

- **Mensagem:**`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Causa**: O ficheiro xml de entrada não está bem formado.

- **Recomendação**: Corrija o ficheiro xml para o tornar bem formado.


## <a name="general-copy-activity-error"></a>Erro de atividade de cópia geral

### <a name="error-code--jrenotfound"></a>Código de erro: JreNotFound

- **Mensagem:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa:** O tempo de integração auto-hospedado não encontra o tempo de funcionação de Java. O Tempo de Execução de Java é necessário para a leitura de uma fonte específica.

- **Recomendação**: Verifique o seu ambiente de tempo de execução de integração, o doc de referência: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Código de erro: WildcardPathSinkNotSupy

- **Mensagem:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa:** Conjunto de dados de afundanço não suporta wildcard.

- **Recomendação**: Verifique o conjunto de dados da pia e corrija o caminho sem valor wildcard.


### <a name="fips-issue"></a>Problema FIPS

- **Sintomas**: A atividade de cópia falha na máquina de tempo de execução de integração auto-hospedada ativada pelo FIPS com mensagem de erro `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` . Isto pode acontecer ao copiar dados com conectores como Azure Blob, SFTP, etc.

- **Causa**: FIPS (Federal Information Processing Standards) define um determinado conjunto de algoritmos criptográficos que podem ser utilizados. Quando o modo FIPS é ativado na máquina, algumas classes criptográficas de que depende a atividade de cópia são bloqueadas em alguns cenários.

- **Resolução**: Pode conhecer a situação atual do modo FIPS no Windows a partir [deste artigo](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)e avaliar se pode desativar o FIPS na máquina de tempo de execução de integração auto-hospedada.

    Por outro lado, se quiser deixar a Azure Data Factory contornar o FIPS e fazer com que a atividade seja bem sucedida, pode seguir estes passos:

    1. Abra a pasta onde está instalada a tempo de execução de integração auto-hospedada, normalmente em `C:\Program Files\Microsoft Integration Runtime\<IR version>\Shared` .

    2. Abra "diawp.exe.config", adicione `<enforceFIPSPolicy enabled="false"/>` na `<runtime>` secção como o seguinte.

        ![Desativar FIPS](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Reinicie a máquina de tempo de execução de integração auto-hospedada.


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](/answers/topics/azure-data-factory.html)
*  [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
