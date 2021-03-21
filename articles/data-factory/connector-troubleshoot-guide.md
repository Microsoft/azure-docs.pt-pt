---
title: Conectores da Fábrica de Dados Azure de resolução de problemas
description: Saiba como resolver problemas de conector na Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 9d8f940e3900c00b1c6f6623dfeff2d92ca85aa3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042441"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Conectores da Fábrica de Dados Azure de resolução de problemas

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora formas comuns de resolver problemas com conectores Azure Data Factory.

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="error-code-azurebloboperationfailed"></a>Código de erro: AzureBlobOperationFailed

- **Mensagem:**"Operação blob falhou. Nome de contentor: %containerName;, caminho: %caminho;."

- **Causa:** Um problema com a operação de armazenamento de bolhas.

- **Recomendação**: Para verificar os detalhes de erro, consulte os [códigos de erro de armazenamento blob](/rest/api/storageservices/blob-service-error-codes). Para mais ajuda, contacte a equipa de Armazenamento Blob.


### <a name="invalid-property-during-copy-activity"></a>Propriedade inválida durante a atividade de cópia

- **Mensagem:**`Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Causa**: O tipo definido no conjunto de dados é inconsistente com o tipo de origem ou pia definido na atividade da cópia.

- **Resolução**: Edite o conjunto de dados ou a definição de JSON do pipeline para tornar os tipos consistentes e, em seguida, reexame a implementação.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Error message: O tamanho do pedido é demasiado grande

- **Sintomas**: Ao copiar dados em Azure Cosmos DB com um tamanho de lote de escrita predefinido, recebe o seguinte erro: `Request size is too large.`

- **Causa**: Azure Cosmos DB limita o tamanho de um único pedido a 2 MB. A fórmula é *tamanho de pedido = tamanho único do lote de \* escrita*. Se o tamanho do seu documento for grande, o comportamento padrão resultará num tamanho de pedido demasiado grande. Pode sintonizar o tamanho do lote de escrita.

- **Resolução**: Na atividade da cópia, reduza o valor do tamanho do *lote de escrita* (o valor padrão é de 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Error message: Unique index constraint violation

- **Sintomas**: Ao copiar dados em Azure Cosmos DB, recebe o seguinte erro:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Causa**: Existem duas causas possíveis:

    - Causa 1: Se utilizar **Insira** como comportamento de escrita, este erro significa que os dados de origem têm linhas ou objetos com o mesmo ID.
    - Causa 2: Se utilizar **Upsert** como comportamento de escrita e definir outra chave única para o recipiente, este erro significa que os seus dados de origem têm linhas ou objetos com diferentes IDs mas o mesmo valor para a chave única definida.

- **Resolução:** 

    - Para a causa 1, coloque **Upsert** como o comportamento de escrita.
    - Para a causa 2, certifique-se de que cada documento tem um valor diferente para a chave única definida.

### <a name="error-message-request-rate-is-large"></a>Error message: A taxa de pedido é grande

- **Sintomas**: Ao copiar dados em Azure Cosmos DB, recebe o seguinte erro:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Causa**: O número de unidades de pedido utilizadas (RUs) é maior do que as RUs disponíveis configuradas em Azure Cosmos DB. Para saber como a Azure Cosmos DB calcula RUs, consulte [unidades request in Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Resolução**: Experimente uma das duas soluções seguintes:

    - Aumente o número *de RUs* do contentor para um valor maior em Azure Cosmos DB. Esta solução irá melhorar o desempenho da atividade da cópia, mas incorrerá em mais custos na Azure Cosmos DB. 
    - Diminuir *a escreverBatchSize* para um valor menor, como 1000, e diminuir *paralelosCopias* para um valor menor, como 1. Esta solução reduzirá o desempenho da copy run, mas não incorrerá em mais custos na Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Colunas em falta no mapeamento de colunas

- **Sintomas**: Quando importa um esquema para Azure Cosmos DB para mapeamento de colunas, algumas colunas estão em falta. 

- **Causa**: Data Factory infere o esquema dos primeiros 10 documentos DB da Azure Cosmos. Se algumas colunas ou propriedades de documentos não contiverem valores, o esquema não é detetado pela Data Factory e consequentemente não é apresentado.

- **Resolução**: Pode sintonizar a consulta como indicado no código seguinte para forçar os valores da coluna a serem apresentados no conjunto de resultados com valores vazios. Suponha que a coluna *impossível* está desaparecida nos primeiros 10 documentos). Em alternativa, pode adicionar manualmente a coluna para mapeamento.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Error message: The GuidRepresentation for the reader is CSharpLegacy

- **Sintomas**: Ao copiar dados da Azure Cosmos DB MongoAPI ou da MongoDB com o campo de identificador universalmente único (UUID), recebe o seguinte erro:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Causa**: Existem duas formas de representar o UUID em Binary JSON (BSON): UuidStardard e UuidLegacy. Por padrão, o UuidLegacy é utilizado para ler dados. Receberá um erro se os seus dados UUID no MongoDB forem UuidStandard.

- **Resolução**: Na cadeia de ligação MongoDB, adicione a opção *uuidRepresentation=standard.* Para obter mais informações, consulte a [cadeia de ligação MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Código de erro: CosmosDbSqlApiOperaçãoFailada

- **Mensagem:**`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Causa:** Um problema com a operação CosmosDbSqlApi.

- **Recomendação**: Para verificar os detalhes do erro, consulte [o documento de ajuda da Azure Cosmos DB](../cosmos-db/troubleshoot-dot-net-sdk.md). Para mais ajuda, contacte a equipa DB da Azure Cosmos.

## <a name="azure-data-lake-storage-gen1"></a>Armazenamento do Azure Data Lake Ger1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Mensagem de erro: A ligação subjacente foi fechada: Não foi possível estabelecer uma relação de confiança para o canal seguro SSL/TLS.

- **Sintomas**: A atividade de cópia falha com o seguinte erro: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Causa**: A validação do certificado falhou durante o aperto de mão TLS.

