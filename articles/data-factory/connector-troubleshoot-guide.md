---
title: Solucionar problemas de conectores Azure Data Factory
description: Saiba como solucionar problemas de conector no Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 218031830a7516dfd539e1c0b9b665807822f38d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533156"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Solucionar problemas de conectores Azure Data Factory

Este artigo explora métodos comuns de solução de problemas para conectores no Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

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

- **Resolução**. 

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

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Mensagem de erro: credencial SFTP inválida fornecida para o tipo de autenticação ' SshPublicKey '

- **Sintomas**: você usa a autenticação `SshPublicKey` e clica no seguinte erro:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Causa**: há três causas possíveis:

    1. Se você usar a interface do usuário de criação do ADF para criar o serviço vinculado do SFTP, esse erro significa que a chave privada que você escolher está em um formato incorreto. Você pode usar um formato PKCS # 8 da chave privada SSH, enquanto Observe que o ADF dá suporte apenas ao formato de chave SSH tradicional. Mais especificamente, a diferença entre o formato PKCS # 8 e o formato de chave tradicional é o conteúdo de chave PKCS # 8 começa com " *-----iniciar a chave privada criptografada-----* ", enquanto o formato de chave tradicional começa com " *-----begin RSA Private Key-----* ".
    2. Se você usar Azure Key Vault para armazenar o conteúdo da chave privada ou usar a maneira programática de criar o serviço vinculado do SFTP, esse erro significa que o conteúdo da chave privada está incorreto, provavelmente ele não é codificado em base64.
    3. Conteúdo de chave privada ou credencial inválida.

