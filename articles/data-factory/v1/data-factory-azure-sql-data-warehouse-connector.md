---
title: Copiar dados de/para o Azure SQL Data Warehouse | Documentos da Microsoft
description: Saiba como copiar dados de/para Azure SQL Data Warehouse com o Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4c431b149edb0677585da3c84e37d64873478ccf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432741"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Copiar dados de e para o Azure SQL Data Warehouse com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Versão 2 (versão atual)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector do Azure SQL Data Warehouse no V2](../connector-azure-sql-data-warehouse.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de/para Azure SQL Data Warehouse. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

> [!TIP]
> Para obter melhor desempenho, utilize o PolyBase para carregar dados para o Azure SQL Data Warehouse. O [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) secção tem detalhes. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Cenários suportados
Pode copiar dados **do Azure SQL Data Warehouse** para os seguintes dados armazena:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Pode copiar dados de arquivos de dados seguintes **ao Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Quando copiar dados do SQL Server ou SQL Database do Azure para o Azure SQL Data Warehouse, se a tabela não existe no arquivo de destino, fábrica de dados pode criar automaticamente a tabela no SQL Data Warehouse, utilizando o esquema da tabela no arquivo de dados de origem. Ver [automaticamente a criação da tabela](#auto-table-creation) para obter detalhes.

## <a name="supported-authentication-type"></a>Tipo de autenticação suportado
O Azure SQL Data Warehouse conector suporte a autenticação básica.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de/para um Azure SQL Data Warehouse, utilizando ferramentas/APIs diferentes.

A maneira mais fácil para criar um pipeline que copia dados de/para Azure SQL Data Warehouse é usar o Assistente para copiar dados. Consulte [Tutorial: Carregar dados para o SQL Data Warehouse com o Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Criar uma **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines. 
2. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados. Por exemplo, se estiver a copiar dados de um armazenamento de Blobs do Azure para um armazém de dados SQL do Azure, criar dois serviços ligados para ligar a sua conta de armazenamento do Azure e o armazém de dados SQL do Azure à fábrica de dados. Para as propriedades do serviço ligado que são específicas para o Azure SQL Data Warehouse, consulte [propriedades do serviço ligado](#linked-service-properties) secção. 
3. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. O exemplo mencionado no último passo, vai criar um conjunto de dados para especificar o contentor de BLOBs e a pasta que contém os dados de entrada. Além disso, crie outro conjunto de dados para especificar a tabela no armazém de dados SQL do Azure que contém os dados copiados do armazenamento de Blobs. Para as propriedades do conjunto de dados que são específicas para o Azure SQL Data Warehouse, consulte [propriedades do conjunto de dados](#dataset-properties) secção.
4. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. No exemplo mencionado anteriormente, vai utilizar BlobSource como uma origem e SqlDWSink como sink para a atividade de cópia. Da mesma forma, se estiver a copiar de armazém de dados SQL do Azure para armazenamento de Blobs do Azure, utilize SqlDWSource e BlobSink a atividade de cópia. Para propriedades de atividade de cópia que são específicas para o Azure SQL Data Warehouse, consulte [propriedades da atividade copy](#copy-activity-properties) secção. Para obter detalhes sobre como usar um arquivo de dados como uma origem ou sink, clique na ligação na secção anterior para seu armazenamento de dados.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON. Para exemplos com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de/para um Azure SQL Data Warehouse, consulte [exemplos JSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para o Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do Azure SQL Data Warehouse.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida como: **AzureSqlDW** |Sim |
| connectionString |Especifique as informações necessárias para ligar à instância do armazém de dados SQL do Azure para a propriedade connectionString. Apenas autenticação básica é suportada. |Sim |

> [!IMPORTANT]
> Configurar [Firewall de base de dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e o servidor de base de dados para [permitir que os serviços do Azure aceder ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se estiver a copiar dados para o Azure SQL Data Warehouse a partir de fora do Azure incluindo de origens de dados no local com o gateway de fábrica de dados, configure o intervalo de endereços IP apropriado para a máquina que está a enviar dados para o Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. O **typeProperties** secção para o conjunto de dados do tipo **AzureSqlDWTable** tem as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista no banco de dados Azure SQL Data Warehouse que o serviço ligado refere-se a. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A atividade de cópia precisar apenas uma entrada e saída de apenas um.

Ao passo que, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

### <a name="sqldwsource"></a>SqlDWSource
Quando a origem é do tipo **SqlDWSource**, as seguintes propriedades estão disponíveis no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. A última instrução de SQL tem de ser uma instrução SELECT no procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

Se o **sqlReaderQuery** é especificado para o SqlDWSource, a atividade de cópia executa esta consulta em relação à origem de armazém de dados SQL do Azure para obter os dados.

Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para criar uma consulta para executar no Azure SQL Data Warehouse. Exemplo: `select column1, column2 from mytable`. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

#### <a name="sqldwsource-example"></a>Exemplo de SqlDWSource

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**A definição do procedimento armazenado:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifica uma consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpo. Para obter detalhes, consulte [secção repetibilidade](#repeatability-during-copy). |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se deve utilizar o PolyBase (quando aplicável), em vez de mecanismo BULKINSERT. <br/><br/> **Com o PolyBase é a forma recomendada para carregar dados para o SQL Data Warehouse.** Ver [utilizar o PolyBase para carregar dados para o Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) secção para as restrições e detalhes. |Verdadeiro <br/>FALSE (predefinição) |Não |
| polyBaseSettings |Um grupo de propriedades que pode ser especificada quando o **allowPolybase** estiver definida como **verdadeiro**. |&nbsp; |Não |
| rejectValue |Especifica o número ou porcentagem das linhas que pode ser rejeitada antes da consulta falha. <br/><br/>Saiba mais sobre as opções de rejeição do PolyBase no **argumentos** secção [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tópico. |0 (predefinição), 1, 2,... |Não |
| rejectType |Especifica se a opção de rejectValue é especificada como um valor literal ou uma percentagem. |Valor de percentagem (predefinição), |Não |
| rejectSampleValue |Determina o número de linhas para obter antes do PolyBase recalcula a porcentagem das linhas rejeitadas. |1, 2, … |Sim, se **rejectType** é **percentagem** |
| useTypeDefault |Especifica como lidar com valores em falta nos ficheiros de texto delimitado quando PolyBase obtém dados a partir do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade da secção argumentos na [criar ficheiro de formato externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |TRUE, False (predefinição) |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera. |Período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

#### <a name="sqldwsink-example"></a>Exemplo de SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Utilize o PolyBase para carregar dados para o Azure SQL Data Warehouse
Usando **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** é uma maneira eficiente de carregamento grande quantidade de dados para o Azure SQL Data Warehouse com um débito elevado. Pode ver um ganho de grandes dimensões no débito ao utilizar o PolyBase, em vez do mecanismo BULKINSERT predefinido. Ver [copie o número de referência do desempenho](data-factory-copy-activity-performance.md#performance-reference) com comparação detalhada. Para obter instruções com um caso de utilização, consulte [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Se os dados de origem estão na **BLOBs do Azure ou do Azure Data Lake Store**e o formato é compatível com o PolyBase, pode copiar diretamente ao Azure SQL Data Warehouse com o PolyBase. Ver **[cópia direta com o PolyBase](#direct-copy-using-polybase)** com detalhes.
* Se seu arquivo de dados de origem e o formato não é suportada originalmente pelo PolyBase, pode utilizar o **[cópia faseada através do PolyBase](#staged-copy-using-polybase)** em vez disso, a funcionalidade. Ele também fornece melhor débito automaticamente converter os dados em formato compatível com o PolyBase e armazenando os dados no armazenamento de Blobs do Azure. Em seguida, carrega dados para o SQL Data Warehouse.

Definir o `allowPolyBase` propriedade **true** conforme mostrado no exemplo a seguir para o Azure Data Factory para utilizar o PolyBase para copiar dados para o Azure SQL Data Warehouse. Quando definir allowPolyBase como true, pode especificar propriedades específicas de PolyBase usando o `polyBaseSettings` grupo de propriedades. consulte a [SqlDWSink](#SqlDWSink) secção para obter detalhes sobre as propriedades que podem ser usados com polyBaseSettings.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Cópia direta com o PolyBase
SQL Data Warehouse PolyBase suporte diretamente a BLOBs do Azure e Azure Data Lake Store (utilizar o principal de serviço) como origem e com os requisitos de formato de ficheiro específico. Se a sua fonte de dados cumprir os critérios descritos nesta secção, pode copiar diretamente de arquivo de dados de origem para o Azure SQL Data Warehouse com o PolyBase. Caso contrário, pode usar [cópia faseada através do PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar dados de Data Lake Store para o SQL Data Warehouse com eficiência, saiba mais a partir da [do Azure Data Factory torna ainda mais fácil e conveniente para descobrir informações a partir dos dados ao utilizar o Data Lake Store com o SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se não forem cumpridos os requisitos, o Azure Data Factory verifica as definições e vai automaticamente para o mecanismo BULKINSERT para o movimento de dados.

1. **Serviço ligado de origem** é do tipo: **AzureStorage** ou **AzureDataLakeStore com autenticação do principal de serviço**.
2. O **conjunto de dados de entrada** é do tipo: **AzureBlob** ou **AzureDataLakeStore**e o formato de tipo sob `type` propriedades é **OrcFormat**, **ParquetFormat**, ou **TextFormat** com as seguintes configurações:

    1. `rowDelimiter` tem de ser **\n**.
    2. `nullValue` está definido como **vazio a cadeia de caracteres** (""), ou `treatEmptyAsNull` está definido como **verdadeiro**.
    3. `encodingName` está definido como **utf-8**, que é **padrão** valor.
    4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, e `skipLineCount` não forem especificadas.
    5. `compression` pode ser **sem compressão**, **GZip**, ou **Deflate**.

    ```JSON
    "typeProperties": {
        "folderPath": "<blobpath>",
        "format": {
            "type": "TextFormat",
            "columnDelimiter": "<any delimiter>",
            "rowDelimiter": "\n",
            "nullValue": "",
            "encodingName": "utf-8"
        },
        "compression": {
            "type": "GZip",
            "level": "Optimal"
        }
    },
    ```

3. Não existe nenhuma `skipHeaderLineCount` em **BlobSource** ou **AzureDataLakeStore** para a atividade de cópia no pipeline.
4. Não existe nenhuma `sliceIdentifierColumnName` em **SqlDWSink** para a atividade de cópia no pipeline. (PolyBase garante que todos os dados são atualizados ou nada é atualizado numa única execução. Para alcançar **repetibilidade**, poderia usar `sqlWriterCleanupScript`).
5. Não existe nenhum `columnMapping` a ser utilizadas na cópia associada na atividade.

### <a name="staged-copy-using-polybase"></a>Cópia faseada através do PolyBase
Quando os dados de origem não cumprem os critérios introduzidos na secção anterior, pode ativar a cópia de dados por meio de um armazenamento de Blobs do Azure teste provisória (não pode ser o armazenamento Premium). Neste caso, Azure Data Factory realiza automaticamente transformações nos dados para atender aos requisitos de formato de dados do PolyBase, em seguida, utilize o PolyBase para carregar dados para o SQL Data Warehouse e em limpeza última seus dados temporários do armazenamento de Blobs. Ver [cópia faseada](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre como copiar dados através de um teste Blob do Azure funciona em geral.

> [!NOTE]
> Quando copiar dados a partir de no local do arquivo de dados no Azure SQL Data Warehouse com o PolyBase e de teste, se a sua versão do Gateway de gestão de dados for inferior 2.4, JRE (Java Runtime Environment) é necessária no seu computador de gateway que é utilizada para transformar a sua origem dados no formato correto. Que Atualize o gateway para a versão mais recente para evitar essa dependência.
>

Para utilizar esta funcionalidade, crie uma [serviço ligado do armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) que se refere à conta de armazenamento do Azure com o armazenamento de BLOBs intermediária, em seguida, especifique a `enableStaging` e `stagingSettings` propriedades para a atividade de cópia conforme mostrado na o código a seguir:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Melhores práticas quando utilizar o PolyBase
As secções seguintes fornecem melhores práticas adicionais para aqueles que são mencionadas [melhores práticas do Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permissão de base de dados necessária
Para utilizar o PolyBase, ele requer o utilizador que está a ser utilizado para carregar dados para o SQL Data Warehouse tem o [permissão de "Controlo"](https://msdn.microsoft.com/library/ms191291.aspx) na base de dados de destino. Uma forma de conseguir isso é adicionar esse utilizador como membro da função de "db_owner". Saiba como fazê-lo ao seguir [esta secção](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Limitação de tipo de tamanho de linha e de dados
Carregamentos do Polybase estão limitados a carregamento linhas ambos inferior **1MB** e não é possível carregar a VARCHR(MAX), nvarchar (Max) ou varbinary (Max). Consulte a [aqui](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Se tiver dados de origem com linhas de um tamanho superior a 1 MB, convém dividir as tabelas de origem verticalmente em vários pequenas onde o maior tamanho de linha de cada um deles não excede o limite. As tabelas mais pequenas, em seguida, é possível carregadas com o PolyBase e mesclados no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse
Para alcançar um débito possível melhor, considere atribuir a classe de recursos maior para o utilizador que está a ser utilizado para carregar dados para o SQL Data Warehouse através do PolyBase. Saiba como fazê-lo ao seguir [alterar um exemplo de classe de recursos de utilizador](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName no Azure SQL Data Warehouse
A tabela seguinte fornece exemplos sobre como especificar a **tableName** propriedade no conjunto de dados JSON para várias combinações de nome de tabela e de esquema.

| Esquema da BD | Nome da tabela | propriedade JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable ou dbo. MyTable ou [dbo]. [MyTable] |
| dbo1 |MyTable |dbo1. MyTable ou [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] ou [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Se vir o seguinte erro, pode ser um problema com o valor especificado para a propriedade tableName. Consulte a tabela para a maneira correta de especificar valores para a propriedade JSON tableName.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colunas com valores predefinidos
Atualmente, a funcionalidade de PolyBase no Data Factory só aceita o mesmo número de colunas como na tabela de destino. Digamos, que tem uma tabela com quatro colunas e um deles é definido com um valor predefinido. Os dados de entrada ainda devem conter quatro colunas. Fornecer um conjunto de dados de entrada de 3 colunas produziria um erro semelhante à mensagem seguinte:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Valor nulo é uma forma especial de valor predefinido. Se a coluna pode ser nula, os dados de entrada (no blob) para essa coluna podem ser vazios (não pode estar em falta o conjunto de dados de entrada). O PolyBase insere nulo para os mesmos no armazém de dados SQL do Azure.

## <a name="auto-table-creation"></a>Criação da tabela automática
Se estiver a utilizar o Assistente de cópia para copiar dados do SQL Server ou SQL Database do Azure para o Azure SQL Data Warehouse e a tabela que corresponde à tabela de origem não existe no arquivo de destino, fábrica de dados pode criar automaticamente a tabela no armazém de dados por u brilhar o esquema da tabela de origem.

Data Factory cria a tabela no arquivo de destino com o mesmo nome de tabela no arquivo de dados de origem. Os tipos de dados para colunas são escolhidos consoante o seguinte mapeamento do tipo. Se for necessário, ele executa conversões de tipo para corrigir qualquer incompatibilidades entre arquivos de origem e de destino. Ele também usa a distribuição da tabela de Round Robin.

| Tipo de coluna de base de dados SQL de origem | Tipo de coluna de armazém de dados do SQL de destino (limite de tamanho) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| bit | bit |
| Decimal | Decimal |
| Numérico | Decimal |
| Flutuante | Flutuante |
| dinheiro | dinheiro |
| Real | Real |
| SmallMoney | SmallMoney |
| Binário | Binário |
| varbinary | Varbinary (até 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Hora | Hora |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Texto | Varchar (até 8000) |
| NText | NVarChar (até 4000) |
| Imagem | VarBinary (até 8000) |
| UniqueIdentifier | UniqueIdentifier |
| char | char |
| NChar | NChar |
| VarChar | VarChar (até 8000) |
| NVarChar | NVarChar (até 4000) |
| Xml | Varchar (até 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo para o Azure SQL Data Warehouse
Conforme mencionado na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, a atividade de cópia executa conversões de tipos automáticas de tipos de origem para o sink de tipos com a seguinte abordagem de passo 2:

1. Converter entre tipos de origem nativas para o tipo de .NET
2. Converter de tipo de .NET para o tipo de sink nativo

Ao mover dados para e a partir do Azure SQL Data Warehouse, são utilizados os seguintes mapeamentos de tipo SQL para o tipo .NET e vice-versa.

O mapeamento é igual a [mapeamento do tipo de dados do SQL Server para o ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de motor de base de dados do SQL Server | Tipo de .NET framework |
| --- | --- |
| bigint |Int64 |
| binário |Byte[] |
| bit |Booleano |
| char |Cadeia de caracteres, Char [] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| decimal |decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Flutuante |Valor de duplo |
| image |Byte[] |
| int |Int32 |
| dinheiro |decimal |
| nchar |Cadeia de caracteres, Char [] |
| ntext |Cadeia de caracteres, Char [] |
| numérico |decimal |
| nvarchar |Cadeia de caracteres, Char [] |
| real |Único |
| ROWVERSION |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimal |
| sql_variant |Objeto * |
| texto |Cadeia de caracteres, Char [] |
| hora |Período de tempo |
| carimbo de data/hora |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Cadeia de caracteres, Char [] |
| xml |Xml |

Também pode mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink na definição da atividade de cópia. Para obter detalhes, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Exemplos JSON para copiar dados de e para o SQL Data Warehouse
Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados de e para o Azure SQL Data Warehouse e o armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das origens qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exemplo: Copiar dados do armazém de dados SQL do Azure para BLOBs do Azure
O exemplo define as seguintes entidades do Data Factory:

1. Um serviço ligado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSqlDWTable](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [SqlDWSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de séries de tempo (por hora, diários, etc.) de uma tabela na base de dados do Azure SQL Data Warehouse para um blob a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do Azure SQL Data Warehouse:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço ligado do armazenamento de Blobs do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada armazém de dados SQL do Azure:**

O exemplo assume que criou uma tabela "MyTable" Azure SQL Data Warehouse e contém uma coluna chamada "timestampcolumn" para dados de séries de tempo.

A definição "externo": "true" informa o serviço Data Factory que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Atividade de cópia num pipeline com SqlDWSource e BlobSink:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **SqlDWSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para o **SqlReaderQuery** propriedade seleciona os dados na hora anterior para copiar.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> No exemplo, **sqlReaderQuery** especificado para o SqlDWSource. A atividade de cópia executa esta consulta em relação à origem de armazém de dados SQL do Azure para obter os dados.
>
> Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).
>
> Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura do conjunto de dados JSON são utilizadas para criar uma consulta (selecione column1, column2 de mytable) para executar no Azure SQL Data Warehouse. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exemplo: Copiar dados de Blobs do Azure para o Azure SQL Data Warehouse
O exemplo define as seguintes entidades do Data Factory:

1. Um serviço ligado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSqlDWTable](#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [SqlDWSink](#copy-activity-properties).

As cópias de exemplo blob de dados (hora a hora, diariamente, etc.) do Azure de séries de tempo para uma tabela no Azure SQL Data Warehouse de base de dados a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

**Serviço ligado do Azure SQL Data Warehouse:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Serviço ligado do armazenamento de Blobs do Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada BLOBs do Azure:**

Dados são captados um blob novo a cada hora (frequência: hora, intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliados com base na hora de início do setor que está a ser processado. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e nome de ficheiro utiliza a parte de hora a hora de início. "externo": "true" definição informa o serviço Data Factory que esta tabela é externa à fábrica de dados e não é produzida por uma atividade na fábrica de dados.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Conjunto de dados do armazém de dados SQL do Azure:**

O exemplo copia dados a uma tabela chamada "MyTable" no Azure SQL Data Warehouse. Crie a tabela no Azure SQL Data Warehouse com o mesmo número de colunas, como esperar que o ficheiro CSV de BLOBs para conter. Novas linhas são adicionadas à tabela de cada hora.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Atividade de cópia num pipeline com BlobSource e SqlDWSink:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Para obter instruções, consulte a [carregar 1 TB para o Azure SQL Data Warehouse menos de 15 minutos com o Azure Data Factory](data-factory-load-sql-data-warehouse.md) e [carregar dados com o Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artigo na documentação do Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
