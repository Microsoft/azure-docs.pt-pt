---
title: Solucionar problemas de conectores de Azure Data Factory | Microsoft Docs
description: Saiba como solucionar problemas de conector no Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143508"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Solucionar problemas de conectores Azure Data Factory

Este artigo explora métodos comuns de solução de problemas para conectores no Azure Data Factory.

## <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Mensagem de erro: O servidor remoto devolveu um erro: (403) proibido

- **Sintomas**: Falha na atividade de cópia com o seguinte erro: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Motivo**: Uma causa possível é que a entidade de serviço ou a identidade gerenciada que você usa não tem permissão para acessar a pasta/arquivo específico.

- **Resolução**: Conceda permissões correspondentes em todas as pastas e subpastas que você precisa copiar. Consulte [este documento](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Mensagem de erro: Falha ao obter o token de acesso usando a entidade de serviço. Erro de ADAL: service_unavailable

- **Sintomas**: falha na atividade de cópia com o seguinte erro:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Motivo**: Quando o servidor de token de serviço (STS) de Propriedade do Azure Active Directory não está disponível, ou seja, muito ocupado para lidar com solicitações, ele retorna um erro HTTP 503. 

- **Resolução**: Execute a atividade de cópia novamente após alguns minutos.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Mensagem de erro: Falha na conversão ao converter de uma cadeia de caracteres em uniqueidentifier

- **Sintomas**: Ao copiar dados da fonte de dados tabulares (como SQL Server) para o Azure SQL Data Warehouse usando cópia preparada e polybase, você encontra o seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Motivo**: O polybase do Azure SQL Data Warehouse não pode converter uma cadeia de caracteres vazia em GUID.

- **Resolução**: Em coletor de atividade de cópia, em configurações do polybase, defina a opção "**usar padrão de tipo**" como false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensagem de erro: Tipo de dados esperado: DECIMAL (x, x), valor incorreto

- **Sintomas**: Ao copiar dados da fonte de dados tabulares (como SQL Server) para o SQL DW usando cópia preparada e polybase, você encontra o seguinte erro:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Motivo**: O polybase do Azure SQL Data Warehouse não pode inserir uma cadeia de caracteres vazia (valor nulo) em uma coluna decimal.

- **Resolução**: Em coletor de atividade de cópia, em configurações do polybase, defina a opção "**usar padrão de tipo**" como false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Mensagem de erro: Mensagem de exceção do Java: HdfsBridge:: CreateRecordReader

- **Sintomas**: Você copia dados para o Azure SQL Data Warehouse usando o polybase e clica no seguinte erro:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Motivo**: A causa possível é que o esquema (largura total da coluna) é muito grande (maior que 1 MB). Verifique o esquema da tabela de destino do SQL DW adicionando o tamanho de todas as colunas:

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

- **Resolução**: Reduzir a largura da coluna para menos de 1 MB

- Ou use a abordagem de inserção em massa desabilitando o polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Mensagem de erro: A condição especificada usando cabeçalho (s) condicional HTTP não foi atendida

- **Sintomas**: Use a consulta SQL para efetuar pull de dados do Azure SQL Data Warehouse e pressione o seguinte erro:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Motivo**: O problema de acesso de SQL Data Warehouse do Azure consulta a tabela externa no armazenamento do Azure.

- **Resolução**: Execute a mesma consulta no SSMS e verifique se você vê o mesmo resultado. Em caso afirmativo, abra um tíquete de suporte para o Azure SQL Data Warehouse e forneça o servidor e o nome do banco de dados do SQL DW para solucionar outros problemas.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Mensagem de erro: O tamanho da solicitação é muito grande

- **Sintomas**: Você copia dados em Azure Cosmos DB com tamanho de lote de gravação padrão e erro de clique *"o**tamanho da solicitação é muito grande**"* .

- **Motivo**: Cosmos DB limita o tamanho de uma única solicitação a 2 MB. A fórmula é, tamanho da solicitação = tamanho do documento único * gravar tamanho do lote. Se o tamanho do documento for grande, o comportamento padrão resultará em um tamanho de solicitação muito grande. Você pode ajustar o tamanho do lote de gravação.

- **Resolução**: Em coletor de atividade de cópia, reduza o valor de ' tamanho do lote de gravação ' (o valor padrão é 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Mensagem de erro: Violação de restrição de índice exclusivo

- **Sintomas**: Ao copiar dados para o Cosmos DB, você encontrou o seguinte erro:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Motivo**: Há duas causas possíveis:

    - Se você usar o comportamento **Inserir** como gravação, esse erro significa que os dados de origem têm linhas/objetos com a mesma ID.

    - Se você usar **Upsert** como comportamento de gravação e definir outra chave exclusiva para o contêiner, esse erro significa que os dados de origem têm linhas/objetos com IDs diferentes, mas o mesmo valor para a chave exclusiva definida.

- **Resolução**: 

    - Para cause1, defina **Upsert** como comportamento de gravação.
    - Para a causa 2, verifique se cada documento tem um valor diferente para a chave exclusiva definida.

### <a name="error-message-request-rate-is-large"></a>Mensagem de erro: A taxa de pedidos é grande

- **Sintomas**: Ao copiar dados para o Cosmos DB, você encontrou o seguinte erro:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Motivo**: As unidades de solicitação usadas são maiores do que a RU disponível configurada em Cosmos DB. Saiba como Cosmos DB calcula RU [aqui](../cosmos-db/request-units.md#request-unit-considerations).

- **Resolução**: Aqui estão duas soluções:

    1. **Aumente o contêiner de ru** para maior valor em Cosmos DB, o que melhorará o desempenho da atividade de cópia, embora incorra mais custo em Cosmos DB. 

    2. Diminua o **writeBatchSize** para um valor menor (como 1000) e defina **parallelCopies** como um valor menor, como 1, o que tornará o desempenho de execução de cópia pior do que o atual, mas não incorrerá mais custo em Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Coluna ausente no mapeamento de coluna

- **Sintomas**: Quando você importa o esquema para Cosmos DB para mapeamento de coluna, algumas colunas estão ausentes. 

- **Motivo**: O ADF infere o esquema dos primeiros 10 documentos Cosmos DB. Se algumas colunas/Propriedades não tiverem valor nesses documentos, elas não serão detectadas pelo ADF, portanto, não aparecerão.

- **Resolução**: Você pode ajustar a consulta como abaixo para impor a coluna a ser exibida no conjunto de resultados com um valor vazio: (Suponha que a coluna "impossível" esteja ausente nos 10 primeiros documentos). Como alternativa, você pode adicionar manualmente a coluna para mapeamento.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Mensagem de erro: O GuidRepresentation para o leitor é CSharpLegacy

- **Sintomas**: Ao copiar dados do Cosmos DB MongoAPI/MongoDB com o campo UUID, você clica no seguinte erro:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Motivo**: Há duas maneiras de representar o UUID em BSON-UuidStardard e UuidLegacy. Por padrão, UuidLegacy é usado para ler dados. Você encontrará um erro se os dados do UUID no MongoDB forem UuidStandard.

- **Resolução**: Na cadeia de conexão do MongoDB, adicione a opção "**uuidRepresentation = Standard**". Para obter mais informações, consulte [cadeia de conexão do MongoDB](connector-mongodb.md#linked-service-properties).

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Mensagem de erro: Credencial SFTP inválida fornecida para o tipo de autenticação ' SshPublicKey '

- **Sintomas**: Você usa `SshPublicKey` a autenticação e clica no seguinte erro:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Motivo**: Há três causas possíveis:

    1. Se você usar a interface do usuário de criação do ADF para criar o serviço vinculado do SFTP, esse erro significa que a chave privada que você escolher está em um formato incorreto. Você pode usar um formato PKCS # 8 da chave privada SSH, enquanto Observe que o ADF dá suporte apenas ao formato de chave SSH tradicional. Mais especificamente, a diferença entre o formato PKCS # 8 e o formato de chave tradicional é o conteúdo de chave PKCS # 8 começa com " *-----iniciar a chave privada criptografada-----* ", enquanto o formato de chave tradicional começa com " *-----begin RSA Private Key-----* ".
    2. Se você usar Azure Key Vault para armazenar o conteúdo da chave privada ou usar a maneira programática de criar o serviço vinculado do SFTP, esse erro significa que o conteúdo da chave privada está incorreto, provavelmente ele não é codificado em base64.
    3. Conteúdo de chave privada ou credencial inválida.

- **Resolução**: 

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais ajuda para solução de problemas, Experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)