- **Resolução**. 

    - Para #1 de causa, execute os comandos a seguir para converter a chave no formato de chave tradicional e, em seguida, use-a na interface do usuário de criação do ADF.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - Para a causa #2, para gerar tal cadeia de caracteres, o cliente pode usar as duas maneiras abaixo:
    - Usando a ferramenta de conversão de Base64 de terceiros: Cole todo o conteúdo da chave privada em ferramentas como [codificar e decodificar Base64](https://www.base64encode.org/), codificá-lo em uma cadeia de caracteres de formato Base64 e, em seguida, Cole essa cadeia de caracteres no cofre de chaves ou use esse valor para criar o serviço vinculado SFTP de forma programática.
    - Usando C# código:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Para a causa #3, verifique se o arquivo ou a senha de chave está correto usando outras ferramentas para validar se você pode usá-lo para acessar o servidor SFTP corretamente.
  

## <a name="azure-sql-data-warehouse--azure-sql-database--sql-server"></a>Azure SQL Data Warehouse \ banco de dados SQL do Azure \ SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Código de erro: SqlFailedToConnect

- **Mensagem**: `Cannot connect to SQL database: '%server;', Database: '%database;', User: '%user;'. Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access.`

- **Causa**: se a mensagem de erro contiver "SqlException", o banco de dados SQL lançará o erro indicando que uma operação específica falhou.

- **Recomendação**: Pesquise por código de erro do SQL neste documento de referência para obter mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Se precisar de ajuda adicional, entre em contato com o suporte do Azure SQL.

- **Causa**: se a mensagem de erro contiver "cliente com endereço IP"... " Não tem permissão para acessar o servidor ", e você está tentando se conectar ao banco de dados SQL do Azure, geralmente ele é causado pelo problema do firewall do banco de dados SQL do Azure.

- **Recomendação**: na configuração do firewall do SQL Server do Azure, habilite a opção "permitir que os serviços e recursos do Azure acessem este servidor". Doc de referência: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Código de erro: SqlOperationFailed

- **Mensagem**: `A database operation failed. Please search error to get more details.`

- **Causa**: se a mensagem de erro contiver "SqlException", o banco de dados SQL lançará o erro indicando que uma operação específica falhou.

- **Recomendação**: Pesquise por código de erro do SQL neste documento de referência para obter mais detalhes: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Se precisar de ajuda adicional, entre em contato com o suporte do Azure SQL.

- **Causa**: se a mensagem de erro contiver "PdwManagedToNativeInteropException", geralmente ela é causada por incompatibilidade entre os tamanhos de coluna de origem e de coletor.

- **Recomendação**: Verifique o tamanho das colunas de origem e do coletor. Se precisar de ajuda adicional, entre em contato com o suporte do Azure SQL.

- **Causa**: se a mensagem de erro contiver "InvalidOperationException", geralmente ela será causada por dados de entrada inválidos.

- **Recomendação**: para identificar qual linha encontra o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar as linhas problemáticas para um armazenamento para uma investigação mais aprofundada. Doc de referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Código de erro: SqlUnauthorizedAccess

- **Mensagem**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: a credencial está incorreta ou a conta de logon não pode acessar o banco de dados SQL.

- **Recomendação**: Verifique se a conta de logon tem permissão suficiente para acessar o banco de dados SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Código de erro: SqlOpenConnectionTimeout

- **Mensagem**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa**: pode ser uma falha transitória do banco de dados SQL.

- **Recomendação**: tente atualizar a cadeia de conexão de serviço vinculado com um valor de tempo limite de conexão maior.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Código de erro: SqlAutoCreateTableTypeMapFailed

- **Mensagem**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(colunm name:'%colunmName;') in auto-create table.`

- **Causa**: a tabela de criação automática não pode atender ao requisito de origem.

- **Recomendação**: Atualize o tipo de coluna em "mapeamentos" ou crie manualmente a tabela de coletor no servidor de destino.


### <a name="error-code--sqldatatypenotsupported"></a>Código de erro: SqlDataTypeNotSupported

- **Mensagem**: `A database operation failed. Please check the SQL errors.`

- **Causa**: se o problema ocorrer na fonte SQL e o erro estiver relacionado ao estouro de SqlDateTime, o valor de dados será sobre o intervalo de tipo lógico (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Recomendação**: Converta o tipo para cadeia de caracteres na consulta SQL de origem ou, no mapeamento de coluna da atividade de cópia, altere o tipo de coluna para ' cadeia de caracteres '.

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


### <a name="error-code--sqlbatchwritetimeout"></a>Código de erro: SqlBatchWriteTimeout

- **Mensagem**: `Timeout in SQL write opertaion.`

- **Causa**: pode ser uma falha transitória do banco de dados SQL.

- **Recomendação**: se o problema for reproduzido, entre em contato com o suporte do SQL do Azure.


### <a name="error-code--sqlbatchwriterollbackfailed"></a>Código de erro: SqlBatchWriteRollbackFailed

- **Mensagem**: `Timeout in SQL write operation and rollback also fail.`

- **Causa**: pode ser uma falha transitória do banco de dados SQL.

- **Recomendação**: tente atualizar a cadeia de conexão de serviço vinculado com um valor de tempo limite de conexão maior.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Código de erro: SqlBulkCopyInvalidColumnLength

- **Mensagem**: `SQL Bulk Copy failed due to received an invalid column length from the bcp client.`

- **Causa**: falha na cópia em massa do SQL devido ao recebimento de um comprimento de coluna inválido do cliente bcp.

- **Recomendação**: para identificar qual linha encontra o problema, habilite o recurso de tolerância a falhas na atividade de cópia, que pode redirecionar as linhas problemáticas para um armazenamento para uma investigação mais aprofundada. Doc de referência: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Código de erro: SqlConnectionIsClosed

- **Mensagem**: `The connection is closed by SQL database.`

- **Causa**: a conexão SQL é fechada pelo banco de dados SQL quando alta execução simultânea e conexão de encerramento do servidor.

- **Recomendação**: servidor remoto feche a conexão SQL. Tente novamente. Se o problema for reproduzido, entre em contato com o suporte do SQL do Azure.

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
            

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="error-code--azurebloboperationfailed"></a>Código de erro: AzureBlobOperationFailed

- **Mensagem**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Causa**: problema de impacto na operação do armazenamento de BLOBs.

- **Recomendação**: Verifique o erro em detalhes. Consulte o documento de ajuda do blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Contate a equipe de armazenamento se precisar de ajuda.



## <a name="azure-data-lake-gen2"></a>Azure Data Lake Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Código de erro: AdlsGen2OperationFailed

- **Mensagem**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causa**: ADLS Gen2 gera o erro indicando que a operação falhou.

- **Recomendação**: Verifique a mensagem de erro detalhada lançada por ADLS Gen2. Se ele for causado por uma falha transitória, tente novamente. Se precisar de ajuda adicional, entre em contato com o suporte do armazenamento do Azure e forneça a ID da solicitação na mensagem de erro.

- **Causa**: quando a mensagem de erro contém ' proibido ', a entidade de serviço ou a identidade gerenciada que você usa pode não ter permissão suficiente para acessar o ADLS Gen2.

- **Recomendação**: consulte o documento de ajuda: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Causa**: quando a mensagem de erro contém ' InternalServerError ', o erro é retornado por ADLS Gen2.

- **Recomendação**: pode ser causada por uma falha transitória, tente novamente. Se o problema persistir, entre em contato com o suporte do armazenamento do Azure e forneça a ID da solicitação na mensagem de erro.


## <a name="next-steps"></a>Passos Seguintes

Para obter mais ajuda para solução de problemas, Experimente estes recursos:

*  [Blogue do Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
            