- **Resolução**: Como solução alternativa, utilize a cópia encenada para saltar a validação de Segurança da Camada de Transporte (TLS) para a Azure Data Lake Storage Gen1. Você precisa reproduzir este problema e recolher o rastreio de rede (netmon) e, em seguida, envolver a sua equipa de rede para verificar a configuração da rede local.

    ![Diagrama de ligações Azure Data Lake Storage Gen1 para problemas de resolução de problemas.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Error message: The remote server retornou um erro: (403) Proibido

- **Sintomas**: A atividade de cópia falha com o seguinte erro: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Causa**: Uma das causas possíveis é que o principal de serviço ou identidade gerida que utiliza não tem permissão para aceder a determinadas pastas ou ficheiros.

- **Resolução**: Conceda permissões adequadas a todas as pastas e sub-dobradores que necessita de copiar. Para obter mais informações, consulte [copiar dados para ou a partir da Azure Data Lake Storage Gen1 utilizando a Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Error message: Failed to access token using service principal. Erro ADAL: service_unavailable

- **Sintomas**: A atividade de cópia falha com o seguinte erro:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Causa**: Quando o Service Token Server (STS) que é propriedade do Azure Ative Directory não está disponível, isso significa que está demasiado ocupado para lidar com pedidos, e devolve o erro HTTP 503. 

- **Resolução**: Reenexsitar a atividade da cópia após vários minutos.


## <a name="azure-data-lake-storage-gen2"></a>Armazenamento do Azure Data Lake Ger2

### <a name="error-code-adlsgen2operationfailed"></a>Código de erro: ADLSGen2OperationFailed

- **Mensagem:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causas e recomendações**: Causas diferentes podem levar a este erro. Consulte a lista abaixo para ver se possível análise de causa e recomendação relacionada.

  | Análise de causa                                               | Recomendação                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Se o Azure Data Lake Storage Gen2 cometer um erro que indique que alguma operação falhou.| Verifique a mensagem de erro detalhada lançada pela Azure Data Lake Storage Gen2. Se o erro for uma falha transitória, recandiduça a operação. Para mais ajuda, contacte o suporte do Azure Storage e forneça o ID do pedido em mensagem de erro. |
  | Se a mensagem de erro contiver a cadeia "Forbidden", o titular do serviço ou a identidade gerida que utiliza pode não ter permissão suficiente para aceder ao Azure Data Lake Storage Gen2. | Para resolver este erro, consulte [copiar e transformar dados no Azure Data Lake Storage Gen2 utilizando a Azure Data Factory](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Se a mensagem de erro contiver a cadeia "InternalServerError", o erro é devolvido pela Azure Data Lake Storage Gen2. | O erro pode ser causado por uma falha transitória. Se for o caso, repita a operação. Se o problema persistir, contacte o suporte do Azure Storage e forneça o ID do pedido a partir da mensagem de erro. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Pedido à conta Azure Data Lake Storage Gen2 causou um erro de timeout

- **Mensagem:** 
  * Código de Erro = `UserErrorFailedBlobFSOperation`
  * Mensagem de Erro = `BlobFS operation failed for: A task was canceled.`

- **Causa**: O problema é causado pelo erro de tempo limite de tempo da Azure Data Lake Storage Gen2, que ocorre geralmente na máquina de tempo de execução de integração auto-hospedada (IR).

- **Recomendação**: 

    - Coloque a sua máquina de INFRAVERMELHOs auto-hospedada e direcione a conta Azure Data Lake Storage Gen2 na mesma região, se possível. Isto pode ajudar a evitar um erro de tempo limite aleatório e produzir um melhor desempenho.

    - Verifique se existe uma definição de rede especial, como o ExpressRoute, e certifique-se de que a rede tem largura de banda suficiente. Sugerimos que baixe o iMC de empregos simultâneos quando a largura de banda geral é baixa. Fazê-lo pode ajudar a evitar a concorrência de recursos de rede em vários empregos simultâneos.

    - Se o tamanho do ficheiro for moderado ou pequeno, utilize um tamanho de bloco menor para uma cópia não binária para atenuar tal erro de tempo. Para obter mais informações, consulte [o Bloco de Colocação de Armazenamento blob](/rest/api/storageservices/put-block).

       Para especificar o tamanho do bloco personalizado, edite a propriedade no seu editor de ficheiros JSON como mostrado aqui:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Armazenamento de Ficheiros Azure

### <a name="error-code-azurefileoperationfailed"></a>Código de erro: AzureFileOperationFailed

- **Mensagem:**`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Causa**: Um problema com a operação de armazenamento de Ficheiros Azure.

- **Recomendação**: Para verificar os detalhes de erro, consulte [a ajuda dos Ficheiros Azure](/rest/api/storageservices/file-service-error-codes). Para mais ajuda, contacte a equipa da Azure Files.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database e SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Código de erro: SqlFailedToConnect

- **Mensagem:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Causas e recomendações**: Causas diferentes podem levar a este erro. Consulte a lista abaixo para ver se possível análise de causa e recomendação relacionada.

    | Análise de causa                                               | Recomendação                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Para o Azure SQL, se a mensagem de erro contiver a cadeia "SqlErrorNumber=47073", significa que o acesso à rede pública é negado na definição de conectividade. | Na firewall Azure SQL, desacorde a opção de acesso à **rede pública Deny** para o *Nº*. Para obter mais informações, consulte [as definições de conectividade Azure SQL](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | Para o Azure SQL, se a mensagem de erro contiver um código de erro SQL como "SqlErrorNumber=[errorcode]", consulte o guia de resolução de problemas do Azure SQL. | Para obter uma recomendação, consulte [problemas de conectividade de resolução de problemas e outros erros com a Base de Dados Azure SQL e Azure SQL Managed Instance](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Verifique se a porta 1433 está na lista de autorizações de firewall. | Para obter mais informações, consulte [as portas utilizadas pelo SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Se a mensagem de erro contiver a cadeia "SqlException", a Base de Dados SQL indica que alguma operação específica falhou. | Para obter mais informações, procure por código de erro SQL em [erros do motor database](/sql/relational-databases/errors-events/database-engine-events-and-errors). Para mais ajuda, contacte o suporte da Azure SQL. |
    | Se se trata de um problema transitório (por exemplo, uma ligação de rede instable), adicione novamente na política de atividade para mitigar. | Para mais informações, consulte [Pipelines e atividades na Azure Data Factory.](./concepts-pipelines-activities.md#activity-policy) |
    | Se a mensagem de erro contiver a cadeia "Cliente com endereço IP '...' não é permitido aceder ao servidor", e está a tentar ligar-se à Base de Dados Azure SQL, o erro é normalmente causado por um problema de firewall da Base de Dados Azure SQL. | Na configuração de firewall do Azure SQL Server, ative os **serviços e recursos do Enable Azure para aceder a esta** opção de servidor. Para mais informações, consulte [a Base de Dados Azure SQL e as regras de firewall IP da Azure Synapse](../azure-sql/database/firewall-configure.md). |
    
### <a name="error-code-sqloperationfailed"></a>Código de erro: SqlOperationFailed

- **Mensagem:**`A database operation failed. Please search error to get more details.`

- **Causas e recomendações**: Causas diferentes podem levar a este erro. Consulte a lista abaixo para ver se possível análise de causa e recomendação relacionada.

    | Análise de causa                                               | Recomendação                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Se a mensagem de erro contiver a cadeia "SqlException", a Base de Dados SQL lança um erro indicando que alguma operação específica falhou. | Se o erro SQL não for claro, tente alterar a base de dados para o nível de compatibilidade mais recente '150'. Pode lançar os erros SQL da versão mais recente. Para obter mais informações, consulte a [documentação](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Para obter mais informações sobre problemas de resolução de problemas do SQL, procure por código de erro SQL em [erros do motor database](/sql/relational-databases/errors-events/database-engine-events-and-errors). Para mais ajuda, contacte o suporte da Azure SQL. |
    | Se a mensagem de erro contiver a cadeia "PdwManagedToNativeInteropException", é geralmente causada por uma incompatibilidade entre os tamanhos das colunas de origem e pia. | Verifique o tamanho das colunas de origem e pia. Para mais ajuda, contacte o suporte da Azure SQL. |
    | Se a mensagem de erro contiver a cadeia "InvalidOperationException", é geralmente causada por dados de entrada inválidos. | Para identificar qual a linha que encontrou o problema, permita a função de tolerância à falha na atividade da cópia, que pode redirecionar linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Para obter mais informações, consulte [a tolerância à falha da atividade de cópia na Azure Data Factory](./copy-activity-fault-tolerance.md). |


### <a name="error-code-sqlunauthorizedaccess"></a>Código de erro: SqlUnauthorizedAccess

- **Mensagem:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: As credenciais estão incorretas ou a conta de login não pode aceder à base de dados SQL.

- **Recomendação**: Verifique se a conta de login tem permissões suficientes para aceder à base de dados SQL.


### <a name="error-code-sqlopenconnectiontimeout"></a>Código de erro: SqlOpenConnectionTimeout

- **Mensagem:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa**: O problema pode ser uma falha transitória da base de dados SQL.

- **Recomendação**: Revendo a operação para atualizar a cadeia de ligação de serviço ligada com um maior valor de tempo de ligação.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Código de erro: SqlAutoCreateTableTypeMapFailed

- **Mensagem:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa:** A tabela de auto-criação não pode satisfazer os requisitos de origem.

- **Recomendação**: Atualize o tipo de coluna em *mapeamentos* ou crie manualmente a tabela da pia no servidor alvo.


### <a name="error-code-sqldatatypenotsupported"></a>Código de erro: SqlDataTypeNotSupported

- **Mensagem:**`A database operation failed. Check the SQL errors.`

- **Causa**: Se o problema ocorrer na fonte SQL e o erro estiver relacionado com o transbordo sqlDateTime, o valor dos dados excede a gama de tipo lógica (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Recomendação**: Lance o tipo para a cadeia na consulta SQL de origem ou, no mapeamento da coluna de atividade de cópia, altere o tipo de coluna para *String*.

- **Causa**: Se o problema ocorrer no lavatório SQL e o erro estiver relacionado com o transbordo sqlDateTime, o valor dos dados excede a gama permitida na tabela do lavatório.

- **Recomendação**: Atualizar o tipo de coluna correspondente ao tipo *de data 2* na tabela do lavatório.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Código de erro: SqlInvalidDbStoredProcedure

- **Mensagem:**`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: O procedimento armazenado especificado é inválido. A causa pode ser que o procedimento armazenado não devolva nenhum dado.

- **Recomendação**: Valide o procedimento armazenado utilizando ferramentas SQL. Certifique-se de que o procedimento armazenado pode devolver os dados.


### <a name="error-code-sqlinvaliddbquerystring"></a>Código de erro: SqlInvalidDbQueryString

- **Mensagem:**`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa**: A consulta SQL especificada é inválida. A causa pode ser que a consulta não devolva nenhum dado.

- **Recomendação**: Valide a consulta SQL utilizando ferramentas SQL. Certifique-se de que a consulta pode devolver dados.


### <a name="error-code-sqlinvalidcolumnname"></a>Código de erro: SqlInvalidColumnName

- **Mensagem:**`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa:** A coluna não pode ser encontrada porque a configuração pode estar incorreta.

- **Recomendação**: Verifique a coluna na consulta, *estrutura* no conjunto de dados e *mapeamentos* na atividade.


### <a name="error-code-sqlbatchwritetimeout"></a>Código de erro: SqlBatchWriteTimeout

- **Mensagem:**`Timeouts in SQL write operation.`

- **Causa**: O problema pode ser causado por uma falha transitória da base de dados SQL.

- **Recomendação:** Recandidou a operação. Se o problema persistir, contacte o suporte da Azure SQL.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Código de erro: SqlBatchWriteTransactionFailed

- **Mensagem:**`SQL transaction commits failed.`

- **Causa**: Se os detalhes da exceção indicarem constantemente um tempo limite de transação, a latência da rede entre o tempo de funcionação da integração e a base de dados é maior do que o limiar padrão de 30 segundos.

- **Recomendação**: Atualize a cadeia de ligação de serviço ligada ao SQL com um valor de intervalo de *ligação* igual ou superior a 120 e reexecute a atividade.

- **Causa**: Se os detalhes da exceção indicarem intermitentemente que a ligação SQL está avariada, pode ser uma falha de rede transitória ou um problema lateral da base de dados SQL.

- **Recomendação**: Recave a atividade e reveja as métricas laterais da base de dados SQL.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Código de erro: SqlBulkCopyInvalidColumnLength

- **Mensagem:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: SQL Bulk Copy falhou porque recebeu um comprimento de coluna inválido do cliente do programa de cópia a granel (bcp).

- **Recomendação**: Para identificar qual a linha que encontrou o problema, permita a função de tolerância à falha na atividade da cópia. Isto pode redirecionar linhas problemáticas para o armazenamento para uma investigação mais aprofundada. Para obter mais informações, consulte [a tolerância à falha da atividade de cópia na Azure Data Factory](./copy-activity-fault-tolerance.md).


### <a name="error-code-sqlconnectionisclosed"></a>Código de erro: SqlConnectionIsClosed

- **Mensagem:**`The connection is closed by SQL Database.`

- **Causa**: A ligação SQL é fechada pela base de dados SQL quando uma execução alta e simultânea e o servidor termina a ligação.

- **Recomendação:** Recandidutar a ligação. Se o problema persistir, contacte o suporte da Azure SQL.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Error message: Conversion failed when conversioning from a character string to uniqueidentifier

- **Sintomas**: Ao copiar dados de uma fonte de dados tabular (como o SQL Server) para o Azure Synapse Analytics utilizando cópia encenada e PolyBase, recebe o seguinte erro:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Causa**: Azure Synapse Analytics PolyBase não pode converter uma corda vazia para um GUID.

- **Resolução**: Na pia da atividade da cópia, nas definições de PolyBase, defina a opção **por defeito do tipo de utilização** para *falso*.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensagem de erro: Tipo de dados esperado: DECIMAL(x,x), Valor ofensivo

- **Sintomas**: Ao copiar dados de uma fonte de dados tabular (como o SQL Server) para o Azure Synapse Analytics utilizando uma cópia encenada e a PolyBase, recebe o seguinte erro:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Causa**: Azure Synapse Analytics PolyBase não pode inserir uma corda vazia (valor nulo) numa coluna decimal.

- **Resolução**: Na pia da atividade da cópia, nas definições de PolyBase, defina a opção **por defeito do tipo de utilização** para falso.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Mensagem de erro: Mensagem de exceção Java: HdfsBridge::CreateRecordReader

- **Sintomas**: Copie os dados no Azure Synapse Analytics utilizando a PolyBase e receba o seguinte erro:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Causa**: A causa pode ser que o esquema (largura total da coluna) seja demasiado grande (maior que 1 MB). Verifique o esquema da tabela target Azure Synapse Analytics adicionando o tamanho de todas as colunas:

    - Int = 4 bytes
    - Bigint = 8 bytes
    - Varchar(n), char(n), binário(n), varbinário(n) = n bytes
    - Nvarchar(n), nchar(n) = n*2 bytes
    - Data = 6 bytes
    - Data/(2), tempo de data = 16 bytes
    - Datatimeoff = 20 bytes
    - Decimal = 19 bytes
    - Boia = 8 bytes
    - Dinheiro = 8 bytes
    - Smallmoney = 4 bytes
    - Real = 4 bytes
    - Smallint = 2 bytes
    - Tempo = 12 bytes
    - Tinyint = 1 byte

- **Resolução:** 
    - Reduza a largura da coluna para menos de 1 MB.
    - Ou utilize uma abordagem de inserção a granel desativando a PolyBase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Error message: A condição especificada através do ou dos cabeçalho condicional HTTP não é cumprida

- **Sintomas**: Utilize a consulta SQL para retirar dados da Azure Synapse Analytics e receber o seguinte erro:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Causa**: A Azure Synapse Analytics encontrou um problema enquanto consultava a tabela externa no Azure Storage.

- **Resolução**: Faça a mesma consulta no SQL Server Management Studio (SSMS) e verifique se obtém o mesmo resultado. Se o fizer, abra um bilhete de apoio à Azure Synapse Analytics e forneça o seu servidor Azure Synapse Analytics e o nome da base de dados.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>O nível de desempenho é baixo e leva a falha de cópia

- **Sintomas**: Copie os dados na Base de Dados Azure SQL e receba o seguinte erro: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Causa**: A azure SQL Database s1 atingiu os limites de entrada/saída (I/O).

- **Resolução**: Atualizar o nível de desempenho da Base de Dados Azure SQL para corrigir o problema. 


### <a name="sql-table-cant-be-found"></a>A mesa SQL não pode ser encontrada 

- **Sintomas**: Copia dados híbridos para uma tabela de servidor SQL no local e recebe o seguinte erro:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Causa**: A conta atual do SQL não tem permissões suficientes para executar pedidos emitidos por .NET SqlBulkCopy.WriteToServer.

- **Resolução**: Mude para uma conta SQL mais privilegiada.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Error message: String or binary data is truncated

- **Sintomas**: Ocorre um erro quando copia dados numa tabela do Servidor Azure SQL no local. 

- **Causa**: A definição de esquema de tabela Cx SQL tem uma ou mais colunas com menos comprimento do que o esperado.

- **Resolução**: Para resolver a questão, tente o seguinte:

    1. Para resolver problemas que as linhas têm o problema, aplique [a tolerância à falha](./copy-activity-fault-tolerance.md)do lavatório SQL, especialmente "redirecione As Sobrancelhas Incompatíveis".

        > [!NOTE]
        > A tolerância à falha pode exigir um tempo adicional de execução, o que pode levar a custos mais elevados.

    2. Verifique novamente os dados redirecionados com o comprimento da coluna de esquemas de placa SQL para ver quais colunas precisam de ser atualizadas.

    3. Atualize o esquema da tabela em conformidade.


## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Código de erro: AzureTableDuplicateColumnsFromSource

- **Mensagem:**`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Razão**: Colunas de origem duplicadas podem ocorrer por uma das seguintes razões:
   * Está a usar a base de dados como fonte e juntas de mesa aplicadas.
   * Tem ficheiros CSV não estruturados com nomes de colunas duplicados na linha do cabeçalho.

- **Recomendação**: Verifique e fixe as colunas de origem, se necessário.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Código de erro: DB2DriverRunFailed

- **Mensagem:**`Error thrown from driver. Sql code: '%code;'`

- **Causa**: Se a mensagem de erro contiver a cadeia "SQLSTATE=51002 SQLCODE=-805", siga a "Dica" em [Copiar dados da DB2 utilizando a Azure Data Factory](./connector-db2.md#linked-service-properties).

- **Recomendação:** Tente definir "NULLID" na `packageCollection`  propriedade.


## <a name="delimited-text-format"></a>Formato de texto delimitado

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Código de erro: DelimitedTextColumnNameNotAllowNull

- **Mensagem:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Causa**: Quando 'firstRowAsHeader' é definido na atividade, a primeira linha é usada como o nome da coluna. Este erro significa que a primeira linha contém um valor vazio (por exemplo, 'ColunaA, ColunaB').

- **Recomendação**: Verifique a primeira linha e fixe o valor se estiver vazio.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Código de erro: DelimitedTextMoreColumnsThanDefined

- **Mensagem:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Causas e recomendações**: Causas diferentes podem levar a este erro. Consulte a lista abaixo para ver se possível análise de causa e recomendação relacionada.

  | Análise de causa                                               | Recomendação                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | A contagem de colunas problemáticas é maior do que a contagem de colunas da primeira linha. Pode ser causado por um problema de dados ou por um delimiter de coluna incorreto ou configurações de carvão de citação. | Obtenha a contagem de linha da mensagem de erro, verifique a coluna da linha e corrija os dados. |
  | Se a contagem de colunas esperada for "1" numa mensagem de erro, pode ter especificado configurações erradas de compressão ou formato, o que fez com que a Data Factory analisasse incorretamente os seus ficheiros. | Verifique as definições do formato para se certificar de que correspondem aos seus ficheiros de origem. |
  | Se a sua fonte for uma pasta, os ficheiros sob a pasta especificada podem ter um esquema diferente. | Certifique-se de que os ficheiros da pasta especificada têm um esquema idêntico. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dinâmica 365, Serviço Comum de Dados e CrM Dinâmico

### <a name="error-code-dynamicscreateserviceclienterror"></a>Código de erro: DynamicsCreateServiceClientError

- **Mensagem:**`This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Causa**: O problema é um problema transitório no lado do servidor Dynamics.

- **Recomendação:** Reencaúndio do gasoduto. Se falhar de novo, tente reduzir o paralelismo. Se o problema persistir, contacte o suporte da Dynamics.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Colunas em falta quando importa um esquema ou dados de pré-visualização

- **Sintomas**: Faltam algumas colunas quando importa um esquema ou dados de pré-visualização. Mensagem de erro: `The valid structure information (column name and type) are required for Dynamics source.`

- **Causa**: Esta questão é por design, porque a Data Factory não consegue mostrar colunas que não contenham valores nos primeiros 10 registos. Certifique-se de que as colunas adicionadas estão no formato correto. 

- **Recomendação**: Adicione manualmente as colunas no separador de mapeamento.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Código de erro: DynamicsMissingTargetForMultiTargetLookupField

- **Mensagem:**`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Causa:** A coluna-alvo não existe na fonte ou no mapeamento da coluna.

- **Recomendação**:  
  1. Certifique-se de que a fonte contém a coluna-alvo. 
  2. Adicione a coluna-alvo no mapeamento da coluna. Certifique-se de que a coluna da pia está no formato *{fieldName} @EntityReference*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Código de erro: DynamicsInvalidTargetForMultiTargetLookupField

- **Mensagem:**`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Causa**: Um nome de entidade errada é fornecido como entidade-alvo de um campo de procuração multi-alvo.

- **Recomendação**: Forneça um nome de entidade válido para o campo de procuração multi-alvo.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Código de erro: DynamicsInvalidTypeForMultiTargetLookupField

- **Mensagem:**`The provided target type is not a valid string. Field: '%fieldName;'.`

- **Causa:** O valor na coluna-alvo não é uma corda.

- **Recomendação**: Forneça uma cadeia válida na coluna alvo de procuração multi-alvo.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Código de erro: DynamicsFailedToRequetServer

- **Mensagem:**`The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Causa**: O servidor Dynamics é instável ou inacessível, ou a rede está a ter problemas.

- **Recomendação**: Para mais detalhes, verifique a conectividade da rede ou verifique o registo do servidor Dynamics. Para mais ajuda, contacte o suporte da Dynamics.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Código de erro: FtpFailedToConnectToFtpServer

- **Mensagem:**`Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Causa**: Um tipo de serviço ligado incorreto pode ser utilizado para o servidor FTP, como utilizar o serviço ligado Secure FTP (SFTP) para ligar a um servidor FTP.

- **Recomendação:** Verifique a porta do servidor alvo. FTP utiliza porta 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Código de erro: HttpFileFailedToRead

- **Mensagem:**`Failed to read data from http server. Check the error from http server：%message;`

- **Causa**: Este erro ocorre quando a Azure Data Factory fala com o servidor HTTP, mas a operação de pedido HTTP falha.

- **Recomendação**: Verifique o código de estado HTTP na mensagem de erro e corrija o problema do servidor remoto.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Código de erro: ArgumentOutOfRangeException

- **Mensagem:**`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Causa**: Na Fábrica de Dados, os valores datatime são suportados no intervalo de 0001-01-01 00:00:00-999-12-31 23:59:59. No entanto, a Oracle suporta uma gama mais alargada de valores datetime, como o século A.C. ou min/seg>59, o que leva à falha na Data Factory.

- **Recomendação**: 

    Para ver se o valor na Oracle está na gama de Data Factory, `select dump(<column name>)` corra. 

    Para aprender a sequência de byte no resultado, veja [como são armazenadas as datas no Oráculo?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)


## <a name="orc-format"></a>Formato ORC

### <a name="error-code-orcjavainvocationexception"></a>Código de erro: OrcJavaInvocationExcepção

- **Mensagem:**`An error occurred when invoking Java, message: %javaException;.`
- **Causas e recomendações**: Causas diferentes podem levar a este erro. Consulte a lista abaixo para ver se possível análise de causa e recomendação relacionada.

    | Análise de causa                                               | Recomendação                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Quando a mensagem de erro contém as cordas "java.lang.OutOfMemory", "Java heap space" e "doubleCapacity", é geralmente um problema de gestão da memória numa versão antiga do tempo de execução da integração. | Se estiver a utilizar o Tempo de Execução de Integração Auto-hospedado, recomendamos que atualize para a versão mais recente. |
    | Quando a mensagem de erro contém a cadeia "java.lang.OutOfMemory", o tempo de integração não tem recursos suficientes para processar os ficheiros. | Limitar as execuções simultâneas no tempo de integração. Para o IR auto-hospedado, dimensione até uma máquina poderosa com memória igual ou superior a 8 GB. |
    |Quando a mensagem de erro contém a cadeia "NullPointerReference", a causa pode ser um erro transitório. | Repita a operação. Se o problema persistir, contacte o suporte. |
    | Quando a mensagem de erro contém a cadeia "BufferOverflowException", a causa pode ser um erro transitório. | Repita a operação. Se o problema persistir, contacte o suporte. |
    | Quando a mensagem de erro contém a cadeia "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable não pode ser lançado para org.apache.hadoop.io.Text", a causa pode ser um problema de conversão do tipo dentro do Java Runtime. Normalmente, significa que os dados de origem não podem ser bem tratados em Java Runtime. | Isto é uma questão de dados. Tente utilizar uma corda em vez de carvão ou varchar em dados de formato ORC. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Código de erro: OrcDateTimeExceedLimit

- **Mensagem:**`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Causa**: Se o valor da data for '0001-01-01 00:00:00', pode ser causado pelas diferenças entre o [calendário juliano e o calendário gregoriano.](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)

- **Recomendação**: Verifique o valor dos carrapatos e evite utilizar o valor da data '0001-01-01 00:00:00'.


## <a name="parquet-format"></a>Formato Parquet

### <a name="error-code-parquetjavainvocationexception"></a>Código de erro: ParquetJavaInvocationExcepção

- **Mensagem:**`An error occurred when invoking java, message: %javaException;.`

- **Causas e recomendações**: Causas diferentes podem levar a este erro. Consulte a lista abaixo para ver se possível análise de causa e recomendação relacionada.

    | Análise de causa                                               | Recomendação                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Quando a mensagem de erro contém as cordas "java.lang.OutOfMemory", "Java heap space" e "doubleCapacity", é geralmente um problema de gestão da memória numa versão antiga do Tempo de Execução de Integração. | Se estiver a utilizar o IR auto-hospedado e a versão for superior a 3.20.7159.1, recomendamos que atualize para a versão mais recente. |
    | Quando a mensagem de erro contém a cadeia "java.lang.OutOfMemory", o tempo de integração não tem recursos suficientes para processar os ficheiros. | Limitar as execuções simultâneas no tempo de integração. Para o IR auto-hospedado, dimensione até uma máquina poderosa com memória igual ou superior a 8 GB. |
    | Quando a mensagem de erro contiver a cadeia "NullPointerReference", pode ser um erro transitório. | Repita a operação. Se o problema persistir, contacte o suporte. |

### <a name="error-code-parquetinvalidfile"></a>Código de erro: ParquetInvalidFile

- **Mensagem:**`File is not a valid Parquet file.`

- **Causa:** Esta é uma questão de arquivo Parquet.

- **Recomendação**: Verifique se a entrada é um ficheiro Parquet válido.


### <a name="error-code-parquetnotsupportedtype"></a>Código de erro: ParquetNotSupportedType

- **Mensagem:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: O formato Parquet não é suportado na Azure Data Factory.

- **Recomendação**: Verifique novamente os dados de origem indo para [formatos de ficheiros suportados e codecs de compressão através da atividade de cópia na Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Código de erro: ParquetMissedDecimalPrecisionScale

- **Mensagem:**`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: A precisão e a escala numeradas foram analisadas, mas não foi fornecida essa informação.

- **Recomendação**: A fonte não devolve a precisão correta e a informação de escala. Verifique a coluna de emissão para obter a informação.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Código de erro: ParquetInvalidDecimalPrecisionScale

- **Mensagem:**`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa**: O esquema é inválido.

- **Recomendação**: Verifique se a coluna de emissão tem precisão e escala.


### <a name="error-code-parquetcolumnnotfound"></a>Código de erro: ParquetColumnNotFound

- **Mensagem:**`Column %column; does not exist in Parquet file.`

- **Causa**: O esquema de origem é um desfasamento com o esquema da pia.

- **Recomendação**: Verifique os mapeamentos da atividade. Certifique-se de que a coluna de origem pode ser mapeada para a coluna correta da pia.


### <a name="error-code-parquetinvaliddataformat"></a>Código de erro: ParquetInvalidDataFormat

- **Mensagem:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: Os dados não podem ser convertidos no tipo especificado em mapeamentos.source.

- **Recomendação**: Verifique novamente os dados de origem ou especifique o tipo de dados correto para esta coluna no mapeamento da coluna de atividade de cópia. Para obter mais informações, consulte [os formatos de ficheiros suportados e os codecs de compressão por cópia na Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Código de erro: ParquetDataCountNotMatchColumnCount

- **Mensagem:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa:** Um desfasamento entre a contagem de colunas de origem e a contagem da coluna da pia.

- **Recomendação**: Verifique novamente se a contagem da coluna de origem é igual à contagem da coluna da pia em 'mapeamento'.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Código de erro: ParquetDataTypeNotMatchColumnType

- **Mensagem:**`The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Causa:** Os dados da fonte não podem ser convertidos para o tipo definido na pia.

- **Recomendação**: Especifique um tipo correto em mapeamento.pia.


### <a name="error-code-parquetbridgeinvaliddata"></a>Código de erro: ParquetBridgeInvalidData

- **Mensagem:**`%message;`

- **Causa**: O valor dos dados excedeu o limite.

- **Recomendação:** Recandidou a operação. Se o problema persistir, contacte-nos.


### <a name="error-code-parquetunsupportedinterpretation"></a>Código de erro: ParquetUnsupportedInterpretation

- **Mensagem:**`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Porque:** Este cenário não é apoiado.

- **Recomendação**: "ParquetInterpretFor" não deve ser 'sparkSql'.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Código de erro: ParquetUnsupportFileLevelCompressionOp

- **Mensagem:**`File level compression is not supported for Parquet.`

- **Porque:** Este cenário não é apoiado.

- **Recomendação**: Remova 'CompressãoType' na carga útil.


### <a name="error-code-usererrorjniexception"></a>Código de erro: UserErrorJniException

- **Mensagem:**`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Causa**: Uma Máquina Virtual Java (JVM) não pode ser criada porque alguns argumentos ilegais (globais) são definidos.

- **Recomendação**: Inicie sessão na máquina que acolhe *cada nó* do seu IR auto-hospedado. Verifique se a variável do sistema está corretamente definida: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Reinicie todos os nós de infravermelhos e, em seguida, reencando o gasoduto.


### <a name="arithmetic-overflow"></a>Transbordo aritmético

- **Sintomas**: A mensagem de erro ocorreu quando copia ficheiros Parquet: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Causa**: Atualmente apenas a decimal de precisão <= 38 e o comprimento da parte completa <= 20 são suportados quando copia ficheiros da Oracle para o Parquet. 

- **Resolução**: Como solução alternativa, pode converter quaisquer colunas com este problema em VARCHAR2.


### <a name="no-enum-constant"></a>Nenhuma constante enum

- **Sintomas**: A mensagem de erro ocorreu quando copia dados para o formato Parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , ou: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Causa:** 

    A questão pode ser causada por espaços brancos ou caracteres especiais não apoiados (tais como,; {} ()\n\t=) no nome da coluna, porque Parquet não suporta tal formato. 

    Por exemplo, um nome de coluna como *contoso (teste)* analisará o tipo de parênteses a partir do [código](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . O erro é lançado porque não existe tal tipo de "teste".

    Para verificar tipos suportados, vá ao site GitHub [apache/parquet-mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Resolução:** 

    Verifique duas vezes se:
    - Há espaços brancos no nome da coluna da pia.
    - A primeira linha com espaços brancos é usada como o nome da coluna.
    - O tipo OriginalType é suportado. Tente evitar a utilização destes caracteres especiais: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Código de erro: RestSinkCallFailed

- **Mensagem:**`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Causa**: Este erro ocorre quando a Azure Data Factory fala com o ponto final REST sobre o protocolo HTTP, e a operação de pedido falha.

- **Recomendação**: Verifique o código de estado HTTP ou a mensagem na mensagem de erro e corrija o problema do servidor remoto.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Resposta inesperada da rede do conector REST

- **Sintomas**: O ponto final recebe por vezes uma resposta inesperada (400, 401, 403, 500) do conector REST.

- **Causa**: O conector de fonte REST utiliza o método URL e HTTP/cabeçalho/corpo a partir do serviço/conjunto de dados/fonte de cópia ligado como parâmetros quando constrói um pedido HTTP. A questão é provavelmente causada por alguns erros em um ou mais parâmetros especificados.

- **Resolução:** 
    - Utilize 'caracóis' numa janela de aviso de comando para ver se o parâmetro é a causa **(Os** cabeçalhos aceita e **do agente de utilizador** devem ser sempre incluídos):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Se o comando retornar a mesma resposta inesperada, fixe os parâmetros anteriores com 'caracóis' até que retorne a resposta esperada. 

      Também pode usar 'curl--help' para uma utilização mais avançada do comando.

    - Se apenas o conector Data Factory REST retornar uma resposta inesperada, contacte o suporte da Microsoft para uma resolução de problemas.
    
    - Note que o 'curl' pode não ser adequado para reproduzir uma emissão de validação de certificado SSL. Em alguns cenários, o comando 'curl' foi executado com sucesso sem encontrar quaisquer problemas de validação de certificado SSL. Mas quando o mesmo URL é executado num browser, nenhum certificado SSL é realmente devolvido para que o cliente estabeleça confiança com o servidor.

      Ferramentas como **o Carteiro** e o **Violinista** são recomendadas para o caso anterior.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Código de erro: SftpOperaçãoFail

- **Mensagem:**`Failed to '%operation;'. Check detailed error from SFTP.`

- **Causa**: Um problema com a operação SFTP.

- **Recomendação**: Verifique os detalhes de erro da SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Código de erro: SftpRenameOperationFail

- **Mensagem:**`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Causa**: O seu servidor SFTP não suporta renomear o ficheiro temporário.

- **Recomendação**: Desconte "useTempFileRename" como falso na pia da cópia para desativar o upload para o ficheiro temporário.


### <a name="error-code-sftpinvalidsftpcredential"></a>Código de erro: SftpInvalidSftpCredential

- **Mensagem:**`Invalid SFTP credential provided for '%type;' authentication type.`

- **Causa:** O conteúdo da chave privada é recolhido do cofre da chave Azure ou SDK, mas não está codificado corretamente.

- **Recomendação**:  

    Se o conteúdo da chave privada for do cofre da chave, o ficheiro original da chave pode funcionar se o enviar diretamente para o serviço ligado à SFTP.

    Para obter mais informações, consulte [copiar dados de e para o servidor SFTP utilizando a Azure Data Factory](./connector-sftp.md#use-ssh-public-key-authentication). O conteúdo da chave privada é o conteúdo de chave privada SSH codificado de base64.

    Codificar *todo o* ficheiro original da chave privada com codificação base64 e armazenar a cadeia codificada no cofre da chave. O ficheiro chave original é o que pode funcionar no serviço ligado SFTP se selecionar **upload** a partir do ficheiro.

    Aqui estão algumas amostras que pode usar para gerar a corda:

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

    - Utilize uma ferramenta de conversão base64 de terceiros. Recomendamos a ferramenta [de formato Encode para Base64.](https://www.base64encode.org/)

- **Causa**: O formato de conteúdo chave errado foi escolhido.

- **Recomendação**:  

    A tecla privada SSH em formato PKCS#8 (a partir de "-----BEGIN ENCRYPTED PRIVATE KEY-----") não é atualmente suportada para aceder ao servidor SFTP na Data Factory. 

    Para converter a chave para o formato chave SSH tradicional, começando com "-----BEGIN RSA PRIVATE KEY-----", executar os seguintes comandos:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Causa:** Credenciais inválidas ou conteúdo de chave privada.

- **Recomendação**: Para ver se o ficheiro chave ou a palavra-passe estão corretos, verifique novamente com ferramentas como o WinSCP.

### <a name="sftp-copy-activity-failed"></a>A atividade de cópia SFTP falhou

- **Sintomas:** 
  * Código de erro: UserErrorInvalidColumnMappingColumnNotFound 
  * Mensagem de erro: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Causa:** A fonte não inclui uma coluna chamada "AccMngr".

- **Resolução**: Para determinar se existe a coluna "AccMngr", verifique novamente a configuração do conjunto de dados mapeando a coluna dataset do destino.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Código de erro: SftpFailedToConnectToSftpServer

- **Mensagem:**`Failed to connect to SFTP server '%server;'.`

- **Causa**: Se a mensagem de erro contiver a cadeia "A operação de leitura da tomada foi cronometrada após 30.000 milissegundos", uma causa possível é que um tipo de serviço ligado incorreto é utilizado para o servidor SFTP. Por exemplo, pode estar a utilizar o serviço ligado FTP para ligar ao servidor SFTP.

- **Recomendação:** Verifique a porta do servidor alvo. Por predefinição, a SFTP utiliza a porta 22.

- **Causa**: Se a mensagem de erro contiver a cadeia "A resposta do servidor não contém identificação do protocolo SSH", uma causa possível é que o servidor SFTP estrangulou a ligação. A Data Factory criará múltiplas ligações para descarregar a partir do servidor SFTP em paralelo, e por vezes irá encontrar estrangulamento do servidor SFTP. Normalmente, diferentes servidores retornam erros diferentes quando encontram estrangulamento.

- **Recomendação**:  

    Especifique o número máximo de ligações simultâneas do conjunto de dados SFTP como 1 e reexplique a atividade da cópia. Se a atividade for bem sucedida, pode ter a certeza de que estrangular é a causa.

    Se pretender promover a baixa produção, contacte o administrador SFTP para aumentar o limite de contagem de ligação simultânea, ou pode fazer um dos seguintes:

    * Se estiver a utilizar o IR auto-hospedado, adicione o IP da máquina de infravermelhos auto-hospedado na lista de admissões.
    * Se estiver a utilizar o Azure IR, adicione [endereços IP de runtime de integração Azure](./azure-integration-runtime-ip-addresses.md). Se não quiser adicionar uma gama de IPs à lista de autorizações do servidor SFTP, utilize o IR auto-hospedado.

## <a name="sharepoint-online-list"></a>Lista do SharePoint Online

### <a name="error-code-sharepointonlineauthfailed"></a>Código de erro: SharePointOnlineAuthFailed

- **Mensagem:**`The access token generated failed, status code: %code;, error message: %message;.`

- **Causa**: A identificação principal de serviço e a chave podem não ser corretamente definidas.

- **Recomendação**: Verifique a sua aplicação registada (ID principal de serviço) e a chave para ver se estão corretamente definidas.


## <a name="xml-format"></a>Formato XML

### <a name="error-code-xmlsinknotsupported"></a>Código de erro: XmlSinkNotSupportado

- **Mensagem:**`Write data in XML format is not supported yet, choose a different format!`

- **Causa**: Um conjunto de dados XML foi usado como um conjunto de dados de lavatório na sua atividade de cópia.

- **Recomendação**: Utilize um conjunto de dados num formato diferente do conjunto de dados da pia.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Código de erro: XmlAttributeColumnNameConflict

- **Mensagem:**`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Causa**: Foi utilizado um prefixo de atributos, que causou o conflito.

- **Recomendação**: Desacorra um valor diferente para a propriedade "atributoPrefix".


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Código de erro: XmlValueColumnNameConflict

- **Mensagem:**`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Causa**: Um dos nomes do elemento da criança foi usado como o nome da coluna para o valor do elemento.

- **Recomendação**: Desacorda um valor diferente para a propriedade "valueColumn".


### <a name="error-code-xmlinvalid"></a>Código de erro: XmlInvalid

- **Mensagem:**`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Causa**: O ficheiro XML de entrada não está bem formado.

- **Recomendação**: Corrija o ficheiro XML para o tornar bem formado.


## <a name="general-copy-activity-error"></a>Erro de atividade de cópia geral

### <a name="error-code-jrenotfound"></a>Código de erro: JreNotFound

- **Mensagem:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa:** O IR auto-hospedado não consegue encontrar Java Runtime. Java Runtime é necessário para ler fontes específicas.

- **Recomendação**: Verifique o seu ambiente de execução de integração, consulte [use Self-hosted Integration Runtime](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Código de erro: WildcardPathSinkNotSupy

- **Mensagem:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa:** O conjunto de dados da pia não suporta valores wildcard.

- **Recomendação:** Verifique o conjunto de dados da pia e reescreva o caminho sem utilizar um valor wildcard.


### <a name="fips-issue"></a>Problema FIPS

- **Sintomas**: A atividade de cópia falha numa máquina de infravermelhos auto-hospedada ativada pelo FIPS com a seguinte mensagem de erro: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Causa**: Este erro pode ocorrer quando copiar dados com conectores como Azure Blob, SFTP, e assim por diante. As Normas Federais de Processamento de Informação (FIPS) definem um certo conjunto de algoritmos criptográficos que podem ser utilizados. Quando o modo FIPS é ativado na máquina, algumas classes criptográficas de que depende a atividade de cópia são bloqueadas em alguns cenários.

- **Resolução**: Saiba [porque não recomendamos mais o "Modo FIPS"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)e avalie se pode desativar o FIPS na sua máquina de INFRAVERMELHOs auto-hospedada.

    Em alternativa, se apenas quiser deixar a Azure Data Factory contornar o FIPS e fazer com que a atividade seja bem sucedida, faça o seguinte:

    1. Abra a pasta onde está instalada a IR auto-hospedada. O caminho é geralmente *C:\Ficheiros de programa\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Abra o ficheiro *diawp.exe.config* e, em seguida, no final da `<runtime>` secção, `<enforceFIPSPolicy enabled="false"/>` adicione, como mostrado aqui:

        ![Screenshot de uma secção do ficheiro diawp.exe.config mostrando FIPS desativado.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Guarde o ficheiro e, em seguida, reinicie a máquina de infravermelhos auto-hospedada.


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página do Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)